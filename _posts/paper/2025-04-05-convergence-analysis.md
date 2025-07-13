---
title: "[OPT] Convergence Analysis of ODE Models for Accelerated First-Order Methods"
categories:
  - paper
---
- novel methodology that systematically analyzes ODE models for first-roder optimization methods
- establish convergence rates of various accelerated gradient flow models.
- Lagrangian dual of a related version of continuous-time PEP를 기반으로 만들어졌다.
- 수렴 속도를 증명하는 작업을 특정 적분 커널의 positive semidefiniteness을 검증하는 문제로 변환한다.
- 전통적인 ODE models의 convergence analysis에서 적절한 Lyapunov functions을 찾는 것은 어려운 문제이다. 하지만 이 방법론은 특정한 적분 커널의 positive semidefinite을 보이기만 하면 되기 때문에 쉽게 ODE models의 수렴 속도를 증명할 수 있다.

# Preliminaries: functional analysis

## Function spaces

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  <span style="color: #000000; background-color:#DB4455; font-weight: bold;">$C([0, T]; \mathbb{R}^d)$</span> : the set of continuous functions from $[0, T]$ to $\mathbb{R}^d$

  <br>

  <span style="color: #000000; background-color:#DB4455; font-weight: bold;">$C^1([0, T]; \mathbb{R}^d)$</span> : the set of continuously differentiable functions from $[0, T]$ to $\mathbb{R}^d$
</div>

일반적인 stability의 정의에서 모든 t에 대해 다루는 반면 이 논문에서는 전반적으로 유한한 시간 구간까지를 다룬다. 문제를 조금 더 단순화한 것이다.

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  the space <span style="color: #000000; background-color:#DB4455; font-weight: bold;">$L^2([0, T]; \mathbb{R}^d)$</span> : the set of all measurable functions $f : [0, T] \rightarrow \mathbb{R}^d$ that satisfy
  
  $$
  \int_0^T \vert\vert f(x) \vert\vert_{\mathbb{R}^d}^2 dx < \infty
  $$
</div>

Function의 measurability는 function의 continuity의 확장이다. Measurable해야 적분이 정의된다.

<br>

Then $L^2([0, T]; \mathbb{R}^d)$ is a Hilbert space equipped with an inner product and a norm

$$
\langle f, g \rangle_{L^2([0, T]; \mathbb{R}^d)} = \int_0^T \langle f(t), g(t)\rangle_{\mathbb{R}^d} dt, \quad \vert\vert f \vert\vert_{L^2([0, T]; \mathbb{R}^d)} = \sqrt{\langle f, f \rangle_{L^2([0, T]; \mathbb{R}^d)}}
$$

Hilbert Space는 inner proudct와 norm이 정의된다. Hilbert space는 $L^2$를 포함한다.

## Integral operators

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  An <span style="color: #000000; background-color:#DB4455; font-weight: bold;">integral operator</span> is a linear operator that maps a function $f$ to another function $K f$ given by

  $$
  (K f)(t) = \int_0^T k(t, \tau) f(\tau) d\tau
  $$

  where $k: [0, T] \rightarrow \mathbb{R}$ is the <span style="color: #000000; background-color:#DB4455; font-weight: bold;">associated integral kernel</span>.
</div>

Integral operator가 linear함을 증명해보자.

$$
\begin{align*}
  (K (f + g))(t) &= \int_0^T k(t, \tau) (f + g)(\tau) d\tau \\
  &= \int_0^T k(t, \tau) (f(\tau) + g(\tau)) d\tau \\
  &= \int_0^T k(t, \tau) f(\tau) d\tau + \int_0^T k(t, \tau) g(\tau) d\tau \\
  &= (K f)(t) + (K g)(t)
\end{align*}
$$

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  A <span style="color: #000000; background-color:#DB4455; font-weight: bold;">Hilbert-Schmidt kernel</span> is an integral kernel $k$ that is square integrable

  $$
  k \in L^2([0, T]; \mathbb{R})
  $$

  <span style="color: #000000; background-color:#DB4455; font-weight: bold;">Hilbert-Schmidt integral operator</span> : the associated integral operator $K$ when $k$ is a Hilbert-Schmidt kernel.
