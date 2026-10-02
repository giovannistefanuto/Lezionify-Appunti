# Robot-Environment Interaction Control and Modeling in Operational Space

## Overview

This lesson focuses on managing the mechanical interaction between the robotic manipulator and the surrounding environment. The primary objective is to define a control methodology capable of imposing a desired dynamics at the contact interface, modeling the relationship between the end-effector and the external environment as a second-order differential system equivalent to a **Mass-Spring-Damper System**. This approach is particularly effective in applications where contact forces need to be kept low and impact velocities are not too high.

To understand and implement this type of control, the lesson reviews and formalizes the following fundamental theoretical aspects:

*   **Differential Kinematics and Force/Velocity Duality**: Review of the mapping of external forces from the end-effector space to the joint space via the transpose of the Jacobian matrix.
*   **Geometric Jacobian vs. Analytical Jacobian**: Analysis of the differences between representations of angular velocities ($\omega$, linked to the *Geometric Jacobian*) and the time derivatives of Euler angles ($\dot{\phi}$, linked to the *Analytical Jacobian*), with the corresponding transformation of the aggregated generalized forces.
*   **Operational Space Dynamic Model**: Reformulation of the equations of motion from the classical *Joint Space* to the Cartesian/operational space. This change of perspective allows describing the robot dynamics directly from the viewpoint of the end-effector, simplifying the design of interaction control algorithms.

---

### Interaction with the Environment and Desired Dynamics

When a robotic manipulator comes into contact with the surrounding environment, managing motion in free space alone is no longer sufficient. The primary objective becomes managing the contact force that develops at the end-effector. 

An effective approach to address this issue consists in imposing a **desired interaction dynamics** between the end-effector and the environment.

#### Choice of a Second-Order Model
The fundamental idea is to ensure that the mechanical interaction between the robot and the environment behaves like a second-order differential system, typically represented by the classic **mass-spring-damper** model (*Mass-Spring-Damper*).

The main reasons for this choice are:
1. **Reliable physical approximation:** Second-order systems approximate a wide range of real physical and mechanical problems with excellent precision.
2. **Simplicity of control:** Linear control of a second-order system using traditional control theory techniques is well known.

> **Key Concept**: Approximation using second-order dynamics works ideally when **contact forces are low** and the **end-effector velocity during interaction is not too high**. It is a technique widely used in industrial and service robotics applications with limited contact.

---

### Review of Dynamics in Joint Space and Kinematic-Static Duality

To understand how to model the robot during interaction, let us review the dynamic model in *Joint Space*. Neglecting joint friction for simplicity, the equation of motion is written as:

$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u - \tau_e$$

Where:
* $q \in \mathbb{R}^n$ is the joint coordinate vector.
* $M(q)$ is the inertia matrix in joint space.
* $C(q, \dot{q})\dot{q}$ represents the centrifugal and Coriolis terms.
* $g(q)$ is the gravity vector.
* $u$ is the vector of torques applied to the joints.
* $\tau_e$ is the vector of joint torques produced by external interaction forces.

#### Mapping External Forces to Joints
If an external force and moment vector $F = \begin{bmatrix} f \\ \tau \end{bmatrix} \in \mathbb{R}^6$ acts on the end-effector (where $f$ represents linear forces and $\tau$ moments), by the principle of virtual work we can map this load into the joint space through the transpose of the **Geometric Jacobian** $J(q)$:

$$\tau_e = J^T(q) F$$

---

### Geometric Jacobian vs. Analytical Jacobian

In analyzing end-effector velocities and forces, a fundamental distinction must be made between the geometric and analytical representations.

1. **Geometric Velocity ($v$):**
   $$v = \begin{bmatrix} \dot{p} \\ \omega \end{bmatrix} = J(q)\dot{q}$$
   Where $\dot{p}$ is the linear velocity and $\omega$ is the instantaneous angular velocity. The force $F$ performs work directly on the geometric velocity $v$.

