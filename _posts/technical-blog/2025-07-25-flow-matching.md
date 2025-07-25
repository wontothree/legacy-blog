---
title: "[Generative AI] Flow Matching"
categories:
  - technical-blog
---
The framework of Flow Matching is based on learning a velocity field (vector field). Each velocity field defines a **flow** $\psi_t$ by solving an ODE in a process called simulation. A flow is a deterministic, time-continuous bijective transformation of the $d$-dimensional Euclidean space, $\mathbb{R}^d$.

The goal of Flow Matching is to build a flow that transforms a sample $X_0 \sim p$ drawn from a source distribution $p$ into a traget sample $X_1:= \psi_1(X_0)$ such that $X_1 \sim q$ has a desired distribution $q$.

---

- Training dataset : amples from some target distribution $q$ over $\mathbb{R}^d$
- Flow Matching : probability path $(p_t)_{0 \leq t \leq 1}$, from a known source distribution $p_0 = p$ to the data target distribution $p_1 = q$, where each $p_t$ is a distribution over $\mathbb{R}^d$.

Our goal is to build a model capable of generating new samples from $q$. Specifically, Flow Matching is a simple regression objective to train the velocity field neural network describing the instantaneous velocities of samples. Later it is used to convert the source distribution $p_0$ into the target distribution $p_1$, along the probability path $p_t$.

Process generating a novel sample from the target distribution $X_1 \sim q$

1. drawing a novel sample from the source distribution $X_0 \sim p$
2. solving the ODE determined by the trained velocity field.

ODE is defined via a time-dependent vector field $u : [0, 1] \times \mathbb{R}^d \rightarrow \mathbb{R}^d$, which is is the velocity field modeled in terms of a neural network.

This velocity field determines a time-dependent flow $\psi : [0, 1] \times \mathbb{R}^d \rightarrow \mathbb{R}^d$, defined as

$$
\dfrac{d}{dt}\psi_t(x) = u_t(\psi_t(x))
$$

The goal of Flow Matching is to learn the parameters $\theta$ of a velocity field $u^\theta_t$ implemented in terms of a neural network

1. design a probability path $p_t$ interpolating between $p$ and $q$
2. train a velocity field $u^\theta_t$ generating $p_t$ by means of regression

## First Step

Let the source distribution $p := p_0 = \mathcal{N}(x \vert 0, I)$.

Construct the probability path $p_t$ as the agregation of the conditional probability paths $p_{t \vert 1}(x \vert x_1)$, each condition on one of the data examples $X_1 = x_1$ comprising the training dataset.

Therefore, the probability path $p_t$ follows the expression

$$
p_t(x) = \int p_{t \vert 1} (x \vert x_1) q(x_1) dx_1
$$

where *conditional optimal-transport* or *linear* path is

$$
p_{t \vert 1} (x \vert x_1) = \mathcal{N}(x \vert t x_1, (1 -t)^2 I)
$$

We define the random variable $X_t \sim p_t$ by drawing $X_0 \sim p$, drawing $X_1 \sim q$, and taking their linear combination:

$$
X_t = tX_1 + (1 - t)X_0 \sim p_t
$$

## Second Step

# Reference

[Flow Matching Guide and Code (Lipman et al., 2024)](https://arxiv.org/abs/2412.06264)
