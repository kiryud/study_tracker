# flutter basic

> flutter app을 만들려고 할 때 내가 사용할 최소한도의 기술적인 영역을 정리한 보일러플레이트

## flutter

-  sequence
    1. 프로젝트 생성, 패키지 설치
    2. directory tree 구성
    3. 기본적인 파일 생성 및 최소한의 코드 작성
        1. 테마
        2. 프리뷰
        3. 페이지
        4. 라우터
        5. config (api host)
        6. 프로바이더
    4. 프로바이더, 라우터, 테마 연동
        - at main.dart

### init project

- 개발 환경 설정
    - [플러터 설치](https://docs.flutter.dev/install)
    - `flutter doctor` 을 통한 환경 설정

- .gitignore 생성
    - flutter project directory 내부의 것을 이 위치에서 정의하거나 레포 최상단의 깃 이그노어에 작성해야함

- 프로젝트 생성

```shell
cd PROJECT_HOME/frontend
flutter create --org dev.kiryud study_tracker
```

- 사용할 라이브러리 설정

```yml pubspec.yml
name: study_tracker
description: "과제 정리 애플리케이션"
publish_to: 'none' # Remove this line if you wish to publish to pub.dev

version: 1.0.0+1

environment:
    sdk: ^3.11.5

dependencies:
    flutter:
        sdk: flutter

    # Assets
    cupertino_icons: ^1.0.8
    lucide_icons_flutter: ^3.1.12

    # Core
    http: # 충돌 가능성때문에 버전 명시 비추천
    go_router: ^17.2.0
    flutter_riverpod: ^3.3.1
    shared_preferences: ^2.5.5
    intl:

    # device preview (최대한 가볍게 태블릿, 분할화면 테스트)
    device_preview: ^3.0.0


dev_dependencies:
    flutter_test: # unit test, widget test (flutter test로 가능)
        sdk: flutter
    integration_test: # optional integration test
        sdk: flutter
    mocktail: ^1.0.0 # Mock 객체를 통한 api, DB접근을 대체

    flutter_lints: ^6.0.0

flutter:
    uses-material-design: true
```

- flutter lint 설정

```yaml analysis_options.yaml
include: package:flutter_lints/flutter.yaml

analyzer:
    exclude:
        - build/**
        - .dart_tool/**

linter:
    rules:
        avoid_print: true
        prefer_const_constructors: true
```

- 라이브러리 설치

```shell
flutter pub get
```

### lib directory 분류 및 정의

- app : 앱의 전반적인 관리 요소
    - theme : 앱의 테마
        - app_theme.dart : 테마 정보
        - preview.dart : 프리뷰용 객체
    - router : 앱의 라우터
        - app_page.dart : enum을 통해 페이지 목록 관리
        - app_router.dart : enum을 인자로 받아 go router 동작
    - config : 앱의 설정 수준의 관리 요소
        - api_host_url.dart : 백엔드의 호스트 url 관리 객체
- core : 어디에서도 사용할 수 있는 공통 요소
    - enum : 어디서나 가져다 쓸 enum
    - extensions : snack bar같은 정해진 구조에서 내가 반복적으로 사용하는 요소 자동화
    - models : 임의의 객체 모델
        - result.dart : api 응답의 성공, 실패를 표현하는 공통 결과 객체
- feature
    - remote : network io
        - provider : api service를 DI할 수 있게 해주는 provider
        - service : api service
    - local
        - provider : local crud service를 DI할 수 있게 해주는 provider
        - service : sqlite crud service
    - timer
        - provider : 
        - service : 
        - models : time state를 관리해 줄 객체의 state model
- models : data class model (dto)
- ui : user interface
    - pages : 각 페이지
    - widget : 페이지에서 사용할 여러 위젯
        - common
        - dashboard
- main.dart : 프로바이더, 테마, 라우터를 명시할 최상단

### test directory

- unit
- widget
- helpers
    - test_data.dart

### integration test directory

- integration_test
    - app_test.dart
    - features

### 테마, 라우터, 프로바이더 생성 및 연동

- app_theme.dart

```dart
class AppTheme {
    static ThemeData get light =>
        ThemeData.light(
            useMaterial3: true,
        ).copyWith();
    static ThemeData get dark =>
        ThemeData.dark(
            useMaterial3: true,
        ).copyWith();
}
```

- preview.dart (`widget_previews`)

```dart
final class AppThemePreview extends Preview {
    const AppThemePreview({}) : super(theme: AppThemePreview.themeBuilder);

    static PreviewThemeData themeBuilder() => PreviewThemeData(
        materialLight: AppTheme.light,
        materialDark: AppTheme.dark,
    );
}
```

- app_page.dart
    - 각 page는 `ui/pages/$NAME/$NAME_page.dart`에서 stateless widget(stl)로 선언

```dart
enum AppPage {
    login, signup, home
}

extension AppPageExtention on AppPage {
    String get path => '/$name';

    Widget get page => switch (this) {
        .login => const LoginPage(),
        .signup => const SignupPage(),
        .home => const HomePage(),
    };
}
```

- app_router.dart (`go_router`)

```dart
final appRouter = GoRouter(
    initialLocation: AppPage.login.path, // 첫 페이지의 이름 지정
    routes: AppPage.values.map((e) {
        return GoRoute(
        name: e.name,
        path: e.path,
        builder: (context, state) => e.page,
        );
    }).toList(),
);
```

- api_host_url.dart
    - HOST : 미정 (무료 도메인 중 하나)
    - PATH : 'api/v1/'

```dart
class ApiHostUrl {
    static const apiHostUrl = 'https://[HOST]/[PATH]';
}
```

- main.dart (`flutter_riverpod` `device_preview`)

```dart
void main() {
    DevicePreview.enable(); // device_preview v3

    runApp(
        const ProviderScope(
        child: StudyTrackerApp()
        )
    );
}

class StudyTrackerApp extends StatelessWidget {
    const StudyTrackerApp({super.key});

    @override
    Widget build(BuildContext context) {
        return MaterialApp.router(
            themeMode: .system,
            theme: AppTheme.light,
            darkTheme: AppTheme.dark,
            routerConfig: appRouter,
        );
    }
}
```




