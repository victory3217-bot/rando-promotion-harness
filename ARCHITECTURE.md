# ARCHITECTURE

이 문서는 저장소의 폴더 구조, 각 파일의 책임, 무엇을 고치려면 어디를 봐야 하는지, 그리고 변경관리
절차를 정의한다.

## 설계 원칙

**Claude Code용 Harness와 GPT/Codex용 Harness를 두 벌 만들지 않는다.** 하나의 공통 Harness를
두 플랫폼이 공유한다.

- 공통 규칙(Input 원칙, 정보 우선순위, Governance, `[확인필요]` 처리 원칙, 채널 공통 사실검증,
  기본 작업 흐름)은 `core/PLAYBOOK.md` 한 곳에만 존재한다.
- `CLAUDE.md`와 `AGENTS.md`는 각 플랫폼이 세션 시작 시 자동으로 읽는 **얇은 진입점**일 뿐이며,
  공통 규칙을 복제하지 않고 `core/PLAYBOOK.md`를 참조만 한다.
- `agents/promotion-agent.md`, `skills/*/SKILL.md`, `knowledge/`는 플랫폼에 관계없이 동일한
  원본을 그대로 사용한다.
- 이 Harness는 고정된 상품 정보 파일을 두지 않는다. 이번 작업의 사실 원본은 사용자가 현재
  대화에서 서술형으로 제공하는 상품·서비스 설명이며, 이 원칙은 `core/PLAYBOOK.md`의 "Input
  원칙"에서 관리한다.
- 규칙이 바뀌면 `core/PLAYBOOK.md` 한 파일만 수정한다. 두 진입점이 서로 어긋나는 상태는 구조적으로
  발생하지 않아야 한다.

## 폴더 트리

```
rando-promotion-harness/
├── CLAUDE.md                  # Claude Code 진입점 (얇음, 공통 규칙 미포함)
├── AGENTS.md                  # GPT/Codex 진입점 (얇음, 공통 규칙 미포함)
├── ARCHITECTURE.md            # 이 문서
├── core/
│   └── PLAYBOOK.md            # 공통 규칙 SSOT
├── agents/
│   └── promotion-agent.md     # 콘텐츠 제작 실행 절차 + 유형별 skill 라우팅 기준
├── skills/
│   ├── blog-promotion/SKILL.md
│   ├── facebook-promotion/SKILL.md
│   ├── instagram-promotion/SKILL.md
│   ├── reservation-message/SKILL.md
│   └── product-description/SKILL.md
├── knowledge/
│   └── personal-profile.md    # 운영자 말투·형식 선호
└── legacy/                    # 원본 교육자료 13개, 보존용 — 수정하지 않음
```

## 파일/폴더 책임

| 경로 | 책임 |
|---|---|
| `CLAUDE.md` | Claude Code 전용 진입점. 세션 시작 시 읽을 파일 목록만 안내. |
| `AGENTS.md` | GPT/Codex 전용 진입점. 세션 시작 시 읽을 파일 목록 + Codex는 Skill 자동탐색이 없다는 점 안내. |
| `core/PLAYBOOK.md` | 공통 규칙 SSOT. Input 원칙, 정보 우선순위, Governance, `[확인필요]` 처리 원칙, 채널 공통 사실검증, 기본 작업 흐름. |
| `agents/promotion-agent.md` | 콘텐츠 제작 요청의 실행 절차. 요청 유형에 따라 어떤 skill을 읽을지의 라우팅 기준. |
| `skills/*/SKILL.md` | 채널/문서 유형별 작성 형식·표현 규칙·이미지 가이드. |
| `knowledge/personal-profile.md` | 운영자(사용자)의 말투·형식 선호 데이터. |
| `legacy/` | 원본 교육자료 보존. 실제 작업 흐름에서 사용하지 않음. |

## 무엇을 수정하려면 어느 파일을 봐야 하는가

