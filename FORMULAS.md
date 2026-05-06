# FORMULAS: Mathematical Foundations for PINO-MagNav

This document collects the main mathematical formulas involved in the PINO-MagNav framework.

The project focuses on:

```math
\text{magnetic actuation}
+
\text{fluid dynamics}
+
\text{soft-body deformation}
+
\text{neural operator learning}
+
\text{predictive control}
+
\text{environment inference}
```

---

# 1. Notation

## 1.1 Spatial and Temporal Variables

```math
\mathbf{x}\in \Omega
```

Spatial coordinate in the fluid domain.

```math
t\in[0,T]
```

Time.

```math
s\in[0,L]
```

Arc-length coordinate along the soft robot body.

---

## 1.2 Fluid Variables

```math
\mathbf{u}_f(\mathbf{x},t)
```

Fluid velocity field.

```math
p(\mathbf{x},t)
```

Pressure field.

```math
\rho
```

Fluid density.

```math
\mu
```

Dynamic viscosity.

```math
\nu=\frac{\mu}{\rho}
```

Kinematic viscosity.

---

## 1.3 Robot Variables

```math
\mathbf{r}(s,t)
```

Centerline of the soft robot.

```math
\mathbf{c}(t)
```

Centroid of the robot.

```math
\mathbf{R}(s,t)\in SO(3)
```

Local orientation frame of the robot cross-section.

```math
\kappa(s,t)
```

Curvature.

```math
\dot{\mathbf{c}}(t)
```

Centroid velocity.

```math
\rho_s
```

Solid density.

```math
A
```

Cross-sectional area.

```math
E
```

Young's modulus.

```math
I
```

Second moment of area.

---

## 1.4 Magnetic Variables

```math
\mathbf{B}(\mathbf{x},t)
```

Magnetic flux density.

```math
\nabla \mathbf{B}(\mathbf{x},t)
```

Magnetic field gradient.

```math
\mathbf{M}
```

Magnetization.

```math
V
```

Magnetic material volume.

```math
\mathbf{F}_m
```

Magnetic force.

```math
\boldsymbol{\tau}_m
```

Magnetic torque.

---

## 1.5 Control Variables

```math
u_t
=
[
B_x(t),B_y(t),B_z(t),
\partial_xB_x(t),
\partial_yB_y(t),
\partial_zB_z(t),
\omega(t),
\phi(t)
]
```

where:

- \(B_x,B_y,B_z\) are magnetic field components,
- \(\partial_iB_j\) are magnetic field gradient components,
- \(\omega(t)\) is magnetic actuation frequency,
- \(\phi(t)\) is magnetic actuation phase.

---

# 2. Magnetic Actuation

## 2.1 Magnetic Force

For a magnetic body with magnetization \(\mathbf{M}\), the magnetic force can be approximated as:

```math
\mathbf{F}_m
\approx
V(\mathbf{M}\cdot\nabla)\mathbf{B}
```

Component-wise:

```math
F_{m,i}
\approx
V
\sum_{j}
M_j
\frac{\partial B_i}{\partial x_j}
```

This shows that a magnetic field gradient is required to generate translational force.

---

## 2.2 Magnetic Torque

The magnetic torque is:

```math
\boldsymbol{\tau}_m
\approx
V\mathbf{M}\times\mathbf{B}
```

This torque tends to align the magnetization direction with the external magnetic field.

---

## 2.3 Magnetic Potential Energy

The magnetic potential energy is:

```math
U_m
=
-
V\mathbf{M}\cdot\mathbf{B}
```

The torque can be derived from:

```math
\boldsymbol{\tau}_m
=
-
\frac{\partial U_m}{\partial \boldsymbol{\theta}}
```

---

## 2.4 Magnetic Force from Potential Energy

The magnetic force can also be written as:

```math
\mathbf{F}_m
=
-\nabla U_m
```

Using:

```math
U_m=-V\mathbf{M}\cdot\mathbf{B}
```

we obtain:

```math
\mathbf{F}_m
=
\nabla
\left(
V\mathbf{M}\cdot\mathbf{B}
\right)
```

---

## 2.5 Rotating Magnetic Field

