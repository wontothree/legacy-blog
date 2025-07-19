---
title: "[Optimal Control] MPC Evangelist"
categories:
  - technical-blog
---
Introduction to MPC

- Model Predictive Control is an optimal control strategy based on numerical optimiation.
- Future control inputs and future plant responses are predicted using a system model and optimized at regular intervals with respect to a performance index.
- MPC provides a systematic method of dealing with constraints on inputs and states. The constraints represent limitations on actuators and plant states arising from physical, economic, or safety constraints.

# Predictive Control Strategy

A model predictive control law contains the basic components of prediction, optimization and receding horizon implementation.

## Prediction

The future response of the controlled plant is predicted using a dynamic model.

This course is concerned mainly with the case od discrete-time linear systems with state-space representation

$$
x(k+1) = Ax(k) + Bu(k)
$$

where $x(k)$ and $u(k)$ are the model state and input vectors at the kth sampling instant.

Notation

$$
\mathbf{u}(k) =
\begin{bmatrix}
  u(k \vert k)  \\
  u(k+1 \vert k)  \\
  \vdots \\
  u(k + N - 1 \vert k)
\end{bmatrix},
\;\;\;
\mathbf{x}(k) =
\begin{bmatrix}
  u(k + 1 \vert k)  \\
  u(k + 2 \vert k)  \\
  \vdots \\
  u(k + N \vert k)
\end{bmatrix}
$$

$u(k + i \vert k)$ and $x(k + i \vert k)$ : input and state vectors at time $k + i$ that are predicted at time $k$

Prediction model

$$
x(k + i \vert k) = A x(k + i \vert k) + B u(k + i \vert k), \;\;\; i = 0, 1, \dots
$$

with initial condition $x(k \vert k) = x(k)$

## Optimization

The predictive control feedback law is computed by minimizing a predicted performance cost, which is defined in terms of the predicted sequences $\mathbf{u}, \mathbf{x}$.

This course is mainly concerned with the case of quadratic cost, for which the predicted cost has the general form:

$$
J(k) = \sum^N_{i = 0} \left[ x^T(k + i \vert k)Qx(k + i \vert k) + u^T(k + i \vert k)R u(k + i \vert k) \right]
$$

where $Q, R$ are positvie definite(or semi-definite for Q) matrices.

The optimal inpute sequence for the problem of minimizing $J(k)$ is denoted $u^*(k)$:

$$
\mathbf{u}^* = \underset{u}{\mathrm{argmin}} J(k)
$$

If the plant is subject to input and state constraints, then these could be included in the optimization as equivalent constraints on $\mathbf{u}(k).$

## Receding horion implementation

Only the first element of the optimal predicted inpute sequence $\mathbf{u}^*$ is input to the plant

$$
u(k) = u^*(k \vert k)
$$

The process of computing $\mathbf{u}^{*}(k)$

by minimizing the predicted cost and implementing the first element of $\mathbf{u}^{*}$

is then repeated at each sampling instant $k = 0, 1, \dots$.

For this reason the optimization defining $\mathbf{u}^*$ is known as an online optimizaiton.

The prediction horizon remains the same length despite the repetition of the optimization at future time instants. (receding horizon strategy)

**Features of receding horizon strategy**

1. Since the state predictions $\mathbf{x}$ and hence the optimal input sequence $\mathbf{u}^*$ depend on the current state measurement $x(k)$, this procedure introduces feedback in the MPC law, thus providing a degree of robustness to modeling errors and uncertainty.
2. By continually shifting the horizon over which future inputs are optimized, it attempts to compensate for the fact that this horizon is finite.

## Historical development

- 1960-70 : Receding horizon approaches were used to define computational methods for optimal control problems that have no closed-form solution.
- 1980 : a means exploiting continual improvements in computational resources to improve performance
- Recent : a eneral technique for deriving stabilizing controllers for constrained systems. And the availability of faster computers and improvements in computational efficiency of predictive controllers have extended its range of applications to include fast sampling systems.

# Prediction Model

A very wide class of plant model can be incorporated in a predictive control strategy.

- Plant models: linear, nonlinear, discrete, or continuous-time
- Prediction models: deterministic, stochastic, or fuzzy

## Linear plant model

For linear systems, the dependence of predictions $\mathbf{x}(k)$ on $\mathbf{u}(k)$ is linear.

A quadratic predicted cost is therefore a quadratic function of the input sequence $\mathbf{u}(k)$.

