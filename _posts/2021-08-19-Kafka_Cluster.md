---
title: "Kafka Cluster 구축하는 법"
excerpt: 

categories:
    - Kafka
tags:
    - Kafk법
last_modified_at: 2021-08-19T11:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# 카프카 클러스터 구성
카프카 클러스터 구성 방법을 알아보도록 하겠습니다

카프카 클러스터는 인스턴스 3개에 각각 주키퍼 노드 1개, 카프카 브로커 1개가 들어있고 총 3개 노드를 가지고 있는 주키퍼 앙상블과 3개의 브로커로 이루어진 카프카 클러스터를 만들고자 합니다.

또한 카프카 클러스터는 동일한 VPC-Subnet 환경에 존재합니다.  클러스터 구조도는 다음과 같습니다.

<img width="787" alt="스크린샷 2021-08-20 오후 1 40 42" src="https://user-images.githubusercontent.com/61309514/130204930-e1d8b456-ee07-4c52-9dca-8d645e93f13f.png">


물론 모든 인스턴스를 인터넷 게이트웨이에 연결해 

AWS 환경에서 모든 과정이 진행되었습니다.

사용된 기술 스택은 다음과 같습니다. 
- Zookeeper 
- Kafka 
- Java



# 1. AWS에서 인스턴스 설정

<img width="720" alt="그림1" src="https://user-images.githubusercontent.com/61309514/130176888-1cdbb30b-ea95-411f-b7b2-0d7810213dea.png">

Amazon Linux 2 AMI를 AMI로 선택합니다. 

인스턴스 유형은 t2.micro로 설정하고 스토리지는 디폴트 값이던 8GB에서 10GB로 늘려줍니다.

<img width="720" alt="인스턴스개수" src="https://user-images.githubusercontent.com/61309514/130177106-6adc3501-2e89-47c5-81f8-28aa617e775a.png">
인스턴스 개수는 3개로 하여 동일한 인스턴스를 3개 만들어 줍니다.

<img width="600" alt="인바운드" src="https://user-images.githubusercontent.com/61309514/130177269-dfa70ff4-8451-4492-bfc8-9dc32c77d16b.png">

보안 그룹을 설정할 때 다음과 같이 설정해 줍니다.
2888,3888,2181,9092 포트를 열어 줍니다. 이는 주키퍼, 카프카 노드 끼리 통신을 하는 포트입니다.


이제 설정이 완료되었음으로 인스턴스에 접속해서 java, zookeeper, kafka를 설치하고 클러스터를 구성해 보도록 하겠습니다.

# 2. Java 설치
인스턴스 접속 후 홈 디렉토리에 위치해 있는 상황에서 다음과 같은 명령어를 순서대로 입력합니다


```
sudo yum update
sudo yum install java-11-amazon-corretto-headless
java --version
```
이렇게 입력했을 경우 터미널에 openjdk 11.0.12 라는 표시가 맨 첫줄에 출력되면 자바가 제대로 설치된 상황입니다.

서버 3대 모두 위 명령어를 입력하고 출력이 제대로 나오는지 확인해 주면 됩니다.

# 3. Zookeeper 설치 및 구동
주키퍼도 다음 명령어를 순서대로 입력하면 됩니다.
```
cd ~
wget https://mirror.navercorp.com/apache/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz
tar xvf apache-zookeeper-3.6.3-bin.tar.gz
ln -s apache-zookeeper-3.6.3-bin zookeeper
```
그 다음 터미널에 ls 명령어를 치면 다음과 같이 화면이 나옵니다
<img width="452" alt="주키퍼깔고나서" src="https://user-images.githubusercontent.com/61309514/130248526-e27f8d66-008a-4121-b3b1-4be5ab7a0756.png">

서버 3대 모두 위 명령어를 입력하고 출력이 사진과 같이 제대로 나오는지 확인해 주면 됩니다.

이제 주키퍼 설치는 완료가 되었고 설정을 해보도록 합시다.

```
cd ~
mkdir -p ./data
```
위 명령어를 입력해 줍니다.

그 다음 서버 별로 다른데 첫번째 서버의 경우

```
echo 1 > ./data/myid
```

두번째 서버의 경우

```
echo 2 > ./data/myid
```
세번째 서버의 경우

```
echo 3 > ./data/myid
```
이렇게 입력해 줍니다. ./data/myid 에 서버의 번호를 넣는 작업입니다.

myid 번호를 넣는 작업이 끝났으면 이제는 zoo.cfg 파일을 수정해 보겠습니다.

다음 명령어를 입력해 주세요 서버 3대 모두 동일하게 입력해주시면 됩니다.

```
cd ~
cd zookeeper/conf
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```

