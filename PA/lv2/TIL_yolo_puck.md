# TIL: YOLOv8 퍽 학습 실습 (Isaac Sim 합성 데이터)

> 요약
> - 실습 저장소: `~/PA_Yolo/Yolo_practice/yolov8-ubuntu24-practice` ([GitHub](https://github.com/SeongminJaden/yolov8-ubuntu24-practice))
> - 설치는 `bash scripts/install.sh`로 끝냄. `check_gpu.py` 결과 PASS (torch 2.9.1+cu128, RTX 5060)
> - 학습 순서: Isaac Sim 6.1로 퍽 데이터 생성 → 라벨 검사 → YOLO 학습 → 평가
> - 가이드에 박힌 `/home/seongmin/...` 경로는 이 PC 경로로 바꿔서 실행

## 0. 설치 (완료)
```bash
cd ~/PA_Yolo/Yolo_practice/yolov8-ubuntu24-practice
bash scripts/install.sh
source .venv-yolo/bin/activate && python scripts/check_gpu.py
# 2.9.1+cu128 0.24.1+cu128 12.8
# PASS GPU + NMS: NVIDIA GeForce RTX 5060 Laptop GPU
```
- 설치 스크립트는 마지막에 `environment-yolo-lock.txt`를 덮어쓴다. git에 변경으로 보여도 정상.

## 1. GPU 비우기 (8 GB 공유)
```bash
sudo systemctl stop ollama; pkill -f ollama; pkill -f hermes; nvidia-smi
```

## 2. 폴더 이동 + 변수 정하기
변수는 같은 터미널에서만 유지된다. 터미널을 바꾸면 다시 지정한다.
```bash
cd ~/PA_Yolo/Yolo_practice/yolov8-ubuntu24-practice
RUN_ID=$(date +%Y%m%d-%H%M%S); DATA="data/tagged-practice-$RUN_ID"; YOLO_NAME="tagged-practice-$RUN_ID"; CP_OUT="models/centerpose-practice-$RUN_ID"
```

## 3. Isaac Sim으로 퍽 데이터 생성
- Isaac Sim 6.1 위치: `~/pa-course/.venv-isaacsim` (가이드의 `/home/seongmin/isaacsim6_venv` 대신 사용)
- 앞의 `env -u ...`는 ROS 환경변수를 빼는 부분
- `OMNI_KIT_ACCEPT_EULA=YES`는 NVIDIA 라이선스 동의 표시. 동의하는 경우에만 붙인다.

작게 먼저 시험:
```bash
env -u PYTHONPATH -u LD_LIBRARY_PATH -u AMENT_PREFIX_PATH -u CMAKE_PREFIX_PATH OMNI_KIT_ACCEPT_EULA=YES ~/pa-course/.venv-isaacsim/bin/python sim/generate_tagged_pucks.py --output "$DATA" --train 24 --val 8 --test 8 --video-frames 8
```
성공하면 `$DATA` 안에 `COMPLETE`, `dataset.yaml`, `manifest.json`, `images`, `labels`가 생긴다.

실제 학습용 (2번 변수를 새로 정한 뒤 실행. 같은 이름이면 `Output exists` 오류):
```bash
env -u PYTHONPATH -u LD_LIBRARY_PATH -u AMENT_PREFIX_PATH -u CMAKE_PREFIX_PATH OMNI_KIT_ACCEPT_EULA=YES ~/pa-course/.venv-isaacsim/bin/python sim/generate_tagged_pucks.py --output "$DATA" --train 240 --val 60 --test 100 --video-frames 120
```

## 4. 라벨 검사 → YOLO 학습
Isaac Sim이 끝난 뒤에 실행한다. 데이터 생성과 학습을 동시에 돌리면 GPU 메모리가 부족해진다.
```bash
source .venv-yolo/bin/activate
python scripts/validate_labels.py "$DATA/labels" --classes 4
python scripts/train_yolo.py --data "$DATA/dataset.yaml" --epochs 50 --batch 4 --device 0 --name "$YOLO_NAME"
```

## 5. 평가
```bash
yolo detect val model="runs/detect/$YOLO_NAME/weights/best.pt" data="$DATA/dataset.yaml" split=test device=0
```
- `best.pt`는 검증(val) 성능이 가장 좋은 모델
- `results.csv`와 학습 그래프를 확인한다. train만 좋아지고 val이 나빠지면 과적합이다.

## 막히는 지점
| 증상 | 확인할 것 |
|---|---|
| CUDA out of memory | Isaac Sim과 학습을 동시에 돌리고 있는지 확인, `--batch` 4 → 2 → 1 |
| `Output exists` | 2번 변수를 새로 지정 (기존 결과를 지울 필요 없음) |
| 모델 경로 없음 | 같은 이름이 있으면 YOLO가 뒤에 `2`, `3`을 붙임. 학습 출력의 실제 경로 확인 |
| `No such file` | 프로젝트 루트에서 실행 중인지 확인 |

## 참고
- 가이드의 CenterPose(퍽 자세 추정) 단계는 YOLO 학습에 필요 없어서 뺐다. 원문: `docs/PRACTICE_GUIDE.md` 4장
- 합성 데이터로만 학습하면 실제 카메라에서 정확도가 떨어질 수 있다. 실제 사진으로 학습하는 방법은 `docs/DATASET_GUIDE.md`와 가이드 6장 참고
