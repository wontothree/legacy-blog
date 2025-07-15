---
title: "[OPT] Solving Diterministic Nonconvex-Strongly-Convex Bilevel Optimization"
categories:
  - paper
---
# Formulating Problem

## Original deterministic bilevel optimization problem

$$
\begin{align*}
  & \underset{
  \substack{
    x \in \mathbb{R}
  }
}{\min} \;\; \varphi (x) := f(x, y^*(x))
\end{align*}
$$

where the lower-level problem is

$$
\begin{align*}
  y^*(x) = \;
  & \underset{
  \substack{
    y \in \mathbb{R}^dy
  }
}{\arg\min} \;\; g(x, y)
\end{align*}
$$

## Relaxation

하위 최적화 문제를 상위 최적화 문제의 제약조건으로 바꾼다.

$$
\begin{align*} & \underset{
  \substack{
    x \in \mathbb{R}^dx, y \in \mathbb{R}^dy
  }
  }{\min} \;\; f(x, y)
\end{align*}
$$

subject to

$$
g(x, y) - g^*(x) \leq 0
$$

where

$$
g^*(x) = g(x, y^*(x)) = \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \; g(x, z)
$$

We optimize the problem in y firstly and in x in secondly.

## Relaxation by Lagrangian

We define lagrangian function. $\lambda > 0$ is a penalty parameter.

$$
\mathcal{L}_{\lambda}(x, y) := f(x, y) + \lambda (g(x, y) - g^*(x))
$$

Thus

$$
\begin{align*}
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min} \mathcal{L}_\lambda(x, y)
  &:=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left[ f(x, y) + \lambda \left( g(x, y) - g^*(x) \right) \right] \\
  &=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left[ f(x, y) + \lambda \left( g(x, y) - \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \;\; g(x, z) \right) \right]
\end{align*}
$$

## Final Form

Define the surrogate single-level objective function

$$
\mathcal{L}_{\lambda}^*(x) := \mathcal{L}_{\lambda} (x, y_{\lambda}^*(x))
$$

where

$$
\begin{align*}
  y_{\lambda}^*(x) = \;
  & \underset{
  \substack{
    y \in \mathbb{R}^dy
  }
}{\arg\min} \;\; \mathcal{L}_{\lambda}(x, y)
\end{align*}
$$

$\mathcal{L}_{\lambda}^*(x)$ is good proxy of $\varphi(x)$ with

$$
\vert\vert \nabla \mathcal{L}_{\lambda}^*(x) - \nabla \varphi (x) \vert\vert = \mathcal{O} (\kappa^3 / \lambda)
$$

Therefore

$$
\begin{align*}
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \;\; \mathcal{L}_{\lambda}^*(x)
  &:=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min}
  \;\mathcal{L}_\lambda(x, y) \\
  &=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left[ f(x, y) + \lambda \left( g(x, y) - \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \; g(x, z) \right) \right]
\end{align*}
$$

<br>
<br>
<br>

# Solver F^2BA

Algorithm of F^2BA is followed by

$$
\begin{aligned}
\text{(1)}\quad & z_0 = y_0 \\
\text{(2)}\quad & \textbf{for } t = 0, 1, \dots, T{-}1 \textbf{ do} \\
\text{(3)}\quad & \quad y_t^0 = y_t,\quad z_t^0 = z_t \\
\text{(4)}\quad & \quad \textbf{for } k = 0, 1, \dots, K{-}1 \textbf{ do} \\
\text{(5)}\quad & \quad\quad z_t^{k+1} = z_t^k - \alpha \nabla_y g(x_t, z_t^k) \\
\text{(6)}\quad & \quad\quad y_t^{k+1} = y_t^k - \tau \left( \nabla_y f(x_t, y_t^k) + \lambda \nabla_y g(x_t, y_t^k) \right) \\
\text{(7)}\quad & \quad \textbf{end for} \\
\text{(8)}\quad & \quad \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t) = \nabla_x f(x_t, y_t^K) + \lambda \left( \nabla_x g(x_t, y_t^K) - \nabla_x g(x_t, z_t^K) \right) \\
\text{(9)}\quad & \quad x_{t+1} = x_t - \eta \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t) \\
\text{(10)}\quad & \textbf{end for}
\end{aligned}
$$

The following expansion make the algorithm readable

