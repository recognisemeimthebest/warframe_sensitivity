# WarSens 안전 정책 (Safety Policy)

> 워프레임 EULA/EAC(Easy Anti-Cheat)와의 경계선을 명시한다.
> 본 문서는 기획서 §9(리스크) 및 `.claude/skills/chapters/05-warframe-domain.md`의 확장판이며,
> **모든 기여자와 감사 에이전트는 이 문서를 기준으로 검증**한다.

---

## 1. 원칙

WarSens는 **플레이 데이터를 관찰·분석·조언**하는 도구다. **플레이를 보조하거나 자동화하지 않는다.**

- 관찰 (Observe): 마우스 입력 수신, 화면 캡처, 로그 저장 — 허용
- 분석 (Analyze): CV/통계/ML로 오프라인 지표 추출 — 허용
- 조언 (Advise): 세션 종료 후 리포트로 민감도 추천 — 허용
- 자동화 (Automate): 실시간 입력 조작·조준 보조 — **금지**

이 원칙이 기술적 구현과 충돌하면, **기술 구현이 양보**한다.

---

## 2. 기술 조사 결과 (2026-04 기준)

EAC가 탐지하는 것 / 탐지하지 않는 것에 대한 공개 정보 정리:

| 행위 | EAC 탐지 여부 | 판정 |
|---|---|---|
| OS 전역 Raw Input 폴링 (Windows `RAWINPUT`) | 미탐지 | 허용 |
| `pynput.mouse.Listener` (수신 전용 훅) | 미탐지 | 허용 |
| DXGI Desktop Duplication 화면 캡처 (`dxcam`, OBS 동급) | 미탐지 | 허용 |
| GDI/BitBlt 화면 캡처 | 미탐지 | 허용 (성능만 떨어짐) |
| **게임 프로세스 메모리 읽기/쓰기** | **탐지 → 밴** | **금지** |
| **DLL 인젝션 / 그래픽스 API 훅** | **탐지 → 밴** | **금지** |
| **입력 주입 (`SendInput`, `mouse_event`, `pynput.Controller`)** | EULA 위반 (unfair advantage) | **금지** |
| **Interception 류 커널 드라이버** | 탐지 가능성 + EULA 위반 | **금지** |

결론: **읽기 전용 관찰은 기술적 리스크 낮음**, 입력 주입/프로세스 접근은 즉시 밴 경로.

---

## 3. MUST (반드시 지킨다)

### 3-1. 입력 수집
- 마우스 이벤트는 **수신 전용 API**만 사용
  - Windows Raw Input API (선호)
  - `pynput.mouse.Listener` (허용)
- 키보드도 동일 — `Listener` 계열만

### 3-2. 화면 캡처
- DXGI Desktop Duplication (`dxcam`) 또는 동급 OS API
- 게임 창을 **외부에서 캡처**하는 방식만 허용
- 캡처 대상은 `Warframe.exe` **창 영역만** — 전체 화면 캡처 시 민감정보(디스코드/웹) 포함될 수 있음

### 3-3. 데이터 처리
- CV/분석은 **세션 종료 후 후처리** 또는 실시간이라도 **UI 표시 전용**
- 분석 결과가 **입력 경로로 돌아가는 루프 금지** (§4-4 참조)
- 원본 영상은 **로컬 디스크에만**, 서버로는 가공 좌표 시계열만

### 3-4. 사용자 고지
- 앱 최초 실행 시 **수집 항목·저장 위치·업로드 여부를 명시한 동의 화면** 제공
- 녹화 중에는 **시각적 표시**(오버레이 또는 트레이 아이콘 상태)

### 3-5. 감사 용이성
- 입력 주입 관련 모듈(`SendInput`, `pyautogui`, `pynput.mouse.Controller` 등)은 **의존성에 포함되지 않아야** 한다.
  - `requirements.txt`에 존재 금지
  - import 정적 검사로 CI/감사 에이전트가 자동 확인

---

## 4. MUST NOT (절대 금지)

