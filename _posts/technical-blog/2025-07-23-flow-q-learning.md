---
title: "[Robot Learning] Flow Q Learning"
categories:
  - technical-blog
---
# Loss Function

Final Loss Function

$$
\begin{aligned}
\mathcal{L}_{Q}(\phi) + \mathcal{L}_{\pi}(\omega) + \mathcal{L}_{\text{Flow}}(\theta)

&= 
\mathbb{E}_{\substack{
s, a, r, s' \sim \mathcal{D}, \\
a' \sim \pi_\omega
}} \left[ ( Q_{\phi} (s, a) - r - \gamma Q_{\bar{\phi}} (s', a') )^2 \right]
\\

&\quad + \mathbb{E}_{s \sim \mathcal{D}, a^{\pi} \sim \pi_{\omega}} \left[ - Q_{\phi} (s, a^{\pi})\right]
+ \alpha \mathbb{E}_{\substack{
s, \sim \mathcal{D}, \\
z \sim \mathcal{N}(0, I_d)
}} \left[ \vert\vert \mu_{\omega} (s, z) - \mu_{\theta} (s, z)  \vert\vert_2^2 \right]
\\
&\quad + \mathbb{E}_{\substack{
s, a = x^1 \sim \mathcal{D}, \\
x^0 \sim \mathcal{N}(0, I_d), \\
t \sim \text{Unif([0, 1])}
}} \left[ \vert\vert v_\theta(t, s, x^t) - (x^1 - x^0) \vert\vert_2^2 \right]
\\
\end{aligned}
$$

where

- BC flow policy is trained only with the BC flow-matching loss

$$
\mathcal{L}_{\text{Flow}}(\theta)

= \mathbb{E}_{\substack{
s, a = x^1 \sim \mathcal{D}, \\
x^0 \sim \mathcal{N}(0, I_d), \\
t \sim \text{Unif([0, 1])}
}} \left[ \vert\vert v_\theta(t, s, x^t) - (x^1 - x^0) \vert\vert_2^2 \right]
$$

- The critic is trained with the original critic loss of behavior-regularized actor-critic

$$
\mathcal{L}_{Q}(\phi)

= \mathbb{E}_{\substack{
s, a, r, s' \sim \mathcal{D}, \\
a' \sim \pi_\omega
}} \left[ ( Q_{\phi} (s, a) - r - \gamma Q_{\bar{\phi}} (s', a') )^2 \right]
$$

- One-step policy is trained with the following actor loss

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