<div class="latex-container">
$$
\begin{align*}
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \;\; \mathcal{L}_{\lambda}^*(x)
  &:=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \;
  \underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\min}
  \;\mathcal{L}_\lambda(x, y) \\
  &=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \;
  \underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left[ f(x, y) + \lambda \left( g(x, y) - \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \; g(x, z) \right) \right] \\
  &=
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \left[ \;
  \underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left( f(x, y) + \lambda g(x, y) \right) - \lambda \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \; g(x, z) \right] \\

  &= \mathcal{L}^*_{\lambda}\left(
  \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\arg\min} \left[ \;
  \underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\min} \left( f(x, y) + \lambda g(x, y) \right) - \lambda \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \; g(x, z) \right]\right) \\

  &=
  \mathcal{L}^*_{\lambda} \left(
  \underbrace{\underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\arg\min} \left[ \;
  \left( f\left(x, \; \underbrace{\underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\arg\min} \left( f(x, y) + \lambda g(x, y) \right)}_{(6)}\right) + \lambda \; g\left(x, \; \underbrace{\underset{
  \substack{
    y \in \mathbb{R}^{d}y
  }
  }{\arg\min} \left( f(x, y) + \lambda g(x, y) \right)}_{(6)} \right) \right) - \lambda g\left(x, \;\underbrace{\underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\arg\min} \; g(x, z)}_{(5)}\right) \right]}_{(8), (9)} \right)\\
\end{align*}
$$
</div>

<br>
<br>
<br>

# Proof of convergence in F^2BA

F2BA has a upper complexity bound of $\tilde{\mathcal{O}}(\epsilon^{-2})$, near-optimal rate achieved by second-order methods.

**Assumption**

1. $g(x, y)$ is $\mu$-strongly convex in $y$
2. $g(x, y)$ is $L_g$-gradient Lipschitz
3. $g(x, y)$ is $\rho_g$-Hessian Lipschiz
4. $f(x, y)$ is $C_f$-Lipschitz in $y$
5. $f(x, y)$ is $L_f$-gradient Lipschitz
6. $f(x, y)$ is two-times continuous differentiable
7. $\varphi(x)$ is lower bounded,  $\inf_{x \in \mathbb{R}^dx} \varphi(x) > -\infty$

<br>

<div style="border: 2px solid #444; padding: 15px; border-radius: 4px">

Notation

$$
\Delta := \varphi(x_0) - \underset{\substack{x \in \mathbb{R}^dx}}{\inf} \varphi (x), \quad R: = \vert\vert y_0 - y^*(x_0) \vert\vert^2
$$

Setting Parameters

$$
\eta = l^{-1} \kappa^{-3}, \quad \lambda \asymp \max \left[ \kappa / R, \; l \kappa^2 / \Delta, \; l \kappa^3 / \epsilon \right], \quad \alpha = \dfrac{1}{L_g}, \quad \tau = \dfrac{1}{2\lambda L_g}, \quad K = \mathcal{O} \left( \dfrac{L_g}{\mu} \log \dfrac{\lambda L_g}{\mu} \right)
$$

F^2BA find $\epsilon$-first-order stationary point of $\varphi(x)$ in

$$
T = \mathcal{O}(l \kappa^4 \epsilon^{-2} \log \dfrac{l \kappa}{\epsilon})
$$

Where

$$
l := \max \left[ C_f, \; L_f, \; L_g, \; \rho_g \right], \quad \kappa := \dfrac{l}{\mu}
$$
</div>

<br>

## Proof

Let $L$ be the gradient Lipschitz coefficient of $\mathcal{L}_{\lambda}^*(x)$.

By (a),

$$
\underset{\substack{x \in \mathbb{R}^dx}}{\sup} \vert\vert \nabla \mathcal{L}_{\lambda(x)}^* - \nabla \varphi(x) \vert\vert = \mathcal{O}(\epsilon)
$$

By (b),

$$
\mathcal{L}_{\lambda}^*(x_0) - \underset{\substack{x \in \mathbb{R}^dx}}{\inf} \mathcal{L}_{\lambda}^*(x) = \mathcal{O}(\Delta)
$$

By (c)

$$
L := \underset{\substack{x \in \mathbb{R}^dx}}{\sup} \vert\vert \nabla \mathcal{L}_{\lambda(x)}^* \vert\vert = \mathcal{O}(l \kappa^3)
$$

By (d),

$$
\vert\vert y_0 - y^*_\lambda(x_0) \vert\vert^2 + \vert\vert y_0 - y^*(x_0) \vert\vert^2 = \mathcal{O}(R)
$$

Now it suffices to show that the algorithm can find an $\epsilon$-first-roder stationary of $\mathcal{L}^*_{\lambda}(x)$