A planar rotating magnetic field can be represented as:

```math
\mathbf{B}(t)
=
B_0
[
\cos(\omega t),
\sin(\omega t),
0
]^T
```

A 3D rotating or conical field can be represented as:

```math
\mathbf{B}(t)
=
B_0
[
\sin\alpha\cos(\omega t),
\sin\alpha\sin(\omega t),
\cos\alpha
]^T
```

where \(\alpha\) is the cone angle.

---

# 3. Fluid Dynamics

## 3.1 Incompressible Navier-Stokes Equations

The incompressible Navier-Stokes equations are:

```math
\rho
\left(
\frac{\partial \mathbf{u}_f}{\partial t}
+
\mathbf{u}_f\cdot\nabla\mathbf{u}_f
\right)
=
-\nabla p
+
\mu\nabla^2\mathbf{u}_f
+
\mathbf{f}_{\text{robot}}
```

```math
\nabla\cdot\mathbf{u}_f=0
```

where \(\mathbf{f}_{\text{robot}}\) represents the force density exerted by the robot on the fluid.

---

## 3.2 Stokes Flow Approximation

At low Reynolds number, inertial terms can be neglected:

```math
0
=
-\nabla p
+
\mu\nabla^2\mathbf{u}_f
+
\mathbf{f}_{\text{robot}}
```

```math
\nabla\cdot\mathbf{u}_f=0
```

---

## 3.3 Reynolds Number

The Reynolds number is:

```math
Re
=
\frac{\rho U L}{\mu}
```

where:

- \(U\) is characteristic velocity,
- \(L\) is characteristic length,
- \(\rho\) is fluid density,
- \(\mu\) is dynamic viscosity.

Low Reynolds number means:

```math
Re\ll 1
```

---

## 3.4 No-Slip Boundary Condition

On solid walls:

```math
\mathbf{u}_f(\mathbf{x},t)=0,
\quad
\mathbf{x}\in\partial\Omega_{\text{wall}}
```

---

## 3.5 Inlet Boundary Condition

At the inlet:

```math
\mathbf{u}_f(\mathbf{x},t)
=
\mathbf{u}_{\text{in}}(\mathbf{x},t),
\quad
\mathbf{x}\in\partial\Omega_{\text{in}}
```

---

## 3.6 Outlet Boundary Condition

At the outlet:

```math
p(\mathbf{x},t)
=
p_{\text{out}},
\quad
\mathbf{x}\in\partial\Omega_{\text{out}}
```

---

# 4. Soft Robot Dynamics

## 4.1 Centerline Representation

The soft robot centerline is:

```math
\mathbf{r}(s,t)
=
[
x(s,t),
y(s,t),
z(s,t)
]^T
```

The tangent vector is:

```math
\mathbf{t}(s,t)
=
\frac{\partial \mathbf{r}}{\partial s}
```

For an inextensible rod:

```math
\left\|
\frac{\partial \mathbf{r}}{\partial s}
\right\|
=
1
```

---

## 4.2 Curvature

The curvature is:

```math
\kappa(s,t)
=
\left\|
\frac{\partial^2\mathbf{r}}{\partial s^2}
\right\|
```

---

## 4.3 Cosserat Rod Force Balance

The force balance equation is:

```math
\rho_s A
\frac{\partial^2 \mathbf{r}}{\partial t^2}
=
\frac{\partial \mathbf{n}}{\partial s}
+
\mathbf{f}_{\text{fluid}}
+
\mathbf{f}_{\text{mag}}
+
\mathbf{f}_{\text{contact}}
```

where:

- \(\mathbf{n}\) is internal force,
- \(\mathbf{f}_{\text{fluid}}\) is hydrodynamic force density,
- \(\mathbf{f}_{\text{mag}}\) is magnetic force density,
- \(\mathbf{f}_{\text{contact}}\) is contact force density.

---

## 4.4 Cosserat Rod Moment Balance

The moment balance is:

```math
\frac{\partial \mathbf{m}}{\partial s}
+
\frac{\partial \mathbf{r}}{\partial s}
\times
\mathbf{n}
+
\boldsymbol{\tau}_{\text{fluid}}
+
\boldsymbol{\tau}_{\text{mag}}
=
\mathbf{0}
```