이러면 zoo.cfg 파일이 열립니다. 그 다음 i 키를 눌러 입력 모드로 변경한 다음 zoo.cfg파일을 변경해 줍니다.
우선 기존 설정을 바꿀 곳 1군데 새로 추가할 곳 3군데입니다.

dataDir 부분의 기존 설정을 바꿔줍니다.

dataDir=/home/ec2-user/data

로 변경해 줍니다.

그 다음 clientPort 설정 아래 들여쓰기를 통해

server.1=1번서버_private_ip:2888:3888

server.2=2번서버_private_ip:2888:3888

server.3=3번서버_private_ip:2888:3888

를 입력해 줍니다. 만약 현재 접속해 있는 서버가 1번 서버라면 1번서버_privat_ip에 실제 private_ip대신 0.0.0.0을 넣어주면 됩니다. 2,3번 서버에서도 마찬가지입니다.

1번 서버의 예시 사진은 다음과 같습니다.
<img width="713" alt="스크린샷 2021-08-20 오후 9 10 22" src="https://user-images.githubusercontent.com/61309514/130249763-08656903-5993-4c3a-93d7-ce7054a59bf9.png">

2번 서버의 예시 사진은 다음과 같습니다.
<img width="668" alt="스크린샷 2021-08-20 오후 8 55 46" src="https://user-images.githubusercontent.com/61309514/130249841-6017f50d-4451-43b9-8bc4-13303749cb7d.png">


3번 서버의 예시 사진은 다음과 같습니다.
<img width="698" alt="스크린샷 2021-08-20 오후 8 55 55" src="https://user-images.githubusercontent.com/61309514/130249875-a5433895-9814-48e2-8cca-987f00b59eb7.png">

private ip는 사진과 다를겁니다. 본인의 인스턴스에 배정된 private ip를 참고해서 적어주세요.

변경이 완료되었으면 :wq를 입력해 변경사항을 저장하고 닫아줍니다.

이제 zookeeper를 실행해 보도록 하겠습니다.

서버 3대 모두의 터미널에서 다음 명령어를 한줄 한줄 따라써주세요

```
cd ~
cd zookeeper/bin
./zkServer.sh start
```
이러면 주키퍼가 실행이 됩니다.

주키퍼가 잘 실행되고 있는지 확인하려면

```
cd ~
cd zookeeper/bin
./zkServer.sh status
```
를 한줄한줄 입력하시면 됩니다.

잘 진행될 경우에 사진은 다음과 같습니다.

<img width="554" alt="스크린샷 2021-08-20 오후 9 02 58" src="https://user-images.githubusercontent.com/61309514/130250526-5a94bbd3-848a-4b1d-b6f1-bdfe88adcac9.png">

위 사진은 서버가 leader인 경우입니다.(나머지 2대에 대한)

<img width="565" alt="스크린샷 2021-08-20 오후 9 03 04" src="https://user-images.githubusercontent.com/61309514/130250566-3acbbc31-e6fc-45d7-8cff-de8b2c279962.png">

위 사진은 서버가 follower인 경우입니다.

leader -follower는 주키퍼 노드 끼리 알아서 선출된것이니 크게 신경 안쓰셔도 됩니다.



에러가 난 경우는 다음과 같습니다.

<img width="539" alt="스크린샷 2021-08-20 오후 9 03 41" src="https://user-images.githubusercontent.com/61309514/130250709-ce5faf7e-aa4e-4df9-a8fb-9f6b0de03003.png">


이렇게 에러가 난 경우에는 큰 확률로 zoo.cfg를 잘못 작성해서입니다. 한글자도 틀리면 안되니 다시 확인해주세요.
아니면 zookeeper 디렉토리의 log 디렉토리로 간 다음 로그를 확인해 보아도 좋습니다.
그 다음에는 주키퍼를 멈췄다가 다시 시작해 보세요.

주키퍼를 멈추는 명령어는 다음과 같습니다.

```
cd ~
cd zookeeper/bin
./zkServer.sh stop
```

자 이제 주키퍼 설치와 작동이 완료되었습니다. 이제 카프카를 설치해보겠습니다.

# 4. Kafka 설치 및 구동

카프카 설치를 해보도록하겠습니다. 서버 3대 모두에서 아래 명령어를 한줄한줄 입력하면 됩니다.
```
cd ~
wget https://mirror.navercorp.com/apache/kafka/2.6.2/kafka_2.12-2.6.2.tgz
tar xvf kafka_2.12-2.6.2.tgz
ln -s kafka_2.12-2.6.2 kafka
```

위 명령어를 다 친 후 ls 명령어를 입력하면 터미널에 다음과 같은 화면이 나옵니다.

<img width="452" alt="kafka설치후" src="https://user-images.githubusercontent.com/61309514/130253475-cb7b18dc-43cb-49f6-bb97-d229865e667b.png">


