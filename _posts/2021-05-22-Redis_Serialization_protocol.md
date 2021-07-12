---
title: "Redis Request Response Protocol"
excerpt: "Redis"

categories:
    - Redis
    - NoSQL
    - Network
tags:
    - Redis
    - NoSQL
last_modified_at: 2021-05-22T22:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Redis Request Response Protocol(Redis Serialization Protocol)
레디스 클라이언트와 서버가 통신하는 방법은 Redis Serialization protocol 줄여서 RESP를 사용합니다. 이 프로토콜은 Redis 2.0 부터는 표준 프로토콜이 되었습니다. 이러한 RESP는 Server-client통신에만 사용되고 만약 레디스 클러스터 내 노드끼리 통신하는 경우에는 RESP가 아닌 binary protocol이 사용됩니다.

RESP에는 4가지 데이터 타입이 존재하는데
Simple Strings, Errors, Integers, Bulk Strings, Arrays가 있습니다. 각가의 데이터 타입은 구분자를 가집니다. 구분자는 각각의 데이터 타입이 어떤 데이터 타입인지를 나타내주는 문자입니다. 앞에 언급한 순서대로 +,-,:,$,* 를 구분자로 가집니다. 각각의 데이터 타입은 끝날 때 항상 \r\n을 맨 뒤에 가지고 있습니다.

Bulk Strings 데이터 타입의 경우 "$문자열길이\r\n문자열\r\n" 형식으로 구성됩니다.

Array의 경우 "*Array길이\r\n(배열안에 들어올 데이터 타입의 형식에 맞는 표현)"과 같은 형식으로 구성됩니다.

앞서 말한 것 처럼 Redis 서버와 클라이언트가 통신 할때도 RESP 방식을 사용하는데 Bulk String의 Array 타입을 활용해서 명령어를 전송합니다.

이런 RESP 방식의 장점은 데이터를 쉽고 간단하게 표현할 수 있고 파싱이 용이합니다.(기호 방식으로 데이터를 표현하기 때문입니다.) 또한 정수, 문자, 배열 데이터를 직렬화 할 수 있고(네트워크 통신을 하기 편하게) Redis client,server간의 통신에만 사용되기 때문에 보안성이 뛰어납니다.

<br>

## 참고문헌: 빅데이터 저장 및 분석을 위한 NoSQL & Redis
## https://sgc109.github.io/2020/07/22/redis-resp-protocol/