Consider the following update of gradient descent

$$
x_{t+1} = x_t - \eta \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t)
$$

즉,

$$
\begin{align}
x_{t+1} - x_t = - \eta \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t)
\end{align}
$$

우리는 $\mathcal{L}^*_{\lambda}(x_t)$가 $L$-smooth하다는 것을 보일 수 있다.

Let $\eta \leq 1/(2L)$, and then by (f) and (1),

$$
\begin{aligned}
\mathcal{L}^*_\lambda(x_{t+1}) 

&\leq \mathcal{L}^*_\lambda(x_t) 
+ \langle \nabla \mathcal{L}^*_\lambda(x_t), x_{t+1} - x_t \rangle
+ \frac{L}{2} \|x_{t+1} - x_t\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
+ \langle \nabla \mathcal{L}^*_\lambda(x_t), -\eta \hat{\nabla} \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{L}{2} \|-\eta \hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \hat{\nabla} \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{\eta^2 L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \nabla \mathcal{L}^*_\lambda(x_t) \rangle
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{\eta^2L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \eta \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{\eta^2L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \eta \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \hat{\nabla} \mathcal{L}^*_\lambda(x_t) \rangle 
+ \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \nabla \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{\eta^2L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \eta \langle \nabla \mathcal{L}^*_\lambda(x_t), \hat{\nabla} \mathcal{L}^*_\lambda(x_t) \rangle 
+ \frac{\eta^2L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \left( \dfrac{\eta}{2} \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
+ \dfrac{\eta}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2 
- \dfrac{\eta}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t)\|^2 \right)
+ \frac{\eta^2L}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \dfrac{\eta}{2} \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
- \left( \dfrac{\eta}{2} - \frac{\eta^2L}{2} \right) \vert\vert \hat{\nabla} \mathcal{L}^*_\lambda(x_t) \vert\vert^2 
+ \dfrac{\eta}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t)\|^2
\\

&= \mathcal{L}^*_\lambda(x_t) 
- \dfrac{\eta}{2} \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
- \left( \dfrac{\eta}{2} - \frac{\eta^2L}{2} \right) \left\| \dfrac{x_{t+1} - x_t}{-\eta} \right\|^2 
+ \dfrac{\eta}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t)\|^2
\\

&\leq \mathcal{L}^*_\lambda(x_t) 
- \dfrac{\eta}{2} \|\nabla \mathcal{L}^*_\lambda(x_t)\|^2 
- \dfrac{1}{4\eta}\left\| x_{t+1} - x_t \right\|^2 
+ \dfrac{\eta}{2} \|\hat{\nabla} \mathcal{L}^*_\lambda(x_t) - \nabla \mathcal{L}^*_\lambda(x_t)\|^2
\\

\end{aligned}
$$

On the other side, the following inequality is given

$$
\begin{aligned}
\vert\vert \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t) - \nabla \mathcal{L}^*_{\lambda}(x_t) \vert\vert

&\leq 2\lambda L_g \vert\vert y^K_t - y^*_{\lambda}(x_t) \vert\vert + \lambda L_g \vert\vert z^K_t - y^*(x_t) \vert\vert
\end{aligned}
$$

By (e),

$$
\begin{align*}
\vert\vert y^K_t - y^*_{\lambda}(x_t) \vert\vert^2
&\leq \left(1 - \dfrac{\mu}{L_g}\right)^K \vert\vert y^0_t - y^*_{\lambda} (x_t) \vert\vert^2 \\
&\leq \left(1 -\dfrac{\mu}{4L_g}\right)^K \vert\vert y^0_t - y^*_{\lambda} (x_t) \vert\vert^2 \\
&\leq \exp\left(-\dfrac{\mu K}{4L_g}\right) \vert\vert y^0_t - y^*_{\lambda} (x_t) \vert\vert^2, \\ \\
\vert\vert z^K_t - y^*(x_t) \vert\vert^2
&\leq \exp\left( -\dfrac{\mu K}{L_g}\right) \vert\vert z^0_t - y^* (x_t) \vert\vert^2 \\
&\leq \exp\left( -\dfrac{\mu K}{4L_g}\right) \vert\vert z^0_t - y^* (x_t) \vert\vert^2 \\
\end{align*}
$$

Taking square and deploy, therefore, 

$$
\begin{aligned}
\vert\vert \hat{\nabla} \mathcal{L}^*_{\lambda}(x_t) - \nabla \mathcal{L}^*_{\lambda}(x_t) \vert\vert^2

&\leq \left( 2\lambda L_g \vert\vert y^K_t - y^*_{\lambda}(x_t) \vert\vert + \lambda L_g \vert\vert z^K_t - y^*(x_t) \vert\vert \right)^2
\\

&\leq 8 \lambda^2 L_g^2 \vert\vert y^K_t - y^*_{\lambda}(x_t) \vert\vert^2 + 2 \lambda^2 L_g^2 \vert\vert z^K_t - y^*(x_t) \vert\vert^2
\\

&\leq 8 \lambda^2 L_g^2 \left( \vert\vert y^K_t - y^*_{\lambda}(x_t) \vert\vert^2 + \vert\vert z^K_t - y^*(x_t) \vert\vert^2 \right)
\\

&\leq 8 \lambda^2 L_g^2 \exp\left(-\dfrac{\mu K}{4L_g}\right) \left(\vert\vert y^0_t - y^*_{\lambda} (x_t) \vert\vert^2 + \vert\vert z^0_t - y^* (x_t) \vert\vert^2 \right)
\\

\end{aligned}
$$

On the other hand, we can show is $y^*_\lambda (x)$ is $(4L_g/\mu)$-Lipschiz, thus,

$$
\begin{aligned}
\vert\vert y^*_\lambda(x_{t+1}) - y^*_{\lambda}(x_t) \vert\vert

\leq \dfrac{4L_g}{\mu} \vert\vert x_{t+1} - x_t \vert\vert
\\

\end{aligned}
$$

$$
\begin{aligned}
\vert\vert y^0_{t+1} - y^*_{\lambda} (x_{t+1}) \vert\vert^2

&= \vert\vert y^0_{t+1} - y^*_\lambda(x_t) + y^*_{\lambda}(x_t) - y^*_\lambda(x_{t+1}) \vert\vert^2
\\

&\leq  2 \vert\vert y^0_{t+1} - y^*_\lambda(x_t) \vert\vert^2 + 2 \vert\vert y^*_{\lambda}(x_t) - y^*_\lambda(x_{t+1}) \vert\vert^2
\\

&=  2 \vert\vert y^K_t - y^*_\lambda(x_t) \vert\vert^2 + 2 \vert\vert y^*_\lambda(x_{t+1}) - y^*_{\lambda}(x_t) \vert\vert^2
\\

&\leq 2 \exp\left(-\dfrac{\mu K}{4L_g}\right) \vert\vert y^0_t - y^*_{\lambda} (x_t) \vert\vert^2
+ \dfrac{32L_g^2}{\mu^2} \vert\vert x_{t+1} - x_t \vert\vert^2
\\

\end{aligned}
$$

And

\begin{aligned}
\vert\vert z^0_{t+1} - y^*(x_{t+1}) \vert\vert^2

&=\vert\vert z^K_{t} - y^*(x_{t+1}) \vert\vert^2
\\

&=\vert\vert z^K_{t} - y^*(x_t) + y^*(x_t) - y^*(x_{t+1}) \vert\vert^2
\\

&\leq 2\vert\vert z^K_{t} - y^*(x_t) \vert\vert^2 + 2\vert\vert y^*(x_t) - y^*(x_{t+1}) \vert\vert^2
\\

&\leq 2 \exp \left( -\dfrac{\mu K}{4L} \right) \vert\vert z^0_t - y^*(x_t) \vert\vert^2
+ \dfrac{2L_g^2}{\mu^2} \vert\vert x_{t+1} - x_t \vert\vert^2
\\

\end{aligned}

<br><br><br>

## Appendix Theorems

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px"> (a)

  $$
  \vert\vert \nabla \mathcal{L}_{\lambda}^*(x) - \nabla \varphi (x) \vert\vert = \mathcal{O} (l \kappa^3 / \lambda), \quad \forall x \in \mathbb{R}^dx
  $$
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px"> (b)

$$
\vert \mathcal{L}_{\lambda}^*(x) - \varphi (x) \vert = \mathcal{O} (l \kappa^2 / \lambda), \quad \forall x \in \mathbb{R}^dx
$$
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">

  (c) $\mathcal{L}_{\lambda}^*(x)$ is $\mathcal{O} (l \kappa^3)$-gradient Lipschiz
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">

  (d) For $\lambda \geq 2L_f / \mu,$
  
  $$
  \quad \vert\vert y^*_\lambda(x) - y^*(x) \vert\vert \leq \dfrac{C_f}{\lambda \mu}
  $$
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">

(e) Suppose $h(x): \mathbb{R}^d \rightarrow \mathbb{R}$ is $\beta$-gradient Lipschitz and $\alpha$-strongly convex. Consider the following update of gradient descent

$$
x_{t+1} = x_t - \dfrac{1}{\beta} \nabla h(x_t)
$$

Let $x^* = \underset{\substack{x \in \mathbb{R}^d}}{\arg\min} \;\; h(x)$. Then

$$
\vert\vert x_{t+1} -x^* \vert\vert^2 \leq \left( 1 - \dfrac{\alpha}{\beta} \right) \vert\vert x_t - x^* \vert\vert^2
$$
</div>

<br>

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">

(f) If $f: \mathbb{R}^d \rightarrow \mathbb{R}$ is $L$-smooth then, for all $x, y \in \mathbb{R}^d$,

$$
f(y) \leq f(x) + \langle \nabla f(x), \;y - x\rangle + \dfrac{L}{2} \vert\vert y - x \vert\vert^2
$$
</div>

<br><br><br>

# Memo

일반적인 경우라면 g가 mu strongly convex이어야 할텐데 여기서처럼 proxy를 사용할테 mu strongly convex이어야 해?

1. 이 세팅에서 강한 볼록성을 줄 수 있을까?
2. 강한 볼록성이 없는 경우 현실적인 다른 relaxation을 할 수는 없을까?

<br>
<br>
<br>

# Exercise

## Exercise 1

$$
\begin{align*}
\underset{
\substack{
  x \in \mathbb{R}
}
}{\min} \; f(x, y^*(x))
\end{align*}
$$

where the lower-level problem is

$$
\begin{align*}
  y^*(x) = \;
  & \underset{
  \substack{
    y \in \mathbb{R}^dy
  }
}{\arg\min} \;\; g(x, y)
\end{align*}
$$

여기서 만약 $f(x, y) = - g(x, y)$ 이라면?

$$
\begin{align*}
y^*(x)
&=
\underset{
\substack{
  y \in \mathbb{R}^dy
}
}{\arg\min} \; g(x, y) \\
&=
\underset{
\substack{
  y \in \mathbb{R}^dy
}
}{\arg\min} \; -f(x, y) \\
&=
\underset{
\substack{
  y \in \mathbb{R}^dy
}
}{\arg\max} \; f(x, y) \\
\end{align*}
$$

이므로

$$
\begin{align*}
\underset{
\substack{
  x \in \mathbb{R}
}
}{\min} \; f(x, y^*(x))
&=
\underset{
\substack{
  x \in \mathbb{R}
}
}{\min} \; f(x,
\underset{
\substack{
  y \in \mathbb{R}^dy
}
}{\arg\max} \; f(x, y)
) \\
&=
\underset{
\substack{
  x \in \mathbb{R}
}
}{\min} \;
\underset{
\substack{
  y \in \mathbb{R}
}
}{\max}
\; f(x, y)
\end{align*}
$$

## Exercise 2

<div style="border: 1px solid #ccc; padding: 15px; border-radius: 4px">
When the lower-level problem is strongly convex in $y$,

$$
\begin{align*}
  \nabla \varphi(x)
  &= \nabla_x f(x, y^*(x)) + \nabla y^*(x) \nabla_y f(x, y^*(x)) \\
  &= \nabla_x f(x, y^*(x)) - \nabla_{xy}^2 g(x, y^*(x)) [ \nabla_{yy}^2 g(x, y^*(x))]^{-1} \nabla_y f(x, y^*(x))
\end{align*}
$$
</div>

<br>

*Proof.*

g에 대한 first order optimality condition에 의해

$$
\begin{aligned}
0
&= \nabla_y g(x, y^*(x)) \\
&= \nabla^2_{xy} g(x, y^*(x)) + \nabla^2_{yy} g(x, y^*(x)) \cdot \nabla y^*(x)
\end{aligned}
$$

에서

$$
\nabla y^*(x) = - [\nabla^2_{yy} g(x, y^*(x))]^{-1} \nabla^2_{xy} g(x, y^*(x))
$$

g가 strongly convex해야 $[\nabla^2_{yy} g(x, y^*(x))]$의 역행렬이 존재한다.

<br>
<br>
<br>

# Reference

- [Near-Optimal Nonconvex-Strongly-Convex Bilevel Optimization with Fully First-Order Oracles](https://arxiv.org/pdf/2306.14853)
