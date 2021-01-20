---
title: "docker container port mapping "
excerpt: "Docker"

categories:
    - Docker
tags:
    - Docker
    - Dockerfile
    - Devops
last_modified_at: 2021-01-20T09:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# docker run 명령어 작성시 포트매핑 하는 방법과 그 이유
도커 파일을 빌드해서 이미지를 만들고 이미지를 run 해서 컨테이너까지 만들어 보자.
엥? 그런데 도커로 실행한 앱에 접속이 되지 않는다... 왜 이렇지?
PORT 설정도 제대로 했는데 되지 않는다. 왜그럴까?

<span style="color:RED; font-size:3em">그건 바로! </span>
<br>
<br>
당신이 포트 매핑을 해주지 않았기 때문이다.
도커 컨테이너 안에서 열린 포트와 로컬에서 열린 포트가 다르기 때문에 docker run을 할때 포트 매핑을 꼭 해줘야한다.
즉 로컬 네트웤과 컨테이너 네트웤을 mapping해주는 작업을 해야한다.

방법은 다음과 같다.
```linux
 docker run -p [로컬 호스트 네트워크 포트]:[컨테이너 네트워크 포트] [이미지 이름] 
```

이렇게 run 명령어를 작성해 주면 된다.

끝!

<span style="color:green; font-size:1.5em"> 추가 꿀팁 -d 옵션이란? </span>

-d 는 detach 옵션의 약어이다.

일반적인 도커 run의 경우 컨테이너를 실행 한 후 컨테이너 실행 화면을 터미널에 계속 보여준다. 하지만 -d를 넣을 경우 컨테이너 실행 화면은 백그라운드로 넘어가서 실행되고 터미널 화면은 처음 도커 run을 한 디렉토리로 돌아오게 된다.

<br>
<br>

출처: [따라하며 배우는 도커와 ci환경](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%8F%84%EC%BB%A4-ci#)