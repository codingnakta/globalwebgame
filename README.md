# 신입인데 퇴근하겠습니다 — 프롤로그 프로토타입

PROJECT 404 · 픽셀 아트 스토리형 웹 게임의 프롤로그(버스 → 학교 정문 → 전화) 프로토타입입니다.
서버 없이 정적 HTML 한 장으로 동작합니다.

## 실행

캔버스로 이미지 배경을 투명 처리하기 때문에 `file://`로 열면 안 되고, 정적 서버가 필요합니다.

```bash
python -m http.server 5173 --directory prologue-bus
```

브라우저에서 <http://localhost:5173> 을 엽니다. (VS Code Live Server 등 다른 정적 서버도 가능)

## 조작

| 입력 | 동작 |
|---|---|
| 화면 클릭 / Enter / Space | 타이핑 중이면 즉시 완성, 완료 상태면 다음 대사 |
| 오른쪽 위 🔊 버튼 | 배경음·효과음 음소거 토글 |
| 전화 화면의 거절 / 응답 | 분기 선택 (거절하면 대사 후 다시 전화가 옴) |

브라우저 자동 재생 제한 때문에 소리는 첫 클릭 이후에 시작됩니다.

## 장면 흐름

1. 버스 안 — 도시 배경이 오른쪽→왼쪽으로 흐르고 버스가 덜컹거림, 속마음 대사 3줄
2. 안내 방송 — 소녀가 놀란 표정으로 바뀌고 안내 방송 재생
3. "내려야겠다!" → 암전
4. 학교 정문 → 소녀 뒷모습 등장 → "아… 들어가기 싫어."
5. 벨소리 + "응? 이 아침에 누구지?" → 전화 수신 화면 (거절 / 응답)
6. 응답 → "여보세요?" (여기부터 이어서 작성)

## 구조

```
prologue-bus/
├─ index.html          # 화면·스타일·대사·이벤트 로직 전부
└─ assets/
   ├─ city-street.png          # 버스 창밖 도시 배경
   ├─ bus-girl.png             # 버스 안 소녀 (기본)
   ├─ bus-girl-surprised.png   # 버스 안 소녀 (놀람)
   ├─ school-gate.png          # 학교 정문
   ├─ girl-back.png            # 정문 앞 소녀 뒷모습
   ├─ phone-call.png           # 전화 수신 화면
   ├─ school-hallway.png       # 학교 복도 (아직 미사용)
   ├─ bus-stop.wav             # 정류장 배경음 (루프)
   ├─ bus-announce.mp3         # 버스 안내 방송
   └─ reflection.mp3           # 전화 벨소리
```

## 대사 수정

`index.html`의 `LINES` 배열만 고치면 됩니다.

```js
{ inner: true, text: '속마음 (회색 글씨)' },
{ text: '일반 대사 (흰 글씨)' },
{ event: 'announce', hide: true, text: '' },        // 이벤트 실행, 대화창 숨김
{ ring: true, inner: true, text: '벨소리 울리는 줄' },
{ id: 'x', text: '...', next: 'y' },                 // 다음 클릭에 id가 y인 줄로 점프
{ event: 'girlBack', hide: true, text: '', autoNext: 1200 }, // 1.2초 뒤 자동 진행
```

이벤트 종류: `announce`(놀람+방송), `gate`(암전→정문), `girlBack`(뒷모습 등장), `phone`(전화 화면).

## 연출 조절 (CSS 변수)

| 변수 | 위치 | 의미 |
|---|---|---|
| `--speed` | `:root` | 배경 스크롤 한 바퀴 시간 (작을수록 빠름) |
| `--bob` | `:root` | 버스 흔들림 폭 |
| `--zoom`, `--lift` | `:root` | 버스 전경 확대 배율 / 위로 올리는 양 |
| `--px`, `--r` | `.dlg` | 대화창 테두리 두께 / 모서리 반지름(칸) |

폰트는 한글 픽셀 폰트 Galmuri(jsDelivr CDN)를 쓰며, 오프라인이면 시스템 글꼴로 대체됩니다.