2. **Analytical Velocity ($\dot{x}$):**
   $$x = \begin{bmatrix} p \\ \phi \end{bmatrix} \in \mathbb{R}^6, \quad \dot{x} = \begin{bmatrix} \dot{p} \\ \dot{\phi} \end{bmatrix} = J_A(q)\dot{q}$$
   Where $p \in \mathbb{R}^3$ represents the position of the origin of the end-effector frame with respect to the base, while $\phi \in \mathbb{R}^3$ is a set of **Euler Angles** describing orientation.

> **Key Concept**: The instantaneous angular velocity $\omega$ **is not** the direct time derivative of the Euler angles ($\omega \neq \dot{\phi}$). There exists a transformation matrix $T(\phi)$ such that:
> $$\omega = T(\phi)\dot{\phi}$$

The relationship between the Geometric Jacobian $J(q)$ and the **Analytical Jacobian** $J_A(q)$ is expressed by:

$$J_A(q) = T_A(x) J(q)$$

Consequently, by force-velocity duality, if we wish to express the external forces $F_A$ related to the analytical velocity representation $\dot{x}$, the equivalent transformation of external forces becomes:

$$J^T(q) F = J_A^T(q) F_A$$

Where $F_A$ is the generalized force associated with the operational state $x$.

---

### Operational Space Dynamic Modeling

When the robot interacts with the environment, it is much more convenient to express the equations of motion directly from the viewpoint of the end-effector, i.e., in **Operational Space**.

We express the dynamics with respect to the Cartesian state vector $x = \begin{bmatrix} p^T & \phi^T \end{bmatrix}^T \in \mathbb{R}^6$.

#### Derivation of the Cartesian Dynamic Model

 Starting from the second-order kinematic relationships:
$$\dot{x} = J_A(q)\dot{q} \implies \ddot{x} = J_A(q)\ddot{q} + \dot{J}_A(q, \dot{q})\dot{q}$$

Solving for $\ddot{q}$:
$$\ddot{q} = J_A^{-1}(q)\left( \ddot{x} - \dot{J}_A(q, \dot{q})\dot{q} \right)$$

*(Note: Step integrated for instructional clarity - The Jacobian is assumed to be square and non-singular).*

Substituting $\ddot{q}$ into the joint dynamic equation and pre-multiplying the entire system by $J_A^{-T}(q)$, we obtain the **Operational Space Dynamic Model**:

$$\Lambda(x)\ddot{x} + \mu(x, \dot{x})\dot{x} + p(x) = F_u - F_A$$

Where the dynamic matrices in operational space are defined as:

* **Operational Space Inertia Matrix**:
  $$\Lambda(x) = \left( J_A(q) M^{-1}(q) J_A^T(q) \right)^{-1}$$
* **Centrifugal and Coriolis Terms in Operational Space**:
  $$\mu(x, \dot{x}) = J_A^{-T}(q) C(q, \dot{q}) \dot{q} - \Lambda(x) \dot{J}_A(q, \dot{q}) \dot{q}$$
* **Gravity Vector in Operational Space**:
  $$p(x) = J_A^{-T}(q) g(q)$$
* **Generalized Control Force**:
  $$F_u = J_A^{-T}(q) u$$

This formulation describes the actual dynamic behavior occurring at the contact point of the end-effector, making the design of force or impedance control algorithms straightforward.

---

### Impedance Control Design

The design of an impedance control is mainly divided into **two logical phases**:
1. **Feedback Linearization**: A control action $u$ (expressed in terms of forces/torques) is designed as a function of an auxiliary control $a$, in order to cancel the nonlinearities of the robot dynamics and reduce the relationship between the Cartesian variable $x$ and the auxiliary input $a$ to a double integrator ($\ddot{x} = a$).
2. **Imposing the Desired Impedance Dynamics**: The auxiliary vector $a$ is designed so that the dynamic relationship between the robot and the environment is governed by a second-order differential equation described by the target parameters of mass, damping, and stiffness.

---

#### Step 1: Feedback Linearization

