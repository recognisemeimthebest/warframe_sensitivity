# 데이터 수집 챕터

## 목적
워프레임 플레이 세션에서 **마우스 입력 로그 + 화면 녹화 + 프레임 타임스탬프**를 동기화하여 수집한다.

## 핵심 구성요소

### 1. 마우스 로거
- Windows Raw Input API 또는 `pynput` 사용
- 권장: 1000Hz 정밀도 (고주사율 마우스 대응)
- 기록 필드: `timestamp_ns, dx, dy, button_event`
- 저장 포맷: Parquet (시계열 압축 효율)

### 2. 화면 캡처
- `dxcam` 권장 (DXGI Desktop Duplication — 저오버헤드)
- 워프레임 창만 타겟 (창 핸들 기반 크롭)
- 해상도: 1920x1080 기준. 필요 시 다운샘플
- FPS: 60 고정. 타임스탬프 반드시 기록

### 3. 동기화
- 마우스 이벤트와 프레임을 **동일 `QueryPerformanceCounter` 기준 타임스탬프**로 정렬
- 후처리 시 프레임별로 구간 내 마우스 델타 누적

## 주의사항
- **워프레임 EAC**: 단순 화면 캡처/raw input 읽기는 일반적으로 감지 대상 아님. 그러나 입력 주입(input injection)은 절대 금지.
- 원본 영상은 **로컬에 임시 저장 후 전처리 완료 시 삭제**. 서버로 보내지 않음.

## 출력 형식
```
session_<uuid>/
├── meta.json              # dpi, 민감도, 해상도, 타임스탬프
├── mouse.parquet          # 시계열 입력
├── frames/                # 전처리된 좌표만 (원본 영상 X)
│   └── detections.parquet # {frame_ts, enemy_boxes, crosshair_xy}
```