</div>

$L^2$ space라는 것 자체가 특별하기 때문에 이름이 붙은 것이다.

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  Hilbert-Schmidt kernel k is symmetric : $k(t, \tau) = k (\tau, t)$ for all $t, \tau \in [0, T]$
  <br>
  The associated operator is symmetric : $\langle Kf, g \rangle = \langle f, K g \rangle$ for all $f, g \in L^2([0, T]; \mathbb{R}^d)$
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  If a Hilbert-Schmidt kernel k is symmetric, then the associated operator is also symmetric.
</div>

*Proof*. $d = 1$일 때에 대해 증명해보자.

## Positive semidefinite kernels

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  A <span style="color: #000000; background-color:#DB4455; font-weight: bold;">symmetric operator $K$ on a Hilbert space is said to be positive semidefinite</span> and denoted by $K \succeq 0$ if
  
  $$
  \langle K f, f \rangle \geq 0
  $$
  
  for all $f$ in the Hilbert space.
</div>

Matrix에 대한 positive semidefinite의 정의와 유사하다. Matrix의 positive semidefinite은 다음과 같이 정의된다.

영벡터가 아닌 임의의 열벡터 $x$와 대칭 행렬 $A$애 대해 다음이 성립한다면 $A$는 positive semidefinite이다.

$$
x^{\top}Ax \geq 0
$$

$\langle Ax, x\rangle \geq 0$와 같이 표현할 수도 있다.

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  When a symmetric kernel $k$ is associated with a positive semidefinite operator $K$, i.e.,

  $$
  \int_0^T\int_0^T k(t, \tau) f(t) f(\tau) dt d\tau \geq 0
  $$

  for all $f \in L^2([0, T]; \mathbb{R})$, we say that the <span style="color: #000000; background-color:#DB4455; font-weight: bold;">kernel $k$ is integrally positive semidefinite</span> and denoted it by $k \succeq 0$.
</div>

첫 번째 정의는 Hilbert space에서 operator에 대한 정의인 반면, 두 번째 정의는 $L^2$ space에서 kernel에 대한 정의이다. operator의 positive semidefinite을 통해 kernel의 positive semidefinite을 정의한다.

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  For continuous kernels, positive semidefiniteness of $k$ is equivalent to the following condition:

  $$
  \sum_{i=1}^{n} \sum_{j=1}^n c_i c_j k(t_i, t_j) \geq 0
  $$

  for any $t_1, \dots, t_n \in [0, T]$ and $c_1, \dots, c_n \in \mathbb{R}$, given $n \in \mathbb{N}$.
</div>

## Proposition 1

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  Basic properties of continuous positive semidefinite kernels:

  <br>

  (a) For $\alpha \in C([0, T]; \mathbb{R})$, the kernel $k(t, \tau) = \alpha(t) \alpha(\tau)$ is positive definite.

  <br>

  (b) For $k_1, k_2 \succeq 0$, their product $k(t, \tau) = k_1(t, \tau) k_2(t, \tau)$ is positive definite.

  <br>

  (c) For $k \succeq 0$, its anti-transpose $(t, \tau) \mapsto k(T - \tau, T - t)$ is also positive semidefinite.

  <br>

  (d) If $\alpha \in C^1([0, T]; \mathbb{R}_{\geq 0})$ is an increasing function on $[0, T]$, then the symmetric kernel $k$ defined as $k(t, \tau) = \alpha(\tau)$ for $t \leq \tau$ is positive semidefinite.

  <br>

  (e) For $k \succeq 0$, we have $k(t, t) \geq 0$ for all $t \in [0, T]$. 
</div>

<br>
<br>
<br>

# Continuous PEP

이 논문은 first-order methods의 ODE 모델에 대한 수렴률을 분석하는 새로운 프레임워크를 제시한다. (Continuous-time performance estimation problem (continuous PEP)). 이를 설명하기 위해서 accelerated gradient flow를 예로 들자.

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  Nesterov’s Accelerated Gradient Method에 대한 limiting ODE는 다음과 같다. 임의의 함수 $f$에 대해

  $$
  \ddot{X} + \dfrac{3}{t} \dot{X} + \nabla f(X) = 0
  $$

  with initial conditions

  $$
  X(0) = x_0, \quad \dot{X}(0) = 0
  $$
