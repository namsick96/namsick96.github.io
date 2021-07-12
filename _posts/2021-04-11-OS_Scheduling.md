---
title: "OS에서 Scheduling 이란 무엇인가?"
excerpt: "OS"

categories:
    - OS
    - Scheduling
tags:
    - OS
last_modified_at: 2021-04-11T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Scheduling
스케쥴링이란 무엇인가? 기본적으로 스케쥴링은 Queueing model이다. 리퀘스트가 들어오면 이 리퀘스트를 큐에 저장하고 큐에서 처리할 리퀘스트를 골라서 처리하는 방식이다. 스케쥴링은 이렇게 대기하고 있는 리퀘스트, 프로세스 중에서 어떤 것을 선택해서 CPU로 넘겨 줄지에 대한 방식과 기준에 대한 개념이다.

스케쥴링의 적용 시점에 있어서 구별되는 선점형 스케쥴링과 비선점형 스케쥴링이 존재한다. 두 스케쥴링 기법의 차이점 때문에 선점형 시스템은 인터렉션이 많은 대화형 시스템에 많이 쓰이고 비선점형 스케쥴링은 계속 뒤에서 돌아가는 배치 시스템에 많이 쓰인다.

## 비선점형 스케쥴링(Nonpreemtive schelduling)
비선점형 스케쥴링은 한번 CPU 자원을 선점하면 프로세스가 끝날 때 까지 CPU자원을 내놓지 않고 독점하는 것이다. I/O를 수행하거나 자발적으로 대기 상태에 들어가지 않는 이상 자원을 반납하지 않는다. Disk나 Printer의 스케쥴링 기법이 이러한 비선점형 스케쥴링 기법을 채택한다. 이러한 비선점형 스케쥴링 기법은 FCFS,SJF 기법이 있다. 비선점형 스케쥴링은 한번 프로세스가 CPU에 들어가면 끝날 때까지 다른 프로세스들은 기다려야 하기 때문에 기다리는 프로세스가 기아 상태에 빠질 수 있다. 대신 컨텍스트 스위칭이 자주 일어나지 않기 때문에 컨텍스트 스위칭으로 인한 오버헤드는 적은 편이다. 

## 선점형 스케쥴링(Preemtive scheduling)
선점형 스케쥴링은 어떤 프로세스가 CPU를 할당 받아 실행 중에 있어도 다른 프로세스가 실행중인 프로세스를 강제로 점유할 수 있다.
선점형 스케쥴링은 프로세스에게 제한된 시간을 한번 실행할 때마다 할당한다. 그래서 프로세스가 작업을 수행하다가 중간에 time이 다 될 경우 대기 상태로 돌아 갈 수 있다. 즉 자원을 반납하는 상황이 발생한다 그렇기 때문에 선점형 스케쥴링은 컨텍스트 스위칭이 자주 발생한다. 이러한 이유 때문에 컨텍스트 스위칭으로 인한 오버헤드가 많이 나타날 수 있다. 또한 선점형 스케쥴링은 우선순위에 따라 스케쥴링을 하기 때문에 Starvation의 문제가 일어 날 수 있다. 이러한 문제는 대기 시간이 오래 될 수록 우선순위를 높이는 방식으로 해결 할 수 있다.

# 장기 스케쥴링, 중기 스케쥴링, 단기 스케쥴링
스케쥴링에는 3가지의 다른 스케쥴링 부분이 있다. 장기 스케쥴링, 중기 스케쥴링, 단기 스케쥴링이다.
## 장기 스케쥴링
작업 스케쥴링이라고도 하며 어떤 프로세스를 준비 큐(Ready queue)에 넣을지 결정하는 역할을 합니다. 장기 스케쥴러는 수십초, 수분 단위로 작동 되며 속도가 느려도 괜찮습니다. job scheduler라고 불립니다. 디스크 내의 작업을 어떤 순서로 가져올지 결정합니다.

## 중기 스케쥴링
장기와 단기 사이의 완충 단계이다. 너무 많은 프로세스에게 메모리를 할당해 시스템의 성능이 저하되는 경우(trashing)를 막기 위해 추가된 스케쥴링 단계이다. 스와핑이라는 작업을 수행하는데 일부 프로세스를 메모리에서 디스크로 보내고(swap out) 시간이 흘러 메모리에 공간이 생기면 다시 swap in한다.