Thus $J(k)$ can be expressed as a function of $\mathbf{u}$ in the form

$$
J(k) = \mathbf{u}^T(k)H\mathbf{u}(k) + 2f^T \mathbf{u}(k) + g
$$

where $H$ is a constant positive definite (or possibly positive semidefinite) matrix, and f, g are respectively a vector and scalar which depend on $x(k)$.

Linear input and state constraints likewise imply linear constraints on $\mathbf{u}(k)$ which can be expressed

$$
A_c \mathbf{u}(k) \leq b_c
$$

where $A_c$ is a constant matrix and, depending on the form of the constraints, the vector $b_c$ may be a function of $x(k)$.

The online MPC optimization thereforece comprises the minimization over $\mathbf{u}$ of a quadratic objective subject to linear constraints (quadratic programming problem):

$$
\underset{u}{\mathrm{minimize}} \;\;\; \mathbf{u}^T H \mathbf{u} + 2f^T \mathbf{u} \\
\mathrm{subject \; to} \;\;\; A_c \mathbf{u} \leq b_c
$$

Given that $H$ is a positive definite matrix and the constraints are linear, it is easy to show the optimization problem is a convex problem.

Matlab's QP solver : 2GHZ PC - 10ms - 10 variables and 100 constrains

## Nonlinear plant model

If a nonlinear prediction model is employed, then due to the nonlinear dependence of the state predictions $\mathbf{x}(k)$ on $\mathbf{u}(k)$, the MPC optimization problem is significantly harder that for the linear model case.

This is because the cost in equation, which can be written as $J(\mathbf{u}(k), x(k))$, and the constraints, $g(\mathbf{u}(k), x(k)) \leq 0$, are in general nonconvex functions of $\mathbf{u}(k)$, so that the optimization problem :

$$
\underset{u}{\mathrm{minimize}} \;\;\; J(\mathbf{u}, x(k)) \\
\mathrm{subject \; to} \;\;\; g(\mathbf{u}, x(k)) \leq 0
$$

becomes nonconvex nonlinear programming (NLP) problem.

As a result there will in general be no guarantee that a solver will converge to a global minimym, and the times requited to find even a local solution are typically orders of magnitude greater than for QP problems of simililar size.

To solve the MPC optimization derived from an inverted pendulum control with 10 variables in $\mathbf{u}$, solution times of 10sec are typically need on a 2GHZ PC.

Unlike QP solvers, the computational loads of solvers for nonlinear programming problems are strongly problem-dependent.

# Constraint Handling

While the equality constraints are usually handled implicitly (i.e the plant model is used to write predicted state trajectories as functions of initial conditions and input trajectories), the inequality constraints are imposed as explicit constraints within the online optimization problem.

This course is concerned with linear inequality constrains

## Input constraints

absolute constraint:

$$
\underline{u} \leq u(k) \leq \overline{u}
$$

rate constraints:

$$
\underline{\Delta u} \leq u(k) - u(k-1) \leq \overline{\Delta u}
$$

Input constraints commonly arise as a result of actuator limits, e.g. torque saturation in d.c. motors and flow saturation in values.

## State constraints

Linear state constraints have the general form

$$
\overline{g_c} \leq G_c x(k) \leq \underline{g_c}
$$

where $G_c$ is a constant matrix and $\overline{g_c}, \underline{g_c}$ are constant vectors.

# Summary

- MPC is a feedback law based on prediction, optimization, and receding horizon implementation. The optimization is performed over open-loop predictions, which are based on a plant model.
- Constraints on system inputs and states can be hard or soft. Constraints are hendled sub-optimally using de-tued optimal control or anti-windup strategies whereas MPC enables constraints to be handled optimally.

# Prediction and Optimization in MPC

## 1. Predictive equations

This section derives an expression for the predicted state trajectory $x(k)$ in terms of the predicted input trajectory $u(k)$.

This expression allows the general quadratic cost:

<div class="latex-container">
$$
J(k) = \sum^{N-1}_{i=0} \left[ x^T(k + i \vert k) Q x(k + i \vert k) + u^T(k + i \vert k) R u(k + i \vert k) \right] + x^T(k + N \vert k) \bar{Q} x(k + N \vert k)
$$
</div>

to be written as a quadratic function of $u(k)$ in following form.

<div class="latex-container">
$$
J(k) = \mathbf{u}^T(k)H\mathbf{u}(k) + 2f^T \mathbf{u}(k) + g
$$
</div>

