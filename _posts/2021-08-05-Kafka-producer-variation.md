---
title: "Kafka producer의 다양한 옵션(idempotence, transaction)"
excerpt: 

categories:
    - Kafka
tags:
    - Kafka
last_modified_at: 2021-08-05T11:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Kafka idempotence Producer

idempotence Producer 즉 멱등성 프로듀서는 여러번 연산을 수행해도 동일한 결과를 나타내는 것을 의미한다. 이러한 멱등성 프로듀서는 데이터를 여러번 전송해도 브로커에는 딱 1개의 데이터만 저장됨을 의미한다. 이러한 멱등성 프로듀서를 만드는 옵션은 enable.idempotence 옵션을 true로 하여 지정할 수 있다. 멱등성 프로듀서는 데이터를 브로커로 전달할 때 프로듀서의 PID와 시퀸스 넘버를 함께 전달한다. 그 결과 브로커는 PID와 시퀸스 넘버를 확인하여 동일한 메세지의 적재 요청이 와도 딱 한번만 데이터를 적재하도록 만든다.



# Kafka transaction Producer
카프카의 트랜잭션 프로듀서는 모든 데이터의 원자성을 만족시키기위한 옵션이다. 다수의 데이터를 트랜잭션으로 묶음으로써 전체 데이터를 처리하거나 처리하지 않도록 만드는 방시기이다. 

이러한 트랜잭션 프로듀서는 컨슈머로 하여금 데이터를 그냥 가져가는 것이 아니라 트랜잭션 처리가 완료된 즉 commit상태인 데이터만 가져가게 된다. 트랜잭션 프로듀서는 파티션에 레코드가 저장될 때 commit레코드를 보내주어 해당 데이터를 가져가도 된다는 것을 표시한다. commit레코드 또한 하나의 레코드로 오프셋 값으로 확인할 수 있다. 따라서 commit레코드가 존재하지 않고 데이터만 존재할 경우 컨슈머는 데이터를 가져갈 수 없다.
<br>
<br>
출처: 

아파치카프카 애플리케이션 프로그래밍 with 자바

카프카, 데이터 플랫폼의 최강자