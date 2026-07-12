# DJ Ramyun — ORDER RUSH 🍜🎧 (v2)

808hertz × DJ Ramyun 바이럴 리듬 쿠킹 게임 — **단일 HTML 마스터 프로토타입** (`index.html`).
Three.js 3D 조리 퍼포먼스 + Canvas 2D UI 오버레이 + Web Audio 실시간 신스 (샘플 파일 0개).

## 실행

```bash
python3 -m http.server 8899 --directory .
# → http://localhost:8899
```

## 조작

| 입력 | 동작 |
|---|---|
| 모바일 | 하단 5버튼 터치 (RAM · BUL · JJA · NEO · ▶PLAY) |
| PC | `1` `2` `3` `4` = 맛 패드 / `Space` = PLAY (대체: `D` `F` `J` `K`) |

## v2 핵심 — 테트리스식 논스톱 플로우

- **게임이 절대 멈추지 않음.** 주문이 컨베이어처럼 끝없이 흘러들어오고, BPM이 주문마다 +3씩 상승 (96→180).
- **MISS/오답 = 라이프 -1 + 콤보 리셋**, 화면은 붉은 비네트 + 스크래치음만. 씬 전환 없이 계속 진행.
- **주문 완성 = 논블로킹 서빙 토스트**: 노미스면 PERFECT 서빙(+1000×난이도, ♥+1 회복), 60% 이상이면 일반 서빙(+400), 미만이면 손님 실망.
- **FEVER MODE (콤보 20+)**: 점수 ×2 · 배경/조명 무지개 · LED "FEVER!!" · LP 고속 회전.
- 콤보 스템 누적: 킥 → +하이햇(4) → +스네어(8) → +베이스(14) → 피버 클랩.

## 3D 조리 퍼포먼스 (Three.js)

- 제품 실루엣 그대로의 덱 (원형+모서리), 회전 LP + DJ Ramyun 라벨, 젓가락 톤암, 노브
- 냄비 안 실시간 조리: 맛별 국물 컬러 전환, 면발, 반숙 계란, 파, 끓는 기포, 스팀 파티클
- 도트 LED 디스플레이: 현재 맛 이름 / FEVER!! / GAME OVER (제품 LED 재현)
- 드롭 순간 카메라 펀치 + 스팀 버스트 + 플레이버 라이트 폭발
- CDN 로드 실패 시 2D 덱 폴백 (게임은 항상 동작)

## 튜닝 포인트

| 항목 | 위치 |
|---|---|
| 판정 윈도우/관대함 | `function windows()` — BPM에 따라 자동 스케일 |
| BPM 곡선·노트 수 | `function orderParams(no)` |
| 노트 속도 | `pxPerBeat()` — `SW*0.24` |
| 점수식 | `applyJudge()` — `100 × 판정 × 난이도 × (1+combo/100) × 피버2` |
| 서빙 보너스 | `finishOrder()` |
| 맛별 사운드 | `tapFX()` / `dropFX()` |
| 3D 카메라 | `update3D()` 하단 |

## 디버그

콘솔: `DJ.G`, `DJ.padTap('ramyun'|'buldak'|'jjapa'|'neogury'|'play')`,
`DJ.forceState('attract'|'game'|'over')`, `DJ.step()`, `DJ.T3`(3D).
