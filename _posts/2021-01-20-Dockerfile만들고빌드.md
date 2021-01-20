---
title: "Dockerfile만들고빌드하기"
excerpt: "Docker"

categories:
    - Docker
tags:
    - Docker
    - Dockerfile
    - Devops
last_modified_at: 2021-01-20T08:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# Dockerfile 만들기

docker는 이미지가 있어야지 컨테이너를 생성할 수 있습니다. Dockerfile은 이런 이미지를 만들 수 있게 해주는 일종의 명세서입니다.
Dockerfile을 빌드해주면 도커 이미지가 나옵니다. 

<img width="577" alt="스크린샷 2021-01-19 오후 11 52 15" src="https://user-images.githubusercontent.com/61309514/105135025-a46c8580-5b32-11eb-8c3a-c6db1ca182cb.png">
<br>
<br>
자 그럼 Dockerfile이 어떻게 생겼는지 직접 확인하면서 체크해 봅시다.

<img width="337" alt="스크린샷 2021-01-19 오후 11 55 28" src="https://user-images.githubusercontent.com/61309514/105135063-bcdca000-5b32-11eb-8742-78e1efce2bc6.png">

<span style="color:purple; font-size:2em">FROM</span>부분은 베이스 이미지를 명시해 줍니다.
베이스 이미지는 도커 이미지에 기본적으로 깔리는 인스톨로 도커 엔진 바로 위 레이어에 존재하는 이미지입니다. 보통 리눅스 운영체제, 언어 패키지가 주를 이룹니다.

<span style="color:purple; font-size:2em">WORKDIR</span>는 이미지 안에서 어플리케이션 소스코드를 가지고 있을 디렉토리를 생성하는 것입니다. 이 설정은 안해주면 도커 이미지를 이용해 만든 컨테이너의 root directory에 로컬 파일에서 복사한 파일들이 널부러져 있습니다. 만약 이름이 같을 경우 덮어씌여지기 때문에 안전하고, 관리하기 쉽게 WORKDIR설정을 해주도록 합시다.
여기서는 /usr/src/app 폴더에 소스코드들을 넣어놓도록 설정해 놓았습니다.
그리고 도커 컨테이너를 실행할 때 WORKDIR에서 지정한 디렉토리에서 부터 시작하게 됩니다.

<img width="518" alt="스크린샷 2021-01-20 오후 3 30 22" src="https://user-images.githubusercontent.com/61309514/105136147-8bfd6a80-5b34-11eb-8c33-ad639e9f68a9.png">

이렇게 shell로 들어가서 pwd 하면 WORKDIR에서 시작합니다.


<span style="color:purple; font-size:2em">COPY</span>는 도커 이미지를 만드려고 하는 기존 로컬 폴더의 파일을 이미지에 복사하는 것입니다.
COPY [로컬파일 이름 ] [저장될 디렉토리] 로 구성되어 있고 공백 맞춰서 적으셔야합니다. 위 사진의 경우 ./ 로 설정했을 경우 WORKDIR로 저장됩니다. 여기서 COPY가 RUN의 npm install보다 앞에 나온 이유는 npm install이 시행되기 위해서는 package.json 파일이 필요하기 때문입니다.
여기서 COPY 명세가 두번 나온 이유는 Docker cache를 이용하고자해서 입니다.
+ 토막 상식! 도커는 이미지 생성 시간을 줄이기 위해서 Dockerfile의 각 과정을 캐시합니다.

만약 package.json에 선언되어 있는 dependencies는 바뀌지 않고 소스코드만 변경 되었을 경우에 이러한 방법은 이미지 빌드를 빠르게 도와줍니다.

사진 위쪽에 있는 COPY package.json ./ 를 먼저 카피하는 절차가 docker cache에 저장되어 있기 때문에 dependencies의 변화가 없을 경우 이러한 캐시를 사용해서 빌드 속도를 빠르게 할 수 있습니다.


<span style="color:purple; font-size:2em">CMD</span>는 '[]' 기호 안에 컨테이너 시작시 실행할 명령어를 명시해 줍니다. docker run으로 컨테이너가 실행 되었을 때 대괄호 안에 있는 명령어가 시행됩니다. 도커 엔진이 CMD안에 인자들을 json 형식으로 인식하기 때문에 큰따옴표를 이용하고 공백이 있을 경우에 콤마를 넣어서 구별해 줍니다. RUN과의 가장 큰 차이점은 RUN은 이미지를 만들때 사용되는 반면 CMD는 이미지에 저장되지 않고 이미지가 컨테이너로 실행될때 실행되는 명령어입니다.
CMD 명령어는 도커파일 전체에 단 하나만 존재 가능하다.
위 방식은 CMD["executable","param1"] 방식으로 만들어 졌으며 "executable"에는 "node" "param1"에는 "server.js"가 들어가 있다. 파라미터는 더 많이 추가가 가능하다. 하지만 "executable" 명령어는 하나만 가능하다.

<br>
<br>

출처: [따라하며 배우는 도커와 ci환경](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%8F%84%EC%BB%A4-ci#)