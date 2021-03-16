---
title: "OS design과 kernel 그리고 Virtual machine의 개념"
excerpt: "OS"

categories:
    - OS
tags:
    - OS
    - kernel
last_modified_at: 2021-03-16T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>
# OS Design 개요

OS 시스템 디자인의 목표는 2가지로 분류할 수 있다.
우선 첫번째로 User입장에서의 목표가 있다. OS는 user입장에서 쉽게 쓰고,배우고,보안성이 좋고, 믿을 수 있고, 빨라야한다. 두번째로 System입장에서의 목표가 있다. OS는 시스템 입장에서 디자인 하기 쉽고, 구현하기 쉽고,유지보수가 쉬워야하며 유연하게 적용이 가능해야하고,신뢰성,무오류 그리고 효율성이 요구된다. 이러한 두가지 목표는 OS 디자인을 할 때 고려되는 요소들이다.

OS 디자인을 할 때 mechanisms과 policies를 구별해야한다. policies는 정책으로 무엇을 할지를 결정하는 역할이지만 Mechanism은 policies에서 정의한 무엇에 대해서 어떻게 할지 정의하는 과정이다. 이 둘을 분리하는 것이 OS의 유연성을 극대화하기 위해서는 꼭 필요한 과정이다.

OS 디자인에서 나타나는 특징 중 하나는 layering approach이다. OS 시스템은 layer로 나누어져 있다. 낮은 레이어 위에 계속 레이어를 쌓는 방식으로 디자인을 한다. 이렇게 레이어를 나눌경우 좋은게 모듈화가 되어서 레이어들은 자기 보다 하위에 있는 레이어의 함수를 쓴다. 이렇게 하면 디버깅하기가 쉽고 시스템 확인이 단순해진다. 하지만 단점도 존재한다. 이렇게 층을 나눌 경우 layer 정의가 어려워지고 성능에서 손해가 존재한다.

시스템 구현도 OS 디자인의 요소중 하나이다. 전통적으로 assembly code로 만들어 졌지만 os 시스템은 현재 high-level인 C언어로 짜여 졌다. 이러면 장점이 쓰기 쉽고, 디버깅,이해하기가 빠르다. 그리고 port하기가 더 쉽다.

# Microkernel
우선 Microkernel에 대해 이해하기 전에 monolithic kernel에 대해서 이해를 할 필요가 있다.

## 1. Monolithic kernel


<img width="440" alt="스크린샷 2021-03-16 오전 11 19 24" src="https://user-images.githubusercontent.com/61309514/111246367-78104980-8649-11eb-9759-04982c8085a9.png">


monolithic kernel은 모든 os 시스템이 하나의 거대한 monolithic kernel에 구현되어 있는 상태를 말한다. 어떠한 procedure나 다른 procedure를 부를 수 있다. 이러한 구조는 커널 내에서 모듈끼리의 dependency가 강하다. 그리고 무엇보다 성능이 좋다. 하지만 모듈간의 dependecy가 강하기 때문에 관리가 어렵고 기술 스택을 변경하기가 어렵다.

## 2. Microkernel

![images](https://user-images.githubusercontent.com/61309514/111246685-13092380-864a-11eb-881a-7661b5f08f0f.jpeg)


반면 micro kernel은 core os function만 커널에 구현을 해놨다. 덜 중요한 모듈은 코어 커널에 있지 않고 코어 커널 위에서 user mode로 작동한다. 이러한 user module과 커널은 메세지 모드로 서로 소통한다. 이러한 microkernel은 커널 내에서 Layering을 최소화 한다.

이러한 microkernel의 장점은 확장성,유지보수(모듈화)가 쉽다는 것이다.

단점은 monolithic kernel에 비해서 성능이 떨어진다. 

<br>

# Virtual Machine(VM)
VM은 real platform의 제약을 없애주고 이식성, 유연성을 더해주는 프로그램이다. cross-platform compatability가능하게 해준다. x86 cpu에는 윈도우,리눅스만 돌아갈 수 있고  window위에서는 window app만 돌아가는데 vm이 있으면 이런 platform간의 차이를 없애준다.

VM의 필요성은 다은과 같다. networked computing(특히 mobile)에 있어서 지속적인 서비스를 위해서는 portability가 필요한데 vm이 이를 해결해 준다. 그리고 old binaries 인터페이스의 한계로 CPU의 발전이 방해 받는데 이걸 해결해 준다. 예전에 만들어진 x86 binaries를 Power PC cpu에서 돌리지 못하는 것과 같이 말이다. 그리고 OS가 하나만 있으면 보안상 문제가 있을 수 있고(os sandbox) OS 개발할 때 vm이 있으면 좋다.

vm solution은 vm layer를 새로 만들어 주는데 새롭게 만들어진 레이어에 있는 os를 게스트 os 원래 있던 os를 호스트 os라고 부른다.

vm에는 두종류가 있는데 process vm, system vm이 있다.

process vm은 cpu위에서 원래 호환되지 않는 application이 가상화를 통해 작동하는 것이다.

jvm이 대표적이다. vm이 어플리케이션을 에뮬레이트해서 돌아가게 해주고 이러한 process vm을 runtime이라고 부른다.


system vm은 다른 os가 완전히 가상화 되어서 게스트 os로 작동하는 것이다. (window에서 리눅스 돌아가는 꼴) vm이 모든 isa 즉 시스템, 유저레벨에서의 OS 전체를 돌아가게 해주는 것이다. 이걸 vmm,hipervisor라고 부른다.

vmm은 2가지 버전이 있는데 host os 위에서 프로세스 중 하나로 돌아가는 stanalone mode가 있다. 우리가 쓰는 virtual box가 이러한 한 종류이다.

standalone 방식은 HW위에 vm이 전체 레이어로 존재해서 vmm 위에서 다양한 os들을 호스팅한다.