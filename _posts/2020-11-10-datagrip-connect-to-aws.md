---
title: "aws RDS SSH로 datagrip에 연결시키기"
excerpt: 

categories:
    - DB
tags:
    - SSH
    - Postgresql
last_modified_at: 2020-10-23T08:06:00-05:00
sitemap :
  changefreq : daily
  priority : 1.0
---

AWS RDS Datagrip에 연결하기

# 1. AWS RDS 인스턴스 네트워크 스펙입니다.
- 보안그룹에서는 모든 ip에 대해서 열어 놓았습니다.
<img width="888" alt="RDS 스펙" src="https://user-images.githubusercontent.com/61309514/98681146-b3ea8800-23a5-11eb-81b9-578fdf93ee64.png">
민감한거는 다 가려놓음.

# 2. Datagrip을 켜줍니다.
- Datagrip은 학생계정으로 다운받으셔서 하시면 됩니다.

# 3. Datagrip에서 데이터 소스를 들어가보도록 합시다.

<img width="1439" alt="데이터소스 들어가기" src="https://user-images.githubusercontent.com/61309514/98680793-3a529a00-23a5-11eb-8b16-1eeefffec6de.png">
- File->New ->Data Source -> Postgresql을 선택하시면 됩니다.

# 4. 데이터 소스 설정을 해줍니다.

<img width="838" alt="스크린샷 2020-11-14 오후 11 20 12" src="https://user-images.githubusercontent.com/61309514/99149433-5326bc80-26d1-11eb-859c-5bca24538e68.png">


- 왼쪽에 PostgresSQL을 선택해줍니다.
- Host에 엔드포인트를 복/붙해줍니다.
- 우리 RDS의 포트는 5432입니다.
- Name은 newsdeskdb
- user,pw는 카톡방에 있습니다.
- database는 newsdesk
- url은 자동으로 만들어지니깐 건!들!지!마!세!요!
- Test Connection을 눌러줍니다.

# 5. Test Connection이 성공하면 ok을 눌러줍니다.
- 연결 완료!
