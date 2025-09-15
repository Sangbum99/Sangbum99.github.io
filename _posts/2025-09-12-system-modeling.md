---
layout: post
title:  "How to model Inertial Measurement Unit"
summary: "IMU modeling with kinematics"
author: sangbum
date: '2025-09-12 1:35:23 +0530'
category: ['Sensor modeling']
tags: IMU
thumbnail: /assets/img/posts/250912_IMU.jpg
keywords: inertial measurement unit, sensor modeling, kinematics
usemathjax: true
permalink: /blog/imu-modeling-with-kineamtics/
---
# System Modeling

## State Definition

$$
\mathbf{x}_k=
\begin{bmatrix} \mathbf{x}_B^\top& \mathbf{x}_S^\top\end{bmatrix}^\top
$$

$$
\mathbf{x}_B = \begin{bmatrix} {\mathbf{q}^{G}_{B}}^\top & {\mathbf{p}^{G}_{B}}^\top & {\mathbf{v}^{G}_{B}}^\top & \mathbf{b}_a^\top & \mathbf{b}_g^\top \end{bmatrix}^\top \in \mathbf{S}^3\times\mathbb{R}^{12}
$$

$$
\mathbf{x}_S = \begin{bmatrix}  \mathbf{x}_{S_1}^\top & \dots & \mathbf{x}_{S_N}^\top \end{bmatrix}^\top \in \mathbf{S}^{3N}\times\mathbb{R}^{3N}
$$

$$
\mathbf{x}_{S_i} = \begin{bmatrix} {\mathbf{q}^{G}_{S_i}}^\top & {\mathbf{p}^{G}_{S_i}}^\top \end{bmatrix}^\top \in \mathbf{S}^3\times\mathbb{R}^{3}
$$

## Error State Definition

$$
    \tilde{\mathbf{x}} = \begin{bmatrix} \tilde{\mathbf{x}}_B^\top & \tilde{\mathbf{x}}_{S_1}^\top & \dots & \tilde{\mathbf{x}}_{S_N}^\top \end{bmatrix}^\top \in \mathbb{R}^{15}
$$

$$
    \tilde{\mathbf{x}}_B = \begin{bmatrix} {\tilde{\boldsymbol{\theta}}^{G}_{B}}^\top & {\tilde{\mathbf{p}}^{G}_{B}}^\top & {\tilde{\mathbf{v}}^{G}_{B}}^\top & \tilde{\mathbf{b}}_a^\top & \tilde{\mathbf{b}}_g^\top \end{bmatrix}^\top \in \mathbb{R}^{15}
$$

$$
    \tilde{\mathbf{x}}_{S_i} = \begin{bmatrix} {\tilde{\boldsymbol{\theta}}^{G}_{S_i}}^\top & {\tilde{\mathbf{p}}^{G}_{S_i}}^\top \end{bmatrix}^\top \in \mathbb{R}^{6}
$$

## System Model

### IMU Modeling

$$
\mathbf{a}^B_m(t) = \mathbf{a}^B(t)+\mathbf{R}^B_G\mathbf{g}^G+\mathbf{b}_a(t)+\mathbf{n}_a(t)\\
\boldsymbol{\omega}^B_m(t) = \boldsymbol{\omega}^B(t)+\mathbf{b}_g(t)+\mathbf{n}_g(t)
$$

System Model은 Nonlinear 방정식으로 다음과 같이 정의된다.

$$
\mathbf{x}_{k|k-1}=f(\mathbf{x}_{k-1|k-1},\mathbf{a}^B_m,\boldsymbol{\omega}^B_m,\mathbf{n}_B)
$$

$$
\dot{\mathbf{x}}(t)=f(\mathbf{x}(t),\mathbf{a}^B_m(t),\boldsymbol{\omega}^B_m(t),\mathbf{n}_B(t))
$$

If it is a nominal (estimated) value,

$$
\hat{\mathbf{x}}_{k|k-1}=f(\hat{\mathbf{x}}_{k-1|k-1},\mathbf{a}^B_m,\boldsymbol{\omega}^B_m,\mathbf{0})
$$

### Nominal body state kinematics equation (time-propagation),

- Attitude kinematics

$$
\dot{\mathbf{q}}^G_B=\frac{1}{2}\mathbf{q}^G_B\otimes\mathbf{q}\{\boldsymbol{\omega}^B\}

\\\frac{1}{2}\Omega\{\mathbf{q}\{\boldsymbol{\omega}^B\}\}\mathbf{q}^G_B
$$

where $\Omega\{\omega^B\}$ represents a matrix for the **quaternion right composition operato**r, which is defined as

$$
\Omega\{\mathbf{q}\{\boldsymbol{\omega}^B\}\}= \begin{bmatrix} {q_s} & -\mathbf{q_v}^\top \\ 
\mathbf{q_v} & q_s\mathbf{I}_3- [\mathbf{q_v}]_\times^\top \end{bmatrix}
$$

- Position kinematics

$$
\dot{\mathbf{p}}^G_B=\mathbf{v}^G_B
$$

- Velocity kinematics

  $$
  \dot{\mathbf{v}}^G_B=\mathbf{R}^G_B(\mathbf{a}^B_m-\mathbf{b}_a-\mathbf{n}_a)+\mathbf{g}^G
  $$

- Bias equation

  $$
  \dot{\mathbf{b}}_a=\mathbf{n}_{wa}\\
  \dot{\mathbf{b}}_g=\mathbf{n}_{wg}
  $$

### Error-body state kinematics equation (linearized)

Nominal body state kinematics derive **the linearized continuous-time kinematics equation for error-body state** through perturbation.

