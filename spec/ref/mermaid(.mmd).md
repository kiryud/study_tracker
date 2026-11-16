# Mermaid 기초 레퍼런스 (.md / .mmd)

> - 스펙(Spec) 문서에서 화면 구조, 상태 전이, 데이터 흐름, DB 스키마를 정의할 때 복붙해 쓰는 기본 문법 정리
> - from gemini argon
> - `Markdown Preview Mermaid Support`

## 0. 공통 규칙 및 문법 에러 방지 수칙

- **작성 방식**
    - 마크다운(`.md`) 내부: ` ```mermaid \r\n``` ` 코드블록 안에 작성
    - 단독 파일(`.mmd`): 백틱 없이 첫 줄에 다이어그램 선언어(`flowchart TD` 등)부터 작성
- **주석**: 줄 앞에 `%%` 작성 (`%% 주석 내용`)
- **문법 에러 방지 3대 규칙**
    - **큰따옴표 필수**: 표시할 이름에 띄어쓰기, 한글, 괄호(`()`, `[]`), 콜론(`:`), 슬래시(`/`)가 들어가면 반드시 `ID["표시할 텍스트"]`처럼 큰따옴표로 감싼다.
    - **예약어 ID 금지**: 노드 ID를 소문자 `end`로 지으면 블록 종료 예약어와 충돌한다. (`END`, `EndNode` 등으로 작성)
    - **화살표 라벨 따옴표**: 화살표 설명에도 특수문자가 들어가면 `-->|"onTap(id)"|`처럼 큰따옴표로 감싼다.

---

## 1. Flowchart (`flowchart`) — 화면 영역 중첩 & 이동 흐름

### 방향 선언
- `flowchart TD` (또는 `TB`): 위 → 아래 (Top-Down)
- `flowchart BT`: 아래 → 위 (Bottom-Top)
- `flowchart LR`: 왼쪽 → 오른쪽 (Left-to-Right)
- `flowchart RL`: 오른쪽 → 왼쪽 (Right-to-Left)

### 노드(박스) 모양
- `A["사각형: 일반 페이지 / 위젯 / 영역"]`
- `B("둥근 사각형: 버튼 / 입력 / 액션")`
- `C(["캡슐형: 시작 / 종료 / 외부 진입점"])`
- `D{"마름모: 조건 분기 (if / switch)"}`
- `E[("원통형: SQLite / DB / Storage")]`
- `F[["서브루틴: 외부 서비스 / API 모듈"]]`

### 연결선(화살표) 종류
- `A --> B` : 실선 화살표 (일반 이동 / 직접 호출)
- `A -->|"이벤트 / 조건"| B` : 라벨이 붙은 실선 화살표
- `A -.-> B` : 점선 화살표 (모달 호출 / 간접 참조 / 설정 이동)
- `A -.->|"비동기 / 참조"| B` : 라벨이 붙은 점선 화살표
- `A ==> B` : 굵은 화살표 (핵심 메인 플로우)
- `A <--> B` : 양방향 화살표
- `A --- B` : 화살표 없는 단순 연결선

### 복붙용 템플릿 (Shell / Page / Widget 중첩 구조)

```mermaid
flowchart TD
    subgraph AuthShell["인증 쉘 (Auth Shell)"]
        direction LR
        Login["로그인 페이지"]
        Signup["회원가입 페이지"]
        Login -->|"회원가입 클릭"| Signup
    end

    subgraph MainShell["메인 쉘 (Main Shell)"]
        direction LR
        subgraph Dashboard["대시보드 페이지"]
            direction TB
            Motive["동기부여 배너"]
            TimerCard["타이머 요약 카드 (1x1)"]
            StartBtn("바로 시작 버튼")
        end

        subgraph Tracker["타임 트래커 페이지"]
            direction TB
            Clock["시계 영역"]
            DB[("SQLite")]
            Clock -.->|"기록 저장"| DB
        end
    end

    Login ==>|"로그인 성공"| MainShell
    StartBtn -->|"onTap: 타이머 실행"| Clock
```

---

## 2. State Diagram (`stateDiagram-v2`) — 상태 머신 & 이벤트 전이

### 핵심 문법
- **시작과 끝**: `[*] --> 초기상태`, `종료상태 --> [*]`
- **상태 별칭 지정**: `state "화면에 보일 한글 이름" as StateID`
- **이벤트 전이**: `StateA --> StateB : 이벤트명 / 실행 액션`
- **중첩(복합) 상태**: `state 상위상태ID { ... }`
- **동시(병렬) 영역 구분**: 상태 블록 내부에 `--` 줄을 그어 독립적으로 돌아가는 하위 상태를 나눔
- **조건 분기**: `state 분기ID <<choice>>` 선언 후 화살표 연결
- **메모 부착**: `note right of StateID : 메모 내용` (방향: `left of`, `right of`)

### 복붙용 템플릿 (타이머 / 비동기 상태 전이)