올바르게 나왔는지 확인하고 이대로 안나왔다면 저장된 kafka_2.12-2.6.2.tgz를 rm명령어로 지우고 다시 실행합니다.

카프카 실행하기 전에 브로커의 힙 메모리를 설정해주고자 합니다.

현재 t2.micro 인스턴스는 메모리 크기가 1GB입니다.
주키퍼의 메모리 사용량이 512MB 이므로 유휴 공간이 512MB밖에 없습니다. 따라서 환경변수로 카프카 브로커의 힙 메모리를 기존의 1GB에서 400MB으로 줄여주고자 합니다.

세 서버 모두에서 다음 명령어를 입력해줍니다.
```
cd ~
vim ~/.bashrc
```

이렇게 하면 .bashrc 파일을 열 수 있습니다.

그 다음 .bashrc 파일의 마지막 줄 아래 추가적으로 다음과 같은 텍스트 한줄을 더해줍니다.
```
export KAFKA_HEAP_OPTS="-Xmx400m -Xms400m"
```

한줄 추가하고 나면 다음과 같이 화면에 나와야 합니다.

<img width="452" alt="그림10" src="https://user-images.githubusercontent.com/61309514/130253806-aa4cf857-f14e-4e22-990c-e0080b8a075b.png">

그 다음

:wq

를 입력해 변경 사항을 저장한 후 .bashrc를 나옵니다.

해당 명령어를 다시 입력해 변경된 .bashrc를 적용해 줍니다.
```
source ~/.bashrc
```

잘 적용 되었는지 확인하기 위해서 다음 명령어를 입력해 보도록 합시다.

```
echo $KAFKA_HEAP_OPTS
```

출력으로 

-Xmx400m -Xms400m

이 나오면 제대로 설정이 완료 된 것입니다.

이제 카프카 클러스터 설정을 해보도록하겠습니다. 클러스터 설정은 server.properties를 통해 할 수 있습니다.

모든 서버에 다음 명령어를 한줄 한줄 입력하도록 합시다.

```
cd ~
cd kafka
cd config
vim server.properties
```

여기까지 3개의 서버에서 모두 동일하게 진행해 줍니다.

그 다음 server.properties의 경우 서버 별로 달라지는게 존재합니다. 현재 server.properties 파일이 열려있는 상태입니다. 해당 파일에서 i를 눌러 입력 모드로 바꾼 후에 broker.id 부분의 설정 값을 변경합니다.

첫번째 서버의 경우 broker.id=1

두번째 서버의 경우 broker.id=2

세번째 서버의 경우 broker.id=3

으로 설정해 줍니다.

또한 #advertised.listeners 를 주석 해제하고 your.host.name 부분을 서버의 private ip로 변경해 줍니다. private ip는 aws의 인스턴스 세부 정보에서 확인할 수 있습니다.

다음 사진은 첫번째 서버의 경우의 예시입니다.

<img width="452" alt="그림11" src="https://user-images.githubusercontent.com/61309514/130254455-698b1b85-64e8-44d5-a411-77e76d68ddd3.png">

broker.id를 1로 설정하였습니다.
advertised.listeners의 기존 설정에서 your.host.name을 해당 서버의 private ip 값으로 바꿔줍니다.
private ip는 aws의 인스턴스 세부 정보에서 확인할 수 있습니다.

그 다음으로

```
log.dirs=/tmp/kafka-logs
```

로 되어있는 기존 설정을 

```
log.dirs=/home/ec2-user/kafka/kafka-logs
```

로 바꿔줍니다.

마지막으로
```
zookeeper.connect=localhost:2181
```

값을 실제 주키퍼 서버값으로 바꿔줍니다.
여기서는 3대의 서버에 각각 주키퍼 서버가 존재합니다.
따라서 3개의 서버들(카프카 브로커가 설치되어있는 서버 포함)의 ip주소와 2181 포트를 같이 써주면 됩니다.

3개의 클러스터인 만큼

```
서버1ip:2181,서버2ip:2181,서버3ip:2181
```

로 씁니다. 연결을 콤마로 해줍니다.

그 다음 클러스터들을 다 쓴다음 맨 뒤에

/test-kafka

를 추가해 줍니다. 이렇게 해주는 이유는 znode의 디렉토리를 루트노드가 아닌 하위 디렉토리로 설정해 주는 작업으로 하나의 주키퍼에서 여러 클러스터를 구동할수 있게하기 위함입니다. 이름은 굳이 test-kafka일 필요는 없습니다.

따라서 결과적으로

```
zookeeper.connect=서버1ip:2181,서버2ip:2181,서버3ip:2181/test-kafka
```

형식으로 텍스트를 입력해줍니다. 만약 서버들이 동일한 vpc에 있는 경우 private ip를 설정해주면 됩니다. 그게 아니라면 퍼블릭 ip주소를 넣어줘야합니다. 지금 우리의 경우는 동일한 vpc에서 작업하는 상황을 가정하고 있으니 private ip를 넣어주면 됩니다.