</div>

<br>

만약 다음 부등식을 만족하는 $\rho$가 존재한다면 $f$가 $f(x^*)$로 수렴한다. 따라서 우리의 목표는 다음 부등식을 만족하는 $\rho$를 찾는 것이다.

$$
f(X(T)) - f(x^*) \leq \rho \vert\vert x_0 - x^* \vert\vert^2
$$

$x_0$가 $x^*$에 충분히 가까우면 $f(X(T))$가 $f(x^*)$에 충분히 가깝다.

Lyapunov stable의 정의와 유사하지만 임의의 $f$가 있다는 것만 다르다.

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  우리는 이 $\rho$를 찾기 위해 다음의 최적화 문제(<span style="color: #000000; background-color:#fff5b1;">Exact PEP</span>)를 구성한다.

  $$
  \begin{align*} & \underset{
    \substack{
      f \in \mathcal{F}_0(\mathbb{R}^d; \mathbb{R}) \\
      X \in C^1([0, T]; \mathbb{R}^d)
    }
  }{\max} \;\; \dfrac{f(X(T)) - f(x^*)}{\vert\vert x_0 - x^* \vert\vert^2} \\ & \mathrm{subject \; to} \;\;\ddot{X} + \dfrac{1}{t} \dot{X} + \nabla f(X) = 0
  \end{align*}
  $$

  여기서 $\mathcal{F}_0(\mathbb{R}^d; \mathbb{R})$는 the set of continuously differentiable $\mu$-strongly convex functions on $\mathbb{R}^d$로 설정한다.
</div>

함수 $f$가 도입되었기 때문에 위와 같이 최적화 문제로 formulation을 할 수 있는 것이다.

Convex function은 $\mu$-strongly convex function을 포함한다. 0-strongly convex function은 convex function과 일치한다.

Exact PEP의 결과값을 $\rho$로 설정할 수 있기 때문에 Exact PEP를 푸는 것은 유용하다.

## Relaxed PEP

하지만 최적화 변수인 임의의 함수 $f$를 모르기 때문에 Exact PEP는 풀기 어렵다. 따라서 우리는 조건

$$
f \in \mathcal{F}_0(\mathbb{R}^d; \mathbb{R})
$$

을 충분조건인 등식과 부등식으로 대체함으로써 Exact PEP 대신 relax된 최적화 문제(<span style="color: #000000; background-color:#fff5b1;">Relaxed PEP</span>)를 다룬다.

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  한편 AGM ODE는 다음과 같이 continuous-time dynamical system으로 표현될 수 있다.

  $$
  \dot{X}(t) = - \int_0^t H(t, \tau) \nabla f(X(\tau)) d\tau
  $$

  by setting $H(t, \tau) = \dfrac{r^3}{t^3}$. 여기서 $H(t, \tau)$ is called the H-kernel.
</div>

<br>

We define $\varphi : [0, T] \rightarrow \mathbb{R}$ and $\gamma : [0, T] \rightarrow \mathbb{R}^d$

$$
\varphi (t) = \dfrac{1}{\vert\vert x_0 - x^* \vert\vert^2} (f(X(t)) - f(x^*)), \quad \gamma (t) = \dfrac{1}{\vert\vert x_0 - x^* \vert\vert^2} \nabla f(X(t))
$$

<br>

Using the chain rule and the convexity of $f$, we can derive the following equality and inequality:

$$
\begin{align*}
0 &= \dot{\varphi}(t) + \Big\langle \gamma(t), \int^t_0 H(t, \gamma) \gamma(r) dr \rangle, \\
0 &\geq \varphi(t) + \Big\langle \gamma(t), v + \int^t_0 \int^t_\tau H(s, \tau) \gamma(\tau) \; ds \; d\tau \Big\rangle
\end{align*}
$$