Let us consider the robot dynamics expressed in operational (Cartesian) space:

$$M_x(x)\ddot{x} + C_x(x, \dot{x})\dot{x} + g_x(x) - f_A = u_x$$

Where:
* $M_x(x)$ is the inertia matrix in Cartesian space.
* $C_x(x, \dot{x})\dot{x}$ represents the Coriolis and centrifugal terms.
* $g_x(x)$ is the gravity force vector.
* $f_A$ is the interaction force exerted by the environment on the end-effector.
* $u_x$ is the control force in Cartesian space.

To apply the control force at the joint level $u$ (joint torques $\tau$), the transposed analytical Jacobian matrix $J_A^T(q)$ is used: $u = J_A^T(q) u_x$.

We design the control law $u$ by inserting terms to cancel nonlinear dynamics and external forces, while introducing the auxiliary command $a$:

$$u = J_A^T(q) \left( M_x(x)a + C_x(x, \dot{x})\dot{x} + g_x(x) - f_A \right)$$

> **Note: Step integrated for instructional clarity**  
> Substituting the control law $u$ into the dynamic model of the system, it is immediately clear that the terms $C_x\dot{x}$, $g_x$, and $f_A$ cancel each other out. This leaves the equality $M_x(x)\ddot{x} = M_x(x)a$. Multiplying by $M_x^{-1}(x)$, we obtain the perfect double integrator:
> $$\ddot{x} = a$$

---

#### Step 2: Design of the Auxiliary Input $a$ for Target Dynamics

We want the behavior of the robot in contact with the environment to satisfy the target second-order impedance model:

$$M_d (\ddot{x} - \ddot{x}_d) + D_d (\dot{x} - \dot{x}_d) + K_d (x - x_d) = - f_A$$

Where:
* $M_d$ is the desired inertia matrix (*Target Inertia*).
* $D_d$ is the desired damping matrix (*Target Damping*).
* $K_d$ is the desired stiffness matrix (*Target Stiffness*).
* $x_d, \dot{x}_d, \ddot{x}_d$ represent the desired Cartesian trajectory (position, velocity, and acceleration).

To derive the explicit formulation of the auxiliary input $a$, we isolate the actual acceleration $\ddot{x}$ in the target model equation (recalling that $\ddot{x} = a$):

$$M_d (\ddot{x} - \ddot{x}_d) = - D_d (\dot{x} - \dot{x}_d) - K_d (x - x_d) - f_A$$

Dividing both sides by $M_d$ (i.e., pre-multiplying by the inverse $M_d^{-1}$):

$$\ddot{x} - \ddot{x}_d = M_d^{-1} \left( D_d (\dot{x}_d - \dot{x}) + K_d (x_d - x) - f_A \right)$$

Since we imposed $\ddot{x} = a$, we obtain the law for the auxiliary input $a$:

$$a = \ddot{x}_d + M_d^{-1} \left( D_d (\dot{x}_d - \dot{x}) + K_d (x_d - x) - f_A \right)$$

---

### Parameter Tuning and Physical Intuition

Impedance design requires choosing **four fundamental elements**:
1. Desired trajectory ($x_d, \dot{x}_d, \ddot{x}_d$)
2. Desired inertia ($M_d$)
3. Desired damping ($D_d$)
4. Desired stiffness ($K_d$)

#### Practical Rules for Parameter Selection

Consider the practical application of surface machining (e.g., writing, grinding, or deburring on a rigid metallic surface):

> **Key Concept: Configuration Strategy in Contact Tasks**  
> To ensure continuity of contact without generating destructive forces, a desired trajectory $x_d$ is set that penetrates **slightly inside the material**. The robot will never physically reach $x_d$, but the virtual deformation will act as the generator of the contact force.

Dynamic parameters should be tuned by differentiating behavior along spatial directions:

