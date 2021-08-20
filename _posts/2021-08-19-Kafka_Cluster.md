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


# 3. Zookeeper 설정


# 4. Kafka 설정


# 5. Zookeeper & Kafka 동작