The predicted state sequence generated by the linear state-space model with input sequence $\mathbf{u}(k)$ can be written.

<div class="latex-container">
$$
x(k + i \vert k) = A x(k + i \vert k) + B u(k + i \vert k), \;\;\; i = 0, 1, \dots
$$
</div>

<div class="latex-container">
$$
\begin{align*}
  x(k \vert k) &= x(k) \\
  x(k + 1 \vert k) &= Ax(k) + Bu(k \vert k) \\
  x(k + 2 \vert k) &= A^2x(k) + ABu(k \vert k) + Bu(k + 1 \vert k)
\end{align*}
$$
</div>

In conpact notation:

<div class="latex-container">
$$
x(k + i \vert k) = A^i x(k) + C_i \mathbf{u}(k), \;\;\; i = 0, \dots, N
$$
</div>

or

<div class="latex-container">
$$
\mathbf{x}(k) = \mathcal{M} x(k) + \mathcal{C} \mathbf{u}(k), \;\;\; \text{where} \;\;\mathcal{M} =
\begin{bmatrix}
  A \\
  A^2 \\
  \vdots \\
  A^N
\end{bmatrix}, \;\;
\mathcal{C} =
\begin{bmatrix}
  B & 0 & \dots & 0 \\
  AB & B & \dots & 0 \\
  \vdots & \vdots & \ddots \\
  A^{N-1} & A^{N-2} & \dots & B \\
\end{bmatrix}
$$
</div>

Substituting for $x(k + i \vert k)$ and collecting terms gives:

<div class="latex-container">
$$
J(k) = \mathbf{u}^T H \mathbf{u} + 2x^T(k) F^T \mathbf{u}(k) + x^T(k) G x(k)
$$
</div>

where

<div class="latex-container">
$$
\begin{align*}
  H &= \mathcal{C}^T \tilde{Q} \mathcal{C} + \tilde{R} \\
  F &= \mathcal{C}^T \tilde{Q} \mathcal{M} \\
  G &= \mathcal{M}^T \tilde{Q} \mathcal{M} + Q \\
\end{align*}
$$
</div>

with

<div class="latex-container">
$$
\tilde{Q} =
\begin{bmatrix}
  Q & 0 & \dots & 0 \\
  0 & \ddots & & \vdots \\
  \vdots & & Q & 0 \\
  0 & \dots & 0 & \bar{Q} \\
\end{bmatrix},
\;\;\;
\tilde{R} =
\begin{bmatrix}
  R & 0 & \dots & 0 \\
  0 & \ddots & & \vdots \\
  \vdots & & R & 0 \\
  0 & \dots & 0 & R \\
\end{bmatrix}
$$
</div>

Note that matrices $H, F$ and $G$ can be computed offline.

### 1.1 LTV prediction models

The above formulation applies to systems with linear time-varying models

$$
x(k+1) = A(k) x(k) + B(k) u(k).
$$

In this case the state predictions can be written

$$
x(k + i \vert k) = \prod_{j=i-1}^0 A(k + j) x(k) + C_i (k) \mathbb{u}(k) \;\;\; i = 0, \dots, N
$$

where

$$
C_0(k) = 0 \\
C_i(k) = 
\begin{bmatrix} 
  \prod_{j=i-1}^1 A(k + j)B(k) & \prod_{j=i-1}^2 A(k + j)B(k) & 0 & \dots & 0
\end{bmatrix}
$$

## 2. Unconstrainted optimization

In the absense of constraints, the optimization $\mathbb{u}^*(k) = \underset{u}{\mathrm{argmin}} J(k)$ has a closed-form soluation which can be derived by considering the graident of $J$ with respect ot $\mathbb{u}$:

$$
\nabla_{\mathbb{u}} J = 2 H \mathbb{u} + 2 F x.
$$

If

- $H$ is positive definite (or positive semidefinite)
- $H$ is nonsingular

from $\nabla_{\mathbb{u}} \mathbf{J} = 0$

$$
\mathbb{u}^*(k) = - H^{-1} F x(k)
$$

If $H$ is singular, then optimal $\mathbf{u}^*$ is non-unique.

...

### 2.1 Horizon length and performance

The linear feedback form $\mathbb{u}^*(k) = - H^{-1} F x(k)$ is to be expected since $\mathbf{u}^*$ is the solution of a LQ-optimal control problem.

