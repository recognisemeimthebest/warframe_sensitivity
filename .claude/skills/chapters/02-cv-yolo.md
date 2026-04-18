# CV / YOLO 챕터

## 목적
녹화된 프레임에서 **적 바운딩 박스**와 **크로스헤어 위치**를 추출한다.

## 적 탐지 — YOLOv8/v11
- 프레임워크: `ultralytics`
- 클래스 (대분류 우선):
  - `grineer` / `corpus` / `infested` / `sentient`
  - 여유 있으면 세분화 (Lancer, Butcher 등)
- 학습 데이터: 시뮬라크럼에서 스폰된 적을 다각도로 스크린샷 → 수동 라벨링 (Roboflow/CVAT)
- 시작 지점: COCO 사전학습 모델에서 finetune

## 크로스헤어 — 템플릿 매칭 (YOLO 대신)
- 크로스헤어는 모양/크기 일정하고 **화면 정중앙 근처 고정**
- `cv2.matchTemplate` 으로 충분 — YOLO 라벨링 비용 절감
- 무기별 크로스헤어가 다르면 템플릿 여러 개 준비

## 추론 파이프라인
1. 프레임 로드 → BGR
2. YOLO 추론 (배치로)
3. 크로스헤어 위치 추출
4. `(frame_ts, enemy_boxes[], crosshair_xy)` Parquet 저장

## 성능 팁
- 추론은 **배치(batch=16~32)** 가 단일 프레임보다 10배 이상 빠름
- GPU 없으면 YOLOv8n (nano) 사용. 정확도 낮지만 CPU에서도 돌아감
- 실시간 필요 없음 — **후처리로 돌려도 됨** (수집 종료 후 배치 처리)