where $v = (x^* - x_0)/\vert\vert x_0 - x^* \vert\vert$. 이는 $f \in \mathcal{F}_0(\mathbb{R}^d; \mathbb{R})$의 충분조건이다.

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  따라서 Relatex PEP는 다음과 같다.

  $$
  \begin{align*}
  & \underset{ \varphi, \gamma, v}{\max} \;\; \varphi (t) \\
  & \mathrm{subject \; to} \;\;\; t \in (0, T), \\
  & 0 = \dot{\varphi}(t) + \Big\langle \gamma(t), \int^t_0 H(t, \gamma) \gamma(r) dr \rangle \\
  & 0 \geq \varphi(t) + \Big\langle \gamma(t), v + \int^t_0 \int^t_\tau H(s, \tau) \gamma(\tau) \; ds \; d\tau \Big\rangle \\
  \end{align*}
  $$
</div>

<br>

val(Relaxed PEP) $\geq$ val(Exact PEP) 이므로 $\rho = $ val(Relaxed PEP)로 잡을 때 이는 더 느슨한 upper bound라고 할 수 있다.

## Lagrangian dual of relaxed PEP

To obtain an upper bound on val(Relaxed PEP), we use Lagrangian duality.

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  Let <span style="color: #000000; background-color:#fff5b1;">Lagrange multipliers</span>

  $$
  \lambda_1 \in C^1 ([0, T]; \mathbb{R}), \;\;\; \lambda_2 \in C ([0, T]; \mathbb{R}_{\geq 0})
  $$

  which are continuous and differentiable to ensure that the dual problem is well-defined.
</div>

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  Then we define the <span style="color: #000000; background-color:#fff5b1;">Lagrangian function</span> and express in terms of the inner products in function spaces

  $$
  \begin{align*}
  \mathcal{L} (\varphi, \gamma, v; \lambda_1, \lambda_2)
  &= \varphi (T) - \int_0^T \lambda_1(t) \left( \dot{\varphi}(t) + \Big\langle \gamma (t), \int_0^t H(t, \tau) \gamma(\tau) d\tau \Big\rangle \right) dt \\
  & \;\;\;\; - \int_0^T \lambda_2(t)\left(\varphi(t) + \Big\langle \gamma(t), v + \int_0^t \int_\tau^t H(s, \tau) \gamma(\tau) ds \; d\tau \Big\rangle \right) dt \\
  &= \varphi (T) - \langle \lambda_1, \dot{\varphi}_{L^2([0, T]; \mathbb{R}^d)} \rangle - \langle \lambda_2(t) v, \gamma(t)\rangle_{L^2([0, T]; \mathbb{R}^d)} \\
  & \;\;\;\; -\dfrac{1}{2} \langle K_{\gamma}, \gamma \rangle_{L^2([0, T]; \mathbb{R}^d)} - \langle \lambda_2(t)v, \gamma(t) \rangle_{L^2([0, T]; \mathbb{R}^d)}
  \end{align*}
  $$

  where $K$ is the Hibert-Schmidt integral operator with the symmetric kernel k defined by

  $$
  k(t, \gamma) = \lambda_1(t) H(t, \tau) + \lambda_2 \int_{\tau}^t H(s, \tau) ds, \;\;\; t \geq \tau
  $$
</div>

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">
  The <span style="color: #000000; background-color:#fff5b1;">dual function</span> is defined as

  $$
  \begin{align*}

  \text{Dual}(\lambda_1, \lambda_2)
  &= \sup_{\varphi, \gamma, v} \mathcal{L}(\varphi, \gamma, v; \lambda_1, \lambda_2) \\
  &=
  \begin{cases}
  \inf_{\nu \in (0, \infty)} [\nu: S_{\lambda_1, \lambda_{2, \nu}} \succeq 0] & \text{if} \quad \lambda_1(0) = 0, \; \lambda_1(T) = 1, \; \dot{\lambda}_1(t) = \lambda_2(t) \\
  \infty & \text{otherwise}
  \end{cases}

  \end{align*}
  $$

  where $S_{\lambda_1, \lambda_2, \nu}$ is a symmetric kernel on $[0, T]^2$ (<span style="color: #000000; background-color:#fff5b1;">PEP kernel</span>) given by

  $$
  S_{\lambda_1, \lambda_2, \nu} (t, \tau) = \nu \left( \lambda_1(t) H(t, \tau) + \lambda_2(t) \int_{\tau}^t H(s, \tau) ds \right) - \dfrac{1}{2} \lambda_2(t) \lambda(\tau), \;\;\; t \geq \tau
  $$
