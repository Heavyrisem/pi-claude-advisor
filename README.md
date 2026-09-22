# pi-claude-advisor

Pi coding agent 확장. 더 강한 리뷰어 모델(Claude Code CLI)에게 2차 의견을 묻는 `advisor` 툴을 추가하고, 에이전트가 검증 없이 질주하지 못하도록 게이트를 건다.

## 무엇을 하나

- **`advisor` 툴** — `task` / `context` / `files` / `model` 을 받아 `claude` CLI 를 띄우고 조언을 돌려준다. 리뷰어는 툴이 없다. 페이로드에 담긴 것만 본다.
- **edit/write 게이트** — 대화 중 첫 `edit` / `write` 호출을 한 번 막고 advisor 부터 부르게 한다.
- **연속 실패 게이트** — advisor 호출 이후 실패한 tool result 를 센다. 2회에서 경고를 붙이고, 3회에서 다음 툴 호출을 막는다. 카운터는 advisor 호출이나 새 세션에서만 리셋된다.
- 토큰 사용량·비용을 결과 하단에 렌더링한다.

## 설치

```bash
pi install git:github.com/heavyrisem/pi-claude-advisor
```

프로젝트 로컬만:

```bash
pi install git:github.com/heavyrisem/pi-claude-advisor -l
```

갱신: `pi update git:github.com/heavyrisem/pi-claude-advisor` (전체는 `pi update --extensions`)

## 요구 사항

- pi >= 0.87.0
- Node >= 22.19.0
- **`claude` CLI 가 설치되어 있고 인증되어 있어야 한다.** 이 확장은 `claude` 를 서브프로세스로 띄운다. 없으면 advisor 툴이 실패한다. (`PI_ADVISOR_BIN` 으로 경로 교체 가능)

## 설정 (환경변수)

| 변수 | 기본값 | 설명 |
|---|---|---|
| `PI_ADVISOR_BIN` | `claude` | 리뷰어 CLI 실행 파일 경로 |
| `PI_ADVISOR_MODEL` | `fable` | 기본 리뷰어 모델 (`fable` \| `opus`) |
| `PI_ADVISOR_TIMEOUT_MS` | `900000` | 하드 타임아웃 (15분) |
| `PI_ADVISOR_GATE` | (미설정=on) | `0` 이면 edit/write·실패 게이트를 끈다. 툴 자체는 남는다 |

## 툴 파라미터

- `task` (필수) — 답이 필요한 구체적 질문. 리뷰어는 다른 맥락을 전혀 모른다.
- `context` (선택) — 시도한 것, 실패한 것(정확한 에러 문구), 믿는 것과 그 근거.
- `files` (선택) — 결정이 걸린 파일 경로. 내용이 인라인된다. 1~5개만, 덤프 금지.
- `model` (선택) — `fable`(기본, 가장 강함) 또는 `opus`(빠름).

## 비활성화

settings.json 에서 소스별로 끌 수 있다:

```json
{ "source": "git:github.com/<user>/pi-claude-advisor", "extensions": ["-advisor.ts"] }
```

## 라이선스

MIT

## 개발

```bash
npm install
npm run typecheck
npm run pi:dev          # 이 확장만 로드해서 실행
npm run pi:install-local  # 현재 프로젝트에 로컬 설치
```

npm 에 safe-chain(minimum package age) 이 걸려 있으면 최신 `@earendil-works/*` 버전이 숨겨져 설치가 실패한다. 그때는 `npm install --safe-chain-skip-minimum-package-age`.
