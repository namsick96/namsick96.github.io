---
title: "DB file Allocation과 Free Space Management"
excerpt: 

categories:
    - OS
    - File System
    - DB
tags:
    - OS
last_modified_at: 2021-06-03T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 

# Allocation Method
DB에 데이터를 어떻게 Allocation할지 여러 방법이 존재합니다.

## contiguous allocation
첫번째로  contiguous allocation이 존재합니다. starting location에서 필요한 블럭만큼 차지하는 것입니다. 디렉토리 메타 정보에 starting point, length만 알면 됩니다. 이러한 방법은 random access에서 효율적입니다.

하지만 dynamic storage allocation problem인 external fragment가 존재하기 때문에 저장공간이 낭비됩니다. 또한 파일이 커질 수 없고 그렇기 때문에 필요한 공간보다 더 많은 공간을 차지해야 합니다.

그렇기 때문에 미리 파일 사이즈를 알고 파일이 변하지 않는 read only 파일 시스템에 많이 쓰입니다.
 
## Linked allocation
파일이 링크드 리스트 형식으로 이어진 블락으로 나눠져 저장되는 경우입니다.

디렉토리 메타 정보에서 starting address만 알면 데이터를 access할 수 있어서 simple하다는 것과 공간 낭비가 적다는 장점이 있씁니다.
문제는 random access가 비효율 적이고 pointer를 위한 공간이 있어야 한다는점이고 중간에 포인터가 한번 없어지면 전체 데이터를 못 찾게 되는 reliability이슈가 발생합니다.

## indexed allocation
디렉토리 메타 정보에 인덱스 블럭만 가지고 있는 저장 방식입니다. 이 인덱스 블럭에는 해당 파일에 해당하는 블럭들이 모두 저장되어 있는 인덱스 테이블이 존재합니다. external fragementation없이 랜덤 access가 나쁘지 ㄴ은 수준으로 구현 가능합니다. 멀티 레벨 구조를 가질 수 있다는 장점이 있씁니다.

유닉스가 이러한 indexed allocation을 멀티레벨로 가지고 있습니다. 유닉스 파일 시스템에는 inode가 모든 파일마다 존재하는데 이런 inode에 direct blocks, single indirect, double indirect에서 인덱스 블럭을 운용해 파일의 데이터를 가져올 수 있습니다.

# Free space management
계속해서 DB에 어떤 블럭이 비어 있는지 관리를 해야합니다.
커널은 이런 빈 공간을 관리를 하는데 비트맵을 씁니다. 각 블럭에 대한 bit를 가지고 있어서 해당 브럭을 쓰고 잇는지 안쓰고 있는지 bit으로 표현합니다.

또는 빈 블럭을 링크로 돌아가면서 관리할 수 도 있습니다.

하나의 디스크 블럭에 자기 자신 뒤에 오는 free block의 개수를 적는 grouping방식을 사용할 수 있습니다. 이렇게 grouping된 블럭중 가장 뒤에 있는 블럭은 또다른 빈 블럭의 시작 점에 대한 주소를 가지고 있을 수 있습니다.

아니면 연속된 빈 블럭의 첫번째 블럭의 주소와 빈 블럭의 개수를 저장하는 방식도 있습니다. (Counting)