where:

- \(\mathbf{m}\) is internal moment,
- \(\boldsymbol{\tau}_{\text{fluid}}\) is fluid torque density,
- \(\boldsymbol{\tau}_{\text{mag}}\) is magnetic torque density.

---

## 4.5 Linear Elastic Bending Moment

For a simple elastic rod:

```math
\mathbf{m}
=
EI\kappa\mathbf{b}
```

where:

- \(E\) is Young's modulus,
- \(I\) is the second moment of area,
- \(\mathbf{b}\) is the binormal direction.

---

## 4.6 Modal Representation of Soft Robot Shape

The robot centerline can be approximated by shape modes:

```math
\mathbf{r}(s,t)
=
\mathbf{c}(t)
+
\sum_{k=1}^{K}
a_k(t)\boldsymbol{\phi}_k(s)
```

where:

- \(\mathbf{c}(t)\) is the centroid,
- \(a_k(t)\) is the \(k\)-th modal coefficient,
- \(\boldsymbol{\phi}_k(s)\) is the \(k\)-th shape basis.

---

## 4.7 State Vector with Shape Modes

A compact robot state can be written as:

```math
x_t
=
[
\mathbf{c}_t,
\dot{\mathbf{c}}_t,
\mathbf{R}_t,
\boldsymbol{\omega}_t,
a_1(t),
a_2(t),
\ldots,
a_K(t)
]
```

---

# 5. Fluid-Structure Coupling

## 5.1 Robot-to-Fluid Force Density

The robot applies force to the surrounding fluid:

```math
\mathbf{f}_{\text{robot}}(\mathbf{x},t)
=
\int_0^L
\mathbf{f}_{\text{body}}(s,t)
\delta
(
\mathbf{x}-\mathbf{r}(s,t)
)
ds
```

where \(\delta(\cdot)\) is a Dirac delta function or regularized immersed-boundary kernel.

---

## 5.2 Fluid Drag on Robot

A simplified drag model is:

```math
\mathbf{f}_{\text{fluid}}
=
-\mathbf{D}
\left(
\frac{\partial \mathbf{r}}{\partial t}
-
\mathbf{u}_f(\mathbf{r}(s,t),t)
\right)
```

where \(\mathbf{D}\) is a drag matrix.

---

## 5.3 Anisotropic Resistive Force Theory

For slender-body motion in low Reynolds number flow:

```math
\mathbf{f}_{\text{fluid}}
=
-\xi_{\parallel}
\mathbf{v}_{\parallel}
-
\xi_{\perp}
\mathbf{v}_{\perp}
```

where:

```math
\mathbf{v}
=
\frac{\partial \mathbf{r}}{\partial t}
-
\mathbf{u}_f(\mathbf{r}(s,t),t)
```

```math
\mathbf{v}_{\parallel}
=
(\mathbf{v}\cdot\mathbf{t})\mathbf{t}
```

```math
\mathbf{v}_{\perp}
=
\mathbf{v}
-
\mathbf{v}_{\parallel}
```

---

# 6. Neural Operator Formulation

## 6.1 General Operator Learning Problem

A neural operator learns a mapping between function spaces:

```math
\mathcal{G}_{\theta}:
\mathcal{A}
\rightarrow
\mathcal{U}
```

where:

- \(\mathcal{A}\) is the input function space,
- \(\mathcal{U}\) is the output function space.

In this project:

```math
\mathcal{G}_{\theta}
:
[
\Omega,
\mathbf{u}_f^0,
\mathbf{B}_{0:T},
\nabla\mathbf{B}_{0:T},
\theta_{\text{robot}}
]
\mapsto
[
\mathbf{r}_{0:T},
\mathbf{c}_{0:T},
\mathbf{u}_{f,0:T},
p_{0:T}
]
```

---

## 6.2 Forward PINO Dynamics Model

The forward model is:

```math
\hat{Y}_{0:T}
=
\mathcal{G}_{\theta}
(
X_0,
U_{0:T},
\Omega,
\eta
)
```

where:

