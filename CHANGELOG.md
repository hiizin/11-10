# CHANGELOG

## v2.2.0 — 서바이벌 스테이지 개편 + 메뉴 hover 수정 (2026-11-09)

### 메뉴 강조
- **수정** `css/screens/hub.css` : 스케줄 메뉴 첫 항목(보컬 트레이닝)이 마우스를 올리지 않아도 핫핑크로 칠해져 있던 규칙(`:first-child`) 삭제.
  이제 마우스를 올린 항목(또는 키보드 Tab 으로 선택한 항목)만 핫핑크로 바뀐다.

### 서바이벌 스테이지 : 리듬게임 → 스탯 대결 5단계
- **추가** `js/gameplay/survivalRules.js` : 규칙 계산 (순수 함수 — 판정, 현재 단계, 도전 가능 여부).
- **추가** `js/gameplay/survivalStage.js` : 서바이벌 화면 (필요 스탯 미리보기 → 도전 → 라운드별 공개 → WIN/LOSE → 보상/엔딩).
- **추가** `css/screens/survival.css`, `index.html` 의 `#survival` 화면.
- **변경** `js/data/gameData.js` : `CFG`(3단계 컷라인)·`AUDITION_STAGE` 삭제 → `SURVIVAL`(5단계, 라이벌 스탯 = 필요 스탯, 팬 보상).
- **변경** `js/gameplay/gameProgress.js` : 4주마다 자동으로 열리던 평가 리듬게임(`startAud`, `verdict`) 삭제. `next()` 는 주차 +1 후 허브로. 엔딩 문구 변경.
- **변경** `js/ui/uiManager.js` : WEEK 패널에 누적 주차 + 현재 단계(STAGE n/5), 서바이벌 버튼 아래 "도전 가능 / 스탯 부족" 표시,
  이길 수 있으면 버튼 반짝임, 캐릭터 대사 조건을 스테이지 기준으로. `surv()` 삭제(버튼은 서바이벌 화면을 연다).
- **변경** `js/core/config.js` : 12주 제한·평가 주기 설정 삭제, 서바이벌 화면에도 BGM.
- **변경** `js/main.js` : 동작 `surv / survival / svChallenge / svHub / svEnding` 연결.
- 저장 호환 : `S.r`(통과한 평가 수)을 "클리어한 스테이지 수"로 그대로 사용 → 기존 저장의 1차 통과는 STAGE 2 부터 이어진다.


## v2.1.0 — index.html 더블클릭 실행 지원 (2026-11-09)

브라우저는 `file://` 에서 ES Module·fetch·이미지 픽셀 읽기를 막는다. 이를 피하기 위해 :

- **추가** `tools/build.mjs`, `package.json` : `npm run build` 로 `offline/` 생성
  (JS 묶음 1개 + 캐릭터 데이터/이미지 + 곡 MP3 + MediaPipe 모델을 일반 스크립트에 담음).
