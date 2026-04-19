# CLAUDE.md — 표정 대장 챌린지 (Face Emoji Master)

## 한 줄 정의
웹캠 + MediaPipe Face Mesh로 5라운드 표정 모방 챌린지 (모든 처리 브라우저 내).

## 라이브 / 저장소
- 🌐 https://face-emoji-master.vercel.app/
- 🐙 https://github.com/tigerjk9/face-emoji-master

## 기술 스택
- 단일 `index.html` (Vanilla JS)
- Tailwind CSS CDN + Pretendard
- MediaPipe Face Mesh (468 landmarks, refineLandmarks=true)
- MediaPipe Camera Utils + getUserMedia

## 핵심 데이터 구조
- `L{}`: Face Mesh 핵심 landmark 인덱스 (입꼬리/입술 위아래/눈 4개점/눈썹)
- `ALL_TARGETS[]`: 5종 표정 (활짝 웃기·입 쩍 벌리기·깜짝 놀라기·한쪽 윙크·눈 감기) + 각각 scorer 함수
- `state{ phase, round, targets, history, bestThisRound, liveScore, ... }`

## 점수 산출 (모두 0~1 정규화)
- **Smile:** mouth_width / face_width 비율 (0.42~0.58)
- **MouthOpen:** vertical_lip / face_height (0.04~0.18)
- **Surprise:** √(mouth_open × eye_open) — AND 조건
- **Wink:** ∛(closed × open × gap) — 비대칭 검출
- **EyesClosed:** 1 − EAR 평균 (0.14~0.26)
- **스무딩:** liveScore = liveScore × 0.5 + raw × 100 × 0.5

## 핵심 결정
- **모든 처리 브라우저 내 완결** — 외부 전송 0건
- **5라운드 5초 제한** — 부스 회전율 고려
- **칭호 시스템** — 5단계 (표정의 신 ~ 신입생)
- **추가 비용 0** — Gemini 호출 없음, MediaPipe만

## 자주 마주치는 이슈
- **얼굴 인식 안 됨:** 조명 부족 / 측면 / 가림 → "조명을 밝게 하고 얼굴 전체가 화면에 들어오게" 안내
- **MediaPipe 모델 로딩 느림:** 첫 진입 시 ~10초, 로딩 스피너 + 안내 문구 노출

## 변경 핵심 이력
- 2026-04-19: 초기 출시 (5라운드, 5종 표정, 글래스모피즘 + 오로라)

## 관련 문서
- [PRD.md](./PRD.md)