다음은 예시케이스입니다.

<img width="452" alt="그림31" src="https://user-images.githubusercontent.com/61309514/130255102-73434402-aa2f-491a-a26b-54d5a2c11d70.png">

ip주소는 본인의 경우에 맞게 서버의 private ip주소를 입력해야합니다.


이제 설정이 완료되었습니다.

:wq를 통해 변경사항을 저장해 주고 server.properties 파일에서 빠져나오면 됩니다.


이렇게 서버 3대가 모두 설정이 되었다면
주키퍼-카프카 순서대로 실행을 해주면 됩니다.
주키퍼 시행 방법은 위에서 말했듯이 zookeeper/bin 디렉토리에 있는 .zkServer.sh 파일을 활용하면 됩니다.

카프카의 실행 방법도 비슷합니다. 세 서버 모두 아래 명령어를 입력해줍니다.
```
cd ~
cd kafka
./bin/kafka-server-start.sh -daemon ./config/server.properties
```

위 명령어를 입력 해주면 됩니다. 이렇게 되면 kafka가 백그라운드에서 실행이 됩니다 (-daemon옵션으로 인해 백그라운드에서 실행이 됩니다.)

멈추고 싶을 경우 다음 명령어를 입력합니다. 역시 3개의 서버 모두에서 카프카 브로커를 종료해야지 클러스터 자체가 종료됩니다. 왜냐하면 카프카 클러스터 자체가 1개만이라도 브로커가 남아있으면 서비스가 지속될 수 있기 때문이비다.

```
cd ~
cd kafka
./bin/kafka-server-stop.sh
```

이런 순으로 명령어를 입력하면 카프카가 종료 됩니다.

카프카가 제대로 실행 되고 있는지는 logs 디렉토리에서 가장 최근의 로그를 확인해 알 수 있습니다. server.log 파일을 확인해서 에러 코드가 로그에 떴는지 확인하는 방법이 있습니다.

만약 에러가 떴다면 server.properties를 다시 한번 확인해 보세요.

또다른 확인 방법으로 9092 포트가 제대로 작동하고 있는지 보면 됩니다.
```
netstat -ntlp | grep 9092
```

명령어를 쳤을 경우 LISTEN이 출력되면 카프카 클러스터가 작동하고 있는 것 입니다.


<img width="723" alt="스크린샷 2021-08-21 오전 12 34 52" src="https://user-images.githubusercontent.com/61309514/130258202-99b1f4d4-cf9c-4616-b291-0e85ba8eb869.png">


만약 카프카, 주키퍼를 종료하고자 하면

카프카 - 주키퍼 순서대로 종료하면 됩니다. 종료 방식은 위에 상술하였듯이 bin 폴더에서 stop에 해당하는 .sh파일을 stop옵션과 함께 실행하면 됩니다.


# 카프카 클러스터 클라이언트 서버 구축

카프카 클러스터에 대한 클라이언트 서버를 구축하는 방법에 대해서 설명하겠습니다.

사실 굳이 클라이언트 서버가 아닌 사용자 PC에서 카프카 클러스터중 하나의 브로커에 접속해서 명령을 내려도 됩니다. 하지만 클라이언트 서버를 구축하면 브로커에 부담을 덜 주고 보안상 우수합니다.

보안상 얻는 이점은 카프카 클러스터를 외부 인터넷 망에서 곧바로 접속하는것을 막을 수 있다는 점입니다. 클러스터가 존재하는 서브넷과 클러스터의 클라이언트 서버가 동일한 VPC에 존재하지만 서로 다른 서브넷에 존재하게 하고 라우터로 하여금 클러스터와 인터넷 게이트웨이와의 연결을 삭제해서 클러스터 클라이언트 서버로만 클러스터에 접속할 수 있게 하는 것 입니다. 이럴 경우 클러스터로의 집적적인 침입을 막을 수 있어 보안상 우수합니다. 


카프카 클러스터 클라이언트 서버를 구축하는 방법은 주키퍼를 설치하지 않습니다. 오직 카프카만 설치합니다.
앞서 위에 상술했던 카프카 설치 방식을 따라 인스턴스에 그대로 설치하면 됩니다. 다만 카프카를 설치하기 전에 java설치를 완료해야합니다. 또한 카프카 클러스터를 만드는 server.properties 부분 이전까지만 위에 상술한대로 따라 설치하면됩니다. 왜냐하면 카프카 클라이언트 서버는 클러스터를 구성하는 것이 아니기 때문입니다.

그 다음 카프카에서 제공하는 클라이언트 명령어를 사용해서 클러스터를 제어하면 됩니다.