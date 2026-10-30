# sehyeok-skills

개인 AI skill 모음. `skills/` 아래 폴더 하나가 skill 하나이고, 각 폴더에 `SKILL.md`가 있다.
SKILL.md는 공개 형식이라 Claude Code 외의 도구에서도 쓸 수 있다.

## 구조

```
.claude-plugin/
  marketplace.json   # 마켓플레이스 목록 (Claude Code용)
  plugin.json        # 플러그인 정보 (Claude Code용)
skills/
  caveman/SKILL.md
```

## skill 목록

### 내 skill (`sehyeok` 플러그인)

| skill | 실행 | 설명 |
| :- | :- | :- |
| caveman | `/sehyeok:caveman` | 인사·군더더기 없이 코드 먼저, 짧게 답하는 토큰 절약 모드. "짧게", "간결하게", "토큰 아껴"라고 해도 켜진다. |

### 외부 플러그인

| 플러그인 | 설명 | 주요 명령 |
| :- | :- | :- |
| antislop | AI 코딩 에이전트가 만드는 뻔한 UI·문구·코드(AI slop)를 막는 규칙 모음. skill 6개. | 자동 적용 |
| humanize-korean | AI가 쓴 한글을 사람이 쓴 글처럼 윤문. 번역투·기계적 병렬·피동태 남용 등 85개 AI 티 패턴을 찾아 문체만 고친다. 내용은 안 바꾼다. | `/humanize`, `/humanize-scan`(AI 티 점검만), `/humanize-redo`(2차 윤문) |

## Claude Code에 설치

```bash
claude plugin marketplace add <GitHub아이디>/my-skills
claude plugin install sehyeok@sehyeok-skills
```

skill은 `/sehyeok:caveman` 처럼 플러그인 이름이 앞에 붙어서 실행된다.

## 함께 쓰는 외부 플러그인

외부 플러그인은 원작자 저장소를 내 GitHub에 fork하고, `marketplace.json`은 fork를 가리킨다.
원작자가 저장소를 지우거나 비공개로 바꿔도 fork가 남아서 설치가 깨지지 않는다.
fork는 `.github/workflows/sync-forks.yml`이 매일 원본과 동기화한다.

| 플러그인 | fork | 원본 | 라이선스 |
| :- | :- | :- | :- |
| antislop | https://github.com/SehyeokKim/anti-slop | https://github.com/miqdadbadjuber/anti-slop | MIT |
| humanize-korean | https://github.com/SehyeokKim/im-not-ai | https://github.com/epoko77-ai/im-not-ai | MIT |

```bash
claude plugin install antislop@sehyeok-skills
claude plugin install humanize-korean@sehyeok-skills
```

### 외부 플러그인 추가하기

1. 원본 라이선스가 fork·재배포를 허용하는지 확인
2. `gh repo fork <주인>/<저장소> --clone=false`
3. `marketplace.json`의 `plugins`에 항목 추가
   ```json
   { "name": "<플러그인 이름>", "source": { "source": "github", "repo": "SehyeokKim/<저장소>" } }
   ```
   플러그인이 저장소 하위 폴더에 있으면 `git-subdir` 소스를 쓴다.
4. `sync-forks.yml`의 `matrix.fork`에 `SehyeokKim/<저장소>` 추가
5. 위 표에 한 줄 추가

fork는 직접 수정하지 않는다. 수정하면 원본과 충돌해서 동기화가 실패할 수 있다.

### 동기화 설정 (처음 한 번)

1. GitHub → Settings → Developer settings → Fine-grained tokens에서 토큰 발급
   - Repository access: fork 저장소들
   - Permissions: Contents **Read and write**, Workflows **Read and write**
2. my-skills 저장소 → Settings → Secrets and variables → Actions에 `FORK_SYNC_TOKEN`으로 등록
3. Actions 탭에서 "Sync plugin forks"를 수동 실행해서 확인

자동 업데이트: Claude Code에서 `/plugin` → Marketplaces → sehyeok-skills → Enable auto-update.
수동 업데이트: `/plugin marketplace update sehyeok-skills`

## 다른 도구에서 쓰기

저장소를 clone한 뒤 `skills/` 안의 폴더를 그 도구의 skill 폴더(예: `~/.agents/skills`)에 복사하거나 링크한다.

## skill 추가하기

1. `skills/<skill이름>/SKILL.md` 추가
2. 커밋 후 push

`plugin.json`에 `version`을 두지 않았으므로, push한 커밋이 곧 새 버전이 된다.
