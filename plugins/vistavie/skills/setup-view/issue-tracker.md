# Issue tracker: VIEW

**This repo's issue tracker is VIEW** (configured by `setup-view`). Issues, specs, triage state and wayfinding maps live in the VIEW project below, not in GitHub Issues.

이 저장소의 과업과 스펙은 VIEW 프로젝트 `<슬러그>`의 항목이다. VIEW MCP 도구(`view_*`)로 다룬다. 항목은 ref `<슬러그>#<번호>`로 가리킨다.

## Conventions

- **과업 만들기**: `view_write` create, `project: "<슬러그>"`, `kind: "task"`, `fields.title`, `body`(마크다운). 같은 요청을 다시 보낼 수 있으면 `id`(UUID)를 정해 보낸다.
- **읽기**: `view_read`로 ref를 읽는다. 필드, 행 version, 관계, 블록 ID가 붙은 본문, 열린 댓글이 온다. `nextOffset`이 있으면 이어 읽는다.
- **목록**: `view_query`로 `items`를 조회한다. 예: `SELECT project_slug || '#' || number AS ref, title, status, triage FROM items WHERE project_slug = '<슬러그>' AND kind = 'task' AND status <> 'done'`.
- **분류 붙이기·떼기**: `view_write` update의 `fields.triage`. 대응표는 `triage-labels.md`. 과업 종류는 `fields.taskKind`(`task_kinds`의 이름)로 쓴다.
- **하위 과업**: `view_write` link, `relation: "parent"`, `other`는 상위 항목 ref.
- **댓글**: VIEW 댓글은 블록에 붙는 스레드다. 사람과 주고받는 대화(질문, `needs_info` 요청, 의견 요청)는 `view_write` comment로 본문 **첫 블록**에 새 스레드를 연다(`blockId`). 기존 스레드에 답할 때는 `threadId`를 쓴다. 남겨야 할 결과는 댓글 대신 본문 끝에 `## 답` 절로 `view_edit_body` insert 한다.
- **닫기**: `view_write` update, `fields.status: "done"`. 하지 않기로 했으면 `triage: "wontfix"`도 함께 둔다. 중복이면 본문에 원래 항목 ref를 적는다.

## Refs

- 쓸 때(커밋, PR, 과업 본문, 댓글)는 항상 `<슬러그>#<번호>` 전체 ref로 쓴다. GitHub에서 `#12`만 쓰면 이 저장소의 GitHub 이슈·PR로 연결된다.
- 사용자가 `#12`만 말하면 `<슬러그>#12`로 읽는다.

## Pull requests as a triage surface

**PRs as a request surface: no.** VIEW가 트래커이므로 GitHub PR은 분류 대상이 아니다.

## When a skill says "publish to the issue tracker"

VIEW 과업을 만든다(**과업 만들기**). 스펙처럼 상태가 필요 없는 글은 `kind: "doc"` 문서로 만들어도 된다.

## When a skill says "fetch the relevant ticket"

ref로 `view_read` 한다(**읽기**).

## Closing work

PR 머지가 VIEW 과업을 닫지 않는다. 일이 끝나면 **닫기**대로 과업을 직접 `done`으로 바꾸고, 필요하면 PR 주소를 본문이나 댓글에 남긴다.

## 쓰기 권한

VIEW는 저장소 지침이 정한 절차 안의 쓰기를 사용자 지시로 본다. 이 파일이 정하는 범위는 다음과 같다.

- **지시로 본다**: 사용자가 실행한 스킬의 절차가 요구하는 쓰기. 과업·문서 만들기, 분류·과업 유형 바꾸기, 댓글, 상위·선행 관계, 맡기, 닫기, 지도 문서 본문 갱신.
- **제안만 한다**: 기한·담당자 바꾸기, `<슬러그>`가 아닌 프로젝트에 쓰기, 다른 사람이 쓴 본문 고치기. 답변에 제안으로 적고 사용자가 지시하면 실행한다.

## Wayfinding operations

`/wayfinder`가 쓴다. **지도**는 문서 하나이고 **하위 과업**이 티켓이다.

- **지도**: `kind: "doc"` 문서. 본문에 Notes / Decisions so far / Not yet specified / Out of scope 절을 둔다. VIEW에는 라벨이 없으니 `wayfinder:map` 라벨은 붙이지 않는다. 지도는 ref로 가리킨다.
- **하위 과업**: `parent`로 지도에 맺은 과업. 티켓 종류는 `taskKind`로 쓴다: research → `조사`, prototype → `프로토타입`, grilling → `결정`, task → `구현`.
- **Blocking**: `view_write` link, `relation: "predecessor"`, `other`는 먼저 끝나야 하는 과업 ref. 선행 과업이 모두 `done`이면 풀린다.
- **Frontier**: 지도의 하위 과업 중 `status = 'todo'`이고 끝나지 않은 선행 과업이 없는 것. 담당자 유무는 보지 않는다. 지도 번호 순으로 첫 번째를 고른다.

  ```sql
  SELECT t.project_slug || '#' || t.number AS ref, t.title FROM items t
  JOIN item_relations r ON r.from_item_id = t.id AND r.kind = 'parent'
  JOIN items m ON m.id = r.to_item_id AND m.project_slug = '<슬러그>' AND m.number = <지도 번호>
  WHERE t.kind = 'task' AND t.status = 'todo' AND NOT EXISTS (
    SELECT 1 FROM item_relations p JOIN items b ON b.id = p.to_item_id
    WHERE p.from_item_id = t.id AND p.kind = 'predecessor' AND b.status <> 'done')
  ORDER BY t.number
  ```

- **Claim**: 담당자를 지정하지 않는다. 담당자는 그 과업을 책임지는 사람이라 바꾸지 않는다. 대신 세션의 첫 쓰기로 `view_write` update `fields.status: "doing"`, `before: { status: "todo" }`를 보낸다. 거절되면 다른 세션이 먼저 맡은 것이니 다음 frontier 과업을 고른다.
- **Resolve**: 본문 끝에 `## 답` 절로 답을 넣고(`view_edit_body` insert), 과업을 `done`으로 닫은 뒤, 지도 문서의 Decisions so far 절 끝에 요지 한 줄과 과업 ref를 넣는다.
