# 맥락노트 — WarSens 프로젝트

> 왜 이렇게 결정했는지, 관련 자료가 어디 있는지 기록하는 문서.
> **새 대화 세션이 열릴 때마다 이 파일을 읽어서 맥락을 복원한다.**
> 작업 단위가 끝날 때마다 업데이트할 것.

---

## 현재 작업
- **Phase 0 진행 중.**
- 프로젝트 디렉토리 구조 생성 완료 (orchestrator 루프 2회 PASS). `src/warsens/` 패키지 레이아웃 확정.
- 다음: Python 가상환경 + `requirements.txt` 초기화, 그다음 마우스 로거 PoC.

## 워크플로우 규칙
- 코드 생성/수정 또는 기획 변경 작업은 **기본적으로 orchestrator를 경유**한다.
- orchestrator는 작성자 도메인 에이전트와 감사관(code-auditor / plan-auditor)을 조율하고, 합의 도달 시 최종 보고만 사용자에게 전달.
- 프로토콜 상세: `.claude/skills/ch03-review-loop.md`.

## 핵심 결정사항

### 추천 기준
- 사용자 실제 플레이 데이터로 학습, 마우스 **사용 면적(손목/팔/어깨 성향)** 을 자동 측정해 반영.
- "좋은 민감도"의 성공 지표는 **조준 궤적 효율성** — 타겟 도달 시간 + 오버슈트율.

### 데이터 수집
- **(a) 마우스 입력 로거 + (b) 화면 녹화 + YOLO 탐지** 병행.
- 환경: **시뮬라크럼(벤치마크) + 일반 미션(보조)** 두 가지 다 수집.
- 프라이버시: **원본 영상은 로컬에서만**, 서버로는 "마우스 로그 + 좌표 시계열"만 업로드.

### 추천 출력
- **절대값 민감도** 추천 (예: 0.42). 델타/퍼센트 아님.
- **종합 최적값 1개** — 지상/파쿠르/에임글라이드 별로 나누지 않음.

### 참여자 모집 / 보상
- 본인 + 오픈채팅방. 플래티넘 직접 지급 + 비금전적 보상 병행.
- 플래티넘 거래 규칙(세금, 쿨다운) 고려해 보상 한도 설정 필요.

### 패키지 레이아웃 (확정)
- 루트 패키지: `src/warsens/`
- 서브모듈: `collector`, `cv`, `analysis`, `recommender`, `ui`, `storage`, `uploader`, `common`
- 테스트: `tests/test_<module>/`
- 런타임 산출물: `data/sessions/`, `data/models/` (gitignored)
- 추적 자원: `data/templates/` (크로스헤어 템플릿 이미지)
- 서버 코드는 Phase 2 도달 시 별도 레포/패키지 여부 재결정.

### 기술 선택
- 언어: Python 3.11+
- UI: **PySide6** (LGPL). PyQt6는 배포 제약으로 제외.
- 화면 캡처: `dxcam`. 마우스: Raw Input / `pynput`.
- CV: YOLOv8/v11 (ultralytics). 크로스헤어는 템플릿 매칭.
- ML: Phase 1 휴리스틱 → Phase 2 XGBoost → Phase 3 (선택) DL.

### 안티치트
- EAC에 대해 **읽기 전용 캡처만** 허용. 입력 주입/메모리 접근 절대 금지.

## 자료 위치

| 자료 | 경로 |
|------|------|
| 기획서 | `기획서.md` |
| 이 맥락노트 | `.claude/hooks/shared/context-notes.md` |
| 체크리스트 | `.claude/hooks/shared/checklist.md` |
| 수정 기록 | `.claude/hooks/shared/change-log.md` (자동 생성) |
| 스킬 목차 | `.claude/skills/INDEX.md` |
| GitHub | https://github.com/recognisemeimthebest/warframe_sensitivity |