```math
\hat{Y}_{0:T}
=
[
\hat{\mathbf{r}}_{0:T},
\hat{\mathbf{c}}_{0:T},
\hat{\mathbf{u}}_{f,0:T},
\hat{p}_{0:T}
]
```

```math
X_0
=
[
\mathbf{r}_0,
\mathbf{c}_0,
\mathbf{u}_{f,0}
]
```

```math
U_{0:T}
=
[
u_0,u_1,\ldots,u_T
]
```

```math
\eta
=
[
\rho,\mu,E,M,V,L,A,I
]
```

---

## 6.3 Fourier Neural Operator Layer

A generic Fourier neural operator layer can be written as:

```math
v_{l+1}(\mathbf{x})
=
\sigma
\left(
W_l v_l(\mathbf{x})
+
\mathcal{F}^{-1}
\left(
R_l
\cdot
\mathcal{F}
(
v_l
)
\right)
(\mathbf{x})
\right)
```

where:

- \(v_l\) is the feature field at layer \(l\),
- \(W_l\) is a local linear transformation,
- \(\mathcal{F}\) is the Fourier transform,
- \(R_l\) is a learnable spectral kernel,
- \(\sigma\) is a nonlinear activation function.

---

# 7. PINO Loss Functions

## 7.1 Total PINO Loss

The total loss is:

```math
\mathcal{L}_{\mathrm{PINO}}
=
\lambda_d\mathcal{L}_{\mathrm{data}}
+
\lambda_f\mathcal{L}_{\mathrm{fluid}}
+
\lambda_s\mathcal{L}_{\mathrm{solid}}
+
\lambda_m\mathcal{L}_{\mathrm{mag}}
+
\lambda_b\mathcal{L}_{\mathrm{BC}}
+
\lambda_c\mathcal{L}_{\mathrm{constraint}}
```

---

## 7.2 Data Loss

```math
\mathcal{L}_{\mathrm{data}}
=
\|
\hat{\mathbf{r}}
-
\mathbf{r}^{\mathrm{true}}
\|^2
+
\|
\hat{\mathbf{c}}
-
\mathbf{c}^{\mathrm{true}}
\|^2
+
\|
\hat{\mathbf{u}}_f
-
\mathbf{u}_f^{\mathrm{true}}
\|^2
+
\|
\hat{p}
-
p^{\mathrm{true}}
\|^2
```

---

## 7.3 Fluid Residual Loss

```math
\mathcal{R}_{NS}
=
\rho
\left(
\partial_t\hat{\mathbf{u}}_f
+
\hat{\mathbf{u}}_f\cdot\nabla\hat{\mathbf{u}}_f
\right)
+
\nabla \hat{p}
-
\mu\nabla^2\hat{\mathbf{u}}_f
-
\hat{\mathbf{f}}_{\text{robot}}
```

```math
\mathcal{L}_{\mathrm{fluid}}
=
\|
\mathcal{R}_{NS}
\|^2
+
\|
\nabla\cdot\hat{\mathbf{u}}_f
\|^2
```

---

## 7.4 Stokes Residual Loss

For low Reynolds number flow:

```math
\mathcal{R}_{Stokes}
=
\nabla \hat{p}
-
\mu\nabla^2\hat{\mathbf{u}}_f
-
\hat{\mathbf{f}}_{\text{robot}}
```

```math
\mathcal{L}_{Stokes}
=
\|
\mathcal{R}_{Stokes}
\|^2
+
\|
\nabla\cdot\hat{\mathbf{u}}_f
\|^2
```

---

## 7.5 Solid Dynamics Residual Loss

```math
\mathcal{R}_{solid}
=
\rho_s A
\partial_{tt}\hat{\mathbf{r}}
-
\partial_s\hat{\mathbf{n}}
-
\hat{\mathbf{f}}_{\text{fluid}}
-
\hat{\mathbf{f}}_{\text{mag}}
-
\hat{\mathbf{f}}_{\text{contact}}
```

```math
\mathcal{L}_{solid}
=
\|
\mathcal{R}_{solid}
\|^2
```

---

## 7.6 Magnetic Consistency Loss