### 4-1. 입력 주입
금지 API / 라이브러리:
- `pynput.mouse.Controller`, `pynput.keyboard.Controller`
- `pyautogui` (전체)
- Win32: `SendInput`, `mouse_event`, `keybd_event`, `PostMessage`/`SendMessage`로 입력 위조
- `Interception` 드라이버, Razer/Logitech G-Hub 매크로 API 우회
- AutoHotkey 스크립트 실행

### 4-2. 프로세스·메모리 접근
금지 API:
- `OpenProcess`, `ReadProcessMemory`, `WriteProcessMemory`, `NtReadVirtualMemory`
- `CreateRemoteThread`, `VirtualAllocEx`
- `pymem`, `ReadWriteMemory` 류 파이썬 바인딩
- Cheat Engine, Frida 등 디버거/인젝터 연동

### 4-3. 훅·인젝션
- DLL 인젝션 (SetWindowsHookEx로 외부 DLL 주입, LoadLibrary 인젝션)
- DirectX/Vulkan/OpenGL 훅 (ReShade 스타일 포함 — 게임 렌더링 파이프라인 개입 금지)
- 커널 드라이버 로드

### 4-4. 자동 보조 (Aimbot/트리거봇)
CV 탐지 결과가 **실시간으로 입력에 영향**을 주는 모든 구조:
- 적 좌표 → 마우스 이동 명령 (aimbot)
- 적 좌표 → 클릭 트리거 (triggerbot)
- 적 좌표 → 오버레이로 **사용자에게 실시간 표시** (aim assist visual)
  - 오버레이는 **세션 종료 후 리플레이**에서만 허용

### 4-5. 공정성 해치는 기타
- 반동 제거, 마우스 가속 보정 등 **게임의 입력 처리 변경**
- 다른 플레이어 정보 수집(적팀/파티원 화면)

---

## 5. 감사 체크리스트 (code-auditor용)

코드 리뷰 시 다음 import/호출을 **정적으로** 검색:

```
# 금지 imports
from pynput.mouse import Controller
from pynput.keyboard import Controller
import pyautogui
import pymem
import frida
from ReadWriteMemory import ...

# 금지 Win32 심볼 (ctypes 경유 호출 포함)
SendInput, mouse_event, keybd_event
OpenProcess, ReadProcessMemory, WriteProcessMemory
CreateRemoteThread, VirtualAllocEx
SetWindowsHookEx  # (Listener 목적이 아닌 경우)
```

발견 시 **심각도: 높음**으로 즉시 플래그, CONDITIONAL PASS 이하로 판정.

실시간 피드백 루프 의심 패턴:
- CV 추론 결과가 `mouse` 관련 모듈에 인자로 전달
- YOLO/template matching 출력이 UI 오버레이의 매 프레임 렌더에 사용 (세션 중 실시간)

---

## 6. 사용자 고지 문구 (앱 내 동의 화면 초안)

> **WarSens는 다음 데이터를 로컬에 저장합니다:**
> - 마우스 이동·클릭 이벤트 (타임스탬프 포함)
> - 워프레임 창 화면 녹화 (세션 동안)
>
> **WarSens는 다음을 하지 않습니다:**
> - 게임 프로세스 접근 또는 메모리 수정
> - 마우스·키보드 입력 주입 또는 자동 보조
> - 영상 원본의 외부 서버 업로드
>
> 분석 결과(좌표 시계열, 지표)는 [동의 시] WarSens 서버로 전송됩니다.
> 동의를 거부해도 로컬 분석·추천은 이용 가능합니다.

---

## 7. 위반 발견 시 절차

1. 해당 기능/커밋 즉시 롤백
2. `change-log.md`에 위반 내용·발견 경위 기록
3. 관련 감사 에이전트(code-auditor) 체크리스트에 패턴 추가
4. 재발 방지용 정적 검사(ruff 커스텀 룰 또는 pre-commit hook) 검토

---

## 8. 변경 이력

| 날짜 | 변경 | 근거 |
|---|---|---|
| 2026-04-18 | 초안 작성 | EAC 경계 조사 + 유사 선례 분석 결과 |
