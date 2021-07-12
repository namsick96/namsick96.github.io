---
title: "OS File System"
excerpt: "OS"

categories:
    - OS
    - File System
tags:
    - OS
last_modified_at: 2021-06-01T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# File system이란?

파일 시스템은 long-term information storage를 위해 필요합니다.
메모리에 올라간 데이터는 전원이 꺼질 경우 휘발되는 성질을 가지고 있습니다.
따라서 전원이 꺼진 후에도 계속해서 정보를 저장하기 위해서 file system이 필요합니다.
또한 여러 process가 동시에 정보에 접근해도 처리 가능함을 보장하기 위해서 file system이 필요합니다.

이러한 이유로 파일 시스템의 책임은 다음과 같습니다.

1. Secondary 저장소를 구현하기 위한 abstraction (file 형태를 지원해야 한다는 의미입니다.)
2. file을 logical하게 구성할것(Directory 구성에 대한 이야기입니다.)
3. process,사람,machine간의 데이터 공유를 가능하게 해야합니다
4. data를 원치않는 접근으로 부터 보호해야합니다.

## File이란?
파일이란 secondary storage에 저장되어있는 정보의 collection입니다. 파워가 나가도 정보 손실이 없으며 OS는 Storage안의 file의 형태를 이해하고 User에게 일관된 형태로 제공합니다.

이러한 파일의 구조는

Flat: byte sequence

Simple record Structure: lines, fixed length, variable length

complex structure

로 존재할 수 있습니다.


## File의 타입
파일 시스템의 입장에서 파일의 타입을 분류할 수 있습니다. 파일 시스템이이 파일이 Device이다, directory이다 라는 걸 판별 할 수 있고 OS가 파일의 타입을 판별하는경우도 있습니다(확장자 등을 이용해서) 또한 application이 파일의 타입을 인지하는 방법도 있습니다.

ms window는 확장자를 이용해 파일의 타입을 확인합니다.

Unix는 파일이 가지고 있는  8bit 값으로 파일의 형식을 파악합니다. (확장자는 유저가 알기 쉽도록 하는 일종의 표시임. OS는 내부 8bit보고 인지합니다.)

## File Access
파일을 Acess하는 방법은 여러 방법이 있는데 Sequential access, direct access (random access on given block number), Record Access (record number를 바탕으로 Access), Index Access가 있습니다.

Index accesss는 유닉스에서 쓰는 방식인데 File system이 파일의 특정 부분에 index를 가지고 잇는데 이러한 인덱스를 바탕으로 파일을 찾는 것이 index access입니다. 

file Access를 위한 file operation으로 open 함수가 존재합니다.


## File sharing and Protection
파일 시스템은 파일이 여러 유저가 동시에 사용할 수 있게 조정해야 합니다.

이걸 위해서 동시에 접속하는것, 동시에 접속해서 한쪽은 쓰고 한쪽은 읽는 경우 등등 파일의 locking, protection을 고려해야합니다.

이러한 파일 시스템은 protection을 위해서 누가 파일에 access할수 있는지, file에 어떤 작업을 할 수 있는지 구분합니다.

유닉스에서는 Access list와 groups를 지정할 수 있습니다.
https://eunguru.tistory.com/93
링크에서 unix운영체제에서의 ACL 형태를 알 수 있습니다.

## File System implementation

파일 시스템은 On-disk structure로 설계되어져 있다. 크게 4부분으로 나뉘는데

boot control block: 부팅을 관장합니다. 시스템 부팅을 가능하게 합니다. 

partition control block: 디스크는 파티션으로 나뉘어지는데 그 파티션을 관리합니다. 해당 파티션의 블럭,블럭사이즈, free block의 개수를 관리합니다.

directory structure: 디렉토리 구조를 구현하기 위해 기능합니다.

individual files: 각각의 파일 데이터와 파일의 컨트롤 블럭 (FCB)를 관리합니다. 유닉스에서 FCB는 i-node 입니다.

파일 시스템 은 인메모리 structure도 고려를 해야한다. 이러한 in-memory structure에서는 파일-시스템 관리와 performance 향상을 목적으로 만들어졌다. 커널에서 프로세스가 어떤 파일을 쓰고 있는지, 성능 향상을 위해 캐싱을 구현하는 데이터 구조를 만들어 놓는 등의 작업을 수행합니다.


## Recovery
파일 시스템은 언제나 consistency가 필요합니다. 특히 cache에 써져 있는 것이 적용이 안되었을 경우에도 문제가 없어야 하고 i-node block인 경우에는 더더욱 데이터의 consistency가 중요합니다. 그래서 모든 파일 시스템은 system consistency를 체크하는 프로그램이 존재합니다. 그리고 시스템 백업을 지속적으로 해야합니다.