- (Ideal) The Infinite horizon LQ-problem control problem : there is no difference between the optimal predicted input sequence and its receding horizon implementation in the absence disturbances and model error.
- (Real) <span style="color: #2D3748; background-color:#fff5b1;">The horizon is reduced</span> : There can be significant fidvtrpsnvy between the prediction s of $\mathbb{u}^*(k) = - H^{-1} F x(k)$ and closed-loop responses with the receding horizon controller

## 3. Infinite horizon cost

Because of the difference between predicted and closed-loop responsed, there is no guarantee that a receding horizon controller based on a finite-horizon cost will achieve the optimal predicted preformace in closed-loop operation.

In fact the closed-loop system may even be unstable.

However, this problem is avioded entirely if performance is evaluated over an infinite prediction horizon, i.e. if the cost is defined as

$$
J(k) = \sum_{i=0}^{\infty} \left[ x^T (k + i \vert k) Q x(k + i \vert k) + u^T(u + i \vert k R) u(k + i \vert k) \right]
$$

To make sure that the problem of minimizing ihis cost is tractable, it is then necessary to define the predicted input sequence over the infinite prediction horizon in such a way that the number of free vaiables in the MPC optimization remains finite.

<span style="color: #2D3748; background-color:#fff5b1;">Dual model predictions</span>

$$
u(k + i \vert k) =
\begin{cases}
  \text{optimization vaiable} \;\;\; i = 0, 1, \dots, N-1 \;\;\; (\text{mode 1}) \\
  K x(k + i \vert k) \;\;\; i = N, N + 1, \dots \;\;\; (\text{mode 2}) \\
\end{cases}
$$

- mode 1 : initial horizon of $N$ samples over which the predicted inputs are variables in the MPC optimization
- mode 2 : inputs are defined by a stabilizing feedback law over the remaining infinite horizon

For the dual mode input predictions, an infinite horizon cost nedd only be evaluated explicitly over mode 1 since $J(k)$ can be re-written in the form of

$$
J(k) = \sum^{N-1}_{i=0} \left[ x^T(k + i \vert k) Q x(k + i \vert k) + u^T(k + i \vert k) R u(k + i \vert k) \right] + x^T(k + N \vert k) \bar{Q} x(k + N \vert k)
$$

This is done by choosing the terminal wighting matrix $\bar{Q}$ so that the term $x^T(k + N \vert k) \bar{Q} x(k + N \vert k)$ is equal to the cost over the mode 2 prediction horizon, which is achieved by specifying $\bar{Q}$ as the solution of the Lyapunov equation:

$$
\bar{Q} - (A + BK)^T \bar{Q}(A + BK) = Q + K^T R K
$$

Theorem 2.1 (Terminal weighting matrix).

Along closed-loop trajectories of the model under the feedback law $u(k) = Kx(k)$, the infinite horizon quadratic cost is given by

$$
\sum_{i=0}^{\infty} \left[ x^T(i)Qx(i) + u^T R u(i) \right] = x^T \bar{Q} x(0)
$$

where $\bar{Q}$ satisfies $\bar{Q} - (A + BK)^T \bar{Q}(A + BK) = Q + K^T R K$

증명 생략

Note

1. The Lyapunov equation $\bar{Q} - (A + BK)^T \bar{Q}(A + BK) = Q + K^T R K$ has a unique solution for $\bar{Q}$ if and only if the eigenvalues of $A + BK$ lie within the unit circle.
2. It is easy to show that $\bar{Q}$ is positive definite if either $Q + K^T R K > 0$ or $Q = C^TC$ where (A + BK, C) is observable.
3. From theorem 2.1, it is clear that the finite horizon cost fomulation is equal to the infinite horizon formulation.

$$
\sum^{N-1}_{i=0} \left[ x^T(k + i \vert k) Q x(k + i \vert k) + u^T(k + i \vert k) R u(k + i \vert k) \right] + x^T(k + N \vert k) \bar{Q} x(k + N \vert k)
\\
= \sum_{i=0}^{\infty} \left[ x^T (k + i \vert k) Q x(k + i \vert k) + u^T(u + i \vert k R) u(k + i \vert k) \right]
$$

### 3.1 The relationship between unconstrainted MPC and LQ optimal control

The obvious choice for the mode 2 feedback gain $K$ is the LQ-optimal gain for the cost.

Due to the optimality of predictions over both modes 1 and 2, the optimal predicted trajectory $\mathbb{u}^*$ is then necessarily identical to the infinite horizon optimal input sequence

