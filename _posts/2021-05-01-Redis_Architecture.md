---
title: "Redis Architecture"
excerpt: "Redis"

categories:
    - Redis
    - NoSQL
tags:
    - Redis
    - NoSQL
last_modified_at: 2021-05-01T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Redis Architecture
레디스 아키텍쳐는 크게 3가지 영역으로 구성되어 있습니다. 메모리 영역, 파일 영역, 프로세스 영역으로 3가지로 나눠져 있습니다.

<img width="704" alt="스크린샷 2021-05-16 오전 10 23 56" src="https://user-images.githubusercontent.com/61309514/118382472-f29d1b80-b630-11eb-9297-a299f46d35ff.png">


출처:https://m.blog.naver.com/PostView.nhn?blogId=jevida&logNo=221540420261&proxyReferer=https:%2F%2Fwww.google.com%2F
## Memory Area

메모리 영역은 Resident Area, Data Structure로 나눠져 있습니다. Resident 영역은 모든 데이터가 가장 먼저 저장되는 영역이자 실제 작업이 수행되는 영역입니다. working set이라고 부르기도 합니다. Data structure영역은 서버를 관리하기 위한 각종 데이터들을 모아 놓는 공간입니다.

## File Area
Redis는 메모리 상에 모든 데이터를 저장하는 In memory 기반의 NoSQL 데이터 베이스입니다. 하지만 중요한 데이터의 경우 디스크에 저장하여 Data persistence를 보장해야합니다. 이를 위해서 Redis는 두가지 데이터 파일 형태를 제공합니다. 첫번째로는 AOF 파일입니다. Append only file로 현재 메모리에 올라가 있는 데이터의 스냅샷을 저장하도록 도와줍니다. 두번째로는 DUMP파일입니다. AOF파일과 같이 사용자 데이터를 디스크에 저장할 수 있지만 소량만 가능합니다.

## Process Area
redis-server,redis-cli로 실행되는 프로세스 또한 Redis 아키텍쳐의 한 부분입니다. redis-server 프로세스는 레디스 인스턴스를 관리해주며 client로 부터 요청된 작업을 수행합니다. Redis server 프로세스는 4개로 구성되는데 작업을 하는 main thread, 그리고 3개의 sub스레드로 구성되어 있씁니다. redis-cli 프로세스는 클라이언트로 부터 명령어를 받는 프로세스입니다. redis-sentinel 프로세스는 레디스 서버의 상태를 감시해서 장애 복구에 쓰입니다.
<br>

## 참고문헌: 빅데이터 저장 및 분석을 위한 NoSQL & Redis