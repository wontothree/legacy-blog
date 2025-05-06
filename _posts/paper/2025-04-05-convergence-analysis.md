---
title: "[OPT for ML] Convergence Analysis of ODE Models for Accelerated First-Order Methods"
categories:
  - paper
---
- novel methodology that systematically analyzes ODE models for first-roder optimization methods
- establish convergence rates of various accelerated gradient flow models.
- Lagrangian dual of a related version of continuous-time PEP를 기반으로 만들어졌다.
- 수렴 속도를 증명하는 작업을 특정 적분 커널의 positive semidefiniteness을 검증하는 문제로 변환한다. (?)

# Continuous-time Performance Estimation Problem

Nesterov’s Accelerated Gradient Method에 대한 limiting ODE

$$
\ddot{X} + \dfrac{1}{t} \dot{X} + \nabla f(X) = 0
$$

with initial conditions

$$
X(0) = x_0, \;\;\; \dot{X}(0) = 0
$$

만약 다음 부등식을 만족하는 $\rho$가 존재한다면 $f$가 $f(x^*)$로 수렴한다. 따라서 우리의 목표는 다음 부등식을 만족하는 $\rho$를 찾는 것이다.

$$
f(X(T)) - f(x^*) \leq \rho \vert\vert x_0 - x^* \vert\vert^2
$$

우리는 이 $\rho$를 찾기 위해 다음의 최적화 문제(Exact PEP)를 구성한다.

<div class="latex-container">
  $$
  \begin{align*} & \underset{
    \substack{
      f \in \mathcal{F}_0(\mathbb{R}^d; \mathbb{R}) \\
      X \in C^1([0, T]; \mathbb{R}^d)
    }
  }{\max} \;\; \dfrac{f(X(T)) - f(x^*)}{\vert\vert x_0 - x^* \vert\vert^2} \\ & \mathrm{subject \; to} \;\;\ddot{X} + \dfrac{1}{t} \dot{X} + \nabla f(X) = 0
  \end{align*}
  $$
</div>

여기서 $\mathcal{F}_0(\mathbb{R}^d; \mathbb{R})$는 the set of continuously differentiable $\mu$-strongly convex functions on $\mathbb{R}^d$.

## Relaxation of PEP

Exact PEP의 결과값을 $\rho$로 설정할 수 있기 때문에 Exact PEP를 푸는 것은 유용하다. 하지만 최적화 변수인 함수 $f$를 모르기 때문에 Exact PEP는 풀기 어렵다. 따라서 우리는 조건

$$
f \in \mathcal{F}_0(\mathbb{R}^d; \mathbb{R})
$$

을 충분조건으로 대체함으로써 Exact PEP를 relax된 최적화 문제(Relaxed PEP)를 다룬다.

<div class="latex-container">
  $$
  \begin{align*}
  & \underset{ \rho, \gamma, v}{\max} \;\; \dfrac{f(X(t)) - f(x^*)}{\vert\vert x_0 - x^* \vert\vert^2} \\
  & \mathrm{subject \; to} \;\;\; t \in (0, T), \\
  & 0 = \dot{\rho}(t) + \Big\langle \gamma(t), \int^t_0 H(t, \gamma) \gamma(r) dr \rangle \\
  & 0 \geq \gamma(t) + \Big\langle \gamma(t), v + \int^t_0 \int^t_\tau H(s, \tau) \gamma(\tau) \; ds \; d\tau \Big\rangle \\
  \end{align*}
  $$
</div>

val(Relaxed PEP) $\geq$ val(Exact PEP)

## Lagrangian dual of relaxed PEP

Let Lagrange multipliers

$$
\lambda_1 \in C^1 ([0, T]; \mathbb{R}), \;\;\; \lambda_2 \in C ([0, T]; \mathbb{R}_{\geq 0})
$$

which are continuous and differentiable to ensure that the dual problem is well-defined.

Then we define the Lagrangian function as

