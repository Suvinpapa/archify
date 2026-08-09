# Archify Memory

## 현재 상태
```yaml
status: active
last_agent: claude-desktop
last_branch: main
last_session: 2026-08-09T09:19+09:00
session_count: 1
```

## 프로젝트 개요
코드베이스/시스템 설명을 검증된 인터랙티브 HTML 시스템 맵으로 만드는 에이전트 스킬 (v2.13.0).

## 기술 스택
- Node.js >= 18, ESM (`"type": "module"`), 런타임 의존성 없음 (devDependency는 ajv뿐)
- 스킬 본체: `archify/` (SKILL.md + bin/renderers/schemas/examples/references/recipes/delta)
- CLI: `archify/bin/archify.mjs` — render / compare / deliver / preview / validate / inspect / check / guide / doctor / demo
- 다이어그램 5종: architecture · workflow · sequence · dataflow · lifecycle
- 산출물: 자체 완결형 HTML (inline SVG) + PNG/SVG/WebM/1200×630 share card

## 아키텍처 결정
- 타입드 JSON IR을 단일 소스로: 렌더러는 IR만 소비, 스키마(`archify/schemas/`)가 계약
- 정적 출력이 기본, 모션은 옵트인 (데모/발표 요청 시에만)
- 자동 Mermaid 파싱·범용 오토레이아웃·호스팅 공유·WYSIWYG 편집은 스코프 밖 (명시적 비목표)
- Architecture Delta는 정확 ID 매칭 기반 결정론적 비교 (added/removed/changed/moved/rerouted)

## 코드 컨벤션
- 표준 에이전트 ID: claude-code / claude-desktop / claude-web / claude-mobile / gemini-cli / antigravity / opencode / other.
- 검증 명령: `cd archify && npm test` (validator 체크 + release identity + golden + 전체 테스트)
- 릴리스 아이덴티티 체크(`scripts/check-release-identity.mjs`)가 버전 표기 불일치를 차단 —
  버전 올릴 때 package.json / SKILL.md metadata.version / README 배지를 함께 갱신

## 알려진 제약사항
- `archify/examples/`가 3.2MB로 스킬 용량 대부분 차지 (전체 5.7MB, 140파일)
- Claude.ai 업로드 설치는 샌드박스 Node.js 가용성에 의존 — 렌더러 실행 보장 안 됨
- Raven은 `npx skills` 스위처 대상 아님 → `archify.zip` 수동 추출만 지원
- workflow 컬럼 x는 고정 `[88, 220, 300, 430, 500, 625]`, `nodeW` 92 (`renderers/workflow/render-workflow.mjs:48`).
  같은 레인에서 col 1→2(80px), 3→4(70px)는 노드 폭보다 좁아 항상 겹침 — 밴드 {0,1} {2,3} {4,5} 안에서만 인접 배치 가능하고 밴드를 넘으려면 레인을 바꿔야 한다. `meta.viewBox`를 키워도 컬럼 간격은 변하지 않는다
- 라벨 마스크는 CJK를 2유닛으로 계산(`6.5px × 유닛 + 13px`) → 한글 라벨·서브라벨은 5자 이내가 안전
- 정적 이미지는 뷰어의 Export(PNG/SVG)만 신뢰할 수 있다 — CLI에는 SVG export 서브커맨드가 없음

## 로컬 설치 위치 (2026-08-09, 레포에서 직접 복사)
- Claude Code: `~/.claude/skills/archify`
- opencode: `~/.config/opencode/skills/archify`
- Antigravity: `~/.gemini/config/skills/archify` (글로벌 커스터마이제이션 루트, README 미기재 대상)
- 셋 다 140파일 일치 확인 + 설치본으로 `render architecture` 스모크 통과

## 하지 말 것
- `npx skills add`와 수동 복사를 섞지 말 것: 수동 복사본은 `.skill-lock.json`에 없어 `skills update`가 조용히 건너뜀
- `MEMORY_AGENT_ID` 환경변수를 에이전트 판별에 쓰지 말 것: 로컬 설정이 항상 `claude-code`를 주입해 Desktop/클라우드 세션이 오기록됨
- 생성된 HTML에서 `<svg>` 블록만 떼어내 독립 SVG를 만들지 말 것: 다이어그램 스타일이 래퍼 요소 기준으로 스코프돼 있어 선택자가 매칭되지 않고 검정 도형만 남는다. 루트에 `data-theme="light"`를 붙여도 해결되지 않으며, 태그 균형 검사만으로는 이 실패를 잡을 수 없다
- 구조 검사(태그 균형·이스케이프)만 통과한 산출물을 렌더 성공으로 보고하지 말 것: 실제 표시는 눈으로 확인해야 한다

## 세션 로그
| session_end | agent | branch | status | summary |
|---|---|---|---|---|
| 2026-08-09T09:19+09:00 | claude-desktop | main | active | archify 스킬을 Claude Code·opencode·Antigravity에 설치 및 렌더 스모크 검증, MEMORY.md 신규 생성. LCMR200 매뉴얼용 workflow 샘플 1장 생성(showcase 9/9) — 수동 SVG 추출은 실패, 매뉴얼 적용은 보류 |
