---
title: "[Robot Learning] Flow Q Learning"
categories:
  - technical-blog
---
# Loss Function

$$
\begin{aligned}
\mathcal{L}_{Q}(\phi) + \mathcal{L}_{\text{Flow}}(\theta) + \mathcal{L}_{\pi}(\omega)

&= 
\mathbb{E}_{\substack{
s, a, r, s' \sim \mathcal{D}, \\
a' \sim \pi_\omega
}} \left[ ( Q_{\phi} (s, a) - r - \gamma Q_{\bar{\phi}} (s', a') )^2 \right]
\\

&\quad + \mathbb{E}_{\substack{
s, a = x^1 \sim \mathcal{D}, \\
x^0 \sim \mathcal{N}(0, I_d), \\
t \sim \text{Unif([0, 1])}
}} \left[ \vert\vert v_\theta(t, s, x^t) - (x^1 - x^0) \vert\vert_2^2 \right]
\\

&\quad + \mathbb{E}_{s \sim \mathcal{D}, a^{\pi} \sim \pi_{\omega}} \left[ - Q_{\phi} (s, a^{\pi})\right]
+ \alpha \mathbb{E}_{\substack{
s, \sim \mathcal{D}, \\
z \sim \mathcal{N}(0, I_d)
}} \left[ \vert\vert \mu_{\omega} (s, z) - \mu_{\theta} (s, z)  \vert\vert_2^2 \right]
\\
\end{aligned}
$$

where

## Critic

trained with the original critic loss of behavior-regularized actor-critic

- $s$ : current state
- $a$ : current action
- $r$ : current reward
- $s'$ : next state
- $Q_{\phi} (s, a)$ : state-action value function with parameter $\phi$ (상태 s에서 행동 a를 취했을 때, 앞으로 받을 누적 보상의 기대값을 예측하는 함수)
- $Q_{\bar{\phi}} (s, a)$ : target network
- $r + \gamma Q_{\bar{\phi}} (s', a')$ : 상태 s에서 행동 a를 했을 때 기대되는 총 보상 (타깃 Q값)
- $s, a, r, s' \sim \mathcal{D}$ : uniform sampling over the dataset’s transition tuples

$$
\mathcal{L}_{Q}(\phi)

= \mathbb{E}_{\substack{
s, a, r, s' \sim \mathcal{D}, \\
a' \sim \pi_\omega
}} \left[ ( Q_{\phi} (s, a) - (r + \gamma Q_{\bar{\phi}} (s', a')))^2 \right]
$$

the critic loss $\mathcal{L}_{Q}(\phi)$ minimizes the standard Bellman error

## (Iterative) BC flow policy

- $\mathcal{N}(0, I_d)$ : d-dimensional standard normal dis- tribution
- $\text{Unif([0, 1])}$ : uniform distribution over the unit interval
- $x^t = (1-t)x^0 + t x^1$ : linear in- terpolation between $x^0$ and $x^1$
- $v_\theta(t, s, x) : [0, 1] \times \mathcal{S} \times \mathbb{R}^d \rightarrow \mathbb{R}^d$ : state- and time-dependent vector field with parameter $\theta$

trained only with the BC flow-matching loss

$$
\mathcal{L}_{\text{Flow}}(\theta)

= \mathbb{E}_{\substack{
s, a = x^1 \sim \mathcal{D}, \\
x^0 \sim \mathcal{N}(0, I_d), \\
t \sim \text{Unif([0, 1])}
}} \left[ \vert\vert v_\theta(t, s, x^t) - (x^1 - x^0) \vert\vert_2^2 \right]
$$

## (Expressive) One-step policy

trained with the following actor loss

$$
\begin{aligned}
\mathcal{L}_{\pi}(\omega)
&= \mathbb{E}_{s \sim \mathcal{D}, a^{\pi} \sim \pi_{\omega}} \left[ - Q_{\phi} (s, a^{\pi})\right]
+ \alpha \mathcal{L}_{\text{Distill}}(\omega)
\\

&= \mathbb{E}_{s \sim \mathcal{D}, a^{\pi} \sim \pi_{\omega}} \left[ - Q_{\phi} (s, a^{\pi})\right]
+ \alpha \mathbb{E}_{\substack{
s, \sim \mathcal{D}, \\
z \sim \mathcal{N}(0, I_d)
}} \left[ \vert\vert \mu_{\omega} (s, z) - \mu_{\theta} (s, z)  \vert\vert_2^2 \right]
\\
\end{aligned}
$$

where

$$
\mathcal{L}_{\text{Distill}}(\omega) = \mathbb{E}_{\substack{
s, \sim \mathcal{D}, \\
z \sim \mathcal{N}(0, I_d)
}} \left[ \vert\vert \mu_{\omega} (s, z) - \mu_{\theta} (s, z)  \vert\vert_2^2 \right]
$$