$$
\dot{\tilde{\mathbf{x}}}_B(t)=\mathbf{F}_B(t)\tilde{\mathbf{x}}_B(t)+\mathbf{G}_B(t)\mathbf{n}_B(t)
$$

$\mathbf{F}_B$ : system Jacobian w.r.t error-state

$\mathbf{G}_B$ : system Jacobian w.r.t system noise vector

여기서 $\mathbf{F}_B$와 $\mathbf{G}_B$는 다음과 같이 구성된다. 

$$
\mathbf{F}_B= \begin{bmatrix}\mathbf{0} & \mathbf{0}&\mathbf{0}&\mathbf{0}& -\mathbf{R}^G_B \\

\mathbf{0} & \mathbf{0} & \mathbf{I_3} & \mathbf{0} & \mathbf{0} \\

-[\mathbf{R}^G_B\mathbf{a}^B]_\times & \mathbf{0} & \mathbf{0} & -\mathbf{R}^G_B & \mathbf{0} \\

\mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{0} \\

\mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{0} \\
 \end{bmatrix} \in \mathbb{R}^{15\times15}
$$

$$
\mathbf{G}_B=
\begin{bmatrix}
\mathbf{0} & -\mathbf{R}^G_B & \mathbf{0} & \mathbf{0} \\

\mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{0} \\

-\mathbf{R}^G_B & \mathbf{0} & \mathbf{0} & \mathbf{0} \\

\mathbf{0} & \mathbf{0} & \mathbf{I} & \mathbf{0} \\

\mathbf{0} & \mathbf{0} & \mathbf{0} & \mathbf{I}\\
 \end{bmatrix} \in \mathbb{R}^{15\times12}
$$

Discrete-time propagation equation for covariance of error-body state는 다음과 같이 정의된다. 

$$
\mathbf{P}_{B_{k|k-1}}=\Phi_{k-1}\mathbf{P}_{B_{k-1|k-1}}\Phi_{k-1}+\mathbf{Q}_B
$$

$\Phi_{k-1}$ : system transition matrix at k-1

$\mathbf{Q}_B$ : covariance of system noise vector

여기서, $\Phi_{k-1}$와 $\mathbf{Q}_B$는 다음과 같이 정의된다. 

$$
\dot{\Phi}(t_{k-1}+\tau, t_{k-1})=\mathbf{F}_B\Phi(t_{k-1}+\tau, t_{k-1})\\ \tau \in[t_{k-1},t_k]\\
\Phi_{k-1}=exp(\mathbf{F}_B dt)=\mathbf{I}+\mathbf{F}_Bdt+\frac{1}{2}\mathbf{F}_B *\mathbf{F}_Bdt^2 + \dots 
$$

Computational saving을 위해 Taylor series expansion은 2nd order로 근사한다.

$$
\mathbf{G}_B\mathbf{Q}_B\mathbf{G}_B^\top =
\mathbf{Q}_B \in \mathbb{R}^{12\times12}
$$

## Error-Modeling for more details

- **자세 오차 모델 유도**

자세 오차는 right-invariant error definition을 따른다. 

이 모델은 undesirable observability characteristic을 피하기 위함이다.

*M. Li and A. I. Mourikis, “Consistency of EKF-based visual-inertial
odometry,*

$$
\mathbf{R}^G_B=(\mathbf{I}_3 + [\tilde{\boldsymbol{\theta}}^G_B]_\times)\hat{\mathbf{R}}^G_B
$$

오차식 유도

$$
\mathbf{R}^G_B\hat{\mathbf{R}}^B_G=(\mathbf{I}_3 + [\tilde{\boldsymbol{\theta}}^G_B]_\times)
$$

$$
\dot{\mathbf{R}}^G_B \hat{\mathbf{R}}^B_G +
\mathbf{R}^G_B\dot{\hat{\mathbf{R}}}^B_G
=[\dot{\tilde{\boldsymbol{\theta}}}^G_B]_\times \\

=\mathbf{R}^G_B[\omega]_\times\hat{\mathbf{R}}^B_G + 
\mathbf{R}^G_B (\hat{\mathbf{R}}^G_B [\hat{\omega}]_\times)^\top \\

=\mathbf{R}^G_B[\omega-\hat{\omega}]_\times\hat{\mathbf{R}}^B_G
\\

=(\mathbf{I}+[\tilde{\boldsymbol{\theta}}^G_B]_\times) \hat{\mathbf{R}}^G_B[\tilde{\omega}]_\times\hat{\mathbf{R}}^B_G
\\

=[\hat{\mathbf{R}}^G_B\tilde{\omega}]_\times
 
$$

결과적으로,

$$
\dot{\tilde{\boldsymbol{\theta}}}^G_B = \hat{\mathbf{R}}^G_B\tilde{\omega}
= -\hat{\mathbf{R}}^G_B (b_g + n_g)
$$

- **위치 오차 모델 유도**

단순 Perturbation을 수행하면 된다. 

$$
\dot{\tilde{\mathbf{p}}}^G_B = \tilde{\mathbf{v}}^G_B
$$

- **속도 오차 모델 유도**

$$
\dot{(\mathbf{v}^G_B +\tilde{\mathbf{v}}^G_B}) = (\mathbf{I}+[\tilde{\boldsymbol{\theta}}^G_B]_\times)\hat{\mathbf{R}}^G_B(\mathbf{a}^B + \tilde{\mathbf{a}}^B)\\
$$

$$
\dot{\tilde{\mathbf{v}}^G_B} = -\hat{\mathbf{R}}^G_B(\mathbf{b}_a + \mathbf{n}_a)\\
$$