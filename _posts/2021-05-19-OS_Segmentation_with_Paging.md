---
title: "OS Segmentation with Paging"
excerpt: "OS"

categories:
    - OS
    - Segmentation
    - Paging
tags:
    - OS
last_modified_at: 2021-05-19T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Segmentation with Paging

이러한 Segmentation 기법은 paging 기법과 같이 사용 될 수 있습니다. Paging은 small internal fragmentation이 발생된다는 문제가 있고 Segmentationdms external fragmentation이 발생한다는 문제가 있습니다. 이러한 두 기법을 섞은 방식이 대부분의 시스템에서 사용되고 있습니다. 두 방식을 섞는 방법은 하나의 Segment안에 여러개의 page를 두는 방법입니다.

Segment는 일반적으로 굉장히 크기 때문에 여러개의 page로 표현이 가능합니다. 이런 segment를 여러 페이지로 쪼개서 관리하는 physical memory에 올리는 방법이 더 적은 페이지 테이블을 운용하고 segment를 더 효율적으로 운용하는 방법입니다.

<img width="660" alt="스크린샷 2021-05-20 오전 10 44 34" src="https://user-images.githubusercontent.com/61309514/118906233-5f2d5880-b958-11eb-8142-6537b2b4ca0c.png">

이렇게 Segmentation과 paging을 혼합한 경우 CPU에서 logical address를 나타내는 방식은 segment table index, page table index, page table offset을 합친 형식입니다. 각 프로스세마다 존재하는 segment table에 먼저 접근한 다음 page table의 주소를 받고 page table에 logical address에서 지정한 인덱스를 통해 접근한 다음 page table에서 확인할 수 있는 프레임 값과 offset을 합쳐서 physical memory에 접근합니다.

이렇게 아키텍쳐를 설계할 경우 스레드 간 메모리 공유가 편리하고 segment별로 특정 작업만 수행하도록 protection을 할 수 있습니다.



# Segmentation with Paging in Intel x86 

<img width="622" alt="스크린샷 2021-05-20 오전 10 43 50" src="https://user-images.githubusercontent.com/61309514/118906204-44f37a80-b958-11eb-9de7-9c4f2e5202c7.png">

인텔 아키텍쳐에서는 Logical address가 segmentation을 해서 Linear Address로 변하고 Linear Addressss는 Paging을 해서 Physical address가 됩니다. 이러한 전환은 인텔 아키텍쳐의 특징입니다. Logical Address는 16+32bit이고 Linear address는 32bit(16bit도 가능)입니다.

Intel x86 아키텍처에서 Segmentation 하는 방법은 다음과 같습니다. 먼저 CPU로 부터 생선된 Logical Address에서 앞부분인 16bit Segment selector를 통해 Segment base address를 기져온 다음 Logical address의 32bit를 offset으로 하여 Linear address space에 접근합니다.

Linear address를 이용해 2단 paging을 해서 physical address에 접근합니다. linear address는 32bit로 이루어져 있는데 앞에 10bit를 첫번째 paging할때 쓰고 중간 10bit를 2번째 Paging할때 쓴 다음 마지막 12bit를 offset으로 하여 physical address에 접근합니다.

이러한 paging은 CR0, CR3 레지스터를 HW적으로 활용해서 구현됩니다.
<img width="758" alt="스크린샷 2021-05-20 오전 10 43 25" src="https://user-images.githubusercontent.com/61309514/118906172-360cc800-b958-11eb-86c4-06d3127b8b25.png">


CR0 레지스터의 Page Enable 비트가 있는데 이 비트가 1이 되는 순간 2단계 paging모드로 CPU가 변경됩니다.

CR3레지스터가 있는데 상위 20bit가 있다. 이 부분은 각 프로세스 이미지가 가지고 있는 PCB에 나타나 있는 Page Directory base address 비트 값을 담는 부분이다. 프로세스가 CPU에서 돌아갈 경우 이 부분에 돌아가는 프로세스의 PCB에서 가르키는 Page Directory Base 주소를 이 레지스터에 저장해 놓는다.