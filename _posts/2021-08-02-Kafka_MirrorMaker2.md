---
title: "Kafka MirrorMaker2"
excerpt: 

categories:
    - Kafka
tags:
    - Kafka
last_modified_at: 2021-08-02T11:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Kafka MirrorMaker2
카프카 미러메이커2는 서로 다른 카프카 클러스터끼리 각자의 클러스터 내에 있는 토픽을 복제해주는 카프카 어플리케이션이다. 미러메이커2는 단순히 토픽을 복제해주는 것을 넘어 토픽 내의 레코드의 고유한 키, 메시지 값, 파티션을 가지는데 이를 그대로 복사해주는 어플리케이션이다. 이러한 미러메이커2는 카프카 2.4.0부터 추가되어 있으며 토픽을 정확히 복제하고 양방향 복제, 정확히 한번 전달 등을 지원한다.


## MirrorMaker2를 이용한 토픽 복제
미러메이커2는 카프카 바이너리 디렉토리의 config 디렉토리에서 connect-mirror-maker.properties 파일을 수정해서 사용할 수 있습니다. 이러한 토픽 복제는 단방향(일방향), 양방향 방식으로 운영할 수 있습니다.

## MirrorMaker2를 이용한 Active-Standby 클러스터 운영
미러메이커2의 단방향 토픽 복제를 이용한 Active-Standby 클러스터 운영 방식은 카프카 클러스터를 액티브 클러스터, 스탠바이 클러스터로 구분해 만약 액티브 클러스터에서 문제가 발생했을 경우 바로 스탠바이 클러스터로 옮겨서 어플리케이션이 지속적으로 운영될수 있게 해줍니다. 미러메이커2를 이용해 액티브 클러스터의 토픽을 지속적으로 스탠바이 클러스터에 복제해 놓아 failover(대체 작동)을 가능하게 해줍니다. 다만 데이터를 복제할 때 나타나는 복제 lag이 존재할 수 있습니다.

## MirrorMaker2를 이용한 Active-Active 클러스터 운영
Active-Active 운영은 2개 이상의 클러스터를 서로 두고 데이터를 서로 미러링 하면서 사용할 수 있게 해주는 방식입니다. 액티브 클러스터를 2개이상 운영해서 서로 미러링을 하는 형식으로 계속해서 토픽을 복제해줍니다. 이러한 방식을 양방향 방식의 MirrorMaker2 운영이라고 합니다.

## MirrorMaker2를 이용한 Hub&Spoke 클러스터 운영
개별 팀에서 카프카 클러스터를 소규모로 운용하고 이러한 소규모 클러스터를 모아 전사적 데이터 레이크를 만드는 방법으로 Hub&Spoke 방식의 클러스터 운영 방식을 채택할 수 있습니다. mirrormaker2는 중앙의 전사적 카프카 클러스터에 각 팀의 카프카 클러스터에서 미러링된 토픽들을 가지고 있는 형식입니다. 중앙의 전사적 클러스터를 HUB, 각 팀의 클러스터를 Spoke라고 합니다.
<br>
<br>
출처: 

아파치카프카 애플리케이션 프로그래밍 with 자바

카프카, 데이터 플랫폼의 최강자