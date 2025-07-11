---
title: "Solving Nonconvex-Strongly-Convex Bilevel Optimization"
categories:
  - mathforpaper
---
Bilevel optimization problem

$$
\begin{align*} & \underset{
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

# 3. Preliminaries

## Assumption

1. $g(x, y)$ is $\mu$-strongly convex in $y$
2. $g(x, y)$ is $L_g$-gradient Lipschitz
3. $g(x, y)$ is $\rho_g$-Hessian Lipschiz
4. $f(x, y)$ is $C_f$-Lipschitz in $y$
5. $f(x, y)$ is $L_f$-gradient Lipschitz
6. $f(x, y)$ is two-times continuous differentiable
7. $\varphi(x)$ is lower bounded, i.. $\inf_{x \in \mathbb{R}^dx} \varphi(x) > -\infty$

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

# 5. Finding Second-Order Stationarity

# Memo

일반적인 경우라면 g가 mu strongly convex이어야 할텐데 여기서처럼 proxy를 사용할테 mu strongly convex이어야 해?

1. 이 세팅에서 강한 볼록성을 줄 수 있을까?
2. 강한 볼록성이 없는 경우 현실적인 다른 relaxation을 할 수는 없을까?
