---
title: "Kafka Consumer Lag"
excerpt: 

categories:
    - Kafka
tags:
    - Kafka
last_modified_at: 2021-08-07T11:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Kafka Consumer Lag
컨슈머 랙이란 토픽의 마지막(최신) 오프셋과 컨슈머 오프셋의 차이이다. 프로듀서는 계속해서 파티션에 데이터를 집어 넣고 컨슈머를 데이터를 가져간다. 이 경우에 두 offset의 차이를 consumer lag이라고 한다.

Consumer Lag이 0 인 경우에는 지연이 없다는 뜻이고 Consumer Lag이 늘어날 경우 지연이 생긴 것임을 알 수 있습니다. 이렇게 컨슈머 랙을 모니터링함으로써 컨슈머의 장애를 확인할 수 있고 파티션 개수, 컨슈머 개수를 정할 수 있습니다. 

이러한 컨슈머 랙을 확인하는 방법은 kafka-consumer-groups.sh에서 --describe 옵션을 통해 확인할 수 있습니다. 또는 kafka consumer 라이브러리의 metric메소드를 통해 확인할 수 있습니다. 다만 이러한 metric 메소드는 컨슈머 실행 파일에서 사용되기 때문에 컨슈머 어플리케이션에 문제가 생겼을 때문 무용지물입니다.

그래서 대안으로 나온 것이 외부 모니터링 툴을 사용하는 것입니다. 이러한 외부 모니터링 툴로는 링크드인에서 공개한 오픈 소스인 카프카 버로우가 존재합니다. 버로우를 사용해서 모니터링 대시보드를 만들고 이를 통해 알람을 받는 시스템을 구축할 수 있습니다.
<br>
<br>
출처: 

아파치카프카 애플리케이션 프로그래밍 with 자바

카프카, 데이터 플랫폼의 최강자