```math
\mathcal{L}_{mag}
=
\|
\hat{\mathbf{F}}_m
-
V(\mathbf{M}\cdot\nabla)\mathbf{B}
\|^2
+
\|
\hat{\boldsymbol{\tau}}_m
-
V\mathbf{M}\times\mathbf{B}
\|^2
```

---

## 7.7 Boundary Condition Loss

```math
\mathcal{L}_{BC}
=
\|
\hat{\mathbf{u}}_f
-
\mathbf{u}_{wall}
\|^2_{\partial\Omega_{wall}}
+
\|
\hat{\mathbf{u}}_f
-
\mathbf{u}_{in}
\|^2_{\partial\Omega_{in}}
+
\|
\hat{p}
-
p_{out}
\|^2_{\partial\Omega_{out}}
```

For no-slip walls:

```math
\mathbf{u}_{wall}=0
```

---

## 7.8 Constraint Loss

```math
\mathcal{L}_{constraint}
=
\lambda_B
\max(0,\|\mathbf{B}\|-B_{\max})^2
+
\lambda_G
\max(0,\|\nabla\mathbf{B}\|-G_{\max})^2
+
\lambda_{\kappa}
\max(0,\kappa-\kappa_{\max})^2
+
\lambda_o
\max(0,d_{\min}-d(\mathbf{c},\mathcal{O}))^2
```

---

# 8. Predictive Control

## 8.1 Open-Loop Predictive Navigation

In a deterministic environment, a magnetic control sequence can be planned as:

```math
u^*_{0:T}
=
\arg\min_{u_{0:T}}
J
\left(
\mathcal{G}_{\theta}
(
x_0,u_{0:T},\Omega,\mathbf{u}_f
)
,
u_{0:T}
\right)
```

The predicted trajectory is:

```math
\hat{x}_{0:T}
=
\mathcal{G}_{\theta}
(
x_0,u_{0:T},\Omega,\mathbf{u}_f
)
```

---

## 8.2 Navigation Cost Function

```math
J
=
\sum_{t=0}^{T}
\|
\hat{\mathbf{c}}_t
-
\mathbf{c}_{ref,t}
\|_{Q}^{2}
+
\|
\hat{\mathbf{c}}_T
-
\mathbf{c}_{goal}
\|_{Q_f}^{2}
+
\sum_{t=0}^{T-1}
\|u_t\|_R^2
+
\lambda_{\Delta u}
\sum_{t=1}^{T-1}
\|u_t-u_{t-1}\|^2
+
\lambda_o
\sum_{t=0}^{T}
\Phi_{obs}(\hat{\mathbf{c}}_t)
```

---

## 8.3 Obstacle Avoidance Potential

A common obstacle penalty is:

```math
\Phi_{obs}(\mathbf{c})
=
\max
(
0,
d_{safe}-d(\mathbf{c},\mathcal{O})
)^2
```

where \(d(\mathbf{c},\mathcal{O})\) is the distance from the robot centroid to the obstacle set.

---

## 8.4 Closed-Loop PINO-MPC

At time \(t\), solve:

```math
u^*_{t:t+H}
=
\arg\min_{u_{t:t+H}}
\sum_{k=0}^{H}
\ell
(
\hat{x}_{t+k},
u_{t+k}
)
+
\ell_f
(
\hat{x}_{t+H}
)
```

subject to:

```math
\hat{x}_{t:t+H}
=
\mathcal{G}_{\theta}
(
\hat{x}_t,
u_{t:t+H},
\Omega,
\mathbf{u}_f,
\theta_{\text{robot}}
)
```

```math
u_{t+k}\in\mathcal{U}
```

```math
\hat{x}_{t+k}\in\mathcal{X}_{safe}
```

```math
d(\hat{\mathbf{c}}_{t+k},\mathcal{O})
\geq
d_{\min}
```

Apply only:

```math
u_t = u_t^*
```

Then re-observe and repeat.

---

## 8.5 Control Constraints

Magnetic field limit:

```math
\|\mathbf{B}(t)\|
\leq
B_{\max}
```

Magnetic gradient limit:

```math
\|\nabla\mathbf{B}(t)\|
\leq
G_{\max}
```

