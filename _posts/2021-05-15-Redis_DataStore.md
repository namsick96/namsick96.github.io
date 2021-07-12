---
title: "Redis DataStore(persistence 보장을 위한 데이터 저장 방법)"
excerpt: "Redis"

categories:
    - Redis
    - NoSQL
tags:
    - Redis
    - NoSQL
last_modified_at: 2021-05-15T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Redis의 데이터 Persistence 보장을 위한 데이터 저장 방법
Redis 서버는 인메모리 기반이기 때문에 서버가 종료될 경우 메모리에 있던 데이터들이 다 날라가 버립니다. 그렇기 때문에 중요한 데이터의 경우 디스크 저장장치에 파일로 저장을 해야합니다. 이렇게 파일로 저장하는 방법은 2가지가 있는데 하나는 RDB파일로 저장하는 것, 다른 하나는 AOF명령어를 사용하여 aof파일로 저장하는 방식입니다.

## RDB파일을 이용해 저장하는 방법
Redis에서 save 명령어를 통해서 저장하는 방법이 있습니다. save 명령어 뒤에 첫번째 파라미터는 저장되는 주기를 나타내고 두번째 파라미터는 몇개의 key를 저장할지 알려줍니다.

예를 들어 save 60 1000 명령어를 입력하면 60초마다 1000개의 key를 저장한다는 의미입니다.

저장하는 파일의 이름은 dump.rdb입니다.
dbfilename [filename] 명령어를 통해 변경할 수 있습니다.

## AOF명령어를 이용해 저장하는 방법
bgrewriteaof 명령어를 통해 이후에 입력,수정,삭제된 데이터 모두를 저장해 줍니다. aof는 메모리의 최종 상태에 대해 Append only file을 만드는 작업입니다.

<br>

## 참고문헌: 빅데이터 저장 및 분석을 위한 NoSQL & Redis