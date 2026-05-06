# PINO-MagNav: Physics-Informed Neural Operator Predictive Control for Magnetically Actuated Soft Robots in Fluidic Environments

## 1. Project Overview

**PINO-MagNav** is a research-oriented framework for autonomous navigation and environmental prediction of **magnetically actuated soft robots** operating in fluidic environments.

The central idea is to use a **Physics-Informed Neural Operator (PINO)** as a fast differentiable surrogate model for the coupled dynamics among:

- magnetic actuation,
- fluid flow,
- soft-body deformation,
- robot navigation,
- and environment inference.

Unlike conventional black-box trajectory predictors, this framework aims to learn a **physics-constrained operator** that maps magnetic control inputs, flow fields, environmental geometry, and robot parameters to future robot states, deformation fields, and potentially surrounding fluid conditions.

The framework is designed for two operating modes:

1. **Vision-free / state-free predictive navigation** in deterministic environments.
2. **State-aware closed-loop predictive control** when real-time robot state measurements are available.

---

## 2. Research Motivation

Magnetically actuated soft robots are promising for navigation in confined, fluid-filled environments such as:

- microfluidic channels,
- biomedical environments,
- lab-on-a-chip systems,
- vascular-like networks,
- confined inspection channels,
- and complex fluidic terrains.

However, controlling such robots remains difficult because the system is governed by strongly coupled nonlinear dynamics:

\[
\text{magnetic field} \leftrightarrow \text{soft robot deformation} \leftrightarrow \text{fluid flow} \leftrightarrow \text{navigation}
\]

Traditional model-based control is often limited by the high computational cost of fluid-structure interaction simulations. Purely data-driven models may be fast but often fail to generalize outside the training distribution.

This project proposes a middle path:

> Use a physics-informed neural operator as a fast, differentiable, and physically constrained surrogate model for predictive control.

---

## 3. Core Research Question

Can a physics-informed neural operator be trained to approximate the coupled magnetic-fluid-soft-body dynamics of a magnetically actuated soft robot, and can this learned operator be embedded into a predictive control framework for autonomous navigation and environmental inference?

---

## 4. Main Contributions

This project aims to contribute the following:

### 4.1 PINO-Based Coupled Dynamics Modeling

Develop a neural operator that learns the mapping:

\[
\left[
\Omega,\mathbf{u}_f^0,\mathbf{B}_{0:T},\nabla \mathbf{B}_{0:T},\theta_{\text{robot}}
\right]
\mapsto
\left[
\mathbf{r}_{0:T},\mathbf{c}_{0:T},\mathbf{u}_{f,0:T},p_{0:T}
\right]
\]

where:

- \(\Omega\) is the fluidic domain,
- \(\mathbf{u}_f^0\) is the initial flow field,
- \(\mathbf{B}_{0:T}\) is the magnetic field sequence,
- \(\nabla \mathbf{B}_{0:T}\) is the magnetic field gradient sequence,
- \(\theta_{\text{robot}}\) represents robot material and geometric parameters,
- \(\mathbf{r}_{0:T}\) is the soft robot centerline evolution,
- \(\mathbf{c}_{0:T}\) is the robot centroid trajectory,
- \(\mathbf{u}_{f,0:T}\) is the fluid velocity field,
- \(p_{0:T}\) is the pressure field.

---

### 4.2 Vision-Free Predictive Navigation

In deterministic environments, the framework can operate without real-time state feedback by planning a magnetic control sequence in advance:

\[
u^*_{0:T}
=
\arg\min_{u_{0:T}}
J\left(
\mathcal{G}_{\theta}(x_0,u_{0:T},\Omega,\mathbf{u}_f)
\right)
\]

This mode is suitable for highly controlled environments such as microfluidic chips with known flow conditions and geometry.

---

### 4.3 Closed-Loop PINO-MPC

When real-time state measurements are available, the system performs receding-horizon control:

\[
u^*_{t:t+H}
=
\arg\min_{u_{t:t+H}}
\sum_{k=0}^{H}
\ell(\hat{x}_{t+k},u_{t+k})
+
\ell_f(\hat{x}_{t+H})
\]

subject to:

\[
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
\]

Only the first control input is applied, and the optimization is repeated after the next observation.

---

### 4.4 Environment Inference

The robot can also serve as a mobile sensor. Its motion and deformation contain information about the surrounding environment.

An inverse PINO can be used to infer hidden environmental variables:

\[
\mathcal{I}_{\phi}:
[
\mathbf{r}_{0:t},
\mathbf{B}_{0:t}
]
\mapsto
[
\hat{\mathbf{u}}_f(\mathbf{x}),
\hat{\mu},
\hat{\Omega},
\hat{\tau}_{\text{wall}}
]
\]