## 단기 스케쥴링
CPU 스케쥴링을 하는 부분입니다. 준비 상태의 프로세스 중 어떤 것을 CPU에 넣어 실행 상태로 만들 것인지 결정합니다. time interrupt가 발생하면 보통 단기 스케쥴러가 호출 됩니다. 일반적인 스케쥴링을 의미하는게 단기 스케쥴링입니다. 단기 스케쥴링은 미리 정리한 스케쥴링 알고리즘에 따라 CPU에 집어 넣을 프로세스를 할당합니다. 또한 단기 스케쥴러는 매우 빈번하게 호출 되기 때문에 수행 속도가 빨라야 합니다. 또한 단기 스케쥴러의 속도, latency는 real-time os에서 굉장히 중요합니다. 단기 스케쥴러는 dispatcher라고 역사적으로 부릅니다. 하지만 리눅스에는 dispatcher가 없습니다. 커널 코드에 스케쥴러라는 이름으로 구현되어 있습니다. 이러한 short term scheduler는 다른 프로세스를 실행하기 위해서 불러집니다. interrupt, traps, system calls가 왔을 때 불립니다.

# 스케쥴링의 기준 (CPU scheduling criteria)
스케쥴링 할 때 스케쥴링 정책은 CPU입장(시스템)과 Process의 입장(유저) 둘 다를 고려해야합니다. 

CPU입장에서는 CPU를 계속 바쁘게, 시간당 process 완료 개수를 최대한으로 하는 것을 우선으로 합니다.

Process입장에서는 특정 프로세가 실행되는데 걸리는 시간을 최소화하고, 프로세스가 waiting queue에 있는 시간을 최소화해야하고, 요청이 입력 되었을 때 반응하는데까지 걸리는 시간을 최소화 하는 것을 목표로 합니다.

이 두 입장의 목적은 서로 충돌하기 때문에 두 입장 사이의 균형, 최적화를 고려해야합니다.

# Process의 형태: CPU-bound, I/O bound
프로세스는 보통 CPU와 I/O를 번갈아 가면서 사용합니다. 이것을 CPU-IO cycle이라고 합니다. 매 사이클은 cpu가 사용되는 cpu burst와 io가 사용되는 io burst가 존재합니다. 

<img width="599" alt="스크린샷 2021-04-11 오전 12 48 22" src="https://user-images.githubusercontent.com/61309514/114275986-a7a34d80-9a5f-11eb-9031-4f330e9d6c6d.png">


보통 io burst가 더 긴 시간을 차지합니다. CPU-bound processes는 보통 CPU burst가 IO-bound processes 보다 더 깁니다. 대화형으로 작동하는 프로세스는 거의다 io/bound process입니다.

# 우선순위에 따른 프로세스 스케쥴링
스케쥴링에서 프로세스는 우선순위에 의해서 결정됩니다. 스케쥴러는 항상 우선순위가 높은 프로세스를 먼저 서택합니다. 현실에서는 우선순위 스케쥴링은 우선순위를 나타내는 여러 큐들이 병렬적으로 존재하는 방식으로 구현되어있습니다.

이러한 우선순위에 의한 스케쥴링 기법은 비선점형, 선점형 둘다에서 가능합니다.

하지만 우선순위에 의한 스케쥴링 기법은 Starvation problem을 야기할 수 있습니다.
낮은 우선순위를 가지는 프로세스는 끊임없이 기다리는 문제가 이를 지칭합니다. 이를 해결하기 위해서 aging이라는 방식을 동원합니다. 시간이 증가할 수록 우선순위를 더 높게 쳐주는 방식입니다.


# 스케쥴링 정책의 특성
스케쥴링 정책은 어떤 프로세스를 고를 것인지 (which), 언제 프로세스를 고를 것인지 (when)를 정의한 것입니다.
## selection function
selection function은 어떤 프로세스를 고를지 결정합니다. 레디 큐에 있는 프로세스 중 어떤 프로세스를 다음에 실행 할지 결정합니다. 
## decision mode
decisionm mode는 언제 프로세스를 교체할지 결정합니다. 즉 언제 selection function이 작동할지 결정하는 것입니다.

<img width="907" alt="스크린샷 2021-04-11 오전 1 14 09" src="https://user-images.githubusercontent.com/61309514/114276804-441b1f00-9a63-11eb-8323-3f14edc24e23.png">


이러한 스케쥴링 decision는 프로세스가

1. running 에서 waiting(block)로 변환될때
2. running에서 ready 상태로 변환될 때
3. waiting(block)에서 ready로 변환될때
4. exits(terminate)될 때
수행된다.

위 경우에서 preemtive 스케쥴링에서는 1,2,3,4의 경우가 모두 실현되지만 nonpreemtive 스케쥴링에서는 1과 4의 경우만 실현된다.

nonpreemtive(비선점형 스케쥴링)에서는 I/O를 받기 위해 프로세스가 스스로 block을 하거나 프로세스가 종료되기 전까지는 계속 프로세스가 돌아가도록 놔둡니다. 즉 프로세스가 running state인 도중에는 프로세스를 교체하지 않습니다.

