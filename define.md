
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
    - [Y] .env.example
    - [Y] .env
    - [Y] init_env.py
    - [?] github secrets
    - [N] 하드 코딩


### CICD

- CI
    - [Y] github actions

- CD
    - [?] 


## frontend

### flutter (for app)

#### platform

- 지원 OS : android
    - [Y] [material app](https://docs.flutter.dev/ui/widgets/material)

- 미지원 OS : ios
    - [N] [cupertino app](https://docs.flutter.dev/ui/widgets/cupertino)

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

- [Y] monolithic architecture
    - user, backup service

- [N] modular monolithic architecture
    - no reason for using

- [N] micro service architecture
    - no reason for using


### web server

- [?] nginx
    - for flutter web


### web application server

- [Y] spring boot
    - JAVA
    - Gradle


### DBMS

- [?] redis
    - no reason for using or not

- [Y] rdbms
    - user service
    - backup data service

- [?] nosql
    - no reason for using or not