</div>

<br>

Given $\nu_{\text{feas}} \in (0, \infty)$, suppose $S_{\lambda_1, \lambda_2, \nu_{\text{feas}}}$ is positive semidefinite with appropriate multiplier functions $\lambda_1$ and $\lambda_2$, and then $\nu_{\text{feas}}$ is a feasible solution of that minimization problem of dual problem.

Thus

$$
\text{Dual} (\lambda_1, \lambda_2) \leq \nu_{\text{feas}}
$$

<br>

Therefore, by weak duality,

$$
\text{val(Relatex PEP)} \leq \text{val(Exact PEP)} \leq \text{Dual} (\lambda_1, \lambda_2) \leq \nu_{\text{feas}}
$$

이걸 이용하면 우리는 알려진 AGM ODE의 convergance guarantee $\rho = \dfrac{2}{T^2}$을 얻을 수 있다.

## Proposition 2

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
  AGM ODE achieves the convergence rate

  $$
  f(X(T)) - f(x^*) \leq \rho \vert\vert x_0 - x^* \vert\vert^2
  $$

  with $\rho = \dfrac{2}{T^2}$.
</div>

<br>

*Proof*. Let multiplier functions

$$
\lambda_1(t) = \dfrac{t^2}{T^2}, \quad \lambda_2(t) = \dfrac{2t}{T^2}
$$

and then PEP kernel is

$$
\begin{align*}
  S_{\lambda_1, \lambda_2, \nu} (t, \tau) &= \nu \left( \lambda_1(t)H(t, \tau) + \lambda_2(t) \int_{\tau}^{t}H(s, \tau) ds \right) - \dfrac{1}{2} \lambda_2(t) \lambda_2(\tau) \\
  &= \nu \left( \dfrac{t^2}{T^2} \dfrac{\tau^3}{t^3} + \dfrac{2t}{T^2} \int_{\tau}^{t} \dfrac{\tau^3}{s^3} ds \right) - \dfrac{1}{2} \dfrac{2t}{T^2} \dfrac{2\tau}{T^2} \\
  &= \nu \left( \dfrac{\tau^3}{t T^2} + \dfrac{2t}{T^2} \left( \dfrac{1}{2\tau^2} - \dfrac{1}{2t^2} \right) \right) - \dfrac{2t\tau}{T^4} \\
  &= \left( \nu - \dfrac{2}{T^2} \right) \dfrac{t \tau}{T^2}
\end{align*}
$$

Since the kernel $(t, \tau) \mapsto t\tau$ is nonzero and positive semdidefinite by *Proposition 1*,

$$
S_{\lambda_1, \lambda_2, \nu}(t, \tau) \succeq 0 \; \Leftrightarrow \; \nu \geq \dfrac{2}{T^2}
$$

Also since $\lambda_1(0) = 0, \; \lambda_1(T) = 1, \; \dot{\lambda}_1(t) = \lambda_2(t)$,

$$
\begin{align*}
  \text{Dual}(\lambda_1, \lambda_2)
  &= \inf_{\nu \in (0, \infty)} \left[\nu: S_{\lambda_1, \lambda_{2, \nu}} \succeq 0 \right] \\
  &= \inf_{\nu \in (0, \infty)} \left[\nu: \nu \geq \dfrac{2}{T^2} \right] \\
  &= \dfrac{2}{T^2}
\end{align*}
$$

, which establishes the convergence guarantee with $\rho = \dfrac{2}{T^2}$.

<!-- <span style="color: #000000; background-color:#fff5b1;">

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">

<div class="latex-container"><div> -->
