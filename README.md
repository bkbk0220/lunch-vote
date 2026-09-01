# 🍽️ 팀 점심 식당 투표 (GitHub Pages 버전)

서버 없이 GitHub Pages(화면) + 별도 데이터 저장소의 `data.json`(투표 데이터)으로 동작하는 팀 투표 사이트.
PC를 꺼도 사이트가 죽지 않으며, 투표 현황은 몇 초 간격으로 모두에게 자동 동기화된다.

## 구조 (저장소 2개)

- **이 저장소 (화면)** — `index.html`을 GitHub Pages로 서빙. 공유 토큰으로는 수정 불가.
- **데이터 저장소 (`lunch-vote-data`)** — 제목/식당/인원/투표/URL을 `data.json` 한 파일에 저장.
  공유 토큰은 이 저장소만 읽기/쓰기 가능 → 토큰이 유출되어도 화면 코드는 못 바꾸고 투표 데이터만 영향.
  - 읽기: 4초 간격 폴링 (ETag 조건부 요청이라 변경 없으면 요청 한도를 소모하지 않음)
  - 쓰기: 최신본을 받아 변경을 적용해 커밋. 동시 수정 충돌(409/422) 시 자동 재시도

## 최초 배포 (관리자 1회)

1. github.com에서 **Public 저장소** 생성 (예: `lunch-vote`)
2. 이 폴더의 파일들을 저장소에 push (또는 웹에서 업로드)
3. 저장소 **Settings → Pages → Branch: `main` / `(root)` → Save**
   - 1~2분 후 `https://<계정명>.github.io/<저장소명>/` 에서 접속 가능
4. **접속 토큰 만들기** (팀원과 공유할 쓰기 권한):
   - github.com **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**
   - Repository access: **Only select repositories** → **데이터 저장소(`lunch-vote-data`)만** 선택
   - Permissions: **Contents → Read and write** (그 외 전부 No access)
   - 만료(Expiration)는 원하는 기간으로 (만료되면 새로 만들어 다시 공유)
5. 팀원에게 **사이트 주소 + 토큰**을 알려준다

## 팀원 사용법

1. 사이트 주소 접속
2. 처음 한 번 토큰 입력 (브라우저에 저장되어 다시 묻지 않음)
3. 끝. 셀 클릭으로 투표.

## 보안 참고

- 토큰은 **데이터 저장소(`lunch-vote-data`) 하나의 파일 읽기/쓰기만** 가능한 최소 권한이므로, 유출되어도 투표 데이터 외에는 영향이 없다 (화면 코드 수정 불가).
- 단, 토큰을 저장소 안에 커밋하면 GitHub가 자동으로 무효화하므로 절대 커밋하지 말 것 (채팅 등으로만 공유).
- Public 저장소이므로 투표 내용 자체는 인터넷에 공개된다.
