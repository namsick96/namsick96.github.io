---
title: "OS에서 process가 작동하는 방식"
excerpt: "OS"

categories:
    - OS
tags:
    - OS
last_modified_at: 2021-03-30T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 


# Execution of the OS 

OS도 결국에는 프로세서에서 돌아가는 하나의 프로세스이다.
이러한 OS가 돌아가는 방식을 디자인 하는 여러가지 방법이 존재한다.
## Execution within User Process

User process안에 OS code가 존재하는 경우이다.

![ch3-1](https://user-images.githubusercontent.com/61309514/112847989-931b9880-90e2-11eb-8528-083573abb128.png)

이렇게 각 Process안에 OS가 존재한다. mode switch를 할 때 마다 User process 안에 있는 os kernel을 실행한다. 즉 유저 모드,커널 모드 스위칭이 한 프로세스 안에서 수행되는 것이다. 만약 process switch를 할 떄면 이러한 os kernel을 거쳐서 실행한다. 

Linux 에서 이러한 Execution within User process 형태의 디자인 철학이 어떻게 작동하는지 확인 해 보자.

![ch3-2](https://user-images.githubusercontent.com/61309514/112848416-07563c00-90e3-11eb-924d-76a2ed0e06ad.png)

위 사진은 32bit 프로세서 UNIX운영체제의 process 그림이다. 아래 부분에는 user address space가 존재해서 user process에 관한 정보를 담고 있다. 이 공간의 길이는 3GB이다. 위에는 kernel address space가 존재한다. 1GB의 크기를 가지고 있다. 그래서 user mode일 때는 user address space에서 process가 수행되지만 kernel mode가 되면 kernel address space에서 작업을 수행한다. 또한 kernel address space에 저장된 자료들은 다른 process들과 모두 같이 공유한다. kernel 부분에 대한 정보는 process가 달라도 모두 같아야 하기 때문이다. 모든 프로세스에 대한 메타데이터를 가지고 있다. 이러한 kernel 코드는 피지컬 메모리에는 1 copy만 존재한다. 실제로 프로세스는 이러한 kernel code에 대한 포인팅을 하고 있는 것이다.

덧,kernel address space와 user address space의 배정 비율을 linux 커널 컴파일을 할 때 조정할 수 있다. 쓰임새에 맞게 조정하면 된다.

## Process-based operating system

process-based operating system은 커널 코드가 하나의 프로세스로 존재하는 것이다. 즉 mode switching이 프로세스 스위칭이 되는 것이다. 이러한 디자인 패턴은 clean하고 multi processor, muti computer환경에서 유용하지만 빈번하게 일어나는 mode swiching이 프로세스 스위칭이 되는 만큼 overhead가 많이 발생한다. 이러한 디자인 패턴이 정석은 아니다.

![ch3-4](https://user-images.githubusercontent.com/61309514/112850892-687f0f00-90e5-11eb-916f-59f9b9311603.png)


# OS에서 Process 만들기

OS입장에서 process만들려면 다음과 같은 과정을 거쳐야 한다.

우선 pid 즉 process의 id를 만들어야 한다.

process에게 memory allocation을 해준다. 배정된 메모리에 프로세스의 images(code, stack, data)가 들어가야 한다.
이러한 PCB(Process control block)을 메모리에 배정해준다. PCB에는 images, kernel context로 이루어져있다.

또한 링크를 생성해 줘서 적절한 큐에 있도록 해준다,

그리고 memory, files, accounting과 같은 다른 데이터 구조들을 만들어 준다.

그렇다면 User 입장에서 process를 만드는 것은 어떻게 될까?

process hierachy가 존재해서 parent process가 child process를 만든다. 이러한 parent-child관계의 process는 tree와 같이 뻗어나간다. 실제로 tree 데이터 구조를 채택해서 관리된다.

실제로 UNIX에서는 최 상단의 parent process로 init이라는 process가 존재한다. 이 프로세스는 boot할때 실행된다.

![IMG_28297AC9CFE7-1](https://user-images.githubusercontent.com/61309514/112854490-e0026d80-90e8-11eb-95d1-7d293a3111c4.jpeg)

위 사진을 보면 shell process를 예시로 설명을 해준다. parent process인 shell process가 cat process를 child process로 만들고 이러한 cat process가 사용자가 요청한 작업을 수행한다. 그리고 작업이 끝난 후 child process는 terminate되고 parent process인 shell process가 다시 작동한다.

추가적으로 child 와 parent process는 다음과 같은 관계가 있다.
child process는 parent process를 복사한다. 이러한 child process는 parent process와 동시에 작동 가능하고 parent는 child가 끝날 때까지 기다리는 경우도 있다. 또한 child,parent process는 서로 리소스를 공유하기도 하고 일부만 공유하기도 하고 아예 공유 안하기도 한다.


## UNIX에서 processes 만들기

### fork()
새로운 프로세스를 만드는 작업, parent process를 clone함. copy요청이 parent process로 부터 오고 child process를 만든다. 두 process는 pid를 제외한 모든 내용물이 같다. 다만 메모리 공간을 공유하지는 않는다. 단순히 dead copy임. fork를 한 후 parent process는 child process의 pid를 리턴 값으로 받는다. 만약 실패했다면 -1을 받는다. 그리고 child process의 경우 fork를 한 후 0을 리턴 값으로 받는다.

![IMG_F0F4C4066C8D-1](https://user-images.githubusercontent.com/61309514/112925455-dd396400-914c-11eb-8ac3-495a3baa494c.jpeg)

이렇게 fork가 이루어 지면 child process로의 jump가 이루어진다. 두 프로세스는 concurrent하게 진행된다.

### exec()
기존의 프로세스에서 시행되던 프로그램을 다른 프로그램으로 바꾼다. 프로세스 내에서 기존의 이미지들을 다 교체한다. 새로운 data, code,heap,stack을 가지게 한다. exec 명령어는 여러 계열이 존재한다. execve,execl,execv 등등....

그중 가장 중요한 명령어는 execve이다. exec계열중 이 명령어만 kernel에서 system을 call한다.

UNIX에서는 child process에서 새로운 프로그램을 실행하고 싶을 때 fork이후 exec명령어를 통해 child process에서 다른 프로그램을 실행한다.

### wait()
프로세스 동기화를 가능하게 한다. parent process가 child process의 결과를 기다릴 수 있게 해준다. kernel이 parent process에서 SIGHLD 시그널을 보낸다. wait 명령어를 받은 프로세스는 block을 당한다. 그리고 child status가 terminate되면 다시 시작한다.

### exit()
프로세스를 종료하게 해준다. 기존 process를 모두 멈추고 정리한다음 kernel로 돌아간다. 또한 모든 input/output stream을 멈춘다. exit을 굳이 부르지 않아도 main을 끝내면 자동으로 작동한다. child에서 exit 명령어 또는 main이 끝남을 통해서 termination이 되면 status를 parent에게 리턴한다. _exit() 명령어는 exit()과 똑같은 역할을 하지만 clean-up이 존재하지 않고 kernel로 즉시 리턴을 한다.


