불완전한 궤도 데이터를 이용한 블랙홀 질량 추론 연구
딥러닝(LSTM)과 전통적 최소자승법(LSM)의 강건성 비교 검증

본 프로젝트는 천체물리학의 전형적인 역문제를 다룹니다. 관측 데이터가 불완전한 상황(Short Arc 및 노이즈)에서 딥러닝 모델이 물리 공식 기반의 전통적 접근법보다 얼마나 더 정확하고 강건하게 정답을 도출하는지 연구했습니다.

1. 연구 배경 및 목적
문제 상황: 실제 천체 관측에서는 공전 주기의 극히 일부분만 관측 가능한 Short Arc 문제와 대기/기기 결함으로 인한 노이즈가 상존합니다.
연구 목적: 극한의 데이터 환경에서 전통적 방식인 LSM(Least Squares Method)의 한계를 확인하고, 시계열 데이터 처리에 특화된 LSTM의 성능을 비교 검증합니다.
<img width="290" height="228" alt="문제 상황 그래프" src="https://github.com/user-attachments/assets/b109a7a5-0d0d-4e8f-8464-fd384311fdb4" />

2. 데이터셋 설계
뉴턴의 만유인력 법칙을 기반으로 시뮬레이션 데이터를 생성했습니다.
물리 공식: accel = -G * M_BH * pos / r**3
제약 조건: 'dt = 0.002' 설정을 통해 전체 궤도의 약 1/4만 생성(Short Arc)하고, 표준편차 2.0AU의 가우시안 노이즈를 주입했습니다.

3. 방법론 비교 분석

A. 전통적 접근 (LSM)
위치를 시간에 대해 미분하여 속도와 가속도를 구합니다.
한계: 미분 과정에서 데이터의 노이즈가 증폭되어 결과값이 불안정하며, 정보 부족으로 인해 원 궤도 근사를 수행할 수밖에 없습니다.

B. 딥러닝 접근 (LSTM)
미분을 하지 않고 점들이 찍히는 궤적의 곡률 패턴을 기하학적으로 학습합니다.
강점: 노이즈에 휘둘리지 않고 궤도의 전체적인 흐름을 파악하여 강건한 추론이 가능합니다.

4. Model Configuration & Hyperparameters

본 모델의 학습 안정성과 성능 극대화를 위해 설정한 하이퍼파라미터 및 전처리 상세 내역입니다.

Data Scaling (StandardScaler): 좌표 데이터와 질량 데이터 간의 수치적 단위 차이를 극복하고 경사 하강법의 수렴 속도를 높이기 위해 표준 정규화(Standardization)를 적용했습니다.

Architecture:
  LSTM Layer: 64 Units (시계열 궤적의 곡률 패턴을 기억하기 위한 충분한 용량 확보)
  Dropout: 0.3 (데이터 노이즈에 대한 강건성(Robustness)을 확보하고 과적합을 방지하기 위한 강력한 제약)
  Dense Layer: 32 Units (ReLU activation)

Training Settings:

  Optimizer: Adam (Learning Rate: 0.001)
  
  Loss Function: Mean Squared Error (MSE)
  
  Early Stopping: Validation Loss가 10 epoch 동안 개선되지 않을 경우 학습을 조기 종료하여 최적의 일반화 성능을 포착했습니다.

6. Technical Discussion: Why AI?
과소결정계(Underdetermined System)의 돌파: Short Arc 상황은 타원의 파라미터를 확정하기 위한 구속 조건이 부족한 전형적인 과소결정계 문제입니다.
LSM은 이를 기하학적 가정(원 궤도 근사)으로 풀려 했으나 실패한 반면, LSTM은 비선형 회귀를 통해 물리적 패턴을 추출했습니다. 미분 없는 추론: 전통적 방식은 미분($dv/dt$) 과정에서 노이즈가 증폭되는 치명적 약점이 있으나, LSTM은 궤적의 기하학적 곡률 패턴을 통째로 학습함으로써 노이즈에 영향을 받지 않는 강건함을 보여주었습니다.

7. 연구 결과
학습 곡선: Train 및 Validation Loss가 안정적으로 수렴하여 과적합 없는 학습을 확인했습니다.
예측 정확도: LSM은 실제 질량보다 3~4배 이상 튀는 오차를 보인 반면, LSTM은 y=x 정답선에 정확히 안착하는 성능을 보였습니다.
<img width="928" height="472" alt="학습의 안정성 그래프" src="https://github.com/user-attachments/assets/d498d31b-2dfa-4631-8184-5c28c1d7923c" />
<img width="1770" height="467" alt="최종 결과 그래프" src="https://github.com/user-attachments/assets/fd59ebca-3982-4c93-91e5-f2454beb0272" />


8. 향후 발전 방향
PINN(Physics-Informed Neural Networks) 도입: 데이터가 극도로 부족한 상황에서도 물리 법칙(에너지 보존 등)을 스스로 준수하며 답을 내놓는 하이브리드 모델로의 확장을 제안합니다.
