# 저장소 지침

## 플러그인 버전

`plugins/vistavie/` 아래를 수정하면 플러그인 버전을 올린다. 버전은 아래 두 파일에 있고, 항상 같은 값으로 맞춘다.

- `plugins/vistavie/plugin.json`
- `plugins/vistavie/.claude-plugin/plugin.json`

버전은 유의적 버전(semver)을 따른다. 호환이 깨지면 major, 기능(스킬·MCP 서버 등)을 추가하면 minor, 문서·오타 수정은 patch를 올린다.