* **Along constrained contact directions (normal to the surface):**
  * **Large inertia (high $M_d$):** Reduces sudden accelerations upon impact.
  * **Small stiffness (low $K_d$):** Prevents the occurrence of excessive reaction forces due to the penetration of the nominal trajectory $x_d$ into the rigid environment.
* **Along free-motion directions (tangent to the surface):**
  * **Small inertia (low $M_d$):** Makes the robot prompt and reactive to motion commands.
  * **Large stiffness (high $K_d$):** Ensures high trajectory tracking accuracy (*Trajectory Tracking*) where there are no obstacles.

```
                  [ Free Motion Direction ]
                  - Mass Md: Small (Reactivity)
                  - Stiffness Kd: Large (Accurate tracking)
                                │
                                ▼
 ──────────────┐   ┌──────────────────────────┐
  End-         │───│ Nominal trajectory xd    │ (Set inside the material)
  Effector     │   └──────────────────────────┘
 ──────────────┘                │
                                ▼
                  [ Constrained Contact Direction ]
                  - Mass Md: Large (Impact damping)
                  - Stiffness Kd: Small (Low contact forces)
 ──────────────────────────────────────────────────────── Dynamic Surface
```

#### Role of Damping ($D_d$) and Force Sensing
* **Damping ($D_d$):** Serves mainly to shape the transient response of the system. An appropriate value of viscous damping avoids overshoot, reduces settling time, and prevents instability or oscillations upon impact.
* **Force Measurement $f_A$:** Can be performed via a Force/Torque Sensor mounted at the robot wrist or estimated indirectly through model-based techniques (*Soft Sensors*).

---

### Special Cases and Relationship with PD Control

#### Static Regulation and Gravity Compensation

Let us analyze what happens when the desired trajectory is static ($x_d = \text{constant}$, thus $\dot{x}_d = 0$ and $\ddot{x}_d = 0$) and there is no interaction with the environment ($f_A = 0$).

Substituting these conditions into the auxiliary law $a$:

$$a = M_d^{-1} \left( K_d (x_d - x) - D_d \dot{x} \right)$$

Substituting $a$ into the complete control law $u$:

$$u = J_A^T(q) \left( M_x(x) M_d^{-1} \left( K_d (x_d - x) - D_d \dot{x} \right) + C_x(x, \dot{x})\dot{x} + g_x(x) \right)$$

In the absence of significant motion and setting $M_d = M_x$, the expression reduces to:

$$u = J_A^T(q) \left( K_d e - D_d \dot{x} \right) + g(q)$$

Where $e = x_d - x$. It is clearly seen that impedance control in the absence of contact and under static conditions reduces exactly to an **Operational Space PD Control with Gravity Compensation**.

#### Interaction with Elastic Environment

When the robot comes into contact with an environment modeled as a pure spring with stiffness $K_e$ and rest position $x_e$:

$$f_A = K_e (x - x_e)$$

Under static equilibrium conditions ($\ddot{x} = 0, \dot{x} = 0$), the system settles into an equilibrium position $x_\infty$ satisfying the equality between the force exerted by the robot impedance and the environment reaction force:

$$K_d (x_d - x_\infty) = K_e (x_\infty - x_e)$$

From which the final equilibrium position is obtained:

$$x_\infty = (K_d + K_e)^{-1} (K_d x_d + K_e x_e)$$

---

### Introduction to Admittance Control

While **Impedance Control** takes position/velocity variations as input and produces force/torque commands as output (requiring direct access to low-level joint controllers), in many industrial contexts this access is not allowed.

Commercial robots often accept only velocity or position commands at the low level. In these cases, **Admittance Control** is used:

* **Operating Principle:** The force sensor measures the external force $f_A$ applied by the environment.
* **Admittance Integrator:** The measured force is fed into a dynamic model (the inverse of impedance) that computes the modified trajectory (velocity $\dot{x}$ or position $x$) to send to the robot.
* **Low-Level Control:** The proprietary architecture of the robot tracks the computed velocity/position at very high frequency.