```mermaid
stateDiagram-v2
    state "대기 (Idle)" as Idle
    state "측정 중 (Running)" as Running
    state "일시정지 (Paused)" as Paused
    state "완료 처리 (Completed)" as Completed
    state check_target <<choice>>

    [*] --> Idle : 페이지 진입
    Idle --> check_target : onStart() 클릭
    check_target --> Running : 계획 선택됨 (planId 바인딩)
    check_target --> Running : 미선택 (자유 학습 모드)

    state Running {
        [*] --> NormalMode
        NormalMode --> FullscreenMode : 전체화면 버튼 클릭
        FullscreenMode --> NormalMode : 축소 버튼 클릭
    }

    Running --> Paused : onPause()
    Paused --> Running : onResume()
    Running --> Completed : 시간 만료 / onStop()
    Paused --> Completed : onStop() (조기 종료)

    Completed --> [*] : SQLite 저장 완료
```

---

## 3. Sequence Diagram (`sequenceDiagram`) — 레이어 간 호출 순서

### 핵심 문법
- **참여자 선언**
    - `actor User as 유저` (사람 모양 아이콘)
    - `participant UI as 페이지(Widget)` (일반 박스)
    - `participant Prov as 프로바이더(Riverpod)`
    - `participant Svc as 서비스(Logic)`
    - `participant DB as SQLite / API`
- **화살표 종류**
    - `A->>B: 메시지` (실선 화살표: 요청 / 함수 호출)
    - `B-->>A: 메시지` (점선 화살표: 응답 / 반환값)
    - `A->>+B: 호출` / `B-->>-A: 반환` (`+`, `-`로 실행 중 활성화 막대 표시)
- **제어 블록**
    - `alt 조건A` ~ `else 조건B` ~ `end` : 성공/실패 등 조건 분기
    - `opt 조건` ~ `end` : 단일 조건일 때만 실행 (if문)
    - `loop 반복조건` ~ `end` : 반복 실행

### 복붙용 템플릿 (UI → Provider → Service → API/DB)

```mermaid
sequenceDiagram
    actor User as 유저
    participant UI as Page (Widget)
    participant Prov as Provider
    participant Svc as Service
    participant DB as API / SQLite

    User->>UI: 버튼 클릭 (이벤트 발생)
    UI->>+Prov: 액션 메서드 호출
    Prov->>+Svc: 비즈니스 로직 요청
    Svc->>+DB: 쿼리 / HTTP 요청
    alt 성공 시
        DB-->>-Svc: 데이터 반환
        Svc-->>-Prov: Result.success(data)
        Prov-->>-UI: State 업데이트 (리빌드)
    else 실패 시
        DB-->>Svc: 에러 발생
        Svc-->>Prov: Result.failure(error)
        Prov-->>UI: 에러 상태 전달 (SnackBar 표시)
    end
```

---

## 4. ER Diagram (`erDiagram`) — SQLite 테이블 및 관계 설계

### 관계 기호 (좌측과 우측에 조합하여 사용)
- `||` : 정확히 1개 (One)
- `|o` 또는 `o|` : 0개 또는 1개 (Zero or One)
- `}|` 또는 `|{` : 1개 이상 (One or More)
- `}o` 또는 `o{` : 0개 이상 (Zero or More)
- **자주 쓰는 조합**
    - `A ||--o{ B : "1:N 관계"` (A 하나에 B가 0개 이상 연결)
    - `A ||--|{ B : "1:N 필수 관계"` (A 하나에 B가 최소 1개 이상 연결)

### 복붙용 템플릿 (테이블 정의 및 FK 연결)

```mermaid
erDiagram
    CATEGORY ||--o{ CATEGORY : "parent_id (자기참조 계층)"
    CATEGORY ||--o{ PLAN : "포함"
    PLAN ||--o{ STUDY_SESSION : "기록"

    CATEGORY {
        int id PK
        int parent_id FK "상위 카테고리 (null이면 최상위 분야)"
        string name "분야/과목/단원명"
        int depth "1:분야, 2:과목, 3:단원"
    }

    PLAN {
        int id PK
        int category_id FK
        string title "계획명"
        string target_date "목표 일자"
    }

    STUDY_SESSION {
        int id PK
        int plan_id FK "null 허용 (계획 없는 자유 학습)"
        int category_id FK
        int duration_sec "학습 시간(초)"
        string started_at "시작 시각"
    }
```

---

## 5. Class Diagram (`classDiagram`) — 모델(DTO) & 객체 구조

### 핵심 문법
- **접근 제어자**: `+` (Public), `-` (Private), `#` (Protected)
- **관계 화살표**
    - `Parent <|-- Child` : 상속 (`extends`)
    - `Interface <|.. Impl` : 인터페이스 구현 (`implements`)
    - `Container *-- Item` : 강한 포함 (Composition)
    - `Caller --> Target` : 참조 및 의존 (Dependency)

### 복붙용 템플릿 (State / Service / Repository 관계)

```mermaid
classDiagram
    class Result~T~ {
        <<sealed>>
        +bool isSuccess
    }
    class Success~T~ {
        +T data
    }
    class Failure~T~ {
        +String message
    }

    Result <|-- Success
    Result <|-- Failure

    class TimerState {
        +int elapsedSeconds
        +bool isRunning
        +int? selectedPlanId
        +copyWith() TimerState
    }

    class TimerNotifier {
        -TimerService _service
        +TimerState state
        +start() void
        +pause() void
        +stop() Future~Result~
    }

    TimerNotifier --> TimerState : 상태 관리
```
