
# 개발환경 정의 및 설정

## convention

### directory structure

```
/ 
│
├─ frontend/
│   └─ flutter/
│
├─ backend/
│   ├─ (optional) nginx/
│   ├─ spring boot/
│   └─ rdbms/
│
├─ .gitignore
├─ .env.example
├─ .env
├─ init_env.py
├─ docker-compose.yml
└─ Makefile
```


### 환경변수

- 관리 방법
    - [O] .env.example
    - [O] .env
    - [O] init_env.py
    - [?] github secrets
    - \[X] 하드 코딩


### CICD

- CI
    - [O] github actions

- CD
    - [?] 


## frontend

### flutter (for app)

#### platform

- 지원 OS : android
    - [O] [material app](https://docs.flutter.dev/ui/widgets/material)

- 미지원 OS : ios
    - \[X] [cupertino app](https://docs.flutter.dev/ui/widgets/cupertino)

#### tools

- [preview]()


#### ?

- [반응형 앱](https://docs.flutter.dev/ui/adaptive-responsive)

- [Material 3](https://m3.material.io/develop/flutter)

- [route](https://docs.flutter.dev/ui/navigation)
    - [go router](https://pub.dev/packages/go_router)

- [networking](https://docs.flutter.dev/data-and-backend/networking)
    - [http](https://pub.dev/packages/http)

- [interactivity](https://docs.flutter.dev/ui/interactivity)

## backend

### architecture

- [O] monolithic architecture
    - user, backup service

- \[X] modular monolithic architecture
    - no reason for using

- \[X] micro service architecture
    - no reason for using


### web server

- [?] nginx
    - for flutter web


### web application server

- [O] spring boot
    - JAVA
    - Gradle


### DBMS

- [?] redis
    - no reason for using or not

- [O] rdbms
    - user service
    - backup data service

- [?] nosql
    - no reason for using or not