```
┌─────────────┐  Force f_A  ┌───────────────────────────┐  Trajectory x_ref  ┌─────────────────────────────┐
│ Environment │────────────>│ Admittance Model          │───────────────────>│ Low-Level Control           │
└─────────────┘             │ (Inverse of Impedance)    │                    │ (Pos./Vel. Tracking)        │
                            └───────────────────────────┘                    └─────────────────────────────┘
```

---

3. Robot-Environment Interaction: Constraint Modeling

In scenarios where the robot comes into contact with the surrounding environment, the presence of obstacles or rigid surfaces limits the freedom of motion of the end-effector. To manage and control force and motion during contact, it is necessary to formalize the geometric and kinematic constraints that arise from this interaction.

#### Working Hypotheses and Role of Feedback

Before defining the constraints, it is appropriate to clarify the initial modeling hypotheses:
* **Perfectly Rigid Environment and Robot:** Structural deformations during contact are assumed to be non-existent.
* **Absence of Friction:** Contact is modeled, as a first approximation, as frictionless (ideal contact).

> **Key Concept**  
> Although friction, surface deformability, and model errors exist in reality, we design the control strategy starting from **ideal conditions**. This approach is justified by the fact that the subsequent implementation of a **feedback control** allows effectively compensating for all non-idealities and unmodeled disturbances present in the real system.

---

#### The Task Frame

To describe the interaction at the contact point, a new local reference frame is defined, called the **Task Frame** and denoted by $RF_T$. 

* **Origin:** Positioned exactly at the contact point between the end-effector and the environment.
* **Dynamic nature:** Since it is a point that can move in space together with the robot, $RF_T$ is a time-varying frame.

Along the three orthogonal axes of this frame $(x, y, z)$, the system can exchange a total of **12 physical quantities** with the environment:

1. **6 velocity components** (Kinematics):
   * 3 linear velocities: $v = [v_x, v_y, v_z]^T$
   * 3 angular velocities: $\omega = [\omega_x, \omega_y, \omega_z]^T$
2. **6 force/torque components** (Dynamics):
   * 3 linear reaction forces: $f = [f_x, f_y, f_z]^T$
   * 3 reaction torques: $m = [m_x, m_y, m_z]^T$

---

#### Natural and Artificial Constraints

The mechanical interaction divides the 12 physical/spatial directions into two fundamental and complementary sets.

##### 1. Natural Constraints
Natural constraints are dictated exclusively by the **geometry of contact** and the physical properties of the environment. They represent what the environment spontaneously "imposes" on the robot:

* **Velocity Subset ($6-k$ directions):** Includes the directions (linear or rotational) along which **motion is prevented** by the physical presence of the environment. In such directions, velocity is constrained to zero by reaction forces/torques.
* **Force/Torque Subset ($k$ directions):** Includes the directions along which **there are no reaction forces or torques** from the environment ("free" motion directions).

##### 2. Artificial Constraints
Artificial constraints represent the specifications of the **desired task** set by the user/designer. They define how the robot *must* behave in the directions left free or constrained by the environment:

* **Velocity Subset ($k$ directions):** Includes directions where **motion is physically possible**. In these directions, a desired velocity profile $v_d$ or $\omega_d$ is imposed.
* **Force/Torque Subset ($6-k$ directions):** Includes directions where the environment opposes resistance. In these directions, a desired force $f_d$ or torque $m_d$ value to be exerted on the environment is imposed.

> **Key Concept**  
> Natural constraints and artificial constraints are **strictly complementary**. What is a natural velocity constraint (motion prevented) becomes the domain of an artificial force constraint (we can decide how much force to exert against the surface). Conversely, where the environment offers no reaction (zero natural force), an artificial velocity constraint is imposed.

---

#### Practical Example: Sliding a Block on a Guide

Let us consider a concrete example to clarify the definitions: a cubic block (end-effector) constrained to slide inside a rigid slotted guide.

```
       z (perpendicular)
       ^
       |   +-----+
       |   | Block  |  ---> x (guide direction)
       +---|-----+----------------->
      /    
     / y (lateral)
```

