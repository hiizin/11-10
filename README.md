# NEON IDOL : DEBUT.EXE — v2.2

AI 아이돌을 트레이닝해서 스탯을 키우고, **서바이벌 스테이지 5단계**를 스탯 대결로 돌파해 데뷔시키는 웹 게임입니다.
카메라(손 인식)로 **댄스**, 마이크(음정 인식)로 **보컬**, 손가락 모양으로 **작곡** 트레이닝을 합니다.

v2.0은 단일 HTML 파일(11MB)을 **기능별 모듈 프로젝트**로 재구성하고, 디자인을 통일하고,
**헤어 / 의상 / 눈 파츠를 데이터만으로 추가할 수 있는 캐릭터 시스템**을 만든 버전입니다.
게임의 플레이 방식과 진행 규칙은 그대로이며, 기존 저장 데이터를 그대로 불러올 수 있습니다.

---

## 1. 실행 방법

### 가장 간단한 방법 : `index.html` 더블클릭
압축을 풀고 **`index.html` 을 더블클릭**하면 바로 실행됩니다. 설치나 서버가 필요 없고, 인터넷이 없어도 됩니다.

- 권장 브라우저 : **Chrome / Edge** 최신 버전.
- 카메라·마이크 권한을 물으면 *허용*을 누르세요.
- 댄스/작곡을 처음 시작할 때 손 인식 모델(약 20MB)을 읽느라 몇 초 걸립니다.
- 저장 데이터는 브라우저에 보관됩니다. **더블클릭 실행과 로컬 서버 실행은 저장이 따로 관리**됩니다(브라우저가 주소별로 저장소를 나누기 때문).

> 더블클릭 실행은 `offline/` 폴더의 파일을 사용합니다. 브라우저가 `file://` 에서 ES Module·fetch·이미지 픽셀 읽기를
> 막기 때문에, 코드와 리소스를 일반 스크립트로 미리 묶어 둔 것입니다. `index.html` 이 실행 방식을 자동으로 고릅니다.

