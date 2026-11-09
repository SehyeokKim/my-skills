# sehyeok-skills

내가 쓰는 Claude Code 플러그인을 한곳에 모아둔 큐레이션 마켓플레이스.
플러그인 파일은 이 저장소에 없다. `marketplace.json`이 각 플러그인의 fork(`sehyeok-plugins` org)를 가리키고,
fork는 GitHub Actions가 매일 원본과 동기화한다.

## 플러그인 목록

| 플러그인 | 설명 | 주요 명령 | 원본 | 라이선스 |
| :- | :- | :- | :- | :- |
| caveman | 원시인 말투로 짧게 답해서 출력 토큰을 줄이는 모드. 기술적인 정확도는 유지한다. | `/caveman:caveman` | [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) | MIT |
| antislop | AI 코딩 에이전트가 만드는 뻔한 UI·문구·코드(AI slop)를 막는 규칙 모음. skill 6개. | 자동 적용 | [miqdadbadjuber/anti-slop](https://github.com/miqdadbadjuber/anti-slop) | MIT |
| humanize-korean | AI가 쓴 한글을 사람이 쓴 글처럼 윤문. 번역투·기계적 병렬·피동태 남용 등 85개 AI 티 패턴을 찾아 문체만 고친다. 내용은 안 바꾼다. | `/humanize`, `/humanize-scan`(점검만), `/humanize-redo`(2차 윤문) | [epoko77-ai/im-not-ai](https://github.com/epoko77-ai/im-not-ai) | MIT |
| ponytail | 가장 짧고 단순한 해결책만 쓰게 하는 모드. 요청하지 않은 추상화를 막고 표준 라이브러리를 먼저 쓴다. | `/ponytail` | [dietrichgebert/ponytail](https://github.com/dietrichgebert/ponytail) | MIT |
| headroom | 세션을 시작할 때 headroom 프록시를 켜서 LLM 입력 토큰을 압축한다. 아래 "headroom 준비"를 먼저 한다. | 자동 적용 | [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) | Apache-2.0 |

fork는 모두 `https://github.com/sehyeok-plugins/<저장소>`에 있다.
skill 명령 이름은 각 플러그인이 정하므로 설치 후 `/`를 눌러 확인한다.

### headroom 준비

headroom 플러그인은 hook만 들어 있고 압축은 `headroom` CLI가 한다. PC마다 한 번 CLI를 설치하고 `headroom init`으로 배포를 만든다.

```bash
uv tool install --python 3.13 "headroom-ai[all]"
headroom init     # 옵션은 headroom init --help로 확인
```

CLI가 없으면 hook은 안내 메시지만 내고 넘어가며 세션은 막지 않는다.

원본 레포에는 플러그인이 2개 더 있지만 이 마켓플레이스에는 넣지 않았다. 필요하면 원본 마켓플레이스(`/plugin marketplace add headroomlabs-ai/headroom`)에서 설치한다.

- `headroom-skill`: Python·TypeScript 앱에 `headroom-ai`의 `compress()`를 붙이는 방법을 알려 주는 skill. Claude Code 자체의 토큰은 줄이지 않는다.
- `headroom-snip`: 프록시가 줄인 토큰을 프롬프트 위 막대와 `/headroom` 창으로 보여 준다. 프록시가 켜져 있어야 한다.

## 따로 설치하는 도구

마켓플레이스로 설치하지 않는 도구. 각 저장소의 README를 보고 직접 설치한다.

| 도구 | 설명 | 저장소 | 라이선스 |
| :- | :- | :- | :- |
| ARES | 보안 점검용 독립 프로그램(서버·웹 화면). Claude Code 플러그인이 아니다. | [Mafifrizi/ARES](https://github.com/Mafifrizi/ARES) | MIT |
| rea | 리버스 엔지니어링 분석 도구. 플러그인 파일이 있지만 별도 실행 환경이 필요할 수 있다. | [morluto/rea](https://github.com/morluto/rea) | MIT |
| graphify | 코드베이스·문서·SQL 스키마·PDF를 질의 가능한 지식 그래프로 만든다. `uv tool install graphifyy` 후 `graphify install`을 하면 `/graphify` skill이 등록된다. | [graphify-labs/graphify](https://github.com/graphify-labs/graphify) | Apache-2.0 |

## 설치

```bash
claude plugin marketplace add SehyeokKim/my-skills
claude plugin install caveman@sehyeok-skills
claude plugin install antislop@sehyeok-skills
claude plugin install humanize-korean@sehyeok-skills
claude plugin install ponytail@sehyeok-skills
claude plugin install headroom@sehyeok-skills
```

자동 업데이트: Claude Code에서 `/plugin` → Marketplaces → sehyeok-skills → Enable auto-update.
수동 업데이트: `/plugin marketplace update sehyeok-skills`

## 플러그인 추가하기

1. 원본 라이선스가 fork·재배포를 허용하는지 확인
2. `gh repo fork <주인>/<저장소> --org sehyeok-plugins --clone=false` (`--org`를 빼면 개인 계정에 fork된다)
3. 원본의 `.claude-plugin/marketplace.json`에서 플러그인 위치 확인
4. 이 저장소 `marketplace.json`의 `plugins`에 항목 추가
   ```json
   { "name": "<플러그인 이름>", "source": { "source": "github", "repo": "sehyeok-plugins/<저장소>" }, "description": "<설명>" }
   ```
   - 플러그인이 저장소 하위 폴더에 있으면 `git-subdir` 소스를 쓴다.
   - 특정 버전에 고정하려면 `source`에 `"ref": "<태그>"` 또는 `"sha": "<커밋>"`을 넣는다.
5. 위 표에 한 줄 추가
6. `claude plugin validate .` 후 커밋·push

동기화 워크플로는 org의 fork를 자동으로 찾으므로 따로 등록하지 않는다.

fork는 직접 수정하지 않는다. 수정하면 원본과 충돌해서 동기화가 실패할 수 있다.

## fork 동기화 설정 (처음 한 번)

1. GitHub → Settings → Developer settings → Fine-grained tokens에서 토큰 발급
   - Resource owner: `sehyeok-plugins`
   - Repository access: All repositories (나중에 추가하는 fork도 포함된다)
   - Permissions: Contents **Read and write**, Workflows **Read and write**
2. 이 저장소에 `FORK_SYNC_TOKEN`으로 등록: `gh secret set FORK_SYNC_TOKEN --repo SehyeokKim/my-skills`
3. Actions 탭에서 "Sync plugin forks"를 수동 실행해서 확인

원본이 삭제되거나 비공개로 바뀌면 동기화만 실패하고 fork는 마지막 상태로 남는다.
토큰이 만료되면 동기화가 실패하므로 실패 메일이 오면 토큰부터 확인한다.
