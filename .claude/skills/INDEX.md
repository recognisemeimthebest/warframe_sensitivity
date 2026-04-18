# 스킬 매뉴얼 목차

> 필요한 챕터만 Read 도구로 로드하세요. 전체를 한번에 읽지 마세요.
> UserPromptSubmit 훅이 키워드를 감지하여 관련 챕터를 자동 주입합니다.

## 도메인 스킬 (WarSens 기술 영역별)

| # | 챕터 | 파일 | 트리거 키워드 |
|---|------|------|--------------|
| 01 | 데이터 수집 | `chapters/01-data-collection.md` | 마우스, 로거, 화면 캡처, dxcam, raw input, 녹화 |
| 02 | CV / YOLO | `chapters/02-cv-yolo.md` | YOLO, ultralytics, 탐지, 라벨링, 크로스헤어, opencv |
| 03 | 추천 모델 (ML) | `chapters/03-ml-recommender.md` | 추천, 민감도, 회귀, xgboost, pytorch, 궤적 |
| 04 | 데스크톱 UI | `chapters/04-desktop-ui.md` | PyQt, PySide, UI, 대시보드, 윈도우 |
| 05 | 워프레임 도메인 | `chapters/05-warframe-domain.md` | 워프레임, 시뮬라크럼, 파쿠르, 에임글라이드, 그리니어 |

## 메타 스킬 (프로세스/품질)

| 챕터 | 파일 | 용도 |
|------|------|------|
| Python 품질 | `ch01-python-quality.md` | 에러 처리, 보안, async 패턴, 코드 품질 기준 |
| 스킬 활성화 규칙 | `ch02-skill-activation.md` | 키워드·패턴·경로·코드 감지 규칙 상세 |
| 리뷰 루프 프로토콜 | `ch03-review-loop.md` | 작성자 ↔ 감사관 최대 5회 합의 루프. orchestrator 에이전트가 실행 |

## 자동 로드 규칙
- 사용자 지시에서 키워드가 감지되면 → 해당 챕터가 Claude 컨텍스트에 자동 주입
- PostToolUse에서 보안/에러 이슈 감지 → `ch01-python-quality.md` 참고 안내
- 수동으로 읽으려면: `.claude/skills/chapters/XX-name.md` 또는 `.claude/skills/chXX-name.md`

## 각 챕터는 아직 스텁(placeholder) 상태
Phase 0 착수 시 해당 도메인 챕터를 점진적으로 채웁니다.
