Skena 완벽 사용 가이드 (VS Code & Antigravity)

from gemini argon

Skena(DmitryMarienko.skena)는 옵시디언(Obsidian)의 표준 JSON Canvas 1.0 (.canvas) 포맷을 VS Code 및 Antigravity 내부에서 완벽하게 읽고 쓸 수 있게 해주는 비주얼 리서치·설계 캔버스 확장 프로그램입니다. 단순한 뷰어를 넘어 마크다운 볼트 연동, Vim 기반 공간 탐색, Jupyter 커널 코드 실행, 그리고 캔버스 내장 AI 컴패니언(MCP) 기능을 하나의 화면에서 제공합니다.

1. 설치 및 초기 세팅

- 1.1 VS Code에서 설치

Quick Open(Ctrl + P 또는 Cmd + P)을 열고 아래 명령어를 입력해 바로 설치합니다.

ext install DmitryMarienko.skena


- 1.2 Antigravity에서 설치 (Open VSX 우회)

Antigravity 기본 스토어에 노출되지 않을 경우 아래 두 방법 중 하나를 사용합니다.

VSIX 수동 설치: VS Marketplace - Skena 우측의 Download Extension에서 .vsix 파일을 받은 뒤, 확장 프로그램 탭(Ctrl + Shift + X) 우측 상단 ... 메뉴 → Install from VSIX...로 설치합니다.

