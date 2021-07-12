---
title: "Redis Memory Managment"
excerpt: "Redis"

categories:
    - Redis
    - NoSQL
tags:
    - Redis
    - NoSQL
last_modified_at: 2021-05-08T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Redis의 메모리 관리 방법
Redis는 인메모리 기반 데이터 저장 관리 및 기술입니다. 그렇기 떄문에 메모리를 어떻게 관리하는지가 핵심입니다. Redis는 메모리 내에서 데이터를 관리하지만 현실적으로 메모리는 한계가 있고 모든 데이터를 메모리에서 관리하기는 불가능하기 때문에 Swap DB를 두고 메모리를 관리합니다. 이러한 메모리 관리 기법에는 LRU, LFU가 있습니다.

## LRU
Least Recently Used의 약자로 사용한지 오래된 데이터를 Enviction하는 알고리즘입니다. 즉 최근에 사용한 데이터를 최대한 메모리 상에 계속 남겨놓고자 하는 알고리즘입니다. 왜냐하면 데이터는 지역성을 가지고 있기 때문에 한번 쓰인 데이터는 앞으로도 쓰일 확률이 높기 때문입니다.

Redis에서 LRU 알고리즘은 이론적으로 완벽한 LRU알고리즘이 아니라 전체 데이터중 몇개의 샘플을 선택하고 그 중에서 가장 LRU한 데이터를 삭제하는 방식으로 작동합니다.

그렇기 떄문에 .conf파일에 maxmemory-sample을 설정해주어 몇개의 샘플을 선택할지 결정해줍니다. 샘플의 개수가 많아지면 이론적인 LRU에 가까워 진다는 장점이 있지만 응답속도가 느려집니다.

## LFU
LFU도 LRU와 같이 데이터의 지역성을 이용한 기법입니다. Least Frequently Used의 약자로 가장 자주 참조된 데이터를 계속 메모리에 남게 하는 알고리즘입니다. LFU의 경우 데이터 노드마다 엑세스 카운터가 존재해서 데이터가 참조될때 마다 1씩 증가하고 이러한 엑세스 카운터를 통해 어떤 데이터를 enviction할지 결정합니다.

.conf 파일에서 LFU의 파라미터는 2개가 있는데 lfu-log-factor는 10이 redis에서 디폴트 값입니다. lfu-decay-time은 몇분마다 데이터 엑세스 카운터를 -1할지 결정하는 것입니다. 0으로 두었을 때는 데이터 엑세스를 할 때마다 모든 엑세스 카운터를 -1을 하게 됩니다.


## LazyFree 파라미터 설정
빅데이터 처리를 하다 보면 메모리가 최대로 꽉 차있는 경우가 발생할 수 있습니다. 이 경우 키 값들이 동시에 삭제되는데 이 경우 프로세스 지연처리,메모리 부족이 동시에 겹쳐서 성능이 지연됩니다.

이러한 메모리 문제의 해결책은 2가지입니다. 메모리를 많이 주는것, 그리고 Lazyfree 파라미터를 배정하는 것입니다. Lazyfree파라미터는 별도의 백그라운드 스레드를 통해 입력과 삭제작업이 수행될 수 있게 해주는 방법입니다.

redis.conf에서 lazyfree 파라미터를 설정할 수 있습니다. 4가지가 있습니다. 

첫번째는 lazyfree-lazy-enviction입니다. 이것을 yes로 설정하면 메모리가 꽉 찰 경우 Delete으로 key를 지우는게 아니라 unlink 명령어로 지우기 때문에 백그라운드에서 작업이 수행되어서 블록킹(작업 지연 상태)에 빠지지 않게 해줍니다.

두번째는 lazy-free-expire입니다. 이거는 일반적인 상황에서 무효화된 키를 삭제할때 DEL 명령어를 안쓰고 UNLINK를 써서 블록킹 현상을 피하게 해줍니다.

세번째는 lazyfree-lazy-server-del입니다. 이 파라미터는 메모리 상에 저장되어있는 키값에 대해 SET,RENAME 명령어가 작동할 경우 DEL 명령어가 내부적으로 작동하는데 이를 UNLINK를 사용하게 만들어줍니다.

네번째는 slave-lazy-flush입니다. master-slave, partition-replication 모델에서 마스터 서버의 데이터를 복제할 때 때에 따라서 기존 복제 데이터를 모두 삭제 후 다시 복제하는 경우도 있습니다. 이러한 작업을 빠르게 하기 위해서 이 파라미터를 yes로 설정해 주면 됩니다. 더 빠른 동기화 작업을 가능하게 해줍니다.


<br>

## 참고문헌 
## 1. 빅데이터 저장 및 분석을 위한 NoSQL & Redis
## 2. https://ssoco.tistory.com/1