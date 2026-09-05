# 홍보 콘텐츠 제작 Harness — Claude Code 진입점

이 저장소는 소규모 사업자의 홍보문구·예약안내·상품소개·이미지 제작을 실제로 수행하는 작업공간이다.
강의/교육용이 아니라 **실제 게시·발송에 쓰이는 콘텐츠**를 만드는 것이 목적이다.

이 파일은 Claude Code가 세션 시작 시 자동으로 읽는 진입점이다. 이 저장소의 공통 규칙은 이 파일이
아니라 `core/PLAYBOOK.md`에 있다. 이 파일은 그 규칙을 복제하지 않고 어디를 읽어야 하는지만 안내한다.

## 세션 시작 시 읽어야 할 파일

1. `core/PLAYBOOK.md` — 정보 우선순위, 점진적 입력수집 원칙, 절대 규칙(Governance), 미확정 정보
   처리, 채널 공통 사실검증 절차, 기본 작업 흐름. **모든 공통 규칙의 SSOT.**
2. `agents/promotion-agent.md` — 콘텐츠 제작 요청을 실제로 수행하는 실행 절차.
3. 사용자 요청의 채널/문서 유형에 맞는 `skills/*/SKILL.md`
   (`blog-promotion`, `facebook-promotion`, `instagram-promotion`, `reservation-message`,
   `product-description`) — `agents/promotion-agent.md`의 라우팅 기준을 따른다.
4. `inputs/business-info-card.md` — 이번 작업 대상 상품·서비스의 사실 SSOT.
5. `knowledge/personal-profile.md` — 운영자(사용자)의 말투·형식 선호.

저장소 구조와 "무엇을 고치려면 어느 파일을 봐야 하는가"는 `ARCHITECTURE.md`를 참고한다.
