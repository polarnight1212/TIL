## NVIDIA

## CPU GPU

1. CPU : 순서와 분기가 많은 처리 / ROS 노드, 파일, 작업관리
2. GPU : 비슷한 연산을 대량으로 처리 / 영상, 행렬, 물리계산
3. VRAM : GPU가 사용하는 메모리 / 모델, 텍스처, 센서 출력
4. Jectson 메모리 : CPU와 GPU가 시스템 메모리 공유 / 모델과 ROS노드가 함꼐사용
5. TensorRT : 학습한 모델의 추론 최적화 / 지원 장치에서 모델 실행


## isaac Sim , Lab, Ros
1. issac sim : 가상로봇과 센서를 시험 / 장면,센서데이터, 시험 기록
2. issac Lab : 학습 실험과 정책 평가 / 정책 체크포인트, 실험 로그
3. Isaac ROS : ros2에서 가속 기능 실행 / 인식, 위치, 지도 등의 메시지