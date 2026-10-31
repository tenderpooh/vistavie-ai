# Triage Labels

엔지니어링 스킬이 말하는 다섯 가지 분류 역할을 VIEW 과업의 **분류 필드 값**에 대응시킨다. VIEW에는 라벨이 없으니 라벨을 만들지 않는다. 역할을 붙이고 떼는 일은 `view_write update`의 `triage` 필드로 한다.

| 스킬의 역할 이름 | VIEW 분류 값 | 뜻 |
| --- | --- | --- |
| `needs-triage` | `needs_triage` (검토 필요) | 아직 살펴보지 않았다 |
| `needs-info` | `needs_info` (정보 대기) | 요청한 사람의 답을 기다린다 |
| `ready-for-agent` | `ready_for_agent` (AI 작업 가능) | 에이전트가 맡아도 된다 |
| `ready-for-human` | `ready_for_human` (사람 작업 필요) | 사람이 해야 한다 |
| `wontfix` | `wontfix` (진행 안 함) | 하지 않기로 했다. 상태도 `done`으로 둔다 |

과업 하나는 분류를 하나만 가진다. 역할을 바꾸면 이전 값이 대체된다. `null`은 미분류다.
