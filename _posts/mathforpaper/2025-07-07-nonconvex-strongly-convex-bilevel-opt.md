---
title: "Solving Nonconvex-Strongly-Convex Bilevel Optimization"
categories:
  - mathforpaper
---
Bilevel optimization problem

$$
\begin{align*}
  & \underset{
  \substack{
    x \in \mathbb{R}
  }
}{\min} \;\; \varphi (x) := f(x, y^*(x))
\end{align*}
$$

where

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

When the lower-level problem is strongly convex in $y$,

$$
\begin{align*}
  \nabla \varphi(x)
  &= \nabla_x f(x, y^*(x)) + \nabla y^*(x) \nabla_y f(x, y^*(x)) \\
  &= \nabla_x f(x, y^*(x)) - \nabla_{xy}^2 g(x, y^*(x)) [ \nabla_{yy}^2 g(x, y^*(x))]^{-1} \nabla_y f(x, y^*(x))
\end{align*}
$$

Kwon proposed a novel first-order algorithm that can find an $\epsilon$-first-roder stationary point of $\varphi$ (x) without resorting to second-order information.

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

# Conclusion

$\mathcal{L}_{\lambda}(x, y) := f(x, y) + \lambda (g(x, y) - g^*(x))$

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

The advantage of this reduction is that $\nabla \mathcal{L}_{\lambda}^*(x)$ can be evaluated with only first-order information of $f$ and $g$

$$
\begin{align*}
  \nabla \mathcal{L}_{\lambda}^*(x)
  &= \nabla_x \mathcal{L}_{\lambda}^*(x, y^*(x)) + \nabla y_{\lambda}^*(x) \nabla_y \mathcal{L}_{\lambda}^*(x, y^*(x)) \\
  &= \nabla_x f(x, y^*(x)) - \lambda (\nabla_x g(x, y_{\lambda}^*(x)) - \nabla_x g(x, y^*(x)))
\end{align*}
$$

Kwon proposed the Fully First-order Bilevel Approximation

# 4. Finding First-Order Stationarity

F2BA has a upper complexity bound of $\tilde{\mathcal{O}}(\epsilon^{-2})$, near-optimal rate achieved by second-order methods.

$$
\begin{align*}
  & \underset{
  \substack{
    x \in \mathbb{R}^{d}x
  }
  }{\min} \;\; \mathcal{L}_{\lambda}^*(x)
  =
  & \underset{
  \substack{
    x \in \mathbb{R}^{d}x,
    y \in \mathbb{R}^{d}y
  }
  }{\min} \;\; \left[ f(x, y) + \lambda \left( g(x, y) - \underset{
  \substack{
    z \in \mathbb{R}^{d}x
  }
  }{\min} \;\; g(x, z) \right) \right]
\end{align*}
$$

# 중요한 정리

## Assumption

1. $g(x, y)$ is $\mu$-strongly convex in $y$
2. $g(x, y)$ is $L_g$-gradient Lipschitz
3. $g(x, y)$ is $\rho_g$-Hessian Lipschiz
4. $f(x, y)$ is $C_f$-Lipschitz in $y$
5. $f(x, y)$ is $L_f$-gradient Lipschitz
6. $f(x, y)$ is two-times continuous differentiable
7. $\varphi(x)$ is lower bounded,  $\inf_{x \in \mathbb{R}^dx} \varphi(x) > -\infty$

## Notation

$$
\Delta := \varphi(x_0) - \underset{\substack{x \in \mathbb{R}^dx}}{\inf} \varphi (x), \quad R: = \vert\vert y_0 - y^*(x_0) \vert\vert^2
$$

## Setting Parameters

$$
\eta = l^{-1} \kappa^{-3}, \quad \lambda \asymp \max \left[ \kappa / R, \; l \kappa^2 / \Delta, \; l \kappa^3 / \epsilon \right], \quad \alpha = \dfrac{1}{L_g}, \quad \tau = \dfrac{1}{2\lambda L_g}, \quad K = \mathcal{O} \left( \dfrac{L_g}{\mu} \log \dfrac{\lambda L_g}{\mu} \right)
$$

## 사용되는 정리

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

## Result

알고리즘 F^2BA은 $\varphi(x)$의 $\epsilon$-first-order stationary point를

$$
T = \mathcal{O}(l \kappa^4 \epsilon^{-2} \log \dfrac{l \kappa}{\epsilon})
$$

안에 찾을 수 있다. 여기서

$$
l := \max \left[ C_f, \; L_f, \; L_g, \; \rho_g \right], \quad \kappa := \dfrac{l}{\mu}
$$

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

# Memo

일반적인 경우라면 g가 mu strongly convex이어야 할텐데 여기서처럼 proxy를 사용할테 mu strongly convex이어야 해?

1. 이 세팅에서 강한 볼록성을 줄 수 있을까?
2. 강한 볼록성이 없는 경우 현실적인 다른 relaxation을 할 수는 없을까?