$$
\begin{align*}
\mathcal{L} (\varphi, \gamma, v; \lambda_1, \lambda_2)
&= \varphi (T) - \int_0^T \lambda_1(t) \left( \dot{\varphi} + \Big\langle \gamma (t), \int_0^t H(t, \tau) \gamma(\tau) d\tau \Big\rangle \right) dt \\
& \;\;\;\; - \int_0^T \lambda_2(t)\left(\varphi(t) + \Big\langle \gamma(t), v + \int_0^t \int_\tau^t H(s, \tau) \gamma(\tau) ds \; d\tau \Big\rangle \right) dt \\
&= \varphi (T) - \langle \lambda_1, \dot{\varphi}_{L^2([0, T]; \mathbb{R}^d)} \rangle - \langle \lambda_2(t) v, \gamma(t)\rangle_{L^2([0, T]; \mathbb{R}^d)} \\
& \;\;\;\; -\dfrac{1}{2} \langle K_{\gamma}, \gamma \rangle_{L^2([0, T]; \mathbb{R}^d)} - \langle \lambda_2(t)v, \gamma(t) \rangle_{L^2([0, T]; \mathbb{R}^d)}
\end{align*}
$$

where K is the Hibert-Schmidt integral operator with the symmetric kernel k defined by

$$
k(t, \gamma) = \lambda_1(t) H(t, \tau) + \lambda_2 \int_{\tau}^t H(s, \tau) ds, \;\;\; t \geq \tau
$$

The dual function is defined as

$$
\begin{align*}

\text{Dual}(\lambda_1, \lambda_2)
&= \sup_{\varphi, \gamma, v} \mathcal{L}(\varphi, \gamma, v; \lambda_1, \lambda_2) \\
&=
\begin{cases}
\inf_{\nu \in (0, \infty)} [\nu: S_{\lambda_1, \lambda_{2, \nu}} \succeq 0] & \text{if } \lambda_1(0) = 0, \; \lambda_2(T) = 1, \; \dot{\lambda}_1(t) = \lambda_2(t) \\
\infty & \text{otherwise}
\end{cases}

\end{align*}
$$

where $S_{\lambda_1, \lambda_2, \nu}$ is a symmetric kernel on $[0, T]^2$ (PEP kernel) given by

$$
S_{\lambda_1, \lambda_2, \nu} (t, \tau) = \nu \left( \lambda_1(t) H(t, \tau) + \lambda_2(t) \int_{\tau}^t H(s, \tau) ds \right) - \dfrac{1}{2} \lambda_2(t) \lambda(\tau), \;\;\; t \geq \tau
$$

Gieven $\nu_{\text{feas}} \in (0, \infty)$, suppose $S_{\lambda_1, \lambda_2, \nu_{\text{feas}}}$ is positive semidefinite with appropriate multiplier functions $\lambda_1$ and $\lambda_2$, and then $\nu_{\text{feas}}$ is a feasible solution of that minimization problem of dual problem.

Thus

$$
\text{Dual} (\lambda_1, \lambda_2) \leq \nu_{\text{feas}}
$$

Therefore

$$
\text{val(Relatex PEP)} \leq \text{val(Exact PEP)} \leq \text{Dual} (\lambda_1, \lambda_2) \leq \nu_{\text{feas}}
$$

## Reparametrization

지금까지는 $\mu = 0$ 인 non-strongly convex 함수에 대해 제안된 방법을 적용해왔지만, 아래와 같은 재매개변수화 기법을 통해

$$
\hat{f}(x) := f(x) - \dfrac{\mu}{2} \vert\vert x - x_0 \vert\vert^2
$$

μ-strongly convex 함수에도 동일한 방법을 적용할 수 있다.

# Example

Continuous PEP를 이용해서 각 ODE 모델의 수렴 속도를 분석해보자.

## AGM-SC ODE

ODE of Nestrov's Acceleration Gradient Method for Strongly Convex Objective

$$
\ddot{X} + 2\sqrt{\mu} \dot{X} + \nabla f(X) = 0
$$

## Unified AGM ODE

ODE unifying AGM ODE and AGM-SC ODE

$$
\ddot{X} + \dfrac{\sqrt{\mu}}{2} (\tan h_t + 3\cot h_t) \dot{X} + \nabla f(X) = 0
$$

## TMM ODE

ODE of triple momentum method

$$
\ddot{X} + 3\sqrt{\mu} \dot{X} + 2 \nabla f(X) = 0

$$

## ITEM ODE

ODE of the information-theoretic exact method

$$
\ddot{X} + 3\sqrt{\mu} \cot h_t \dot{X} + 2 \nabla f(X) = 0
$$