| 바꾸고 싶은 것 | 수정할 파일 |
|---|---|
| 절대 규칙(과장 금지, 개인정보 금지 등)을 바꾸고 싶다 | `core/PLAYBOOK.md` |
| 정보 우선순위나 입력수집 순서를 바꾸고 싶다 | `core/PLAYBOOK.md` |
| 미확정(`[확인필요]`) 정보를 노출하는 기준을 바꾸고 싶다 | `core/PLAYBOOK.md` |
| 결과물 마지막에 붙는 사실검증 절차를 바꾸고 싶다 | `core/PLAYBOOK.md` |
| 콘텐츠 제작의 전체 실행 순서(몇 단계로 진행하는지)를 바꾸고 싶다 | `agents/promotion-agent.md` |
| 어떤 요청이 어떤 skill로 라우팅되는지 바꾸고 싶다 | `agents/promotion-agent.md` |
| 특정 채널(블로그/페이스북/인스타그램/예약안내/상품소개)의 글 구조·표현 규칙을 바꾸고 싶다 | 해당 `skills/<채널>/SKILL.md` |
| Claude Code에서만 다르게 동작해야 한다 | `CLAUDE.md` |
| GPT/Codex에서만 다르게 동작해야 한다 | `AGENTS.md` |
| 이번 작업 대상 상품·서비스의 가격·일정·환불 등 사실정보를 바꾸고 싶다 | 파일이 아니라 대화 중 서술형으로 다시 설명한다 (`core/PLAYBOOK.md`의 "Input 원칙") |
| 운영자의 말투·형식 선호를 바꾸고 싶다 | `knowledge/personal-profile.md` |
| 저장소 구조 자체나 변경관리 절차를 바꾸고 싶다 | `ARCHITECTURE.md` |

## 데이터·실행 흐름

```
Input (사용자가 현재 대화에서 제공하는 서술형 상품·서비스 설명 + knowledge/personal-profile.md)
   │
   ▼
PLAYBOOK (core/PLAYBOOK.md) — Input 원칙·정보 우선순위·Governance 적용
   │
   ▼
Agent (agents/promotion-agent.md) — 요청 유형 판단, 필요한 skill로 라우팅
   │
   ▼
Skill (skills/*/SKILL.md) — 채널별 작성 형식으로 초안 생성
   │
   ▼
Validation — PLAYBOOK의 "채널 공통 사실검증 절차" 적용
   │
   ▼
Output — 최종 문안 + [확인필요] 목록 + 사실확인 체크 + 다음 수정 제안
```

Claude Code와 GPT/Codex는 이 흐름 전체를 동일하게 따른다. 차이는 오직 진입점(`CLAUDE.md` vs
`AGENTS.md`)이 이 흐름의 시작 파일들을 읽는 방식(자동 로드 vs 수동으로 열어 읽기)에만 있다.

## 변경관리 절차

이 저장소의 어떤 파일을 수정하든 아래 순서를 따른다.

1. **영향 파일 식별** — 바꾸려는 내용이 `core/PLAYBOOK.md`(공통 규칙)인지, `agents/`·`skills/`(실행/표현)인지,
   `knowledge/`(데이터)인지, `CLAUDE.md`·`AGENTS.md`(진입점)인지 먼저 특정하고, 이 표
   ("무엇을 수정하려면 어느 파일을 봐야 하는가")로 실제 영향 범위를 확인한다.
2. **사용자 승인** — 어떤 파일을 왜 바꾸는지 먼저 보고하고 승인을 받는다. 승인 없이 파일을 만들거나
   고치지 않는다.
3. **수정** — 승인된 범위 내에서만 수정한다. 공통 규칙은 `core/PLAYBOOK.md`에서만 고치고, 진입점
   파일에 규칙을 복제하지 않는다.
4. **테스트** — 실제 요청 시나리오(채널별 콘텐츠 생성 등)로 변경 사항이 의도대로 동작하는지 확인한다.
5. **git diff** — 변경 내용을 diff로 검토해 의도치 않은 변경이 없는지 확인한다.
6. **검증** — `core/PLAYBOOK.md`가 여전히 공통 규칙의 유일한 원본인지, `CLAUDE.md`/`AGENTS.md`가
   규칙을 복제하지 않았는지 확인한다.
7. **사용자 승인** — 최종 변경 내용을 보고하고 commit 여부를 승인받는다.
8. **commit** — 승인 후에만 commit한다.

### 추가 원칙

- `legacy/`는 어떤 이유로도 수정하지 않는다. 원본 교육자료 보존 전용 폴더다.
- 공통 규칙 변경이 특정 채널에만 영향을 준다면, 관련 `skills/*/SKILL.md`도 함께 검토해 표현이
  `core/PLAYBOOK.md`와 모순되지 않는지 확인한다.
