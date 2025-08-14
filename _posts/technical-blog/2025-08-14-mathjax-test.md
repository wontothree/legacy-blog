---
title: "MathJax Test"
categories:
  - technical-blog
---
자율 주행 시스템에서 위치 추정은 필수적인 모듈이다.

우리는 모바일 로봇의 2차원 위치 추정 문제를 다룰 것이다.

먼저 우리가 추정하고자 하는 로봇의 자세는 다음과 같다.

1. 2차원 절대 위치 $x$
2. 2차원 절대 위치 $y$
3. heading 각도 $\theta$

우리는 모바일 로봇이 2D LiDAR와 IMU 센서를 탑재하고 있다고 가정한다.

# 표기

Observable 변수

- LiDAR 측정치 $\mathbf{z} = (\mathbf{z}^1, \mathbf{z}^2, \dots, \mathbf{z}^k)$, 여기서 $\mathbf{z}^k$는 $k$번째 측정치이다.
- IMU 측정치 $\mathbf{u}$
- 맵 $m = \left( m^{(1)}, m^{(2)}, \dots, m^{(M)} \right)$
- 위치 추정 상태 분류기 $d$는 위치 추정의 성공 실패 여부를 구분한다.

Hidden 변수

- 로봇의 자세 $\mathbf{x} = (x, y, \theta)$, $m^{(i)} \in \{\text{occupied}, \text{free space}, \text{unknown}\}$는 occupancy grid map이다.
- 측정 클래스 $\mathbf{c} = (c^{(1)}, c^{(2)}, \dots, c^(k))$, $c^{(k)} \in \mathcal{C} = \{ \text{known}, \text{unknown} \}$는 LiDAR 측정치가 기존의 맵에 있는 물체로부터 얻었는지 여부를 나타낸다.
- 위치 추정 상태 $s \in \mathcal{S} = \{ \text{success}, \text{failure} \}$는 위치 추정의 성공 실패 여부를 의미한다. 

# Graphical Model for Pose Tracking

우리의 목표는 시점 $t$에서 로봇의 pose, 측정 클래스, localization 상태 간의 joint posterior distribution를 구하는 것이다.

$$
p(\mathbf{x}_t, \mathbf{c}_t, s_t | \mathbf{u}_{1:t}, \mathbf{z}_{1:t}, d_{1:t}, \mathbf{m})
$$

여기서 $1 : t$는 예를 들어 $\mathbf{u}_{1 : t} = \left( \mathbf{u}_1, \mathbf{u}_2, \dots, \mathbf{u}_t \right)$와 같은 시간 수열을 나타낸다.

최종 식은 다음과 같이 나타낼 수 있다. 계산 과정은 생략한다.

$$
\begin{aligned}
p(\mathbf{x}_t, \mathbf{c}_t, s_t | \mathbf{u}_{1:t}, \mathbf{z}_{1:t}, d_{1:t}, \mathbf{m})

= \; & \eta \underbrace{\sum_{c_t \in \mathcal{C}} p( \mathbf{z}_t | \mathbf{x}_t, \mathbf{c}_t, \mathbf{m}) p(s_t) \int p(\mathbf{x}_t | \mathbf{x}_{t-1}, \mathbf{u}_t) p(\mathbf{x}_{t-1} | \mathbf{u}_{1 : t - 1}, \mathbf{z}_{1 : t - 1}, d_{1 : t - 1}, \mathbf{m}) d\mathbf{x}_{t-1}}_{\text{robot pose}}
\\

&\underbrace{p(\mathbf{z}_t | \mathbf{x}_t, \mathbf{c}_t, \mathbf{m}) p(\mathbf{c}_t)}_{\text{measurement classes}}
\\

&\underbrace{p(d_t | \mathbf{x}_t, s_t, \mathbf{z}_t, \mathbf{m})
\sum_{s_{t-1} \in \mathcal{S}} p(s_t | s_{t-1}, \mathbf{u}_t) p (s_{t-1} | \mathbf{x}_{t-1}, \mathbf{c}_{t-1}, \mathbf{u}_{1 : t - 1}, \mathbf{z}_{1 : z - 1}, d_{1 : t - 1}, \mathbf{m})}_{\text{localization state}}
\end{aligned}
$$

# Fusion of Pose Tracking and Global Localization

우리는 global localization 문제는 INS, LiDAR 측정치, 맵이 주어졌을 때 로봇 pose에 대한 확률 분포를 예측하는 것이라고 가정한다.

우리의 목표는 다음 분포를 예측하는 것이다.

$$
p(\mathbf{x}_t | u_{t - N + 1 : t}, \mathbf{z}_{t - N : t}, \mathbf{m})
$$

여기서 $N$은 global localization에 사용된 사전 step의 수이다.

Importance sampling을 사용한다.

1. 위 식에서 pose를 샘플링한다.
2. 샘플링된 pose가 위 식을 PF와 같이 근사한다고 가정한다.

$$
p\bigl(\mathbf{x}_t \,\big|\, u_{t-N+1\colon t}, \mathbf{z}_{t-N\colon t}, \mathbf{m}\bigr)
\simeq \frac{1}{{}^{G}\! M} \sum_{i=1}^{{}^{G}\! M} 
\delta\!\bigl(\mathbf{x}_t - {}^{G}\mathbf{x}_t^{(i)}\bigr)
$$

여기서 ${}^{G} M$는 샘플이된 pose의 수이고 $\mathbf{x}_t^{i}$는 시점 $t$에서 $i$번째 샘플이된 pose다.

