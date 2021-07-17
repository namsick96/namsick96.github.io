---
title: "Intellij에서 Gradle 버전 바꾸기"
excerpt: 

categories:
    - build tool
tags:
    - Gradle
    - Intellij
last_modified_at: 2021-07-17T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Gradle 버전 바꾸기 (@Intellij)

프로젝트의 gradle 버전을 바꾸고 싶은 경우에는 다음과 같은 과정을 거치면 바꿀 수 있습니다.

우선 프로젝트의 gradle 버전은 해당 gradle-wrapper.properties에서 확인 할 수 있습니다. 

<img width="1303" alt="스크린샷 2021-07-17 오후 11 27 33" src="https://user-images.githubusercontent.com/61309514/126040183-d4ee39f9-5ed4-4e21-ba7f-45626136b787.png">

gradle-wrapper.properties에서 distributionUrl의 값으로 존재하는 링크의 뒷부분의 숫자를 통해 이 프로젝트에 적용되는 gradle의 버전을 알 수 있습니다.

자 그럼 이 gradle 버전을 바꿔보도록 합시다.

## gradle-wrapper 다운로드를 통한 gradle 버전 change

우선 터미널을 켜봅시다. 터미널을 켠 다음 현재 프로젝트의 디렉토리에 터미널이 위치해 있는 것을 확인합니다. 그다음 다음과 같은 명령어를 입력합시다.

<img width="989" alt="스크린샷 2021-07-17 오후 10 47 10" src="https://user-images.githubusercontent.com/61309514/126040268-73b95cfa-fdb8-4ba7-9271-7be48e046dc4.png">

gradle 뒤의 숫자는 원하는 gradle 버전을 입력하면 됩니다.

gradle 버전 리스트는 [gradle홈페이지](https://gradle.org/releases/)에서 확인하실 수 있습니다. gradle홈페이지 글자를 클릭하시면 해당 링크로 바로 이동하실 수 있습니다.

이렇게 gradle wrapper를 통해 해당 프로젝트에서 사용할 gradle 버전을 gradlew를 통해 얻은 파일로 변환하면 자동으로 해당 버전의 gradle이 다운로드 됩니다. 그리고 다시 gradle-wrapper.properties 파일을 확인하면 gradle의 버전이 바뀌어 있음을 확인할 수 있습니다.

이렇게 자동으로 gradlew를 통해 gradle 버전이 바뀌려면 gradle에 대한 intellij의 환경설정도 중요합니다.

<img width="980" alt="스크린샷 2021-07-17 오후 10 47 54" src="https://user-images.githubusercontent.com/61309514/126040347-6a1198f1-0cd9-438c-8dd1-8af24384b2a2.png">

Preference-Build,Execution,Deployment-Build Tools-Gradle에서 
제일 하단의 Gradle 설정에서 Use Gradle from 설정을 'gradle-wrapper.properties' file로 해야합니다. 이는 defualt 옵션이기도 합니다. 이렇게 옵션이 적용되어야 자동으로 버전에 맞는 gradle 버전이 업데이트 됩니다.