---
title: "Kafka Streams 스트림 처리 어플리케이션 만들어보기"
excerpt: "Kafka Streams"

categories:
    - Kafka
    - java
tags:
    - Kafka
    - java
last_modified_at: 2021-02-10T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# Kafka Streams Wordcount와 Consumer 

Kafka Streams 모듈을 사용해서 Wordcount를 만들어 보고 이를 Consumer를 이용해 로컬 환경에서 출력해 보자

Intellij에서 진행 했습니다.

# Gradle 설정


```gradle
plugins {
    id 'java'
}

group 'producer_practice'
version '1.0'

repositories {
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile group: 'org.apache.kafka', name: 'kafka-clients', version: '2.5.1'
    compile group: 'org.slf4j', name: 'slf4j-simple', version: '1.7.9'
    compile group: 'org.apache.kafka', name: 'kafka-streams', version: '2.6.0'


}
```

Gradle로 빌드하였습니다. gradle 빌드할때 dependencties는 위와 같이 진행 하면 됩니다.

# Kafka Streams Architecture

앞으로 우리가 만들고자 하는 카프카 스트림즈 어플리케이션의 구조는 다음과 같습니다. 

<img width="587" alt="스크린샷 2021-02-10 오후 4 24 56" src="https://user-images.githubusercontent.com/61309514/107478307-98696600-6bbc-11eb-8029-9e72d6a18325.png">


이렇게 namsick-topic은 소스 프로세서로 Producer에서 만든 메세지를 받아 들입니다. 
그 다음 Wordcount 스트림 프로세서가 namsick-topic에 들어온 메세지를 끌어와 Ktable을 만들고 단어 수를 셉니다, 그리고 그 결과를 싱크 프로새서인 namsick-topic2에 넣습니다.
싱크 프로세서인 namsick-topic2에서 우리는 데이터를 가져오는 consumer를 제작해 데이터를 확인 합니다.

그럼 실제 구현한 코드를 보도록 하겠습니다.

# Kafka Producer

카프카 프로듀서를 만들어 보겠습니다.

```java
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;


public class Firstproducer {
    public static void main(String[] args){
        Properties props = new Properties();
        props.put("bootstrap.servers", "test-broker01:9092,test-broker02:9092,test-broker03:9092"); //properties를 추가해 줍시다.
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        Producer<String, String> producer = new KafkaProducer<>(props);
        try{
            producer.send(new ProducerRecord<String, String>("namsick-topic","whole new world"));// 대상이 되는 토픽과 보낼 메세지를 넣어 줍시다.
            producer.send(new ProducerRecord<String, String>("namsick-topic","IU whole"));//한번더! 
        }
        catch(Exception e){
            e.printStackTrace();
        }
        finally {
            producer.close();
        }
    }
}

````

이렇게 프로듀서를 만들어 줍시다. 이건 카프카 스트림즈에서 소스 프로세서에 해당합니다. 

자 이제 그럼 Kafka 스트림에서 스트림 프로세서인 WordCount 부분을 만들어 봅시다.

```java
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.common.utils.Bytes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.Topology;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KeyValueMapper;
import org.apache.kafka.streams.kstream.Materialized;
import org.apache.kafka.streams.kstream.Produced;
import org.apache.kafka.streams.kstream.ValueMapper;
import org.apache.kafka.streams.state.KeyValueStore;

import java.util.Arrays;
import java.util.Locale;
import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class WordCount{

    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-wordcount");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "test-broker01:9092,test-broker02:9092,test-broker03:9092");
        props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());

        final StreamsBuilder builder = new StreamsBuilder();
        // 소스 프로세서 인 namsick-topic을 여기에 적어 줍니다.
        builder.<String, String>stream("namsick-topic")
                .flatMapValues(new ValueMapper<String, Iterable<String>>() {
                    @Override
                    public Iterable<String> apply(String value) {
                        return Arrays.asList(value.toLowerCase(Locale.getDefault()).split("\\W+"));
                    }
                })
                .groupBy(new KeyValueMapper<String, String, String>() {
                    @Override
                    public String apply(String key, String value) {
                        return value;
                    }
                })
                .count(Materialized.<String, Long, KeyValueStore<Bytes, byte[]>>as("counts-store"))// Ktable에 저장이 됩니다.
                .toStream()
                .to("namsick-topic2", Produced.with(Serdes.String(), Serdes.Long()));//내가 보내고 싶은 토픽을 설정해 줍니다.여기서 namsick-topic2는 싱크 프로세서입니다.

        final Topology topology = builder.build();
        System.out.println(topology.describe());

        final KafkaStreams streams = new KafkaStreams(topology, props);
        final CountDownLatch latch = new CountDownLatch(1);

        // Ctrl+C를 처리하기 위한 핸들러 추가
        Runtime.getRuntime().addShutdownHook(new Thread("streams-shutdown-hook") {
            @Override
            public void run() {
                streams.close();
                System.out.println("topology started");
                latch.countDown();
                System.out.println("topology terminated");
            }
        });

        try {
            streams.start();
            latch.await();
        } catch (Throwable e) {
            System.exit(1);
        }
        System.exit(0);
    }


}

```

자 그럼 마지막 싱크 토픽인 namsick-topic2에서 컨슈머를 이용해서 wordcount된 결과를 뽑아 내 봅시다.

```java
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Properties;

public class KafkaBookConsumer1 {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "test-broker01:9092,test-broker02:9092,test-broker03:9092");
        props.put("group.id", "namsick-consumer");
        props.put("enable.auto.commit", "true");
        props.put("auto.offset.reset", "latest");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.LongDeserializer");
        // Long type의 Value값이 serialize되어서 오니 deserialize도 필요 합니다.
        KafkaConsumer<String, Long> consumer = new KafkaConsumer<>(props);// consumer로 들어오는 Key 와 value 값은 각각 String ,Long 입니다. 
        consumer.subscribe(Arrays.asList("namsick-topic2"));// 어느 토픽에서 가져 올지 보여 줍니다.
        try {
            while (true) {
                ConsumerRecords<String, Long> records = consumer.poll(5);
                for (ConsumerRecord<String, Long> record : records)
                    System.out.printf("Topic: %s, Partition: %s, Offset: %d, Key: %s, Value: %d\n", record.topic(), record.partition(), record.offset(), record.key(), record.value());// 레코드를 하나 하나 펼쳐 봅니다.
            }
        } finally {
            consumer.close();
        }
    }
}
```

이렇게 지정을 하면 Intellij의 터미널에서 key,value가 프린트 됩니다.

출처: 책: 카프카, 데이터 플랫폼의 최강자, 소스코드: [안지훈 Github](https://github.com/namsick96/KafkaWordcount)