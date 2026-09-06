# rando-promotion-harness

소규모 사업자의 홍보문구·예약안내·상품소개·이미지 프롬프트를 실제로 만드는 데 쓰는 Harness다.
강의/교육용 예시가 아니라 **실제 게시·발송에 쓰이는 콘텐츠**를 만드는 것이 목적이다.

## 이 저장소가 하는 일

- 블로그·페이스북·인스타그램 홍보문구
- 예약안내문
- 상품소개글
- 채널별 이미지 프롬프트

를 만들고, 매 결과물마다 가격·일정·환불조건 같은 사실을 검증한 뒤 `[확인필요]` 목록과 함께 내놓는다.
AI는 초안을 만들고 사실을 점검하는 역할까지만 하고, 최종 확인과 게시 결정은 항상 사용자(사업자) 몫이다.

## Claude Code와 GPT/Codex가 함께 쓰는 하나의 Harness

이 저장소는 **Claude Code용과 GPT/Codex용을 따로 만들지 않는다.** 공통 규칙은 `core/PLAYBOOK.md`
한 곳에만 있고, 두 플랫폼은 각자의 얇은 진입점(`CLAUDE.md`, `AGENTS.md`)으로 그 규칙을 읽어 동일하게
동작한다. 어느 플랫폼에서 열어도 결과물의 품질과 규칙은 같다.

## 구조

```
rando-promotion-harness/
├── README.md               # 이 문서
├── LICENSE                 # MIT License
├── CLAUDE.md               # Claude Code 진입점 (얇음)
├── AGENTS.md               # GPT/Codex 진입점 (얇음)
├── ARCHITECTURE.md         # 폴더/파일 책임, 변경관리 절차
├── core/
│   └── PLAYBOOK.md         # 공통 규칙 SSOT (Input 원칙, Governance, 사실검증 절차 등)
├── agents/
│   └── promotion-agent.md  # 콘텐츠 제작 실행 절차 + Skill 라우팅 기준
├── skills/                 # 채널/문서 유형별 작성 가이드
├── knowledge/
│   └── personal-profile.md # 운영자의 말투·형식 선호
└── legacy/                 # 원본 교육자료 보존용 (실제 작업 흐름에서는 사용하지 않음)
```

각 파일의 정확한 책임과 "무엇을 고치려면 어디를 봐야 하는가"는 [`ARCHITECTURE.md`](./ARCHITECTURE.md)에
정리되어 있다.

## Input 방식 — 고정 양식 대신 서술형 대화

이 Harness는 고정된 상품 정보 파일이나 입력 체크리스트를 쓰지 않는다. **이번 작업의 사실 원본은
사용자가 지금 대화에서 서술형으로 설명하는 상품·서비스 내용이다.**

- 사용자가 명시적으로 말한 사실만 사용하고, 말하지 않은 가격·일정·조건은 추측하지 않는다.
- 빠진 정보가 있어도 콘텐츠가 성립하면 묻지 않고 생략한다. 콘텐츠 자체가 성립하지 않을 때만 질문한다.
- 한 대화에 여러 상품이 섞여 있으면, 이번 요청의 대상 상품만 사실 원본으로 쓰고 다른 상품 정보와
  섞지 않는다.

자세한 규칙은 `core/PLAYBOOK.md`의 "Input 원칙"과 "`[확인필요]` 처리 원칙"을 참고한다.

## 설치 / 사용 방법

별도 빌드나 의존성 설치가 필요 없다. 저장소를 clone한 뒤 그 폴더에서 Claude Code 또는 GPT/Codex를
열면 된다.

```
git clone https://github.com/victory3217-bot/rando-promotion-harness.git
cd rando-promotion-harness
```

### Claude Code에서 실행

Claude Code가 세션 시작 시 `CLAUDE.md`를 자동으로 읽는다. 이어서 `core/PLAYBOOK.md`(공통 규칙),
`agents/promotion-agent.md`(실행 절차), 요청 유형에 맞는 `skills/*/SKILL.md`, `knowledge/personal-profile.md`
순으로 자연스럽게 이어서 읽고 작업을 진행한다. 별도 명령 없이 홍보 콘텐츠 제작을 요청하면 된다.

### GPT/Codex에서 실행

GPT/Codex는 `AGENTS.md`를 진입점으로 사용한다. 루트의 `skills/*/SKILL.md`는 Claude Code와
GPT/Codex 어느 쪽도 자동으로 탐색·매칭하지 않으므로, `agents/promotion-agent.md`의 라우팅
기준에 따라 필요한 Skill 파일을 **직접 열어 읽은 뒤** 작업한다. 이 방식은 두 플랫폼에서 동일하다.

## 현재 지원 Skill

| Skill | 용도 |
|---|---|
| `skills/blog-promotion` | 블로그 홍보글 + 이미지 프롬프트 |
| `skills/facebook-promotion` | 페이스북 게시글 + 이미지 프롬프트 |
| `skills/instagram-promotion` | 인스타그램 게시글 + 이미지 프롬프트 |
| `skills/reservation-message` | 예약안내문(운영문서) |
| `skills/product-description` | 상품소개글 |

## 현재 한계

- 이미지를 직접 생성하지 않는다 — 이미지 프롬프트까지만 만들고, 실제 생성은 별도 이미지 도구로 사용자가 진행해야 한다.
- 고정 데이터베이스나 RAG가 없다 — 사실 정보는 현재 대화 범위 안에서만 유지되며, 세션이 끝나면 다시 서술형으로 설명해야 한다.
- 완전히 채널 독립적이지 않다 — Skill 구조 일부(예: 예약안내문의 필수 항목, 상품소개글의 프로그램 순서)는 대면·체험형 서비스를 염두에 두고 만들어져 있어, 무형/디지털 상품에는 항목을 취사선택해서 써야 한다.
- 한국어 중심으로 설계되어 있다 — 규칙과 결과물 형식이 한국어 시장 관행(과장 표현 금지 목록 등)에 맞춰져 있다.
- 게시·발송은 사용자 책임이다 — AI는 초안 작성과 사실검증까지만 하고, 최종 확인과 실제 게시는 항상 사람이 한다.

---

자세한 설계 원칙, 폴더별 책임, 변경관리 절차는 [`ARCHITECTURE.md`](./ARCHITECTURE.md)를 참고한다.