# 구현

1. 주어진 초기 자세로 초기화
2. motion model에 의해 입자 업데이트
3. 신뢰도 전이 모델에 의해 신뢰도 업데이트
4. global localization과 입자 샘플링
5. 클래스 conditional measurement와 decision model을 사용하여 motion model에 의해 업데이트된 입자의 likelihood 계산
6. 클래스 conditional measurement, decision model, 예측 분포를 사용해 global localization에 의해 심플링된 입자의 likelihood 계산
7. 로봇 자세, 센서 측정 클래스, 신뢰도 예측
8. 입자 재샘플링

## 1. 초기화

- 입자들의 자세는 주어진 초기 자세 근처에서 random sampling된다.
- 모든 입자들의 신뢰도는 0.5로 설정된다.

## 2. 움직임 모델

우리는 로봇의 움직임을 이산 시간 상태 방정식으로 모델링한다.

$$
\mathbf{x}_{t+1} = f(\mathbf{x}_t, \mathbf{u}_t)
$$

입자의 자세를 업데이트 할 때는 IMU 측정치 $\mathbf{u}_t$에 Gaussian random noise를 더한다.

$$
\mathbf{x}_t^{(i)} = f(\mathbf{x}_{t-1}^{(i)}, \mathbf{u}_t^{(i)}), \quad \mathbf{u}_t^{(i)} \sim \mathcal{N}(\mathbf{u}_t, {}^{u} \Sigma (\mathbf{u}_t))
$$

여기서 ${}^{u}\Sigma$는 IMU 측정치에 의해 결정되는 대각 행렬이다. IMU 측정ㅊ가 크면 (작으면) 대각 성분은 크다 (작다)

## 3. 신뢰도 전이 모델

기본적으로 위치 정확도는 로봇이 움직임에 따라 감소하기 때문에 우리는 로봇의 움직임에 따라 신뢰도가 감소한다고 가정한다. $\hat{r}_t \leq r_{t-1}$, 여기서 $\hat{r}$은 신로도 전이 모델에 의해 이전 단계에서 업데이트된 신뢰도이다.

우리는 신뢰도 전이 모델에 대해 다음과 같은 휴리스틱을 이용한다.

$$
\hat{r}_t = r_{t-1} \left(1 - \sum_i \alpha_i \Delta_i^2 \right)
$$

여기서 $\alpha_i$는 임의의 양수, $\Delta_i$는 IMU로 측정한 시간 간격 동안의 변위이다. 예를 들어, $\Delta d, \Delta \theta$.

우리는 $\alpha_i = 0$로 둔다.

## 4. Global Localization

Global localization을 위해 free-space feature를 사용한다.

### Free-space feature

### Pose sampling

## 5, 6. Likelihood 계산

## 7. Estimation

### 로봇 자세

로봇의 자세는 두 입자 집합의 가중합으로 예측된다.

$$
\mathbf{x}_t
= \sum_{i=1}^{{}^P\!M} {}^P\!\omega_t^{(i)}\, {}^P\mathbf{x}_t^{(i)}
+ \sum_{i=1}^{{}^G\!M} {}^G\!\omega_t^{(i)}\, {}^G\mathbf{x}_t^{(i)}
$$

여기서 likelihood는 사전에 정규화된다.

$$
\sum_{i=1}^{{}^P\!M} {}^P\!\omega_t^{(i)} + \sum_{i=1}^{{}^G\!M} {}^G\!\omega_t^{(i)} = 1

$$

### 센서 측정치 클래스

최대의 likelihood를 가진 입자 ${}^{\text{ML}} \mathbf{x}_t$를 추출한다. 센서 측정치 클래스들에 대한 확률 분포는 다음과 같이 계산된다.

$$
\eta \; p\bigl(\mathbf{z}_t \,\big|\, {}^{\text{ML}}\mathbf{x}_t, {}^{\text{ML}}\mathbf{c}_t, \mathbf{m}\bigr) \; p({}^{\text{ML}}\mathbf{c}_t)
$$

이것은 빠르게 계산될 수 잇기 때문에 메모리 비용을 줄이기 위해 likelihhod 계산 이후에 이 계산을 수행한다.

### 신뢰도

최대의 likelihood를 가진 입자를 사용하여 신뢰도를 예측한다. 예측된 신뢰도는 다음과 같이 계산한다.

$$
\eta \; p\bigl(d_t \,\big|\, {}^{\text{ML}}\mathbf{x}_t, {}^{\text{ML}}s_t = \text{success}, \mathbf{z}_t, \mathbf{m}\bigr) \; p({}^{\text{ML}}s_t = \text{success})
$$

## 8. 재샘플링

$$
b^{(i)} =
\begin{cases}
\sum_{j=1}^i {}^P\!\omega_t^{(j)}, &\quad \text{if } j \le {}^G\!M \\
\sum_{j=1}^{{}^G\!M} {}^P\!\omega_t^{(j)} + \sum_{j={}^G\!M + 1}^i {}^P\!\omega_i^{(j - {}^G\!M)}, &\quad \text{if } j > {}^G\!M
\end{cases}
$$

## 참조

[1] [Naoki Akai. Reliable Monte Carlo Localization for Mobile Robots. In Journal of Field Robotics, 2023.](https://arxiv.org/abs/2205.04769)

[2] [A. Millane, H. Oleynikova, J. Nieto, R. Siegwart, and C. Cadena. Free-space features: Global localization in 2D laser SLAM using distance function maps. In IROS, 2019.](https://arxiv.org/abs/1908.01863)
