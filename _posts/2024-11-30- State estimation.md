---
layout: single
title: "State estimation "
---

### State Estimation

상태 추정이란 알고자 하는 상태를 수학적으로 정의하고 추정해나가는 것임.

##### Multi-state state 

$$
\mathrm{X = [X_i, X_s]^\top} \\
\mathrm{X_i} = [q^g_b, p^g, v^g, b_a, b_g] \in R^{16\times1} \\
\mathrm{X_s} = [q_0, p_0, \dots, q_{N_{slw}}, p_{N_{slw}}]\in R^{7N_{slw}\times1} \\
n = [n_a, n_g, b_a, b_g]^\top \in R^{12\times1}

$$

##### Error-state modeling

$$
\mathrm{\delta X = [\delta X_i, \delta X_s]^\top} \\
\mathrm{\delta X_i} = [\delta\theta, \delta p, \delta v, \delta b_a, \delta b_g]^\top \in R^{15\times1}\\
\mathrm{\delta X_s} = [\delta\theta_0, \delta p_o, \dots, \delta\theta_{N_{slw}}, \delta p_{N_{slw}}]^\top \in R^{6N_{slw}\times1}
$$

섭동 모델을 통해 state transition matrix, $F$, noise matrix, $Q$ 유도 가능.

선형화 모델은 다음과 같다.

$\hat{(\cdot)}$ : value after time update

$$
\mathrm{\dot{\delta X_i} = F\delta X_i +Gn} \\
\mathrm{\hat{\delta X_i} = exp(F)\delta X_idt + Gndt} \\
\mathrm{= (I + Fdt + \frac{1}{2}F^2dt) + Gndt} \\
\mathrm{\hat{P} = E[\hat{\delta X_i} \hat{\delta X_i}^\top]} \\
\mathrm{= \Phi P \Phi^\top + Qdt^2}
$$

$$
\mathbf{F} =
\begin{bmatrix}
0 & 0 & 0 & 0 & -R^g_b \\
0 & 0 & I_3 & 0 & 0 \\
-R^g_bf^b\times & 0 & 0 & -R^g_b & 0 \\
0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0
\end{bmatrix} \in R^{15\times15}
$$

$$
\mathbf{G} =
\begin{bmatrix}
0 & -R^g_b & 0 & 0 \\
0 & 0 & 0 & 0  \\
-R^g_b & 0 & 0 & 0 \\
0 & 0 & 0 & 0  \\
0 & 0 & 0 & 0 
\end{bmatrix} \in R^{15\times12}
$$

##### Measurement update of Basic Kalman Filter

$r$ : measurement residual

$K$ : Kalman Gain

$(\cdot)^+$ : value after measurement update
$$
r = \delta z =  z - \hat{z} = z-H\hat{x} \\
\mathrm{S = HPH^\top + R} \\
\mathrm{K = PH^\top S^{-1}} \\
x^+ = x + \mathrm{K}r \\
\mathrm{P^+ = (I-KH)P(I-KH)^\top + KRK^\top}
$$

##### Multi-State Constraints Kalman Filter

$$
r = \mathrm{H_x}X + \mathrm{H_f}p_f^g + n_z \\
L_jH_f=0
$$

Point feature jacobian의 left-null space로 projection.
$$
r_o = \mathrm{H_{x, o}} + n_{z, o} \\
\begin{bmatrix} \mathrm{Q_1} & \mathrm{Q_2} \end{bmatrix}
\begin{bmatrix} \mathrm{T_h} \\ \mathrm{0} \end{bmatrix}
= \mathrm{QR}(\mathrm{H_o}) \\
r_n = \mathrm{Q_1}^\top r_o \\
\mathrm{R_n = Q_1^\top R_o Q_1} \\
$$
Update
$$
\mathrm{S_n = T_h P T_h^\top + R_n} \\
\mathrm{K_n = PT_h^\top S_n^{-1}} \\
x^+ = x + \mathrm{K_n}r_n \\
\mathrm{P^+ = (I-K_n T_h)P(I-K_h T_h)^\top + K_n R_n K_n^\top}
$$