Frequency limit:

```math
\omega_{\min}
\leq
\omega(t)
\leq
\omega_{\max}
```

Deformation limit:

```math
\kappa(s,t)
\leq
\kappa_{\max}
```

Obstacle clearance:

```math
d(\mathbf{c}_t,\mathcal{O})
\geq
d_{\min}
```

---

# 9. State Estimation

## 9.1 Observation Model

If sensors are available:

```math
y_t
=
h(x_t)
+
\epsilon_t
```

where:

- \(y_t\) is the observation,
- \(x_t\) is the latent robot state,
- \(h(\cdot)\) is the observation function,
- \(\epsilon_t\) is measurement noise.

---

## 9.2 State Estimator

A general estimator is:

```math
\hat{x}_t
=
\mathcal{E}_{\psi}
(
y_{0:t},
u_{0:t-1}
)
```

For direct visual estimation:

```math
\hat{x}_t
=
\mathcal{E}_{\psi}(y_t)
```

---

## 9.3 Recursive Bayesian Filtering

The belief update can be written as:

```math
p(x_t|y_{0:t},u_{0:t-1})
\propto
p(y_t|x_t)
\int
p(x_t|x_{t-1},u_{t-1})
p(x_{t-1}|y_{0:t-1},u_{0:t-2})
dx_{t-1}
```

This formulation includes Kalman filters, extended Kalman filters, unscented Kalman filters, and particle filters as special cases.

---

# 10. Environment Inference

## 10.1 Inverse PINO

An inverse PINO maps robot motion and magnetic inputs to hidden environmental variables:

```math
\mathcal{I}_{\phi}
:
[
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
]
\mapsto
[
\hat{\mathbf{u}}_f(\mathbf{x}),
\hat{\mu},
\hat{\Omega},
\hat{\tau}_{wall}
]
```

---

## 10.2 Flow Field Estimation

```math
\hat{\mathbf{u}}_f(\mathbf{x},t)
=
\mathcal{I}_{\phi}
(
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
)
```

Loss:

```math
\mathcal{L}_{flow-inv}
=
\|
\hat{\mathbf{u}}_f
-
\mathbf{u}_f^{true}
\|^2
+
\lambda_{div}
\|
\nabla\cdot\hat{\mathbf{u}}_f
\|^2
+
\lambda_{NS}
\|
\mathcal{R}_{NS}
\|^2
```

---

## 10.3 Viscosity Estimation

```math
\hat{\mu}
=
\mathcal{I}_{\phi}
(
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
)
```

Loss:

```math
\mathcal{L}_{\mu}
=
|
\hat{\mu}
-
\mu
|^2
```

---

## 10.4 Geometry Estimation Using Signed Distance Function

The environment geometry can be represented by a signed distance function:

```math
\phi_{\Omega}(\mathbf{x})
=
\begin{cases}
-d(\mathbf{x},\partial\Omega), & \mathbf{x}\in\Omega \\
0, & \mathbf{x}\in\partial\Omega \\
d(\mathbf{x},\partial\Omega), & \mathbf{x}\notin\Omega
\end{cases}
```

The inverse model predicts:

```math
\hat{\phi}_{\Omega}(\mathbf{x})
=
\mathcal{I}_{\phi}
(
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
)
```

Loss:

```math
\mathcal{L}_{geom}
=
\|
\hat{\phi}_{\Omega}
-
\phi_{\Omega}
\|^2
```

---

## 10.5 Wall Proximity Estimation

```math
\hat{d}_{wall}(t)
=
\mathcal{I}_{\phi}
(
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
)
```

Loss:

```math
\mathcal{L}_{wall}
=
|
\hat{d}_{wall}
-
d_{wall}^{true}
|^2
```

---

# 11. Adaptive PINO-MPC

When environmental variables are inferred online:

```math
\hat{\eta}_t
=
[
\hat{\mu}_t,
\hat{\mathbf{u}}_{f,t},
\hat{\Omega}_t
]
```

the control problem becomes:

```math
u^*_{t:t+H}
=
\arg\min_{u_{t:t+H}}
J
\left(
\mathcal{G}_{\theta}
(
\hat{x}_t,
u_{t:t+H},
\hat{\eta}_t
)
\right)
```

