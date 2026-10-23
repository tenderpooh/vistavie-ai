# VIS TA VIE AI

비따비의 AI 플러그인과 공통 스킬을 배포하는 공개 저장소입니다. 마켓플레이스와 첫 플러그인의 이름은 모두 `vistavie`입니다.

현재 `vistavie` 플러그인에는 VIEW 원격 MCP 연결 설정이 들어 있습니다. 업무 스킬을 추가할 공통 폴더는 마련했으며, 실행 스킬은 아직 배포하지 않습니다. 플러그인 설치로 VIEW 데이터 접근 권한이 생기지는 않습니다. 각 사용자가 회사 계정으로 로그인하고 접근을 허용해야 합니다.

## 구조

```text
.agents/plugins/marketplace.json       # Codex 마켓플레이스
.claude-plugin/marketplace.json        # Claude Code 마켓플레이스
plugins/
└── vistavie/
    ├── plugin.json                   # Codex용 표준 플러그인 정보
    ├── mcp.json                      # Codex용 VIEW MCP 설정
    ├── .claude-plugin/plugin.json    # Claude Code 플러그인 정보
    ├── .mcp.json                     # Claude Code용 VIEW MCP 설정
    └── skills/                       # 두 클라이언트가 공유하는 스킬 원본
        └── README.md
```

`plugins/`는 플러그인을 담는 상위 폴더입니다. 현재 플러그인은 `vistavie` 하나이며, 여러 사내 스킬을 그 안에 추가할 수 있습니다.

## 설치

최신 Codex 또는 Claude Code가 설치되어 있어야 합니다. 다음 명령은 터미널에서 실행합니다. 클라이언트 버전에 따라 플러그인 기능과 표시 위치가 다를 수 있습니다.

### Codex

```sh
codex plugin marketplace add tenderpooh/vistavie-ai
codex plugin add vistavie@vistavie
```

설치 후 사용하는 Codex 클라이언트에서 MCP 인증을 진행합니다. 설치만으로 로그인까지 완료되지는 않습니다.

### Claude Code

```sh
claude plugin marketplace add tenderpooh/vistavie-ai
claude plugin install vistavie@vistavie
```

Claude Code를 다시 열고 `/mcp`에서 VIEW 서버의 인증을 진행합니다. 이미 VIEW를 직접 연결했다면 중복 등록 여부를 확인합니다.

### 연결 확인

AI에게 “VIEW에서 내가 접근할 수 있는 프로젝트 목록을 확인해 줘”라고 요청합니다. 실제 조회가 성공해야 연결과 인증이 작동한 것입니다. 실패하면 해당 클라이언트의 MCP 상태와 인증 화면을 확인합니다.

이 저장소는 Codex와 Claude Code용 배포 설정입니다. ChatGPT 웹이나 Claude 일반 채팅 앱에 자동으로 설치되지는 않습니다.

## MCP 설정 관리

서버 식별자는 `view`, 운영 주소는 `https://view.vistavie.app/api/mcp`입니다. 자격 증명은 배포 파일에 넣지 않고 사용자별 OAuth로 처리합니다.

Codex 표준 설정은 `mcp.json`의 `streamable-http`, Claude Code 설정은 `.mcp.json`의 `http`를 사용합니다. 두 파일은 같은 서버를 가리키므로 주소를 바꿀 때 함께 수정합니다. 플러그인 이름과 버전도 두 `plugin.json` 파일에서 맞춥니다.

## 스킬 추가

[스킬 폴더 안내](plugins/vistavie/skills/README.md)에 따라 `plugins/vistavie/skills/<스킬 이름>/SKILL.md`를 추가합니다. 공통 원본을 한 번만 작성하고 두 클라이언트에서 확인합니다.

변경한 플러그인은 두 manifest의 버전을 함께 올립니다. 클라이언트의 마켓플레이스 및 플러그인 업데이트 기능으로 새 버전을 받은 뒤 설치와 실제 도구 호출을 확인합니다.

## 공개 범위와 검증

공개 가능한 설정·스킬·가상 예시만 올립니다. API 키, OAuth 토큰, 고객 자료, 개인 정보, 기밀 지침은 포함하지 않습니다. 외부 스킬을 가져올 때는 원본 라이선스와 재배포 조건을 확인합니다.

초기 구성에서는 JSON 스키마와 Claude 플러그인·마켓플레이스 형식, MCP의 인증 요구 응답을 확인합니다. 사용자 계정의 OAuth 왕복, 앱에서의 설치와 업데이트, 실제 프로젝트 조회는 별도 검증이 필요합니다.

## 공식 참고 문서

- [OpenAI 플러그인 패키징](https://developers.openai.com/plugins/build/plugins)
- [Claude Code 마켓플레이스](https://code.claude.com/docs/en/plugin-marketplaces)
- [Claude Code 플러그인 형식](https://code.claude.com/docs/en/plugins-reference)