마켓플레이스 경로 변경: 설정(Ctrl + ,)에서 marketplace를 검색해 Item URL(https://marketplace.visualstudio.com/items)과 Gallery URL(https://marketplace.visualstudio.com/_apis/public/gallery)을 등록한 뒤 재시작하여 검색·설치합니다.

- 1.3 `.gitignore`에 `.vscode` `.mcp.json` 추가


2. 캔버스 열기 및 파일·노드 추가 (Paste & Drop)

Skena는 클립보드 붙여넣기(Ctrl + V)와 드래그 앤 드롭만으로 데이터 타입을 자동 판별하여 적절한 노드를 생성하고, 현재 포커스된 노드에 자동으로 연결선(Edge)을 이어줍니다.

탐색기 드래그 앤 드롭: VS Code/Antigravity 파일 탐색기(Explorer)에서 여러 파일을 한 번에 캔버스로 끌어다 놓으면 파일 노드(File Node)로 배치됩니다. 디스크에서 원본 파일이 수정되면 캔버스 안의 프리뷰도 실시간으로 자동 갱신됩니다.

스마트 붙여넣기 (Ctrl + V):

일반 텍스트: 텍스트/마크다운 노드로 생성됩니다.

스크린샷 / 이미지 / 노트북 차트: 이미지 및 셀(Cell) 노드로 즉시 렌더링됩니다.

복사한 파일: 파일 노드로 생성됩니다.

다른 .canvas 파일: 다른 캔버스로 이동할 수 있는 포털(Portal) 노드가 됩니다.

경로/파일.canvas#노드ID 텍스트: 해당 캔버스의 특정 노드로 바로 점프하는 다이아몬드 포털 노드로 변환됩니다.

웹 URL: 외부 링크 노드로 생성됩니다.

노드 복제: 캔버스 내 노드에 포커스를 두고 yy(Vim 복사) 후 Ctrl + V를 누르면 해당 노드가 복제됩니다.

3. 마우스 없이 다루는 Vim 공간 탐색 & 편집 단축키

Skena의 가장 강력한 특징은 마우스에 손을 대지 않고 Vim 키바인딩만으로 2차원 캔버스를 이동하고 노드를 확장할 수 있다는 점입니다. 텍스트 노드 내부 편집창 또한 Monaco Editor(Vim 모드 내장)로 작동하므로 i, o, yy, dd, p 등의 명령어가 그대로 먹힙니다.

3.1 공간 이동 및 연결선 탐색

단축키

동작 설명

h / j / k / l

현재 포커스된 노드의 행(h/l) 또는 열(j/k) 기준으로 가장 가까운 좌/하/상/우 노드로 포커스 이동

gg / G

현재 섹션의 첫 번째 노드(gg) 또는 마지막 노드(G)로 즉시 이동

g

현재 노드에 연결된 모든 인입/인출 연결선(상하좌우 4면)에 1.5초간 점프 라벨 표시

g + 라벨 키

연결선 반대편 끝의 노드로 즉시 점프 및 화면 이동 (각 방향 첫 번째 연결은 h, j, k, l, 이후는 1~9, a~z 순 할당)

Shift + ( / Shift + )

현재 섹션 접기(Fold) / 펼치기(Unfold)

Enter / Ctrl + Enter

포커스된 파일 노드를 에디터 탭으로 열기 (분할 창으로 열기 / 전체 창으로 열기)

3.2 노드 생성 및 배치 제어

단축키

동작 설명

Ctrl + Shift + H/J/K/L (또는 방향키)

해당 방향에 연결선으로 이어진 빈 텍스트 노드를 즉시 생성하고 편집 모드(Insert) 진입

Alt + X 입력 후 h/j/k/l

해당 방향에 연결된 새 노드 생성 메뉴 호출 (새 텍스트 노트, 코드 노드, URL, 워크스페이스/볼트 파일 중 선택)

Space

현재 노드를 핀(Pin) 고정 선택 (다중 그룹 이동이나 연결선 생성 시 사용)

Shift + H/J/K/L

Space로 핀 고정한 노드들을 그리드 한 칸 단위로 이동 (뷰포트도 함께 따라감)

코드 셀에서 o

현재 코드 셀 바로 아래(동일 열)에 새로운 코드 셀 생성

4. 리치 인라인 프리뷰 (Rich Previews)

캔버스에 올라간 파일과 노드는 별도로 파일을 열지 않아도 내부 콘텐츠를 즉시 렌더링합니다.

Markdown (.md): YAML 프론트매터(Frontmatter) 헤더 바와 상태 배지, 스크롤 가능한 본문이 표시됩니다. [이동](./main.py#L42) 형태의 링크를 클릭하면 에디터에서 해당 파일의 42번째 줄을 바로 열어줍니다.

수식 렌더링: KaTeX($...$, $$...$$) 및 Typst(%...%, %%...%%) 수식을 벡터(SVG)로 깔끔하게 컴파일해 보여줍니다.

코드 파일 (.py, .yaml 등): Shiki 하이라이터가 적용된 구문 강조 프리뷰를 제공합니다.

Jupyter Notebook (.ipynb): 코드 셀, 마크다운, 차트 결과물, 이미지를 캔버스 위에서 그대로 보여줍니다.

인터랙티브 Plotly 차트: go.Figure 출력물이나 fig.to_json() 데이터를 붙여넣으면 노드 내부에서 마우스로 확대/축소(Zoom), 패닝(Pan), 호버(Hover)가 가능한 라이브 차트로 동작합니다 (Alt + P로 노트북 출력을 독립 노드로 고정 가능).

HTML (.html): 독립된 Shadow Root 내부에 렌더링되어 CSS 스타일이 캔버스 UI와 충돌하지 않습니다.

5. 캔버스 위에서 바로 코드 실행 (Jupyter Kernel 연동)

Skena는 캔버스 자체를 비선형(Non-linear) 라이브 노트북처럼 쓸 수 있게 해줍니다.

커널 노드 배치: 명령 팔레트(Ctrl + Shift + P)에서 Skena: Add Kernel을 실행해 캔버스에 커널 노드를 떨어뜨립니다.

코드 노드 연결: Ctrl + N 또는 Alt + X 메뉴에서 Code node를 만든 뒤, 커널 노드와 화살표(Edge)로 연결합니다.

의존성 순서 실행: 코드 노드 안에서 Shift + Enter (또는 Ctrl + Enter, Alt + R, 헤더의 ▶ 버튼)를 누르면 실행됩니다. 만약 다른 코드 노드들이 상류(Upstream)에 연결되어 있다면, 아직 실행되지 않은 선행 셀들을 의존성 순서에 맞춰 먼저 실행한 뒤 현재 셀을 실행합니다.

실시간 출력 스트리밍: 실행 결과(stdout, tqdm 진행률 바, ipywidgets, 차트 등)는 코드 노드와 연결된 출력 노드(Output Node)로 실시간 스트리밍됩니다.

실행 중단 및 리셋: 실행 중 Ctrl + C 또는 ■ 버튼으로 중단할 수 있으며, 커널 노드에서 Restart를 누르면 모든 셀의 실행 플래그가 초기화되어 깔끔한 네임스페이스에서 재실행할 수 있습니다.

6. 캔버스 내장 AI 컴패니언 & MCP 연동

사이드바 채팅창이 아니라 캔버스 하단에 도킹된 전용 AI 콘솔이 내장되어 있습니다. AI가 현재 내가 보고 있는 캔버스의 맥락을 시각적·구조적으로 함께 인식합니다.

6.1 AI가 자동으로 전달받는 컨텍스트

현재 포커스된 노드의 전체 텍스트 및 데이터

포커스된 노드와 1촌(1-hop)으로 연결된 주변 노드 정보

현재 화면 뷰포트(줌 레벨, 화면에 보이는 노드 라벨 목록)

Space로 핀 고정한 뒤 입력창의 + 버튼으로 첨부한 노드, 파일, 클립보드 이미지

6.2 MCP 기반 캔버스 직접 제어

Skena에는 전용 MCP(Model Context Protocol) 도구가 번들링되어 있어, AI에게 지시하면 AI가 대답만 하는 것이 아니라 직접 노드를 읽고, 수정하고, 포커스된 노드 옆에 새로운 요약 노트를 생성하거나 Jupyter 코드 셀을 실행합니다.

6.3 채팅 콘솔 전용 단축키

단축키

동작 설명

Alt + I

캔버스 화면 ↔ 하단 AI 채팅 입력창 간 포커스 전환

`Alt + `` (백틱)

대화 내역 창 접기 / 펼치기 (접어도 하단 입력 바는 유지됨)

Ctrl + Enter

메시지 전송

Shift + H/J/K/L

입력창이 Vim Normal 모드일 때 상단 대화 내역 스크롤

7. 핵심 환경 설정 (settings.json)

VS Code 또는 Antigravity의 settings.json에 아래 설정을 추가해 AI 프로바이더, 마크다운 테마, Jupyter 서버를 커스터마이징할 수 있습니다.

{
  // 1. 마크다운 렌더링 테마 설정 ("vscode": 기본 테마 연동 | "factors": 다크 리서치 터미널 테마)
  "skena.markdownTheme": "factors",
  "skena.markdownMaxWidth": 720,

  // 2. AI 컴패니언 프로바이더 설정
  // - "harness": 로컬에 설치된 Claude Code CLI 세션을 API 키 없이 그대로 구동 (권장)
  // - "anthropic": Anthropic API 직접 호출
  // - "openai-compat": Ollama, LM Studio, OpenAI, Groq 등 호환 엔드포인트 연결
  "skena.ai.provider": "harness",
  "skena.ai.model": "claude-sonnet-4-20250514",
  // "skena.ai.apiKey": "sk-ant-...", // anthropic 또는 openai-compat 사용 시 입력

  // 3. 외부/로컬 Jupyter 커널 서버 등록
  "skena.jupyter.kernels": [
    {
      "name": "Local Research Kernel",
      "hubUrl": "http://localhost:8888",
      "token": "your-jupyter-token"
    }
  ]
}


8. 실전 추천 워크플로우 (Obsidian + Antigravity + Git)

초기 아키텍처 스케치: 옵시디언이나 Antigravity의 Skena에서 .canvas 파일을 만들고 Ctrl + Shift + H/J/K/L 단축키로 아이디어나 모듈 구조를 빠르게 노드로 뻗어 나갑니다.

코드 및 문서 매핑: 프로젝트 폴더의 실제 .py, .ts, .md 파일들을 탐색기에서 캔버스로 드래그 앤 드롭하여 설계 노드들과 연결합니다.

AI 협업 및 검증: 특정 노드에 포커스를 둔 상태로 Alt + I를 눌러 AI 컴패니언에게 *"현재 연결된 모듈 구조를 분석해서 예외 처리가 필요한 케이스를 옆에 새 노드로 추가해 줘"*라고 지시합니다.

Git 버전 관리: .canvas 파일은 순수 JSON 텍스트이고 AI 대화 히스토리는 .skena.json 사이드카 파일로 저장되므로, 코드와 함께 Git에 커밋하여 팀원이나 옵시디언 환경과 100% 동일하게 공유합니다.