$$
\mathbb{u}^*(k)
=
\begin{bmatrix}
  K \\
  K ( A + BK) \\
  \vdots \\
  K ( A + B K)^{N-1} \\
\end{bmatrix}
x(k)
$$

implying the the receding horizon control law

$$
u(k) = u^*(k \vert k) = K_N x(k), \;\;\;, K_N = - \left[ I_{n_u} 0 \dots o \right] H^{-1}F
$$

is in fact equal to the LQ-optimal feedback law $u = Kx$.

This result should bot be surprising since the model and cost are the same for MPC and LQ-optimal control, here MPC simply provides an alternative method determining the optimal control law

## 4. Incorporating constraints

The real advantage of MPC lies in its ability ot determine nonlinear feedback laws which are optimal for constrained systems through numerical calculations that are performed online (i.e. in between samples)

$$
\underline{u} \leq u(k) \leq \overline{u} \\
\underline{x} \leq x(k) \leq \overline{x} \\
$$

or

The input constraint are equivalent

$$
u(k) \leq \overline{u}, \;\;\; -u(k) \leq - \underline{u}
$$

As applying mode 1 prediction $u(k + i \vert k), i = 0, \dots N-1$ can therefore be expressed in terms of $u(k)$ as

$$
\begin{bmatrix}
  I \\
  -I \\
\end{bmatrix}
u(k) \leq
\begin{bmatrix}
  \mathbf{1} \overline{u} \\
  -\mathbf{1} \underline{u} \\
\end{bmatrix}
$$

where $\mathbf{1}$ is a vector ones for the single input case.

Similary

$$
\begin{bmatrix}
  C_i \\
  -C_i
\end{bmatrix}
u(k) \leq
\begin{bmatrix}
  \overline{x} \\
  \underline{x} \\
\end{bmatrix}
+ 
\begin{bmatrix}
  -A^i \\
  A^i
\end{bmatrix}
x(k)
\;\;\; i = 1, \dots, N
$$

Therefore

$$
A_c \mathbf{u}(k) \leq b_0 + B_x x(k)
$$

where $A_c, b_0, B_x$ are constant matrices that can be determined offline.

## 5. Quadratic Programming

Combining the objective function and constraints dervied above, the optimization of the infinite horizon cost subject to constraints requites the solution of a QP problem:

$$
\underset{u}{\mathrm{minimize}} \;\;\; \mathbf{u}^T H \mathbf{u} + 2x^T(k) F^T \mathbf{u} \\
\mathrm{subject \; to} \;\;\; A_c \mathbf{u} \leq b_0 + B_x x(k)
$$

Since $H$ is positive semi-definite and constraint are linear, this is a convex optimization problem which has a unique solution.

Purpose of this section

- outlines the 2 types of algorithm commonly used to solve QP problem : active set and interior point algorithms
- general result of constrainted optimization theory

Theorem 2.2 (Optimization with equality constraints)

If $\mathbf{u}^*$ satisfies

$$
\mathbf{u}^* = \underset{u}{\mathrm{argmin}} f(\mathbf{u}) \\
\mathrm{subject \; to} \;\;\; c_i(\mathbf{u}) = 0, \;\;\; i = 1, \dots, m \\
$$

where $f$ and $c_i$ are smooth functions, then scalar $\lambda_i^*, i = 1, \dots, m$ exist satisfying

$$
\nabla_{\mathbf{u}} f(\mathbf{u}^*) + \sum_{i=1}^m \lambda_i \nabla_{\mathbf{u}} c_i (\mathbf{u}^*) = 0 \\
c_i (\mathbf{u}^*) = 0, \;\;\; i = 1, \dots, m
$$

- Condition 1 : extension of the gradient condition, $\nabla_{\mathbf{u}} f(\mathbf{u}^*)$, which must be satisfied at a minimum point of $f(u)$ if $\mathbf{u}$ is unconstrained. The addition of the second term simply ensures that $f(\mathbf{u})$ cannot be reduced by perturbing $\mathbf{u}^*$ by incremental distance in any direction for which the constraint $c_i(\mathbf{u}) = 0$ remains satisfied.

Scalar $\lambda_i$ : Lagrange multipliers

Note that

1. Condition 1 applies to the case of inequality constraints $c_i (\mathbf{u}) \leq 0$.
2. Problems with inequality constraints are generally harder to solve than similar equality constrainted problems since only a subset of the constraints may be active at the solution.