We fix the frame $RF_T$ at the contact point with axes oriented as follows:
* $x$-axis: parallel to the guide direction (sliding direction).
* $y$-axis: orthogonal to the guide on the horizontal plane.
* $z$-axis: orthogonal to the sliding plane (vertical).

##### Analysis of Natural Constraints

1. **Prevented Velocities (System geometry):**
   * $v_y = 0$: the block cannot translate laterally due to the walls of the guide.
   * $v_z = 0$: the block cannot penetrate the surface downwards.
   * $\omega_x = 0$: the shape of the guide prevents roll around the $x$-axis.
   * $\omega_z = 0$: the shape of the guide prevents yaw around the $z$-axis.
   *(Note: $\omega_y$ is not mechanically prevented, as the block could theoretically roll around $y$).*

2. **Absence of Reactions (Assumption of no friction):**
   * $f_x = 0$: no opposing force along the guide (in the absence of friction).
   * $m_y = 0$: no reaction torque around the $y$-axis.

##### Analysis of Artificial Constraints

Based on the remaining degrees of freedom, we set the control specifications for the robot:

1. **Desired Velocities (Velocity controlled):**
   * $v_x = v_{d}$: we want to slide the block along the $x$-axis with a specific velocity $v_d$.
   * $\omega_y = \omega_{y,d} = 0$: although rotating around $y$ is physically possible, we impose $\omega_y = 0$ because the requested task is *pure sliding* and not rolling.

2. **Desired Forces/Torques (Force controlled):**
   * $f_y = f_{y,d} = 0$: we do not want to exert unnecessary forces against the side walls.
   * $f_z = f_{z,d}$: we can decide to press against the surface with a desired force $f_{z,d}$ (e.g., for a machining or chip removal operation).
   * $m_x = m_{x,d} = 0$ and $m_z = m_{z,d} = 0$: applying torsional torques along these axes is not desired.

---

#### Matrix Parameterization and Orthogonality Principle

In order to use these concepts in designing the dynamic controller, constraints must be expressed in compact matrix form.

We define the generalized velocity vector $V$ and the generalized force/torque vector $F$ in the frame $RF_T$:

$$V = \begin{bmatrix} v \\ \omega \end{bmatrix} \in \mathbb{R}^6, \quad F = \begin{bmatrix} f \\ m \end{bmatrix} \in \mathbb{R}^6$$

We can parameterize the admissible velocities (structured by artificial constraints) and the environment reaction forces using two selection matrices, $D$ and $Y$:

1. **Matrix of admissible motion directions ($D$):**
   Relates a reduced velocity vector $v_a \in \mathbb{R}^k$ (in this case $k=2$, containing independent variables $v_x$ and $\omega_y$) to the total vector $V$:
   $$V = D \cdot v_a$$

2. **Matrix of reaction directions ($Y$):**
   Relates a reduced reaction force/torque vector $f_a \in \mathbb{R}^{6-k}$ (in this case $6-k=4$, related to $f_y, f_z, m_x, m_z$) to the total vector $F$:
   $$F = Y \cdot f_a$$

##### The Principle of Virtual Work and Orthogonality

Under ideal conditions (absence of friction), constraint reaction forces $F$ do no virtual work along the allowable motion directions $V$. 

> **Key Concept**  
> The matrices $D$ and $Y$ describe **mutually orthogonal** vector spaces. Mathematically, this translates to the fundamental relation:
> $$Y^T D = 0 \quad \text{or} \quad D^T Y = 0$$

This orthogonality property is the cornerstone of **Hybrid Force/Position Control** theory: it guarantees that the position control subspace and the force control subspace are completely decoupled, allowing their respective control loops to be designed independently.

---

> [!NOTE]
> ### Exam Notes and Instructor Announcements
> - The topic regarding robot-environment interaction and natural and artificial constraints is expressly confirmed as part of the required syllabus for the final exam ("This is due for the final exam").