This enables prediction of:

- local flow velocity,
- viscosity,
- wall proximity,
- obstacle geometry,
- shear stress,
- and local environmental disturbances.

---

## 5. System Architecture

The proposed system consists of four main modules:

```text
+-----------------------+
|  Environment Encoder  |
|  Geometry / Flow / BC |
+----------+------------+
           |
           v
+-----------------------+
|  PINO Dynamics Model  |
|  Magnetic-Fluid-Soft  |
|  Coupled Predictor    |
+----------+------------+
           |
           v
+-----------------------+
|  Predictive Control   |
|  MPC / MPPI / CEM     |
+----------+------------+
           |
           v
+-----------------------+
| Magnetic Actuation    |
| Coils / Field Control |
+-----------------------+
```

For closed-loop operation:

```text
Camera / Sensor
      |
      v
State Estimator
      |
      v
PINO Predictor
      |
      v
MPC Controller
      |
      v
Magnetic Actuator
      |
      v
Soft Robot in Flow
```

---

## 6. Modeling Assumptions

The framework can be studied under different levels of complexity.

### Level 1: Simplified 2D Model

- Quasi-2D microfluidic channel.
- Soft robot represented as a magnetic elastic filament.
- Low Reynolds number flow.
- Resistive force approximation.
- Obstacles represented by signed distance functions.

### Level 2: Cosserat Rod Model

- Soft robot modeled as a deformable rod.
- Distributed magnetization along the robot body.
- Hydrodynamic drag forces.
- Wall contact and deformation constraints.

### Level 3: Fluid-Structure Interaction

- Full or reduced Navier-Stokes solver.
- Immersed boundary method or FEM-CFD coupling.
- Magnetic body force and torque.
- Time-dependent flow and deformation.

### Level 4: Experimental Validation

- Magnetic coil system.
- Microfluidic channel.
- Soft magnetic elastomer robot.
- Overhead camera or microscopy.
- Optional particle image velocimetry for flow ground truth.

---

## 7. Input and Output Variables

### 7.1 Inputs

The model may take the following inputs:

\[
\Omega
\]

environment geometry,

\[
\mathbf{u}_f(\mathbf{x},0)
\]

initial flow field,

\[
\mathbf{B}(\mathbf{x},t)
\]

magnetic field,

\[
\nabla \mathbf{B}(\mathbf{x},t)
\]

magnetic field gradient,

\[
\theta_{\text{robot}}
=
[
E,\rho_s,M,V,L,A,I
]
\]

robot material and geometric parameters.

---

### 7.2 Outputs

The PINO model may predict:

\[
\mathbf{r}(s,t)
\]

robot centerline,

\[
\mathbf{c}(t)
\]

centroid trajectory,

\[
\mathbf{u}_f(\mathbf{x},t)
\]

fluid velocity field,

\[
p(\mathbf{x},t)
\]

pressure field,

\[
\kappa(s,t)
\]

curvature,

\[
\mathbf{F}_m(t), \boldsymbol{\tau}_m(t)
\]

magnetic force and torque,

\[
\mathbf{F}_{fluid}(t)
\]

hydrodynamic force.

---

## 8. PINO Loss Function

The overall training loss is:

\[
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
\]

This combines:

- supervised data loss,
- fluid PDE residuals,
- soft-body dynamics residuals,
- magnetic actuation consistency,
- boundary condition penalties,
- safety and feasibility constraints.

---

## 9. Predictive Control Objective

The navigation cost may be written as:

\[
J
=
\sum_{t=0}^{T}
\|\hat{\mathbf{c}}_t-\mathbf{c}_{\mathrm{ref},t}\|_{Q}^{2}
+
\|\hat{\mathbf{c}}_T-\mathbf{c}_{\mathrm{goal}}\|_{Q_f}^{2}
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
\Phi_{\mathrm{obs}}(\hat{\mathbf{c}}_t)
\]

The controller seeks to minimize tracking error, terminal error, control energy, control variation, and obstacle collision risk.

---

## 10. Control Input

The magnetic control input can be defined as:

\[
u_t
=
[
B_x(t),B_y(t),B_z(t),
\partial_x B_x(t),
\partial_y B_y(t),
\partial_z B_z(t),
\omega(t),
\phi(t)
]
\]

where:

- \(B_x,B_y,B_z\) are magnetic field components,
- \(\partial_i B_j\) are field gradient terms,
- \(\omega(t)\) is actuation frequency,
- \(\phi(t)\) is actuation phase.

---

## 11. Constraints

Typical constraints include:

### Magnetic Field Limit

\[
\|\mathbf{B}(t)\|\leq B_{\max}
\]

### Magnetic Gradient Limit

