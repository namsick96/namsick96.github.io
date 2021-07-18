---
title: "Kafka Command Line Tools 톺아보기"
excerpt: 

categories:
    - kafka
tags:
    - kafka
    - AWS
last_modified_at: 2021-07-18T16:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 

# kafka command line tool이란?

kafka command line tool은 카프카를 운영할 때 카프카 브로커 운영에 대한 다양한 명령을 내리는 tool들이다. 이러한 명령어 들은 ssh로 원격으로 접속하여 명령을 내려도 되고 브로커에 9092 포트(카프카 기본 포트)로 접속 가능한 컴퓨터에서 명령을 내릴 수 있다.


# kafka-topic.sh<br>
kafka-topic.sh는 카프카에 토픽에 관련된 명령어를 내릴 수 있게한다.

우선 첫번째로는 topic 생성의 명령을 내릴 수 있다. --create 옵션으로 카프카 토픽을 새롭게 만들 수 있다. --bootstrap-server 로 카프카_ip:9092를 설정하고 --topic으로 토픽 명, --partitions로 파티션 개수, --replication-factor로 토픽의 파티션을 복제할 복제 개수 등을 설정할 수 있다.

두번째로는 토픽 리스트 조회입니다. 해당 카프카 클러스터에 어떤 토픽들이 있는지 이름을 확인할 수 있습니다. --bootstrap-server 브로커_ip:9092 , --list 옵션을 통해서 확인 할 수 있다.

개별 토픽을 상세 조회할 수도 있습니다. --bootstrap-server 브로커_ip:9092 , -describe, --topic [topic이름] 옵션을 통해 확인 할 수 있습니다.

마지막으로 토픽 옵션 수정이 있습니다. 토픽의 옵션을 변경하기 위해서 kafka-topic.sh와 kafka-configs.sh두개가 사용되는 파티션 개수 변경은 전자, 토픽삭제 정책인 리텐션 기간 변경은 후자를 사용해야합니다. 또한 토픽 옵션 중 다이나믹 토픽 옵션인 log.segment.bytes, log.retention.ms등은 kafka-configs.sh를 통해 수정될 수 있습니다.

# kafka-console-producer.sh<br>
kafka-console-producer.sh스크립트 파일을 이용해서 토픽에 레코드를 넣을 수 있습니다. 레코드는 데이터를 지칭하는 말로 메세지 키와 메세지 value로 이루어져있습니다. 만약 key를 설정하지 않았을 경우 key값은 null로 입력됩니다.

kafka-console-producer.sh를 커맨드 라인에 쓰면서 옵션으로 --bootstrap-server 브로커_ip:9092 , --topic [topic이름], --property "parse.key=true" (키를 나타내기 위함), --property "key.seperator=:"(: 앞에는 키, 뒤는 value) 옵션을 주어서 레코드를 생성할 수 있습니다. 이렇게 명령어를 입력하면 키:valu면 값 형식으로 데이터를 입력해 브로커의 토픽으로 보낼 수 있습니다. 이렇게 보내진 레코드는 토픽의 파티션에 저장됩니다.

# kafka-console-consumer.sh<br>
kafka-console-consumer.sh스크립트 파일을 이용해서 토픽에서 레코드를 가져올 수 있습니다.

kafka-console-consumer.sh를 커맨드 라인에 쓰면서 옵션으로 --bootstrap-server 브로커_ip:9092 , --topic [topic이름], --from-beginning 을 줄 수 있습니다. --from-beginning 옵션은 토픽에 저장된 가장 처음 데이터 부터 출력하도록 만드는 것입니다.

만약 레코드의 메세지 키와 메세지 값을 확인하고 싶으면 --property "print.key=true" (키를 나타내기 위함), --property "key.seperator=:" 를 설정으로 주어 출력할 수 있습니다.

--group 옵션을 통해 컨슈머 그룹을 생성할 수 있는데 이 컨슈머 그룹은 가져간 메세지에 대한 표시인 offset을 공유합니다. 즉 동일 그룹 내의 다른 컨슈머가 메세지를 미리 가져갔으면 또 다른 컨슈머는 그 뒤의 메세지를 가져갑니다.

consumer에서 주의할 것은 producer에서 생성한 순서대로 데이터가 출력되는것이 아니라는 점이다. 이는 데이터를 파티션별로 나누어 저장하기 때문에 그러한 것이다.


# kafka-consumer-groups.sh<br>
미리 생성된 컨슈머 그룹을 확인 하는 방법은 --list 옵션을 주는 것입니다. kafka-consumer-groups.sh명령어와 함께 bootstrap옵션을 주고 --list 옵션을 주면 생성된 컨슈머 그룹을 확인할 수 있습니다. 

특정 group의 세부 정보를 알고 싶으면 --group [group 명] --describe 옵션을 --list 옵션 대신 주면 됩니다.

# kafka-verifiable-producer,consumer.sh<br>
kafka-verifiable-producer.sh/kafka-verifiable-consumer.sh를 사용하면 String 타입 레코드를 코드 없이 주고 받을 수 있습니다.옵션으로 bootstrap-server, max-message(보낼 메세지 개수),--topic [topic 이름]을 설정해 주면 레코드를 브로커에 보내고 받을 수 있습니다. 받는 쪽에서 group-id 옵션을 줘서 consumer group id를 설정할 수 있습니다.

# kafka-delete-records.sh<br>
이미 카프카 브로커에 저장된 데이터를 지우는 방식으로 kafka-delete-records.sh를 사용할 수 있습니다. kafka-delete-recods.sh는 가장 오래된 데이터 부터 특정 시점의 offset까지 삭제할 수 있습니다.

이러한 delete의 조건은 .json 형식의 파일에 담아져서 보내집니다.

{"partitions":[{"topic":"test","partition":0,"offset":50}],"version":1} 이렇게 topic, partition, offset 정보를 담고 있는 text를 json형식으로 .json 파일에 저장합니다.

그리고 kafka-delete-records.sh를 실행하며 옵션으로 bootstrap과 --offset-json-file [json 파일] 을 지정해 줍니다. 이렇게 지정해 주고 스크립트를 실행하면 json 파일 내에 지정된대로 데이터가 삭제됩니다. 그리고 터미널에 각 파티션에서 삭제가 된 오프셋 정보를 출력합니다.

여기서 주의해야할 점은 offset을 기반으로 삭제가 이루어지므로 특정한 데이터만을 삭제하는 것은 불가능하다는 점이다.



참조문헌: 아파치 카프카 애플리케이션 프로그래밍 with 자바, 카프카, 데이터 플랫폼의 최강자, [Apache Kafka documentation](https://kafka.apache.org/documentation/)