# 워프레임 도메인 챕터

## 민감도 설정 구조
- 인게임: **Look Sensitivity (X/Y 분리), ADS Sensitivity, Controller Sensitivity** 등 여러 값
- 본 프로젝트는 **Look Sensitivity** 위주 (종합 최적값 1개 방침)
- ADS 민감도는 Phase 2+ 고려

## 벤치마크 환경: 시뮬라크럼
- 코드: 허브(Relay)에서 입장
- 고정 적 스폰 가능 (종류/수량 선택) — **컨트롤된 데이터 수집에 최적**
- 권장 루틴 예시:
  - Grineer Lancer 10마리 스폰 → 지정 무기로 헤드샷 시도 5분
  - 거리 중간, 적은 정적 배치

## 워프레임 고유 기동
- **파쿠르**: 불릿 점프, 에임 글라이드로 공중에서 슬로우모션 조준
- 민감도 추천에 이 요소를 별도 고려할지 — 현재 기획: **통합 지표로 처리**

## 안티치트 (EAC — Easy Anti-Cheat)

상세 정책: [`docs/SAFETY_POLICY.md`](../../../docs/SAFETY_POLICY.md).

### MUST (허용/권장)
- **마우스 입력 수신**: Windows Raw Input API 또는 `pynput.mouse.Listener` (수신 전용)
- **화면 캡처**: `dxcam` (DXGI Desktop Duplication), OBS/디스코드 오버레이와 동급 — 미탐지
- 캡처 대상은 **Warframe 창 영역만** (전체 화면 캡처 금지 — 타 앱 민감정보 포함 위험)
- 분석 결과는 **세션 종료 후 리포트**로만 표시

### MUST NOT (즉시 밴 또는 EULA 위반)
금지 API — import 자체가 코드에 등장하면 안 됨:

| 카테고리 | 금지 심볼 |
|---|---|
| 입력 주입 | `pynput.mouse.Controller`, `pynput.keyboard.Controller`, `pyautogui`(전체), `SendInput`, `mouse_event`, `keybd_event` |
| 프로세스 접근 | `OpenProcess`, `ReadProcessMemory`, `WriteProcessMemory`, `pymem`, `frida`, `ReadWriteMemory` |
| 훅/인젝션 | `CreateRemoteThread`, `VirtualAllocEx`, DLL 인젝션, DirectX/Vulkan 훅 (ReShade 류 포함) |
| 드라이버 | `Interception` 드라이버, AutoHotkey 연동 |

### 실시간 피드백 루프 금지
CV 탐지 결과가 **실시간으로** 다음 경로에 도달하는 구조 금지:
- 마우스/키보드 입력 생성
- 적 위치를 오버레이로 **세션 중** 사용자에게 표시 (aim assist)

오버레이 표시는 **세션 종료 후 리플레이/리포트**에서만 허용.

## 플래티넘 거래 규칙
- Trade Tax: 구매자·판매자 모두 크레딧 세금
- 거래 쿨다운 있음 (1일 거래 횟수 제한)
- 다른 유저에게 지급하려면 아이템 거래 필요 (플랫 직접 전송 불가)

## 적 유닛 분류 (YOLO 클래스 기준)
- **Grineer**: 회색톤, 기계적 갑옷
- **Corpus**: 흰색/주황, 에너지 무기, 로봇(MOA) 포함
- **Infested**: 생체 질감, 뭉쳐 있음
- **Sentient**: 노랑/자주, 적응형 저항. 변이형 디자인 — 라벨링 난이도 높음