\[
\|\nabla \mathbf{B}(t)\|\leq G_{\max}
\]

### Frequency Limit

\[
\omega_{\min}
\leq
\omega(t)
\leq
\omega_{\max}
\]

### Obstacle Avoidance

\[
d(\mathbf{c}_t,\mathcal{O})
\geq
d_{\min}
\]

### Deformation Limit

\[
\kappa(s,t)
\leq
\kappa_{\max}
\]

---

## 12. Dataset Generation

Training data can be generated from simulation and experiments.

Each trajectory may contain:

\[
\{
\Omega,
\mathbf{u}_{f,0},
\mathbf{B}_{0:T},
\nabla \mathbf{B}_{0:T},
\mathbf{r}_{0:T},
\mathbf{c}_{0:T},
\mathbf{u}_{f,0:T},
p_{0:T},
\text{contact}_{0:T}
\}
\]

Parameter variations should include:

- flow speed,
- fluid viscosity,
- magnetic field amplitude,
- magnetic actuation frequency,
- magnetic gradient,
- robot stiffness,
- magnetization pattern,
- channel geometry,
- obstacle layout,
- initial pose.

---

## 13. Recommended Experimental Tasks

### Task 1: Reaching Target

The robot starts from a fixed initial position and reaches a target point.

### Task 2: Path Following

The robot follows a predefined reference path under background flow.

### Task 3: Obstacle Avoidance

The robot navigates around fixed obstacles.

### Task 4: Upstream Navigation

The robot moves against the direction of flow.

### Task 5: Narrow Passage Navigation

The robot passes through a constrained region without collision.

### Task 6: Environmental Inference

The robot estimates unknown local flow velocity, viscosity, or wall proximity from its deformation response.

---

## 14. Evaluation Metrics

The framework can be evaluated using:

- final distance to goal,
- trajectory tracking error,
- collision rate,
- success rate,
- energy consumption,
- computation time per MPC step,
- long-horizon rollout stability,
- generalization to unseen flow fields,
- generalization to unseen geometries,
- deformation prediction error,
- environmental inference error.

---

## 15. Suggested Repository Structure

```text
PINO-MagNav/
│
├── README.md
├── FORMULAS.md
├── configs/
│   ├── model.yaml
│   ├── training.yaml
│   └── control.yaml
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── synthetic/
│
├── src/
│   ├── models/
│   │   ├── pino.py
│   │   ├── fno.py
│   │   └── inverse_pino.py
│   │
│   ├── physics/
│   │   ├── magnetic.py
│   │   ├── fluid.py
│   │   ├── soft_body.py
│   │   └── constraints.py
│   │
│   ├── control/
│   │   ├── mpc.py
│   │   ├── mppi.py
│   │   └── trajectory_optimizer.py
│   │
│   ├── estimation/
│   │   ├── state_estimator.py
│   │   └── environment_estimator.py
│   │
│   └── utils/
│       ├── geometry.py
│       ├── visualization.py
│       └── metrics.py
│
├── scripts/
│   ├── generate_data.py
│   ├── train_pino.py
│   ├── train_inverse_pino.py
│   ├── run_mpc.py
│   └── evaluate.py
│
├── notebooks/
│   ├── 01_data_generation.ipynb
│   ├── 02_pino_training.ipynb
│   ├── 03_pino_mpc_navigation.ipynb
│   └── 04_environment_inference.ipynb
│
├── results/
│   ├── figures/
│   ├── videos/
│   └── logs/
│
└── tests/
    ├── test_physics.py
    ├── test_model.py
    └── test_control.py
```

---

## 16. Possible Baselines

The proposed method can be compared against:

- LSTM dynamics model,
- Transformer trajectory predictor,
- standard FNO,
- PINN surrogate,
- DeepONet,
- Koopman operator model,
- reduced-order physics model,
- classical MPC with simplified dynamics,
- sampling-based open-loop planner,
- PID or heuristic magnetic control.

---

## 17. Long-Term Vision

The long-term goal of this project is to develop a unified framework in which a magnetically actuated soft robot can:

1. predict its own deformation and trajectory,
2. navigate in a fluidic environment,
3. infer hidden environmental properties,
4. adapt its control policy online,
5. and operate under limited sensing conditions.

This framework connects:

- physics-informed machine learning,
- neural operators,
- soft robotics,
- magnetic actuation,
- fluid-structure interaction,
- model predictive control,
- and active environmental sensing.

---

## 18. One-Sentence Summary

**PINO-MagNav uses a physics-informed neural operator as a differentiable surrogate of magnetic-fluid-soft-body dynamics, enabling predictive control, vision-free navigation, closed-loop adaptation, and environmental inference for magnetically actuated soft robots in fluidic environments.**
