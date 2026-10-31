---
name: setup-view
description: "이 저장소를 VIEW 프로젝트에 연결한다. 이슈 트래커를 VIEW로 정하고 에이전트 지침에 프로젝트 맥락을 적는다. setup-matt-pocock-skills보다 먼저 실행한다."
disable-model-invocation: true
---

# VIEW 연결 설정

이 저장소를 VIEW 프로젝트 하나에 연결한다. 결과물은 두 가지다.

- **이슈 트래커**: 엔지니어링 스킬(`to-tickets`, `triage`, `wayfinder`, `to-spec`, `implement-spec` 등)이 과업을 VIEW에서 만들고 읽게 하는 `docs/agents/issue-tracker.md`와 `docs/agents/triage-labels.md`
- **프로젝트 맥락**: 어떤 에이전트든 작업 중 VIEW 문서·회의록을 찾아보게 하는 짧은 `## VIEW` 절

이 스킬은 setup-matt-pocock-skills보다 먼저 까는 기반이다. 같은 파일과 같은 블록 모양을 쓰므로, 나중에 setup-matt-pocock-skills를 실행하면 이슈 트래커가 VIEW로 정해져 있음을 알아본다. 도메인 문서(`docs/agents/domain.md`, `### Domain docs`)는 이 스킬이 다루지 않는다.

탐색하고, 찾은 것을 보여 주고, 확인을 받은 뒤 쓴다.

## 1. 탐색

- `git remote -v`: GitHub 주소를 `https://github.com/<owner>/<repo>` 소문자 형태로 바꾼다.
- VIEW 프로젝트: `view_query`로 `SELECT slug, name FROM projects WHERE github_repo = '<바꾼 주소>'`를 실행한다.
- 루트의 `CLAUDE.md`와 `AGENTS.md`: 있는지, 이미 `## Agent skills`나 `## VIEW` 절이 있는지.
- `docs/agents/issue-tracker.md`와 `docs/agents/triage-labels.md`: 이미 있는지, 있으면 어떤 트래커인지.

완료 조건: 연결할 프로젝트 후보, 고칠 지침 파일, 기존 `docs/agents/` 내용을 모두 파악했다.

## 2. 프로젝트 정하기

- 후보가 하나면 이름과 슬러그를 보여 주고 확인받는다.
- 후보가 없거나 여럿이면 `view_overview`(인자 없음) 목록을 보여 주고 고르게 한다. 후보가 없었다면 "VIEW 웹의 프로젝트 정보에 GitHub 저장소를 등록하면 다음부터 자동으로 찾는다"고 한 줄 안내한다. 프로젝트 정보는 MCP로 바꿀 수 없다.
- VIEW 도구 호출이 인증 오류로 실패하면 멈추고 `/mcp`에서 VIEW 서버 인증을 하라고 안내한다.

완료 조건: 슬러그 하나가 사용자 확인을 받았다. 웹 주소는 `view_overview(project)`가 주는 값을 쓴다.

## 3. 고칠 지침 파일 고르기

setup-matt-pocock-skills와 같은 규칙을 쓴다. 두 setup이 같은 파일을 고르게 하기 위해서다.

- `CLAUDE.md`가 있으면 그것을 고친다.
- 없고 `AGENTS.md`가 있으면 그것을 고친다.
- 둘 다 없으면 어느 쪽을 만들지 사용자에게 묻는다.

## 4. 초안 보여 주기

다음 초안을 보여 주고 고칠 기회를 준다.

- `docs/agents/issue-tracker.md`: [issue-tracker.md](./issue-tracker.md)의 `<슬러그>`를 채운 것
- `docs/agents/triage-labels.md`: [triage-labels.md](./triage-labels.md) 그대로
- 지침 파일에 넣을 두 블록(아래)

`docs/agents/issue-tracker.md`가 이미 있고 VIEW가 아니면(예: GitHub), 지금 내용을 요약해 보여 주고 VIEW판으로 바꿀지 따로 확인받는다. `triage-labels.md`도 같다. `triage` 스킬 설치 여부와 상관없이 쓴다. VIEW의 분류 값은 고정이라 라벨을 새로 만들 일이 없다.

지침 파일의 `## Agent skills` 블록은 setup-matt-pocock-skills와 같은 모양으로 쓴다. 이미 있으면 그 자리에서 아래 두 하위 블록만 넣거나 바꾸고, `### Domain docs` 등 다른 하위 블록은 그대로 둔다.

```markdown
## Agent skills

### Issue tracker

과업은 VIEW 프로젝트 `<슬러그>`에 있다(setup-view로 설정). See `docs/agents/issue-tracker.md`.

### Triage labels

분류는 VIEW 과업의 분류 필드 값(`needs_triage` 등)을 쓴다. See `docs/agents/triage-labels.md`.
```

`## VIEW` 절은 `## Agent skills` 밖에 따로 둔다. setup-matt-pocock-skills가 Agent skills 블록을 고쳐도 남게 하기 위해서다. 이미 있으면 그 자리에서 바꾼다.

```markdown
## VIEW

이 저장소의 VIEW 프로젝트는 `<슬러그>`다([VIEW](<웹 주소>)). 작업에 관련 결정이나 요구사항이 필요하면 `view_search`로 이 프로젝트의 문서·회의록을 찾아본다.
```

## 5. 쓰기

확인받은 초안대로 파일을 쓴다. 지침 파일의 다른 절은 건드리지 않는다.

## 6. 마무리

연결한 프로젝트와 쓴 파일을 알린다. 다음 단계로 setup-matt-pocock-skills를 실행하면 이슈 트래커가 VIEW로 정해져 있음을 알아보고, 도메인 문서 설정을 이어서 한다고 안내한다. 나중에 바꿀 것은 `docs/agents/*.md`를 직접 고치면 된다.

구조는 [mattpocock/skills](https://github.com/mattpocock/skills)의 setup-matt-pocock-skills를 참고했다.
