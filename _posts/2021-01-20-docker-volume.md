---
title: "docker volume"
excerpt: "Docker"

categories:
    - Docker
tags:
    - Docker
    - Dockerfile
    - Devops
last_modified_at: 2021-01-20T10:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---
<br>

# Docker Volume

도커 볼륨은 도커 컨테이너에서 특정 파일들을 로컬 파일에 참조하도록 설정하는 방법이다.

도커 볼륨을 COPY와 비교해 보자.

<img width="536" alt="스크린샷 2021-01-20 오후 4 22 12" src="https://user-images.githubusercontent.com/61309514/105141234-31680c80-5b3c-11eb-9d74-0bf4ee55b3f0.png">


Dockerfile에 COPY로 설정을 해줄 경우 이렇게 파일들을 컨테이너에 복사해 놓는다.
이렇게 하면 문제점이 소소스코드를 변경할 때마다 변경 된 것을 빌드해줘야지 된다. 그래야 컨테이너에 적용이 된다.

이런 COPY를 대체하는게 Volume 설정이다.

<img width="534" alt="스크린샷 2021-01-20 오후 4 22 25" src="https://user-images.githubusercontent.com/61309514/105141263-3af17480-5b3c-11eb-8541-2fc725ed7615.png">


이렇게 컨테이너에 파일을 매핑을 해주면 소스코드가 변경 될 때 마다 빌드를 할 필요가 없다. 로컬 파일에 매핑이 되어있기 때문에 변경 사항이 바로바로 반영 된다.

<br>

# 도커 Volume 설정 

도커 볼륨 설정하는 법은 다음과 같다.

```linux
docker run [포트 설정] [-v [로컬 파일중 컨테이너에 매핑하지 않을 파일 ex)/usr/src/app/node_module]] [-v [컨테이너에 매핑할 파일 경로]:[컨테이너 디렉토리 경로 ]] [이미지 아이디]
```
이렇게 설정하면 된다.
즉 매핑할거면 로컬 경로:컨테이너 경로  이런 식으로 적어 주고 아니면 컨테이너 경로만 적어주고 끝내면 된다. 컨테이너 경로만 적어주면 해당 파일은 매핑을 하지 않는다.

이런 Volume 설정은 나중에 docker compose에서 docker-compose.yml에 Volume 설정을 통해서 docker compose에서도 사용할 수 있다.

<br>
<br>

출처: [따라하며 배우는 도커와 ci환경](https://www.inflearn.com/course/%EB%94%B0%EB%9D%BC%ED%95%98%EB%A9%B0-%EB%B0%B0%EC%9A%B0%EB%8A%94-%EB%8F%84%EC%BB%A4-ci#)