- **추가** `js/systems/assetLoader.js` : 서버 실행이면 fetch, 더블클릭 실행이면 offline 데이터를 쓰는 공용 로더.
- **변경** `index.html` : 실행 방식 자동 선택 (file:// → offline 묶음, http:// → js/main.js 소스). offline 이 없으면 부트 화면에 안내.
- **변경** `characterPartsData.js`(JSON), `characterRenderer.js`(이미지), `songEngine.js`(MP3), `handTracking.js`(MediaPipe) : 직접 fetch 대신 assetLoader 사용.
- **변경** `main.js` : 디버그 객체 `window.NeonIdol.debug` 에 진행 함수 노출 (테스트용).
- 문서 : README 실행 방법, 파츠 규격서·개발 규칙에 빌드 단계 추가.


## v2.0.0 — 모듈 프로젝트 재구성 + 디자인 통일 + 캐릭터 파츠 시스템 (2026-11-09)

원본 : 단일 `index.html` (11.2MB, 코드 약 2,600줄, `<style>` 19개, `<script>` 8개, base64 리소스 포함)

### 구조
- HTML / CSS(14개) / JS(35개 모듈)로 분리. ES Module import/export 로만 연결.
- base64로 들어 있던 리소스를 파일로 추출 : BGM 1곡, MP3 5곡, 캐릭터 레이어 19장, 곡 분석 데이터.
- 전역 변수·`window.__xxx` 플래그 15개 → 모듈 내부 상태 + 이벤트 버스(`eventManager`)로 교체.
- 게임 상태 `S` → `core/state.js` 의 `store.S` 한 곳에서 관리.
- 인라인 `onclick="함수()"` → `data-action="함수"` + `main.js` 동작표 (요소 ID는 모두 유지).
- Three.js r161, MediaPipe Hands, 글꼴을 로컬에 포함 → 인터넷 없이 동작, CDN 장애 영향 없음.

### 캐릭터 시스템 (character/*)
- 하드코딩되어 있던 리그(`createRig` 안의 레이어 이름·좌표·변형 코드)를 데이터로 분리 :
  `assets/characters/manifest.json` + 파츠별 `part.json` (base / hair / outfits / eyes).
- 레이어별 움직임을 **디포머 레지스트리**(static, torso, torsoWarp, neck, head, faceDetail, gaze, headLag, headSpring, hairFlow)로 일반화.
  원본과 같은 수식이라 움직임은 동일.
- 색상 변경을 **tint 채널**로 일반화하고 새 아트용 `mask` 모드 추가.
- 파츠 변경 창 재설계 : 헤어/의상/눈 탭, 파츠 카드(썸네일), 색상, 실시간 애니메이션 미리보기, 탭별 카메라 줌,
  저장 / 되돌리기 / 기본값 / 닫기 확인. '추가 예정' 빈 슬롯 표시.
- 메쉬 변형 최적화 : 칸마다 레이어 전체 대신 필요한 영역만 그림 (결과 동일).
- 축소 뷰는 1:1로 그린 뒤 축소 → 미리보기 이음새 제거.
- 캐릭터 반응 연출 추가 : 클릭·허브 복귀·파츠 저장 시 가볍게 뛰어오름.

### 디자인 / UX
- 디자인 토큰(`css/variables.css`) 도입. 패널 2종(네이비 스테이지 패널 / 글라스 카드)과 강조색 3종으로 통일.
- 글꼴 : 한글·본문 **Pretendard**, 영문 라벨·숫자 **Orbitron** (원본은 Noto Sans KR 을 Google Fonts 에서 불러옴).
- 버튼 통일 : `.btn--primary / --secondary / --glass` (사선, 오프셋 그림자, hover 반사광, 누름 물결, 클릭음).
- 타이틀 로고를 허브 로고와 같은 크롬 스타일로, 결과·평가·엔딩 화면을 카드 + 등급 연출 + 스탯 칩으로 재디자인.
- 화면 전환 애니메이션, 큰 장면 전환 사선 와이프, 허브 패널 순차 등장, 판정 피드백(PERFECT 테두리 펄스 / MISS 흔들림),
  콤보 10단위 강조, 반짝이 파티클, 스토리 챕터 전환 효과, 곡 정보 교체 애니메이션.
- 부트(로딩) 화면 추가. 모바일(≤700px) 허브 배치 정리.
- '동작 줄이기' 설정 사용자 배려 (`prefers-reduced-motion`).
- GPU 가속이 없는 환경에서는 3D 배경 대신 2D 배경 자동 사용 (`?forcegl` 로 강제 가능).

### 버그 수정
- **곡 선택이 한 번에 2곡씩 넘어감** : 휠·↑↓ 키 핸들러가 두 번 등록되어 있던 것을 한 벌로 정리.
- **고득점인데 'NaN위'로 탈락** : 종합 점수가 100을 넘으면 `Math.pow(음수, p)` = NaN → 0 이상으로 제한해 최고 1위.
- **평가 무대에서 '나가기' 시 평가를 건너뜀** : 곡 선택으로 이동하던 것을 평가 안내 화면으로 복귀하도록 수정.
- **데뷔 순위(rank)가 저장되지 않음** : 엔딩 시 저장하도록 수정 (FINAL 스토리 해금 유지).
- 곡 선택에서 GAME START 버튼에 포커스가 있을 때 Enter 가 두 번 실행될 수 있던 문제 수정.
- `act()` 의 `window.__busy` 가 설정되지 않던 문제 → 작곡 진입 중 중복 실행 방지로 실제 동작하게 함.

### 삭제 (화면에 보이지 않거나 실행되지 않던 코드)
- Three.js **r128** 전체 로드 + `makeIdol()` 3D 아이돌 (호출되지 않음)
- `avatar()` SVG 아바타 (호출되지 않음)
- MediaPipe **Pose** 스크립트 로드 + `initPose / pmatch / skel` (호출되지 않음, 댄스는 Hands 사용)
- **3D 카메라 이동(플랫폼 스톱) 기능** : `setNav`, 휠 내비, `#pstop` 입장 팝업, 캐릭터 글리치 전환, 보이지 않는 플랫폼 메쉬 — 요청에 따라 삭제.
  메뉴의 '스토리'는 이제 바로 스토리 창을 연다.
- HTML 이 없는 `#fx` 화면(휴식/TV 연출) CSS, `#shint`, `#chips`, `.mini`, `#av`, `.how`, `.story` 등 대상 없는 CSS
- 사용되지 않던 변수 : `CH`, `NMS`, `P`, `Z`, `PS`, `popup()`/`#rpop`, `heroURL`, `__SRC`, `__composeAnalyze`, `composeFinish`
- 19개 `<style>` 의 서로 덮어쓰는 중복 규칙 (최종 적용값만 남김)
- MediaPipe `hand_landmark_lite.tflite` (modelComplexity 1 에서는 사용 안 함)

### 파일별 대응표 (원본 → 새 위치)
| 원본 | 새 위치 |
|---|---|
| `<style>` ×19 | `css/*.css`, `css/screens/*.css` |
| 메인 스크립트 : 저장·BGM·go | `systems/saveSystem.js`, `systems/audioSystem.js`, `core/screenManager.js` |
| 진행 (start/fin/next/startAud/verdict/endGame) | `gameplay/gameProgress.js` |
| runDance / runVocal | `gameplay/danceTraining.js`, `gameplay/vocalTraining.js` |
| 2D 배경 IIFE | `scene/fallbackBackground.js` |
| OPT, STO, 파츠/스토리 창 | `assets/characters/**/part.json`, `data/storyData.js`, `character/characterCustomization.js`, `gameplay/story.js` |
| RIGDATA + createRig + 히어로 연결 | `assets/characters/**`, `character/characterRenderer.js`, `characterAnimation.js`, `characterManager.js` |
| SONGS, DIF, 곡 선택 | `data/songData.js`, `data/gameData.js`, `gameplay/songSelect.js` |
| 레이더/HUD/토스트 | `ui/uiManager.js` |
| `#daycycle` | `systems/dayCycle.js` |
| `type="module"` 3D 씬 | `scene/backgroundScene.js` |
| 허브 프로필 스크립트 | `ui/uiManager.js` (`updProf`, `surv`, `sideBtn`) |
| 작곡 스크립트 | `gameplay/composeTraining.js`, `systems/voiceSynth.js`, `systems/handTracking.js`, `systems/songLibrary.js`, `systems/songEngine.js` |
| `#songdata` / `#songmp3` | `data/songAnalysis.js`, `assets/audio/songs/*.mp3`, `systems/songEngine.js` |
| `#gamepause` | `systems/pause.js` |
