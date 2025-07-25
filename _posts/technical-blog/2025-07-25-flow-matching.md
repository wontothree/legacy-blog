---
title: "[Generative AI] Flow Matching"
categories:
  - technical-blog
---
The framework of Flow Matching is based on learning a velocity field (vector field). Each velocity field defines a **flow** $\psi_t$ by solving an ODE in a process called simulation. A flow is a deterministic, time-continuous bijective transformation of the $d$-dimensional Euclidean space, $\mathbb{R}^d$.

The goal of Flow Matching is to build a flow that transforms a sample $X_0 \sim p$ drawn from a source distribution $p$ into a traget sample $X_1:= \psi_1(X_0)$ such that $X_1 \sim q$ has a desired distribution $q$.

---

We consider a training dataset of samples from some target distribution $q$ over $\mathbb{R}^d$.

Our goal is to build a model capable of generating new samples from $q$.

To address this task, Flow Matching build a probability path $(p_t)_{0 \leq t \leq 1}$, from a known source distribution $p_0 = p$ to the data target distribution $p_1 = q$, where each $p_t$ is a distribution over $\mathbb{R}^d$.

Specifically, Flow Matching is a simple regression objective to train the velocity field neural network describing the instantaneous velocities of samples. Later it is used to convert the source distribution $p_0$ into the target distribution $p_1$, along the probability path $p_t$.

After training, we generate a novel sample from the target distribution $X_1 \sim q$ by

1. drawing a novel sample from the source distribution $X_0 \sim p$
2. solving the ODE determined by the velocity field.

# Reference

[Flow Matching Guide and Code (Lipman et al., 2024)](https://arxiv.org/abs/2412.06264)
