---
title: "OS Segmentation"
excerpt: "OS"

categories:
    - OS
    - Segmentation
tags:
    - OS
last_modified_at: 2021-05-18T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Segmentation

하나의 program을 여러 Segment로 쪼개서 메모리에 올리는 것을 Segmentation이라고 한다.

이렇게 프로그램을 여러 세그멘트로 쪼갤 경우 (ex. program text segment, stack segment, heap segment 등등...) segment마다 다른 제어(Read/Write), segment 공유(thread 간)가 편해집니다. 이렇게 프로그램을 여러 segment로 쪼개서 physical memory 공간에 올린 것을 segmentation이라고 부릅니다.

<img width="769" alt="스크린샷 2021-05-20 오전 10 47 03" src="https://user-images.githubusercontent.com/61309514/118906404-b7645a80-b958-11eb-8d62-fb7c80553ce3.png">

이러한 Segemtation 기법을 적용하기 위해서는 각 segment의 base index와 크기를 저장하고 있는 segment table이 필요합니다.

<img width="402" alt="스크린샷 2021-05-20 오전 10 46 20" src="https://user-images.githubusercontent.com/61309514/118906355-9dc31300-b958-11eb-94ed-252c7a3140a5.png">

Segmentation를 구현한 컴퓨터의 아키텍쳐는 Physical memory에 접근하기 위한 Logical address 로 segment number와 offset이 결합된 값을 가집니다. segment number를 이용해 segment table에 존재하는 segemnt의 limit값과 base 값을 확인합니다.
이후 limit값과 logical address에서의 offset부분을 확인하여 segement 참조가 제대로 된 것인지를 확인합니다. 그 다음 offset과 segment table에서 가져온 base값을 더해 physical memory에 접근합니다.

또한 이러한 아키텍쳐를 구현할 경우 보통 Segment table에 빠르게 access 하기 위해 Segment-table base register로 테이블의 주소값을 저장하는 레지스터, Segment-table length register로 테이블의 인덱스 값을 저장하는 레지스터를 두고 운용합니다.

Segmentation을 운영하는 것은 Allocation이 Dynamic하게 segment table에 의해 진행됩니다. 그렇기 때문에 External Fragmentation issue가 일어나고 Dynamic allocation policy를 어떻게 할지에 관한 선택을 해야합니다.

이러한 Segmentation은 일반적으로 Paging 기법과 함께 쓰입니다.
