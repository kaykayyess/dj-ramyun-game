# DJ Ramyun — 전세계 실시간 랭킹 설정 (Firebase)

웹(GitHub Pages)·iOS 앱·Android 앱이 **같은 코드**라 리더보드 하나를 셋이 공유한다.
연동 코드는 이미 게임에 들어가 있고, 아래 **키 2개만** 넣으면 켜진다.

---

## Part 1 — 직접 하실 것 (약 10분, 구글 계정 필요)

1. **console.firebase.google.com** 접속 → 구글 로그인
2. **프로젝트 추가** → 이름 `dj-ramyun` → (Google Analytics는 꺼도 됨) → 만들기
3. 왼쪽 메뉴 **빌드 → Firestore Database** → **데이터베이스 만들기**
   - 위치: **asia-northeast3 (서울)** 선택 (한국 유저에 가장 빠름)
   - **프로덕션 모드**로 시작 (규칙은 아래에서 붙여넣음)
4. Firestore의 **규칙(Rules)** 탭 → 내용 전부 지우고 아래를 붙여넣기 → **게시**

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function ok() {
      let d = request.resource.data;
      return d.keys().hasOnly(['n','s','t'])
        && d.n is string && d.n.size() <= 16
        && d.s is int && d.s >= 0 && d.s <= 100000000
        && d.t is int;
    }
    match /scores/{tag} {
      allow read: if true;
      allow create: if ok();
      allow update: if ok() && request.resource.data.s >= resource.data.s;  // 점수는 오르기만
      allow delete: if false;
    }
  }
}
```

5. **키 2개 복사**: ⚙️(설정) → **프로젝트 설정** → **일반** 탭
   - **프로젝트 ID** (projectId) — 예: `dj-ramyun-1a2b3`
   - 아래로 스크롤 → **내 앱**에서 `</>`(웹) 아이콘으로 앱 추가 → 등록하면
     `firebaseConfig`에 **apiKey**(예: `AIzaSy...`)가 보임. 이것도 복사.

## Part 2 — 저에게 주실 것

위 **projectId** 와 **apiKey** 2개만 채팅으로 주시면, 코드에 넣고 배포합니다.
그 순간부터 웹에서 실시간 전세계 TOP 10이 뜨고, 나중에 앱스토어/플레이스토어 앱도 **같은 랭킹**을 공유합니다.

---

## 알아두실 점

- **apiKey는 공개돼도 안전** — Firebase 웹 키는 비밀번호가 아니라 프로젝트 식별자다.
  보안은 위 **규칙**이 담당한다 (점수는 숫자·범위 검증 + 오르기만 가능).
- **무료** — Firestore 무료 티어: 읽기 5만/일, 쓰기 2만/일, 저장 1GB.
  점수 전송은 **개인 최고점 갱신 때만** 1회라 아주 여유롭다.
  바이럴로 이걸 넘기면 Blaze(종량제)로 전환하면 되고 비용도 미미하다.
- **상품 검증** — 클라이언트가 점수를 올리는 구조라, 마음먹으면 가짜 고득점 전송은 가능하다.
  파운더스 에디션 같은 **실물 상품은 상위 입상자에게 플레이 영상 제출을 받아 검증**하는 걸 권장.
  (더 단단히 막으려면 나중에 App Check 추가 가능)
- **닉네임** — 지금은 자동 태그(`DJ-A3F9`)로 표시된다. 원하면 첫 플레이 때 **닉네임 입력**을
  넣어서 리더보드에 실명이 뜨게 할 수 있다 (바이럴·상품 수령에 유리). 말씀만 주세요.

## 연동 지점 (참고)

`index.html` 상단 `const FIREBASE={projectId:'',apiKey:''};` — 여기 2개를 채우면 활성화.
비어 있으면 브랜드풍 더미 차트(808 KING 등)로 자동 폴백. 관련 함수: `lbSync()`(읽기)·`lbSubmit()`(쓰기)·`lbName()`/`lbTag()`.
