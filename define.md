
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
│   ├─ .env.example
│   ├─ .env
│   ├─ init_env.py
│   ├─ docker-compose.yml
│   ├─ (optional) nginx/
│   ├─ spring boot/
│   └─ rdbms/
│
├─ .gitignore
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

- 특정 버전의 완성시 build 및 packaging을 거쳐 artifact를 만드는 구조
    - app의 경우 apk 혹은 abb형태로, 웹의 경우 정적 파일 기반의 directory로 만들어질 수 있다 (명령어를 통해 관리)

#### platform

- 지원 OS : android
    - [Y] [material app](https://docs.flutter.dev/ui/widgets/material)
    - [integration](https://docs.flutter.dev/platform-integration/android/setup)

- 미지원 OS : ios ipados
    - [N] [cupertino app](https://docs.flutter.dev/ui/widgets/cupertino)

- 추후 재고려 : web window linux macos

### tools

#### preview

- [android emulator](https://developer.android.com/studio/run/emulator?hl=ko)

- [hot reload](https://docs.flutter.dev/tools/hot-reload) : flutter는 개발중 실시간 UI 갱신이 가능하다

- [preview](https://docs.flutter.dev/tools/widget-previewer) : 특정 위젯의 미리보기

- [device_preview](https://pub.dev/packages/device_preview) : (왜 다른게 아니라 이게 필요하다는건지 아직 모르겠지만 일단 링크 기록)

#### test

- [testing at flutter](https://docs.flutter.dev/testing/overview)
    - flutter에서는 `unit` ⊂ `widget` ⊂ `integration`으로 구성되어있다
        - [unit test](https://docs.flutter.dev/cookbook/testing/unit/introduction)
        - [widget test](https://docs.flutter.dev/cookbook/testing/widget/introduction)
        - [integration test](https://docs.flutter.dev/cookbook/testing/integration/introduction)

- 어차피 위젯 단위로 프리뷰 활용하고 필요시 로컬 애뮬레이터, 브라우저, 윈도우 세가지 다 활용 가능해서 unit test만이라도 도입해볼 생각 하는중


#### module

- [flutter lints](https://pub.dev/packages/flutter_lints) : flutter의 linter
    - [lints](https://pub.dev/packages/lints) : dart linter

- [uuid](https://pub.dev/packages/uuid) : UUIDv7용

- [반응형 앱](https://docs.flutter.dev/ui/adaptive-responsive)

- [Material 3](https://m3.material.io/develop/flutter)

- [route](https://docs.flutter.dev/ui/navigation)
    - [go router](https://pub.dev/packages/go_router)

- [networking](https://docs.flutter.dev/data-and-backend/networking)
    - [http](https://pub.dev/packages/http)
        - [serialization](https://docs.flutter.dev/data-and-backend/serialization/json)
        - [Parse JSON in the background](https://docs.flutter.dev/cookbook/networking/background-parsing)

- [interactivity](https://docs.flutter.dev/ui/interactivity)

- [provider](https://pub.dev/packages/provider) : 기존 프로젝트 unitask에서 사용해 봄
    - [riverpod](https://pub.dev/packages/flutter_riverpod) : 빠른 적용 가능하다면 공부해보고 써보는걸 추천

- [알림 기능](https://pub.dev/packages/flutter_local_notifications) : 상단 바 푸시 알림

- [백그라운드 동작](https://pub.dev/packages/flutter_foreground_task) : 앱이 백그라운드에 가도 포그라운드에서 살릴 작업 관리하기

- [SQLite](https://docs.flutter.dev/cookbook/persistence/sqlite)
    - [flutter app의 dbms 선택 조언](https://docs.flutter.dev/data-and-backend/persistence/choose-a-database)
        - 놀랍게도 firebase는 rdbms로도 사용 가능하다고 한다 (postgresSQL을 내장시켰다고 한다)

#### local storage

-  secure storage
    - JWT 암호화 저장 (client side에서 OAuth2.0 OIDC 기반의 JWT를 다루진 않아 우리 서버의 access token 발급 저장소)

- local database (SQLite)
    - guest, users 등의 여러 주인이 존재할 수 있음 (학습용 기기 공유라니 다시 생각해봐도 아찔해지는 맹점이야)
        - guest.db, user_UUID.db 형태가 될 듯
    - guest로 쓰다가 다른 유저 로그인을 허용하는 경우가 있을것인가.....?
    - 일단 guest 연동시 데이터 이관 및 삭제 처리라는 정책이 어떤 의미이고 어떤 사용성을 가질지는 고민이 더 필요함


## backend

### architecture

- [Y] monolithic architecture
    - user, backup service

- [N] modular monolithic architecture
    - no reason for using

- [N] micro service architecture
    - no reason for using

### Infra

- docker compose

```ascii
[monolithic 3tier backend api server]
┌───────────────────────┐                   ┌────────────────────────────────┐
│ docker network bridge │ <- RESTful API -> │ flutter app                    │
│ [80:80] [443:443]     │    (/api/*)       │                                │
│┌─────────────────────┐│                   │ [API: https://DOMAIN/api/v1/*] │
││ nginx               ││                   └────────────────────────────────┘
││ [:80 :443]          ││
│└──────────┬──────────┘│                   ┌────────────────────────────────┐
│           │           │ <- Web ---------- │ flutter web (opthinal)         │
│┌──────────┴──────────┐│    (/)            │ [URI: https://DOMAIN/PATH]     │
││ spring boot         ││ <- RESTful API -> │ [API: https://DOMAIN/api/v1/*] │
││ [:8080]             ││    (/api/*)       └────────────────────────────────┘
│└──────────┬──────────┘│
│           │           │
│┌──────────┴──────────┐│
││ postgresSQL         ││
││ [:5342]             ││
│└─────────────────────┘│
└───────────────────────┘
```

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
        - SQLite기반의 file을 다뤄야하고 해당 파일에 대한 메타데이터를 가져야함

- [?] nosql
    - no reason for using or not at backend server
    - client가 SQLite기반으로 만든 file을 backend server에서 해석해야만 하는 이유가 아직 없어보임

---

- expanded ascii tool box

```ascii
[기본 실선 / 단선 (Light)]
┌ ┬ ┐   ┌─┬─┐
├ ┼ ┤   │ │ │
└ ┴ ┘   └─┴─┘
선: ─ │

[굵은 실선 (Heavy)]
┏ ┳ ┓   ┏━┳━┓
┣ ╋ ┫   ┃ ┃ ┃
┗ ┻ ┛   ┗━┻━┛
선: ━ ┃

[이중선 (Double)]
╔ ╦ ╗   ╔═╦═╗
╠ ╬ ╣   ║ ║ ║
╚ ╩ ╝   ╚═╩═╝
선: ═ ║

[둥근 모서리 (Rounded)]
╭ ┬ ╮   ╭─┬─╮
├ ┼ ┤   │ │ │
╰ ┴ ╯   ╰─┴─╯

[단선 + 이중선 혼합 분기점]
╒ ╤ ╕  ╓ ╥ ╖
╞ ╪ ╡  ╟ ╫ ╢
╘ ╧ ╛  ╙ ╨ ╜

[화살표 및 포인터]
삼각 채움: ► ◄ ▲ ▼
삼각 빈칸: ▷ ◁ △ ▽
선 화살표: → ← ↑ ↓ ↔ ↕
이중 화살표: ⇒ ⇐ ⇑ ⇓ ⇔

[체크 / 불릿 / 상태 기호]
블록: █ ▓ ▒ ░ ■ □ ▣ 
원형: ● ○ ◐ ◑ ◈ ◇ ◆
체크: ✔ ✖ ✕ ✗ 
```

- 집합 포함관계 기호
```
부분집합 (진부분집합): ⊂ / ⊃
예시: unit ⊂ widget ⊂ integration
부분집합 (동등 포함 가능): ⊆ / ⊇
부분집합 아님: ⊄ / ⊅
원소 기호: ∈ / ∋ / ∉
합집합 / 교집합: ∪ / ∩
```
