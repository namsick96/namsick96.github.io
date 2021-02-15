---
title: "Kafka Streams"
excerpt: "Kafka Streams"

categories:
    - Kafka
    - java
tags:
    - Kafka
    - java
last_modified_at: 2021-02-15T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# Stream Proccessing이란?

카프카는 대규모 메세지를 저장하고 빠르게 처리하기 위해 만들어진 제품이지만 연속된 메세지인 스트림을 처리하는데도 사용할 수 있다. 카프카는 Streams API를 통해 스트림을 처리하는 것을 도와준다.

이러한 스트림 프로세싱의 장점은 어플리케이션이 이벤트에 즉각적으로 발생하기 때문에 지연시간이 적습니다.(배치 처리에 비해서) 또한 데이터를 저장한 후 분석하지 않기 때문에 더 많은 데이터를 분석할 수 있습니다. 그리고 지속적으로 유입되는 데이터 분석에 최적화되어 있습니다. 마지막으로 스트림 처리는 대규모 데이터베이스에 대한 요구를 줄일 수 있어서 인프라에 독립적으로 수행할 수 있습니다.

스트림 처리를 하다 보면 이전에 처리한 스트림을 참조해야할 경우가 있습니다. 이런 경우를 상태 기반 처리라고 하는데 이를 위해서 상태 저장소가 필요합니다.

# Kafka streams

카프카 스트림즈는 카프카에서 스트림 프로세스를 위해서 만들어진 클라이언트 라이브러리이다.

카프카 스트림즈는 스트림 처리를 하는 프로세서들이 서로 연결되어 토폴로지를 구성하여 데이터를 처리하는 API이다.

**용어 정리 **

스트림: 끊임 없이 전달 되는 데이터 세트 (카프카 스트림즈에서는 키,값 쌍 이다.)

스트림 처리 어플리케이션: 카프카 스트림 클라이언트를 사용하는 어플리케이션.토폴로지에서 처리되는 로직을 의미함.

스트림 프로세서: 프로세서 토폴로지를 이루는 하나의 노드를 말함. 데이터를 스트림으로 부터 받아서 다음 연결 된 프로세서에 보내줌

소스 프로세서: 위쪽으로 연결된 프로세서가 없는 프로세서를 지칭함. 이 프로세서는 하나 이상의 카프카 토픽에서 데이터 레코드를 읽어서 아래족 프로세서에 전달한다.

싱크 프로세서: 이것은 토폴로지 아래쪽에 프로세서가 없는 프로세서를 지칭함.이전 프로세서에게 받은 데이터를 카프카 토픽에 저장함.

<img width="587" alt="스크린샷 2021-02-10 오후 4 24 56" src="https://user-images.githubusercontent.com/61309514/107478307-98696600-6bbc-11eb-8029-9e72d6a18325.png">

전체 구성을 도식화 하면 다음과 같다. 여기서 태스크는 스트림 스레드라고 부르기도 하며 태스크의 숫자는 여러개가 될 수 있다. 

# 카프카 스트림즈 환경 설정

gradle 로 카프카, 카프카 클라이언트,카프카 스트림즈 패키지를 빌드 해주면 된다.

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


# 카프카 스트림즈 예제 (파이프 프로그램 만들기)

샘플 코드는 다음과 같습니다.

```java
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.Topology;

import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class Pipe {

    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-pipe");//스트림 어플리케이션의 이름입니다.
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "test-broker01:9092,test-broker02:9092,test-broker03:9092");//여기에 스트림즈 어플리케이션이 접근할 브로커:ip 를 적어 주면 됩니다.
        props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass())
        props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        //키 값과 value 값을 시리얼라이즈 해줘야합니다.

        final StreamsBuilder builder = new StreamsBuilder();//토폴로지를 만들기 위해서 빌더는 StreamBuilder를 씁니다.

        builder.stream("namsick-topic").to("namsick-topic2");
        // 이렇게 소스 프로세서와 연결될 토픽과 싱크 프로세서와 연결될 토픽을 적어 줍니다.

        final Topology topology = builder.build();//빌드해서 토폴로지를 최종적으로 만들어 줍니다.
        System.out.println(topology.describe());
        // 이 명령어로 토폴로지 상태를 파악합니다.

        final KafkaStreams streams = new KafkaStreams(topology, props); // 이렇게 카프카 스트림즈에 토폴로지와 properties를 넣어주면 스트림즈 어플리케이션을 만들 수 있습니다. 
        final CountDownLatch latch = new CountDownLatch(1);// 이걸 통해서 만약 메인 스레드는 계속 대기하고 Ctrl+C가 입력되면 종료되게 합니다.

        // Ctrl+C를 처리하기 위한 핸들러 추가
        Runtime.getRuntime().addShutdownHook(new Thread("streams-shutdown-hook") {
            @Override
            public void run() {
                streams.close();
                latch.countDown();
                System.out.println("topology terminated");
            }
        });

        try {
            streams.start();
            System.out.println("topology started");
            latch.await();
        } catch (Throwable e) {
            System.exit(1);
        }
        System.exit(0);
    }
}

```

해당 java 파일을 실행하면 입력된 스레드가 출력됩니다.

<br>

# 행 분리 예제 프로그램 만들기

이전에는 파이프 프로세스를 만들어 보았지만 이제는 행 분리 프로그램을 만들어 봅시다.

코드를 살펴 보기 전 Kstreams의 두 메소드를 확인 해 봅시다.

flatMap 메소드는 새로운 스트림을 만들 때 키와 값 모두 새롭게 만들어서 사용합니다.

flatMapValues는 새로운 스트림을 만들 때 키는 변경하지 않고 값만 변경합니다. 

행 분리 예제 프로그램에서 우리는 flatMapValues를 만들어서 값을 array로 변환해 행을 분리합니다. 

```java
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.StreamsBuilder;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.Topology;
import org.apache.kafka.streams.kstream.KStream;

import java.util.Arrays;
import java.util.Properties;
import java.util.concurrent.CountDownLatch;

public class LineSplit {

    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "streams-linesplit");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "test-broker01:9092,test-broker02:9092,test-broker03:9092");
        props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());

        final StreamsBuilder builder = new StreamsBuilder();

        KStream<String, String> source = builder.stream("namsick-topic");
        source.flatMapValues(value -> Arrays.asList(value.split("\\W+")))
                .to("namsick-topic2");// 람다식을 이용해 array 형식으로 값을 바꾼후 나눠 줍니다. 그리고 그 결과를 namsick-topic2로 보내줍니다.

        final Topology topology = builder.build();
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
코드가 근본적으로 Pipe.java와 많이 비슷합니다. 다만 차이가 있는 부분은 flatMapper 부분입니다.

이상으로 기본적인 스트림 프로세싱을 알아 보았습니다.
상태 기반 스트림 프로세싱에 대해서 알고 싶다면 [Kafka Streams 스트림 처리 어플리케이션 만들어보기](https://namsick96.github.io/kafka/java/Kafka-Streams-Application(WordCount)/)를 확인해 보세요.


<br>
출처: 책: 카프카, 데이터 플랫폼의 최강자, 소스코드: [안지훈 Github](https://github.com/namsick96/KafkaWordcount)