preemptive(선점형 스케쥴링)에서는 time quantum을 부여해서 만약 부여된 시간이 다 끝나면 ready state로 프로세스를 돌립니다. 즉 배정된 시간이 다 끝나면 프로세스를 교체합니다. 어느 한 프로세스가 프로세서를 오랜 시간동안 독점하지 못하게 하는 방식입니다. 

# 대표적인 스케쥴링 정책들
스케쥴링 정책은 상당히 많다 그 중 대표적인 것은 FCFS, Round Robin, SJF 스케쥴링 정책이 있다.

## FCFS 스케쥴링
FCFS는 First Come First Served의 약자이다. 다른 말로 FIFO 방식을 채택하는 스케쥴링이다. 가장 원시적인 스케쥴링 방법이다. 어떤 프로세스를 선택할지 결정하는 Selection function은 FCFS 스케쥴링에서 가장 오랫동안 ready queue에 있었던 프로세스를 선택한다. 또한 decision mode에서는 nonpreemptive 방식을 채택하여서 프로세스가 스스로 끝내거나 block상태로 가기 전에는 멈추지 않는다. 또한 time quantum도 존재하지 않는다. 하지만 이러한 FCFS의 문제점은 한 프로세스가 CPU자원을 독점할 수 있다는 점이다. I/O 처리가 없는 프로세스의 경우 프로세서를 계속 해서 점유하는 문제가 발생한다. 그렇기 때문에 이 방식은 CPU-bound 프로세스에게 좋은 방식이다. 또한 FCFS는 프로세스 순서에 따라 대기 시간이 달라진 다는 단점이 있다. burst time이 긴 프로세스가 먼저 처리 될 경우 brust time이 작은 프로세스가 먼저 처리되는 경우보다 더 오랫동안 프로세스들이 waiting을 하게 되는 단점이 있다.
## Round Robin 스케쥴링
Round Robin의 selection function은 FCFS와 같다. Decision mode에서는 time quantum을 사용해서 preemptive 방식을 사용한다. 이러한 round robin방식은 process가 주어진 시간 만큼 동안 run할수 있게 하고 시간이 만료될 경우 run을 멈추게 합니다. timer interrupt가 발생하여 프로세스에게 주어진 시간이 만료되었음을 파악하면 해당 프로세스를 다시 ready queue로 배정한다. 이러한 round robin 방식은 time sharing 환경에서 적절한 방식이다. 하지만 이러한 process switching 때문에 overhead가 많이 발생한다.

Round robin 스케쥴링은 process에게 배정하는 time quantum의 길이에 따라 그 성능이 달라진다. quantum이 길면 FCFS와 같은 결과를 보여주고 quantum이 짧으면 context switching이 너무 자주 발생해 오버헤드가 너무 많아진다. 따라서 이러한 context switching, timer interrupt, dispatching 을 고려해서 time quantum을 배정해야한다. 일반적인 interaction보다는 더 긴 time quantum을 배정해야한다. CPU-bound interaction의 길이보다 살짝 더 길게 time quantum을 잡는게 일반적이다.

이러한 Round robin 방식은 CPU-bound process에게 유리한 방식이다. 

## SJF 스케쥴링
SJF 스케쥴링은 이론적인 스케쥴링 기법이다. 실제로 이러한 스케쥴링 기법을 이론적인 최적 수준까지 구현하는 것은 불가능하다. waiting time이 최적화 된 것이 SJF 스케쥴링이다. CPU time을 예측해서 최적화 하는 것이기 때문인데 예측은 언제나 틀리기 마련이다.

selection function은 가장 짧은 CPU burst time을 기준으로 선택한다. 프로세스의 cpu burst time이 가장 작을 것을 우선으로 프로세서에 집어 넣는다. decision mode는 preemtive하기도 할 수 있고 nonpreemptive 할 수도 있다. Nonpreemptive할 경우에는 cpu burst가 끝나기 전까지 프로세스를 교체하지 않는다. 반면 preemptive할땐 ready queue에 있는 프로세스의 cpu burst가 지금 돌아가고 있는 프로세스의 남은 cpu burst보다 작다면 process switching을 진행한다. 이러한 방법을 Short Remaining Time First라고 하며 SRTF라고 한다.

이러한 SJF 스케쥴링 기법은 I/O process를 우선적으로 선택하게 되며 starvation 문제가 발생할 수 있다.(CPU burst길이가 너무 길 경우!) 또한 CPU burst time 예측이 힘들다는 한계가 존재한다.