### 코드를 수정한 뒤 : 다시 묶기 (`npm run build`)
`js/` 코드나 `assets/characters/` 파츠를 바꿨다면, 더블클릭 실행에 반영하려면 한 번 다시 묶어야 합니다.
[Node.js](https://nodejs.org) 가 필요합니다.

```bash
npm install      # 처음 한 번 (esbuild 설치)
npm run build    # offline/ 폴더 다시 생성
```

### 개발할 때 추천 : 로컬 서버
로컬 서버로 열면 `offline/` 대신 `js/` 소스를 바로 쓰므로, **빌드 없이 수정 즉시 새로고침으로 확인**할 수 있습니다.

| 방법 | 명령 | 접속 주소 |
|---|---|---|
| Python 3 | `python -m http.server 8000` | http://localhost:8000 |
| Node.js | `npx serve .` | 표시되는 주소 |
| VS Code | 확장 **Live Server** → `index.html` 우클릭 → *Open with Live Server* | 자동으로 열림 |

### 공통
- GPU 가속이 없는 환경(가상 머신 등)에서는 3D 배경 대신 가벼운 2D 배경이 자동으로 쓰입니다. 강제로 3D를 켜려면 주소 뒤에 `?forcegl` 을 붙이세요.

---

## 게임 진행 (v2.2)

- 메뉴에서 **보컬 / 댄스 / 작곡 트레이닝**을 하면 스탯(VOCAL / DANCE / CHARM)과 팬이 오르고 주차가 1주 지납니다.
- 메인 화면의 **서바이벌 스테이지** 버튼을 누르면 현재 단계의 라이벌과 **스탯 대결**을 합니다 (리듬게임 없음).
  - 3라운드(VOCAL → DANCE → CHARM) : 내 스탯이 라이벌 스탯 이상이면 그 라운드 승리.
  - 2라운드 이상 이기면 클리어 → 팬 보상, 다음 단계 해금. 지면 스탯을 더 올려서 언제든 다시 도전.
  - 5단계 : 예선 → 1차 평가 → 2차 평가 → 준결승 → 데뷔 결승. 단계가 올라갈수록 필요 스탯이 높아지며, 결승 승리 시 데뷔 엔딩.
  - 도전 전에 필요 스탯과 부족한 양을 보여 주고, 지금 이길 수 있으면 메인 화면 버튼이 반짝입니다.

---

## 2. 폴더 구조

```text
NeonIdol/
├── index.html                  마크업만 (원본 요소 ID 유지, onclick 대신 data-action)
├── css/
│   ├── reset.css               브라우저 기본값 정리
│   ├── variables.css           디자인 토큰 : 색·글꼴·간격·모서리·그림자·모션 ← 디자인 변경은 여기부터
│   ├── typography.css          글자 규칙 (Pretendard + Orbitron)
│   ├── layout.css              배경 레이어, 화면(section) 골격, 부트 화면
│   ├── components.css          버튼(.btn)·카드·칩·모달·토스트 등 공용 부품
│   ├── animations.css          키프레임, 화면 전환, 와이프, 판정 연출, 파티클
│   ├── responsive.css          화면 크기별 조정, 밤 시간대 가독성
│   └── screens/                화면별 스타일
│       ├── title.css  hub.css  songselect.css  training.css
│       └── compose.css  result.css  modals.css
├── js/
│   ├── main.js                 진입점 : 초기화 순서 관리 + data-action 동작표
│   ├── core/
│   │   ├── config.js           설정값 (저장 키, 경로, 볼륨, 연출 옵션)
│   │   ├── state.js            게임 상태 S 의 유일한 보관소 (store.S)
│   │   ├── screenManager.js    화면 전환 go(id)
│   │   ├── eventManager.js     모듈 간 이벤트 버스 (on / emit)
│   │   └── dom.js              $, $$, clamp, mb(시드 난수) 등 유틸
│   ├── character/              ★ 캐릭터 시스템 (게임 진행 코드와 분리)
│   │   ├── characterManager.js        외부에서 쓰는 유일한 출입구
│   │   ├── characterPartsData.js      manifest/part.json 로드·검증 → 레지스트리
│   │   ├── characterRenderer.js       레이어 로드·색상 적용·그리기·뷰 관리
│   │   ├── characterAnimation.js      호흡·시선·스프링·반응 + 부착 방식(디포머) 레지스트리
│   │   └── characterCustomization.js  파츠 변경 창 (헤어/의상/눈 탭, 미리보기, 저장/되돌리기)
│   ├── gameplay/
│   │   ├── gameProgress.js     새 게임·주차·레슨 결과·엔딩 (진행 규칙)
│   │   ├── survivalRules.js    서바이벌 판정 계산 (순수 함수)
│   │   ├── survivalStage.js    서바이벌 스테이지 화면 (스탯 대결 5단계)
│   │   ├── songSelect.js       곡 선택 디스크
│   │   ├── danceTraining.js    댄스 (손 인식 리듬 게임)
│   │   ├── vocalTraining.js    보컬 (마이크 음정)
│   │   ├── composeTraining.js  작곡 (손가락 모양 → 음, 녹음·투고)
│   │   └── story.js            스토리 창
│   ├── systems/
│   │   ├── assetLoader.js      리소스 읽기 (서버 실행 = fetch / 더블클릭 실행 = offline 데이터)
│   │   ├── saveSystem.js       localStorage 저장/불러오기 + 마이그레이션
│   │   ├── audioSystem.js      AudioContext, 합성 효과음, BGM 페이드/덕킹
│   │   ├── songEngine.js       MP3 로드, 채보 생성, 투고곡 재생
│   │   ├── songLibrary.js      곡 목록 (기본곡 + 투고곡 저장)
│   │   ├── voiceSynth.js       포먼트 보컬 합성
│   │   ├── handTracking.js     MediaPipe Hands 래퍼
│   │   ├── mediaDevices.js     카메라/마이크 획득
│   │   ├── pause.js            트레이닝 일시정지 (GP)
│   │   └── dayCycle.js         5분 = 하루 하늘 연출
│   ├── scene/
│   │   ├── backgroundScene.js  3D 무대 배경 (Three.js)
│   │   └── fallbackBackground.js  2D 대체 배경
│   ├── ui/
│   │   ├── uiManager.js        허브 HUD·레이더·팬 수·말풍선·토스트·창
│   │   ├── buttonManager.js    data-action 클릭 위임, 버튼 물결·클릭음
│   │   └── transitionManager.js 와이프 전환, 판정 피드백, 파티클
│   ├── data/
│   │   ├── gameData.js         서바이벌 5단계·난이도·등급·대사
│   │   ├── songData.js         기본 곡 목록
│   │   ├── storyData.js        스토리 챕터 + 해금 조건
│   │   └── songAnalysis.js     MP3 곡 박자/멜로디 분석 데이터 (자동 생성)
│   └── vendor/three.module.min.js   Three.js r161
├── libs/mediapipe/hands/       MediaPipe Hands 0.4 (모델·wasm)
├── assets/
│   ├── characters/             ★ 캐릭터 파츠 (manifest.json + 슬롯별 폴더)
│   │   ├── manifest.json
│   │   ├── base/character-base/   몸 + 리그 기준좌표
│   │   ├── hair/hair-001/   outfits/outfit-001/   eyes/eyes-001/
│   │   └── _template/          새 파츠용 예시 part.json (게임에 등록 안 됨)
│   ├── audio/bgm/hub-theme.mp3   audio/songs/*.mp3
│   ├── fonts/                  Pretendard Variable, Orbitron (+ 라이선스)
│   ├── images/  effects/       (예비 폴더)
├── offline/                    ★ 더블클릭 실행용 자동 생성 파일 (npm run build — 직접 수정 금지)
│   ├── neonidol.bundle.js      js/ 전체 + Three.js 를 일반 스크립트 하나로
│   ├── characters.embed.js     캐릭터 파츠 데이터·이미지
│   ├── bin/*.mp3.js            곡 MP3
│   └── mediapipe-hands.embed.js 손 인식 모델
├── tools/build.mjs             offline/ 생성 스크립트
├── package.json                npm run build
├── docs/
│   ├── CHARACTER_PARTS_SPEC.md 캐릭터 파츠 규격 (좌표·z순서·부착 방식·색상·추가 절차)
│   └── DEV_RULES.md            코드 수정 규칙
├── CHANGELOG.md                변경 내역 (파일별 변경 이유, 버그 수정, 삭제 항목)
└── README.md
```

---

## 3. 모듈 연결과 데이터 흐름

### 초기화 순서 (`js/main.js`)
1. 버튼 동작표 등록 → 일시정지 → BGM → HUD → 전환 연출
2. 곡 선택·작곡 입력 연결 → 저장된 투고곡 불러오기
3. 타이틀 준비 (저장이 있으면 '불러오기' 활성화)
4. 2D 배경 즉시 시작 → GPU가 있으면 3D 배경 시작
5. 캐릭터 파츠 매니페스트 로드 → 허브 캔버스에 연결
6. 글꼴·캐릭터가 준비되면 부트 화면 제거 (최대 4초)

### 의존 방향
```
main ─▶ gameplay/* ─▶ ui/* ─▶ character/characterManager ─▶ character/* (내부)
  │          └──────▶ systems/*            core/* (모두가 사용)
  └▶ scene/*
```
- `character/*` 는 `gameplay/*` 를 import 하지 않습니다 → 외형과 게임 진행이 분리됨.
- 게임 상태는 `core/state.js` 의 `store.S` **한 곳**에만 있습니다.

### 상태 / 저장
```
트레이닝 결과 ─▶ gameProgress.fin() ─▶ store.S 변경 ─▶ persist() ─▶ localStorage 'ni'
파츠 변경창   ─▶ draft ─(저장)─▶ store.S.look ─▶ persist()
                    └(미리보기)─▶ characterManager.applyLook(draft) ─▶ 렌더러
```

### 주요 이벤트 (`eventManager`)
| 이벤트 | 보내는 곳 | 받는 곳 |
|---|---|---|
| `screen:change` | screenManager | audioSystem(BGM), songSelect(미리듣기 정지) |
| `training:judge` | 댄스/보컬 | transitionManager (판정 연출) |
| `songs:changed` | songLibrary | songSelect (디스크 다시 그리기) |
| `state:changed` / `look:changed` | state / characterManager | (확장용) |

### 저장 데이터 (기존 버전과 동일한 키)
| 키 | 내용 |
|---|---|
| `ni` | 게임 진행 `{name, wk, v, d, c, fans, r, rank?, look}` |
| `ni_day` | 데이 사이클 `{e, d}` |
| `ni_songs` | 투고곡 목록 |

`look` 은 기존 색상 인덱스(`hair/eye/top/skirt`)를 그대로 두고 `parts` 만 추가했습니다 → 예전 버전과 양방향 호환.

---

## 4. 캐릭터 파츠 추가 / 교체 (요약)

1. `assets/characters/<슬롯>/<새-id>/` 폴더에 레이어 이미지와 `part.json` 을 넣는다 (`_template` 복사).
2. `assets/characters/manifest.json` 의 해당 슬롯 `parts` 배열에 경로를 추가한다.
3. 끝. 파츠 변경 창에 자동으로 나타나고, 선택·미리보기·저장·되돌리기가 모두 동작합니다.
4. 더블클릭 실행에도 반영하려면 `npm run build` 를 한 번 실행합니다. (로컬 서버 실행은 바로 반영)

좌표 기준(720×1163), 그리는 순서(z) 표, 부착 방식(bind) 목록, 색상(mask) 규칙,
기본 몸 교체 방법은 **[docs/CHARACTER_PARTS_SPEC.md](docs/CHARACTER_PARTS_SPEC.md)** 에 자세히 있습니다.

---

## 5. 새 기능을 추가할 때 어느 파일을 고치나

| 하고 싶은 것 | 수정할 파일 |
|---|---|
| 색·글꼴·버튼 모양 전체 변경 | `css/variables.css` (→ `components.css`) |
| 특정 화면 레이아웃 | `css/screens/<화면>.css` + `index.html` 해당 section |
| 밸런스 (스탯 상승, 난이도) | `js/data/gameData.js` |
| 서바이벌 단계 수·필요 스탯·라이벌 이름·팬 보상·승리 라운드 수 | `js/data/gameData.js` 의 `SURVIVAL` |
| 캐릭터 대사 | `js/data/gameData.js` 의 `LINES` |
| 스토리 챕터 | `js/data/storyData.js` |
| 기본곡 추가 (합성 반주) | `js/data/songData.js` 에 항목 추가 |
| MP3 곡 추가 | `assets/audio/songs/<키>.mp3` + `songAnalysis.js` 에 분석 데이터 + `songData.js` 에 `mp3:'<키>'` |
| 새 트레이닝 종류 | `js/gameplay/<새>.js` 작성 → `gameProgress.act()` 에 분기 → `index.html` 메뉴 버튼(`data-action="act" data-arg="…"`) |
| 새 버튼 동작 | HTML 에 `data-action="이름"` → `main.js` 의 `registerActions` 에 함수 연결 |
| 사이드 버튼(메뉴/보상/알림) 기능 구현 | `ui/uiManager.js` 의 `sideBtn` 대신 새 함수 + `main.js` 동작표 |
| 저장 항목 추가 | `core/state.js`(newGame 기본값) + `systems/saveSystem.js`(loadGame 보정) |
| 헤어/의상/눈 파츠 | `assets/characters/` 만 (코드 수정 없음) |
| 새로운 캐릭터 움직임 | `character/characterAnimation.js` 에 `registerDeformer()` |
| 화면 전환/연출 | `ui/transitionManager.js`, `css/animations.css` |
| 3D 배경 | `js/scene/backgroundScene.js` |

> **MP3 곡 분석 데이터** : `songAnalysis.js` 의 형식(bpm, off, on/lo/rm/mp 문자열, h0/hn)은 원본 게임 제작 시
> 별도 도구로 만든 것입니다. 새 MP3를 넣을 때 분석 데이터가 없으면 `mp3` 를 빼고 기본곡으로 등록하세요. *(분석 도구는 포함되어 있지 않음 — 미완성 항목)*

---

## 6. 테스트 결과 (v2.2)

Chromium(Playwright, 헤드리스, 가짜 카메라/마이크)으로 확인했습니다.
아래 항목은 **로컬 서버 실행과 더블클릭(file://) 실행 두 방식 모두**에서 확인했습니다.

**확인한 항목**
- 모든 import/export 경로, CSS/JS/이미지/MP3/폰트/MediaPipe 파일 로드 : 실패 요청 0건
- 브라우저 콘솔 오류 0건 (모든 시나리오)
- 타이틀 → 새 게임 → 허브, 불러오기
- **기존 버전 저장 데이터**(주차·스탯·팬·색상 look·데이 사이클·투고곡) 주입 후 불러오기 → 그대로 복원
- 캐릭터 렌더링 : 허브 애니메이션, 색상 변경, 미리보기(이음새 없음), 썸네일
- 파츠 변경 : 탭 전환, 색 선택, 저장 / X → 아니요(원복) / 되돌리기 / 기본값
- **새 파츠를 manifest 에만 추가** → 목록 표시·mask 색상·저장 동작 (코드 수정 없이)
- 스토리 해금 (평가 단계에 따라), Esc 로 닫기
- 곡 선택 : 투고곡 포함, 휠 1회/↑↓ 1회 = 정확히 1곡 이동
- 보컬 트레이닝(MP3 곡) 시작 → 일시정지 → 나가기 → 곡 선택 복귀
- 댄스 트레이닝(기본곡) 끝까지 진행 → 결과 → 스탯 상승 → 주차 +1 → 저장
- MediaPipe Hands 로컬 모델 로드 (작곡 화면에서 "손가락 모양을…" 단계까지, 더블클릭 실행에서는 offline 데이터로)
- 더블클릭 실행 : MP3 곡 로드(보컬 MP3 곡 진행), 캐릭터 색상 변경, 투고곡 저장
- 작곡 : 입장, 녹음 시작/취소, 나가기, 투고곡 추가 → 곡 목록 반영·저장
- 서바이벌 스테이지 : 미리보기, 패배(부족 스탯 표시), 5단계 연속 클리어 → 데뷔 엔딩, 기존 저장(r=1) → STAGE 2 부터, 레슨 후 자동 평가 없음
- 메뉴 강조 : 마우스를 올리지 않으면 모든 항목이 기본색, 올린 항목만 핫핑크
- 3D 배경 (`?forcegl`) 렌더링, 모바일(390px) 레이아웃 가로 스크롤 없음

**확인하지 못한 항목 (실제 기기 필요)**
- 실제 손을 사용한 댄스 판정 / 작곡 손가락 인식 정확도 (가짜 카메라는 손이 없음)
- 실제 목소리 음정 인식 (가짜 마이크는 신호음)
- 스피커로 듣는 음악·효과음의 실제 음질/타이밍 (헤드리스는 소리 출력 없음 — 재생 코드는 오류 없이 실행됨)
- GPU 가 있는 실제 브라우저에서의 프레임 속도, Safari / Firefox / 모바일 실기기
- Windows/macOS 실제 탐색기에서의 더블클릭 (헤드리스 Chromium 에서 file:// 주소로 확인)
