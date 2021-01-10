---
title: "Zookeeper aws cluster 만들"
excerpt: "안지훈 주키퍼 포스트"

categories:
    - zookeper
tags:
    - zookeper
    - aws
    - Kafka
last_modified_at: 2021-01-10T15:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

카프카 클러스터를 구성하기 위해서 zookeeper cluster는 우선적으로 설치되어 있어야 한다.
근데 이놈의 주키퍼는 제대로 깔리지가 않는다. 구글링을 해봐도 잘 안되서 내가 어케어케 찾아서 했다.
그럼 그 삽질의 역사를 정리해서 나중에 나도 다시 삽질 안해보고자 한다.

<br>

# aws 인스턴스 만들기
<br>
kafka 용으로 인스턴스를 만들거면 3개를 만들면 된다. 클러스터라고 부를려면 최소한 3개는 있어야 한다고 구글에서 그랬다. 걍 하자.ㅣ
인스턴스 이미지는 우분투 18.04 LTS로 했다.
보안 그룹설정은 다음과 같이 해준다.
<img width="1016" alt="security_group" src="https://user-images.githubusercontent.com/61309514/104116692-4388c480-535e-11eb-9146-407408ac0de6.png">

<br>
<br>
<br>

# Java 설치 
카프카는 jdk 1.8.0 이상 버전이 필요하다.
자바를 깔자.

우분투에서 root 계정으로 전환한다.
```linux
sudo su
```
명령어 치면 됨.

그 다음 
```linux
sudo apt-get update
```

명령어를 한번 쳐준다. 그냥 자바 다운이 안되서 이거 해줘야한다.

그다음
```linux
apt-get install openjdk-8-jdk
```
쳐서 자바 다운로드 받으면됨.
<br>
<br>
# Zookeeper 설치

저장 위치는 /usr/local 에다가 저장해라
```linux
cd /usr/local
```

주키퍼 다운로드 할 링크는 다음과 같다.

https://zookeeper.apache.org/releases.html

여기로 가서 다운 받아라. 나는 LTS버전인 3.6.2 받았다.

<span style="color:red; font-size:20px">주의!!!!!!</span>

여기서 암거나 다운 받으면 안된다.

<img width="808" alt="zookeeper download" src="https://user-images.githubusercontent.com/61309514/104116894-f9a0de00-535f-11eb-8854-7a45f10209e9.png">

여기 빨간색 체크 된 곳으로 들어가서 받아야 한다. 
왜냐면 아래 Source release 링크는 bin 파일이 아니다. 그래서 나중에 주키퍼 실행 할 때 컴파일이 안되있어서 quoram머시기 클래스를 못찾는다고 에러뜬다. 무조건 위에 빨간색 친 링크로 들어가서 다운 받아라.

<br>
<img width="1058" alt="스크린샷 2021-01-10 오후 4 24 06" src="https://user-images.githubusercontent.com/61309514/104116924-50a6b300-5360-11eb-9887-6d65fb94b6d1.png">
요 링크 복사해서 

wget 명령어로 다운 받아라.

```linux
wget https://downloads.apache.org/zookeeper/zookeeper-3.6.2/apache-zookeeper-3.6.2-bin.tar.gz
```

그 다음 압축 풀어주자
```linux
tar zxf {다운 받은 파일 이}
```
<span style="color:yellow; font-size:20px">꿀팁</span>

심볼릭 링크로 주키퍼 폴더의 이름을 바꿀 수 있다.

```linux
ln -s apache-zookeeper-3.6.2-bin zookeeper
ls -la zookeeper
```
이러면 다음과 같은 출력이 나온다.
```linux
lrwxrwxrwx 1 root root {오늘 날짜} zookeeper -> apache-zookeeper-3.6.2-bin
```
이러면 cd zookeeper 하면 apache-zookeeper-3.6.2-bin 폴더로 가게 된다. 포인터 같다고 이해하면 될 듯

<br>

# data 디렉토리 설정 및 hosts 설정

주키퍼는 애플리케이션에서 별도의 데이터 디렉토리를 사용한다. 여기서 pid,myid가 저장되어 있음. 나중에 적을 zoo.cfg에서도 이 디렉토리를 참조해서 zookeeper를 돌린다. 스냅샷도 여기 폴더에 저장함.
배경은 대충 이렇다.

먼저 root 이용자의 root 디렉토리로 이동하자
```linux
cd /
```
그다음 data 디렉토리 만들어 주자
```linux
mkdir data
```

myid 설정을 해준다.
해당 인스턴스가 1번째 서버 역할을 하게 되면 1
2번째 서버 역할을 하면 2
3번째 서버 역할을 하면 3 을 해준다.

아래 예시는 1번째 서버를 기준으로 코드 작성을 했다.
2,3번째 서버이면 1 대신 2 or 3을 넣으면 된다.

```linux
echo 1 > /data/myid
```

hosts 설정은 서로 다른 인스턴스 끼리 통신을 하기 위해 설정을 해주는 것이다.
우선 /etc/hosts 파일에 들어가라. hosts 파일은 vim으로 열어주자.

<img width="718" alt="스크린샷 2021-01-10 오후 4 51 19" src="https://user-images.githubusercontent.com/61309514/104117419-7d5cc980-5364-11eb-9bbb-1ae3f76d8330.png">


이런 식으로 hosts 설정을 해준다.
여기서 나는 인스턴스 이름을 test-broker01,02,03으로 편의상 만들었다. 이 이름은 기호에 따라 바꿀 수 있다.
해당 인스턴스 본인의 주소는 0.0.0.0으로 지정하고 다른 2 인스턴스의 ip는 적어주면 된다. public ip4를 적어주면 된다.

<br>
<br>

# Zoo.cfg 설정
자 다시 zookeeper 폴더로 돌아가자
```linux
cd /usr/local/zookeeper/conf
```
여기서 vim zoo.cfg 로 zoo.cfg파일을 만든다.

```linux
tickTime=2000
dataDir=/data
clientPort=2181
initLimit=20
syncLimit=5
server.1=test-broker01:2888:3888
server.2=test-broker02:2888:3888
server.3=test-broker03:2888:3888
```

이거 복붙 하면 된다. test-broker는 인스턴스 아이디 다른걸로 했으면 그에 맞게 맞춰주면 된다.

# zookeeper 실행 
자 이제 끝났다.
zookeeper를 실행해보자.
```linux
/usr/local/zookeeper/bin/zkServer.sh start
```
(현재 디렉토리 위치에 따라 명령어가 달라질 수 있지만 bin/zkServer.sh 파일을 작동시키는 건 다 같다.)
이렇게 하면 다음 과 같은 output이 뜨면서 실행이 된다.
<img width="575" alt="스크린샷 2021-01-10 오후 5 01 47" src="https://user-images.githubusercontent.com/61309514/104117591-b2b5e700-5365-11eb-9e5f-50afdf21ad50.png">

그럼 된거다. 클러스터가 제대로 작동하고 있는지 보려면

```linux
/usr/local/zookeeper/bin/zkServer.sh status
```
입력을 하면 3개 인스턴스 중에 2개는 MODE: follower가 뜰거고
하나는 MODE:Leader 가 뜰것이다. 이러면 제대로 구성된 것이다.

<br>
<br>

# 끝
수고하셨습니다. zookeeper 설치가 다 완료되었습니다. 이제 Kafka를 깔러 가봅시다.