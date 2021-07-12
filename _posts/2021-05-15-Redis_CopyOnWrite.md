---
title: "Redis 에서 Copy on Write(COW) 현상이란 무엇인가"
excerpt: "Redis"

categories:
    - Redis
    - NoSQL
tags:
    - Redis
    - NoSQL
last_modified_at: 2021-05-15T22:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Redis에서 Copy on Write 현상 이란?
Linux(Unix)에서는 자식 프로세스(child process)를 생성(fork)하면 같은 메모리 공간을 공유하게 됩니다..그런데 부모 프로세스가 데이터를 새로 넣거나, 수정하거나, 지우게 되면 같은 메모리 공간을 공유할 수 없게 된다.   이때 부모 프로세스는 해당 페이지를 복사한 다음 수정한다.   이것을 Copy-on-Write(COW)라고 한다.

이러한 COW작업을 위해서 메모리 요구량은 해당 메모리 복사를 위해 늘어난다. 이러한 복사 작업은 SAVE 명령어를 입력할때(BGSAVE 포함), bgrewriteaof명령어를 사용해서 aof파일을 만들때, master-slave,partition-replication server환경 즉, 동기화가 발생할때, auto-aof-rewrite-percentage 파라미터가 적용되어 appendonly파일이 일정수준 이상 커지면 다시 갱신되어야 할때 발생합니다.


<br>

## 참고문헌: 빅데이터 저장 및 분석을 위한 NoSQL & Redis
## http://redisgate.kr/redis/configuration/copy-on-write.php 