# Google Form → Google Sheet → 응답자 이메일 회신 운영안

## 권장 방식

### A안. 수업용 기본형 — 코드 없이

1. Google Form을 만든다.
2. **이메일 주소 수집**을 켠다.
3. 응답자가 제출한 내용을 받을 수 있도록 **응답 사본 전송**을 `항상`으로 설정한다.
4. Form의 `응답` 메뉴에서 응답 저장 위치를 Google Sheet로 연결한다.
5. 설문 응답 링크를 QR코드로 만들어 수업 화면과 인쇄물에 넣는다.

이 방식이면 수강생은 자기 이메일로 제출내용의 사본을 받고, 강사는 연결된 Google Sheet에서 전체 응답을 관리할 수 있다.

## B안. 권장 확장형 — 제출 직후 '사업정보카드' 형식으로 자동 이메일

Google Forms의 기본 응답 사본은 설문 응답 형태다.  
아래 Apps Script를 연결된 Google Sheet에 넣으면 제출된 행을 읽어 **사업정보카드 형태로 다시 정리하여 이메일**로 보낼 수 있다.

### 준비

- Form 첫 질문 또는 이메일 수집 기능으로 이메일 주소 확보
- Form 응답이 연결된 Google Sheet
- 응답 시트의 헤더 이름을 코드의 항목명과 맞춤

### 설치

1. 응답 Google Sheet 열기
2. `확장 프로그램 → Apps Script`
3. 아래 코드를 붙여넣기
4. 저장
5. Apps Script 왼쪽 `트리거`
6. `트리거 추가`
7. 실행 함수: `sendBusinessCardEmail`
8. 이벤트 소스: `스프레드시트에서`
9. 이벤트 유형: `양식 제출 시`
10. 저장 후 권한 승인
11. 테스트 응답 1건 제출

### 샘플 코드

```javascript
function sendBusinessCardEmail(e) {
  const nv = e.namedValues;

  // 아래 헤더명은 실제 Google Form 질문 제목과 동일하게 맞추세요.
  const email = first(nv['이메일 주소']) || first(nv['Email Address']);
  if (!email) return;

  const fields = [
    '사업장명',
    '지역',
    '상품명',
    '주요고객',
    '고객이 이 상품을 찾는 상황/문제',
    '체험·서비스 내용',
    '고객이 실제로 하게 되는 활동',
    '고객이 얻는 경험/결과',
    '다른 곳과 다른 점 한 줄',
    '실제 프로그램 순서',
    '소요시간',
    '가격',
    '가격에 포함되는 것',
    '운영일',
    '운영시간',
    '예약방법',
    '장소/오시는 길',
    '주차 안내',
    '준비물',
    '우천 시 운영',
    '취소·변경·환불 규정',
    '문의 연락처'
  ];

  const lines = [];
  lines.push('# 내 사업 정보카드');
  lines.push('');
  fields.forEach((key, idx) => {
    const value = first(nv[key]) || '[확인필요]';
    lines.push(`${idx + 1}. ${key}: ${value}`);
  });

  lines.push('');
  lines.push('※ 이 카드는 AI 홍보·안내 실습의 사실 원본입니다.');
  lines.push('※ 가격·일정·환불·상품조건은 실제 운영정보와 다시 확인하세요.');
  lines.push('※ 고객 개인정보는 AI에 입력하지 마세요.');

  MailApp.sendEmail({
    to: email,
    subject: '[AI 홍보·안내 실습] 내 사업 정보카드',
    body: lines.join('\n')
  });
}

function first(value) {
  if (!value) return '';
  return Array.isArray(value) ? String(value[0] || '').trim() : String(value).trim();
}
```

## Google Form 질문 구성 권장안

### 필수 12개
- 이메일 주소
- 사업장명
- 지역
- 상품명
- 주요고객
- 체험·서비스 내용
- 고객이 실제로 하게 되는 활동
- 다른 곳과 다른 점 한 줄
- 소요시간
- 가격
- 운영일/운영시간
- 예약방법

### 선택 또는 `[확인필요]` 허용
- 고객이 얻는 경험/결과
- 실제 프로그램 순서
- 가격 포함사항
- 장소/오시는 길
- 주차 안내
- 준비물
- 우천 시 운영
- 취소·변경·환불 규정
- 문의 연락처

## 교육 운영 팁

- 강의 시작 전에 QR과 자동회신을 반드시 테스트한다.
- 수강생이 응답 완료 후 자신의 이메일에 카드가 왔는지 확인하게 한다.
- 이메일이 바로 도착하지 않으면 스팸함도 확인한다.
- 자동메일용 Apps Script는 강사가 미리 설치한다. 수강생에게 코딩시키지 않는다.
- 수강생이 받은 이메일의 정보카드를 복사해 프로젝트의 `02_Business_Info_Card.md`로 저장하게 한다.
