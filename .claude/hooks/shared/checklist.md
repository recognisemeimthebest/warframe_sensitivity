# 체크리스트 — WarSens

> `기획서.md` 로드맵에 근거한 작업 추적.
> 하나 끝낼 때마다 `[x]`로 체크. 다음 할 일을 항상 정리해둘 것.
> **한 번에 여러 항목 체크 금지.**

---

## 초기 세팅

- [x] 기획서 작성 (기획서.md)
- [x] GitHub 저장소 연동 (recognisemeimthebest/warframe_sensitivity)
- [x] HARNESS 오케스트레이션 적용 (.claude/)
- [x] 리뷰 루프 오케스트레이션 추가 (orchestrator 에이전트 + ch03-review-loop, 최대 5회)
- [x] 밴 리스크 리서치 — EAC 경계 조사, `docs/SAFETY_POLICY.md` 작성, code-auditor에 금지 import 체크 반영
- [x] 유사 선례 리서치 — Chimpshot/Mouselabs/Oblivity/aim_trainer_analysis/NVIDIA 논문 분석, 기획서 §2-1/§2-2에 차별점·함정 반영

## Phase 0 — 프로토타입 (혼자 검증)

- [x] 프로젝트 디렉토리 구조 생성 (`src/`, `tests/`, `data/` 등) — orchestrator 루프 2회에 PASS
- [x] Python 가상환경 + `requirements.txt` 초기화 — .venv (3.13.9), pyproject.toml(src layout), 2회 PASS
- [ ] 마우스 입력 로거 PoC (Raw Input 또는 pynput, 1000Hz)
- [ ] 화면 캡처 PoC (dxcam, 60 FPS, 워프레임 창 크롭)
- [ ] 마우스 ↔ 프레임 **타임스탬프 동기화** 검증
- [ ] 시뮬라크럼 테스트 녹화 1회 → Parquet 저장 확인
- [ ] YOLO 사전학습 모델로 적 탐지 테스트 (COCO 클래스)
- [ ] 크로스헤어 템플릿 매칭 PoC
- [ ] 본인 플레이 **서로 다른 민감도 2~3개**로 녹화 → 오버슈트율 차이 확인

## Phase 1 — MVP

- [ ] PySide6 기본 UI (홈 / 녹화 / 대시보드)
- [ ] 단일 사용자(본인) 기준 휴리스틱 추천 로직
- [ ] 세션 데이터 관리 (저장/조회/삭제)
- [ ] pyinstaller로 exe 빌드

## Phase 2 — 데이터 수집 공개

- [ ] 오픈채팅방 베타 모집 공지문 작성
- [ ] 참여자용 간편 설치 가이드
- [ ] 가공 데이터 업로드 파이프라인 (영상 제외, 좌표/로그만)
- [ ] 서버 수신/저장소 구축 (아직 미정 — 로컬 ML 가능성도 열어둠)
- [ ] 수집 데이터로 XGBoost 모델 학습

## Phase 3 — 정식 추천 서비스

- [ ] 개인화 추천 + 근거 설명 UI
- [ ] 신규 사용자 온보딩 (캘리브레이션 가이드)
- [ ] YOLO 커스텀 모델 (Grineer/Corpus/Infested/Sentient 라벨링)