subject to:

```math
\hat{x}_{t:t+H}
=
\mathcal{G}_{\theta}
(
\hat{x}_t,
u_{t:t+H},
\hat{\eta}_t
)
```

This enables adaptive navigation under changing flow or uncertain environmental conditions.

---

# 12. Differentiable Control Optimization

Because the PINO model is differentiable, the gradient of the control objective can be computed as:

```math
\frac{\partial J}{\partial u_{0:T}}
=
\frac{\partial J}{\partial \hat{x}_{0:T}}
\frac{\partial \hat{x}_{0:T}}{\partial u_{0:T}}
```

where:

```math
\hat{x}_{0:T}
=
\mathcal{G}_{\theta}
(
x_0,u_{0:T}
)
```

This allows gradient-based optimization of magnetic control inputs.

---

# 13. Sampling-Based Control

## 13.1 MPPI / CEM Rollout

Sample candidate control sequences:

```math
u^{(i)}_{0:T}
=
u_{0:T}
+
\epsilon^{(i)}_{0:T}
```

Predict trajectories:

```math
\hat{x}^{(i)}_{0:T}
=
\mathcal{G}_{\theta}
(
x_0,u^{(i)}_{0:T}
)
```

Evaluate cost:

```math
J^{(i)}
=
J
(
\hat{x}^{(i)}_{0:T},
u^{(i)}_{0:T}
)
```

Select or weight the best trajectories.

---

## 13.2 Soft-Min Control Update

A weighted update can be written as:

```math
u_{0:T}^{new}
=
\sum_{i=1}^{N}
w_i
u^{(i)}_{0:T}
```

where:

```math
w_i
=
\frac{
\exp
(
-\frac{1}{\lambda}J^{(i)}
)
}{
\sum_{j=1}^{N}
\exp
(
-\frac{1}{\lambda}J^{(j)}
)
}
```

---

# 14. Evaluation Metrics

## 14.1 Final Goal Error

```math
e_{goal}
=
\|
\mathbf{c}_T
-
\mathbf{c}_{goal}
\|
```

---

## 14.2 Trajectory Tracking Error

```math
e_{track}
=
\frac{1}{T}
\sum_{t=0}^{T}
\|
\mathbf{c}_t
-
\mathbf{c}_{ref,t}
\|
```

---

## 14.3 Deformation Prediction Error

```math
e_{shape}
=
\frac{1}{T}
\sum_{t=0}^{T}
\|
\hat{\mathbf{r}}_t
-
\mathbf{r}_t
\|^2
```

---

## 14.4 Flow Prediction Error

```math
e_{flow}
=
\frac{1}{T}
\sum_{t=0}^{T}
\|
\hat{\mathbf{u}}_{f,t}
-
\mathbf{u}_{f,t}
\|^2
```

---

## 14.5 Control Energy

```math
E_u
=
\sum_{t=0}^{T}
\|u_t\|^2
```

---

## 14.6 Collision Rate

```math
R_{collision}
=
\frac{N_{collision}}{N_{trials}}
```

---

## 14.7 Success Rate

```math
R_{success}
=
\frac{N_{success}}{N_{trials}}
```

---

# 15. Full Framework Summary

The complete framework can be summarized as:

```math
\boxed{
\text{Magnetic Input}
+
\text{Flow Field}
+
\text{Geometry}
+
\text{Robot Parameters}
\rightarrow
\text{PINO Dynamics Prediction}
}
```

```math
\boxed{
\text{PINO Prediction}
\rightarrow
\text{MPC / MPPI / CEM}
\rightarrow
\text{Magnetic Control}
}
```

```math
\boxed{
\text{Robot Motion}
+
\text{Magnetic Input}
\rightarrow
\text{Inverse PINO}
\rightarrow
\text{Environment Inference}
}
```

The overall research idea is:

```math
\boxed{
\text{Physics-Informed Neural Operator}
+
\text{Magnetically Actuated Soft Robot}
+
\text{Predictive Control}
+
\text{Environmental Inference}
}
```

