# Operational Space Control: PD Algorithm with Gravity Compensation

## Educational Overview

In this lecture, the fundamental transition from control in the **Joint Space** to control in the **Operational Space** is introduced. This shift in perspective is essential when analyzing the robot's interaction with the external environment, which occurs directly at the level of the **End-Effector**.

The key concepts covered include:
* **Error Formulation in the Operational Space**: Definition of the end-effector pose error $\tilde{x} = x_d - x$, moving away from direct dependence on the joint error $\tilde{q}$.
* **PD Control Design with Gravity Compensation**: Extension of the Proportional-Derivative (*PD*) control algorithm based on a suitable **Lyapunov Candidate Function**.
* **Differential Kinematics and Analytical Jacobian**: Use of the differential kinematics relationship via the **Analytical Jacobian** $J_A(q)$ to link operational space velocities ($\dot{x}$) with joint space velocities ($\dot{q}$).
* **Stability Analysis**: Derivation of the joint torque control law $u$ such as to guarantee that the time derivative of the Lyapunov function $\dot{V}$ is negative semi-definite, ensuring that the desired pose is reached at zero velocity ($\dot{q} = 0$).

---

### Introduction to Operational Space Control

Up to this point, most control algorithms have been developed in the **joint space**, a natural choice given that the actuators act directly on the individual joints of the manipulator. 

However, when the robot must perform **interaction with the environment** tasks, the main variable of interest becomes the position and orientation (pose) of the **end-effector**. In these contexts, it is much more effective and intuitive to design the control algorithm directly based on the error calculated in the **operational space**.

---

### Error Formulation and Lyapunov Candidate Function

We assume that the end-effector must reach a constant desired pose $x_d$ in the operational space. We define the pose error in the operational space $\tilde{x}$ as:

$$\tilde{x} = x_d - x$$

where $x$ represents the current pose of the end-effector (expressed, for example, by position and Euler angles).

To design a regulation proportional-derivative (PD) control law with gravity compensation, we propose a **Lyapunov function candidate** $V(q, \dot{q})$ expressed as the sum of the system's kinetic energy and the potential energy associated with the error in the operational space:

$$V(q, \dot{q}) = \frac{1}{2} \dot{q}^T B(q) \dot{q} + \frac{1}{2} \tilde{x}^T K_p \tilde{x}$$

where:
* $B(q)$ is the manipulator's inertia matrix, positive definite ($B(q) > 0$).
* $K_p$ is the proportional gain matrix, chosen as a symmetric and positive definite matrix ($K_p = K_p^T > 0$).

#### Properties of the Lyapunov Function
The function $V(q, \dot{q})$ satisfies the following fundamental properties:
1. $V(q, \dot{q}) \ge 0$ for any state.
2. $V(q, \dot{q}) = 0$ if and only if the error and velocity are simultaneously zero, i.e., $\tilde{x} = 0$ and $\dot{q} = 0$.

---

### Mathematical Derivation of the Control Law

To guarantee closed-loop stability of the system, it is necessary to compute the time derivative of $V(q, \dot{q})$ along the system trajectories and design the control law $u$ such that it is negative semi-definite ($\dot{V} \le 0$).

#### Differential Kinematics Relationship
Differentiating the pose error $\tilde{x}$ with respect to time, recalling that the desired pose $x_d$ is constant ($\dot{x}_d = 0$), we obtain:

$$\dot{\tilde{x}} = -\dot{x}$$

Using **analytical differential kinematics**, the end-effector velocity $\dot{x}$ is related to the joint velocities $\dot{q}$ via the **analytical Jacobian** $J_A(q)$:

$$\dot{x} = J_A(q) \dot{q} \implies \dot{\tilde{x}} = -J_A(q) \dot{q}$$

#### Computation of the Time Derivative $\dot{V}$
Differentiating $V(q, \dot{q})$ with respect to time yields:

$$\dot{V} = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} + \tilde{x}^T K_p \dot{\tilde{x}}$$

Substituting the kinematic relationship $\dot{\tilde{x}} = -J_A(q)\dot{q}$:

$$\dot{V} = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} - \tilde{x}^T K_p J_A(q) \dot{q}$$

Exploiting the dynamic model of the robot:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u \implies B(q)\ddot{q} = u - C(q, \dot{q})\dot{q} - g(q)$$

Substituting the expression for $B(q)\ddot{q}$ into $\dot{V}$:

$$\dot{V} = \dot{q}^T \left( u - C(q, \dot{q})\dot{q} - g(q) \right) + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} - \dot{q}^T J_A^T(q) K_p \tilde{x}$$

Grouping terms and exploiting the **skew-symmetry property** whereby the matrix $\dot{B}(q) - 2C(q, \dot{q})$ is skew-symmetric (i.e., $\dot{q}^T (\dot{B}(q) - 2C(q, \dot{q})) \dot{q} = 0$), the derivative simplifies to:

$$\dot{V} = \dot{q}^T \left( u - g(q) - J_A^T(q) K_p \tilde{x} \right)$$

#### Synthesis of the PD Control Law
To make $\dot{V}$ negative semi-definite or negative definite, we select the control input $u$ by introducing gravity compensation, the proportional action in the operational space, and a derivative damping term:

$$u = g(q) + J_A^T(q) K_p \tilde{x} - J_A^T(q) K_d J_A(q) \dot{q}$$

where $K_d > 0$ is a positive definite derivative gain matrix. 

*Note*: The derivative term can also be expressed directly as a function of the end-effector velocity: since $\dot{x} = J_A(q)\dot{q}$, the term $-J_A^T(q) K_d J_A(q) \dot{q}$ corresponds to $-J_A^T(q) K_d \dot{x}$.

Substituting $u$ into $\dot{V}$, we obtain:

$$\dot{V} = -\dot{q}^T J_A^T(q) K_d J_A(q) \dot{q} \le 0$$

Since $K_d > 0$, the quadratic form is always less than or equal to zero for any value of $\dot{q}$.

---

### Stability Analysis and Equilibrium Points

> **KEY CONCEPT: Convergence Analysis and Kinematic Singularity**
> 
> To verify whether the system converges exactly to the desired pose ($\tilde{x} = 0$), **LaSalle's Invariance Principle** is applied.
> 
> When $\dot{V} = 0$, we have $J_A(q)\dot{q} = 0$, which implies $\dot{q} = 0$ (except for velocities belonging to the null space). With $\dot{q} = 0$ and $\ddot{q} = 0$, substituting the control law into the manipulator dynamic equation yields the steady-state equilibrium equation:
> 
> $$J_A^T(q) K_p \tilde{x} = 0$$
> 
> From this relationship, two fundamental conclusions can be drawn:
> 
> 1. **Non-Singular Configuration (Full Rank)**: If the analytical Jacobian $J_A(q)$ has **full rank**, then $J_A^T(q)$ defines an injective transformation. Consequently, the only possible solution to the equilibrium equation is:
>    $$K_p \tilde{x} = 0 \implies \tilde{x} = 0$$
>    In this case, asymptotic convergence to zero error in the operational space is guaranteed.
> 
> 2. **Singular Configuration**: If the robot passes through or is in a **kinematic singularity**, the matrix $J_A^T(q)$ loses rank. In such a situation, the error $K_p \tilde{x}$ could belong to the kernel (null space) of $J_A^T(q)$ ($\tilde{x} \in \ker(J_A^T)$). This implies that the robot could get stuck in an undesirable equilibrium configuration with a non-zero pose error ($\tilde{x} \neq 0$).

---

### Feedback Linearization in Joint Space (Recall)

Before moving to operational space, let us briefly recall how **Feedback Linearization** works in **Joint Space**, used for trajectory tracking.

Consider the dynamic model of the manipulator expressed in compact form:

$$B(q)\ddot{q} + n(q, \dot{q}) = u$$

where $B(q)$ is the inertia matrix and $n(q, \dot{q})$ collects all non-linear terms (centrifugal, Coriolis, friction, and gravitational forces).

The feedback linearization technique consists of two main steps:

1. **Exact linearization of dynamics:** The control law $u$ is chosen as a function of an auxiliary input $y$:

   $$u = B(q)y + n(q, \dot{q})$$

   Substituting $u$ into the dynamic model, the non-linear terms cancel out, obtaining a decoupled and linearized dynamic system described by a double integrator:

   $$\ddot{q} = y$$

2. **Design of auxiliary input $y$:** In joint space, the auxiliary input is designed using a **Feedforward + Proportional-Derivative (PD)** scheme:

   $$y = \ddot{q}_d + K_d(\dot{q}_d - \dot{q}) + K_p(q_d - q)$$

   Defining the joint error as $\tilde{q} = q_d - q$, the error dynamics becomes a second-order differential equation with constant coefficients:

   $$\ddot{\tilde{q}} + K_d \dot{\tilde{q}} + K_p \tilde{q} = 0$$

   If the gain matrices $K_p$ and $K_d$ are positive definite, the error $\tilde{q}(t)$ converges to zero exponentially fast.

---

### Extension of Feedback Linearization to Operational Space

In most real applications, the desired trajectory $x_d(t)$ is defined in **Operational Space**, not in joint space. We therefore do not want to compute joint errors, but rather directly control the error in operational space:

$$\tilde{x} = x_d - x_e$$

To do this, we must link the auxiliary dynamics $\ddot{q} = y$ to operational space quantities (position, velocity, and acceleration).

#### Second-Order Differential Kinematics Relationship
From differential kinematics, knowing that $\dot{x}_e = J_A(q)\dot{q}$ (where $J_A$ is the **Analytical Jacobian**), we differentiate with respect to time to obtain the acceleration relationship:

$$\ddot{x}_e = J_A(q)\ddot{q} + \dot{J}_A(q, \dot{q})\dot{q}$$

#### Design of Auxiliary Input $y$
We retain the first step of dynamic cancellation $u = B(q)y + n(q, \dot{q})$, so as to maintain the simplified dynamics $\ddot{q} = y$. 

We want the error dynamics in operational space to be governed by a second-order equation analogous to the one seen in joint space:

$$\ddot{\tilde{x}} + K_d \dot{\tilde{x}} + K_p \tilde{x} = 0 \implies \ddot{x}_e = \ddot{x}_d + K_d (\dot{x}_d - \dot{x}_e) + K_p (x_d - x_e)$$

To achieve this result, the auxiliary input $y$ must "translate" quantities from operational space to joint space. We thus define $y$ as:

$$y = J_A^{-1}(q) \left( \ddot{x}_d + K_d(\dot{x}_d - \dot{x}_e) + K_p(x_d - x_e) - \dot{J}_A(q, \dot{q})\dot{q} \right)$$

#### Proof and Verification of Error Dynamics
*Note: Step integrated with educational clarity.*

To verify the effectiveness of this control law, we substitute the definition of $y$ into the operational space acceleration relationship $\ddot{x}_e = J_A(q)y + \dot{J}_A(q, \dot{q})\dot{q}$ (recalling that $\ddot{q} = y$):

$$\ddot{x}_e = J_A(q) \left[ J_A^{-1}(q) \left( \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x} - \dot{J}_A(q, \dot{q})\dot{q} \right) \right] + \dot{J}_A(q, \dot{q})\dot{q}$$

Since $J_A(q) J_A^{-1}(q) = I$ (identity matrix), the terms simplify:

$$\ddot{x}_e = \left( \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x} - \dot{J}_A(q, \dot{q})\dot{q} \right) + \dot{J}_A(q, \dot{q})\dot{q}$$

The terms related to the derivative of the Jacobian $\dot{J}_A(q, \dot{q})\dot{q}$ cancel each other out:

$$\ddot{x}_e = \ddot{x}_d + K_d\dot{\tilde{x}} + K_p\tilde{x}$$

Rearranging terms and recalling that $\ddot{\tilde{x}} = \ddot{x}_d - \ddot{x}_e$, we obtain the desired error dynamics:

$$\ddot{\tilde{x}} + K_d\dot{\tilde{x}} + K_p\tilde{x} = 0$$

If $K_p$ and $K_d$ are positive definite matrices, the error $\tilde{x}(t)$ in operational space converges to zero exponentially fast.

---

### Critical Analysis and Limitations of the Operational Space Scheme

> **KEY CONCEPT: Computational Complexity and Singularities**
>
> Control via feedback linearization in operational space presents a series of severe structural disadvantages compared to its counterpart in joint space:
>
> 1. **Continuous Inverse Computation:** Requires analytical inversion of the Jacobian $J_A^{-1}(q)$ at every sampling instant. This renders control much more computationally complex.
> 2. **Square Matrix Assumption:** The existence of $J_A^{-1}(q)$ assumes that the Jacobian is a square matrix (number of joints equal to the degrees of freedom of the operational space). It is not directly applicable to redundant manipulators without appropriate modifications.
> 3. **Presence of Kinematic Singularities:** If the manipulator passes through or approaches a singular configuration, $\det(J_A) \to 0$ and the matrix $J_A^{-1}(q)$ is not well defined (diverges).
> 4. **Null Space Problem:** If the error enters the null space of the Jacobian (for example in PD controllers with gravity compensation), the manipulator risks getting stuck in a configuration different from the desired one (*stuck configuration*).

---

3. Operational Space Dynamic Model Formulation and Inertia Control

Maintaining the model expressed in joint variables $\tau$ and defining the control requirements in the *Operational Space* makes the design of control laws complex. To overcome this problem, the point of view changes: the entire dynamic model is reformulated directly as a function of the operational space coordinates.

### Shift in Perspective: The Fictitious Dynamic Model

The goal is to derive a dynamic model describing the direct relationship between generalized forces acting on the end-effector and a minimal set of coordinates to describe its position and orientation in operational space.

#### Introduction of Equivalent Forces
Joint torques $\tau$ are applied to move the structure. If a vector of forces and torques $h_e$ acts on the end-effector, their equivalent effect at the joints is given by $J^T(q) h_e$. 

Similarly, a vector of **fictitious equivalent forces/torques in operational space** $\gamma_e$ applied to the end-effector is defined, such as to produce exactly the same torques $\tau$ at the joints:
$$\tau = J^T(q) \gamma_e$$

> **Key Concept**: The forces $\gamma_e$ are not forces physically applied from the outside to the end-effector, but represent a *fictitious* quantity allowing joint motor torques to be interpreted as if they were generated directly in operational space.

#### Transition to Analytical Jacobian
To guarantee consistency of description in operational space, it is preferred to use the *Analytical Jacobian* $J_A(q)$ rather than the *Geometric Jacobian* $J(q)$. 

Recalling the transformation relationship $J(q) = T_A(x_e) J_A(q)$ (where $T_A$ is the kinematic transformation matrix), it is possible to define the equivalent forces associated with the analytical Jacobian as $\gamma_a$:
$$\tau = J_A^T(q) \gamma_a$$
where $\gamma_a = T_A^T(x_e) \gamma_e$. Similarly, the rescaled external forces become $h_a = T_A^T(x_e) h_e$.

---

### Mathematical Derivation of the Model in Operational Space

We start from the standard dynamic model in joint space (omitting friction for simplicity):
$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau - J_A^T(q)h_a$$

Substituting $\tau = J_A^T(q)\gamma_a$, we solve for the joint acceleration $\ddot{q}$:
$$\ddot{q} = B^{-1}(q) \left[ J_A^T(q)(\gamma_a - h_a) - C(q,\dot{q})\dot{q} - g(q) \right]$$

We now consider the acceleration kinematic relationship in operational space:
$$\ddot{x}_e = J_A(q)\ddot{q} + \dot{J}_A(q)\dot{q}$$

Substituting the expression for $\ddot{q}$ into the kinematic relationship:
$$\ddot{x}_e = J_A(q) B^{-1}(q) J_A^T(q) (\gamma_a - h_a) - J_A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) + \dot{J}_A(q)\dot{q}$$

#### Definition of Fictitious Inertia, Coriolis, and Gravity Matrices
To isolate the force term $(\gamma_a - h_a)$, we define the **Operational Space Inertia Matrix** $B_A(q)$:
$$B_A(q) = \left( J_A(q) B^{-1}(q) J_A^T(q) \right)^{-1}$$

Multiplying both sides of the relationship by $B_A(q)$, we obtain:
$$B_A(q)\ddot{x}_e = \gamma_a - h_a - B_A(q) J_A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) + B_A(q)\dot{J}_A(q)\dot{q}$$

Rearranging terms, we define the fictitious components viewed from operational space:
1. **Operational Space Gravity Vector** $g_A(q)$:
   $$g_A(q) = B_A(q) J_A(q) B^{-1}(q) g(q)$$
2. **Operational Space Coriolis and Centrifugal Matrix** $C_A(q, \dot{q})$:
   *Note: Step integrated with educational clarity.* Grouping terms depending on velocity $\dot{q}$ and $\dot{x}_e$:
   $$C_A(q, \dot{q})\dot{x}_e = B_A(q) J_A(q) B^{-1}(q) C(q, \dot{q})\dot{q} - B_A(q)\dot{J}_A(q)\dot{q}$$

The final structure of the dynamic model in operational space is therefore:
$$B_A(q)\ddot{x}_e + C_A(q, \dot{q})\dot{x}_e + g_A(q) = \gamma_a - h_a$$

> **Key Concept**: The mathematical structure of this model reflects exactly that of joint space, but the matrices $B_A, C_A, g_A$ **are not the real physical masses or gravities**, but rather the equivalent quantities seen "through the lens" of operational space.

#### Special Case: Non-Redundant Manipulator
If the robot is non-redundant and is away from singularities, the Jacobian matrix $J_A(q)$ is square and invertible. In this case, the expressions simplify by applying the inverse product property $(ABC)^{-1} = C^{-1}B^{-1}A^{-1}$:
$$B_A(q) = J_A^{-T}(q) B(q) J_A^{-1}(q)$$

---

### Feedback Linearization Control in Operational Space

Assuming that the robot does not interact with the environment ($h_a = 0$), the goal is to make the end-effector track a desired trajectory defined in position, velocity, and acceleration: $x_d(t), \dot{x}_d(t), \ddot{x}_d(t)$.

The control strategy is applied in two stages (*Feedback Linearization* + *Auxiliary Control*):

```
   Desired     +  _   LBL         Control         Linearization         Real Model 
 Trajectory    --->(X)--->[  A  ]------------>[ γ_a ]------------->[  τ = J_A^T γ_a  ]---> Robot
  (x_d, ... )       ^   
                    | Errors
                    +--- Measured Operational State (x_e, x_e_dot)
```

#### Stage 1: Feedback Linearization
The fictitious force $\gamma_a$ is designed to cancel the non-linear operational space dynamics:
$$\gamma_a = B_A(q) a + C_A(q, \dot{q})\dot{x}_e + g_A(q)$$

Substituting $\gamma_a$ into the operational space model, the system reduces to a decoupled double integrator:
$$\ddot{x}_e = a$$

#### Stage 2: Design of Auxiliary Control $a$
The auxiliary control input $a$ is designed as a PD (Proportional-Derivative) control with acceleration feedforward:
$$a = \ddot{x}_d + K_d (\dot{x}_d - \dot{x}_e) + K_p (x_d - x_e)$$

Defining the operational space error as $\tilde{x} = x_d - x_e$, the error dynamics becomes:
$$\ddot{\tilde{x}} + K_d \dot{\tilde{x}} + K_p \tilde{x} = 0$$

By choosing the gain matrices $K_p$ and $K_d$ as positive definite, the error $\tilde{x}(t)$ converges to zero exponentially fast.

#### Calculation of Real Joint Torques
Once the fictitious force vector $\gamma_a$ is computed, the actual torques $\tau$ to be sent to the joint motors are simply calculated via the transpose of the Analytical Jacobian:
$$\tau = J_A^T(q) \left[ B_A(q) \left( \ddot{x}_d + K_d \dot{\tilde{x}} + K_p \tilde{x} \right) + C_A(q, \dot{q})\dot{x}_e + g_A(q) \right]$$

---

3. Extensions of Modeling and Introduction to Interaction with the Environment

#### Summary on Operational Space Model and PD Control with Gravity Compensation

Before moving to interaction with the environment, let us make a concluding remark regarding the dynamic model in *Operational Space*. 

We have seen that by remodeling the equations of motion directly in operational space, we can describe the robot's dynamics through equivalent matrices: operational space inertia matrix, matrix of Coriolis and centrifugal terms, and vector of gravitational forces.

Starting from this description, it is possible to design a **Proportional-Derivative (PD) Control with gravity compensation** directly in task space. 

> **Key Concept**: To apply Lyapunov's direct method and formally derive the PD control with gravity compensation in operational space, we cannot use classical kinetic energy expressed in joint coordinates. It is necessary to define a **fictitious kinetic energy** expressed via operational space variables:
>
> $$T = \frac{1}{2} \dot{x}_c^T B_a(x) \dot{x}_c$$
>
> where $B_a(x)$ represents the analytical inertia matrix in operational space and $\dot{x}_c$ is the operational velocity. Associating a quadratic function defined on the position error $\tilde{x}$ with this, the appropriate Lyapunov function is constructed to guarantee the stability of the target equilibrium.

---

#### Robot-Environment Interaction: Passive vs. Active Strategies

When the robot's **end-effector** comes into contact with the surrounding environment, interaction forces arise that alter the dynamic behavior of the system. To manage and potentially control these forces, there are two families of approaches:

1. **Passive Control Strategies**: Based on compliant mechanical elements integrated directly into the physical structure of the kinematic chain, without the need to measure forces or modify the control algorithm in real time.
2. **Active Control Strategies**: Utilize force sensors to measure interactions and actively modify trajectories or joint torques via dedicated algorithms.

---

#### Passive Control and the Remote Center Compliance (RCC) Device

Passive control is widely used in industry due to its simplicity, immediacy, and robustness. The most famous passive device is the **Remote Center Compliance (RCC) Device**.

##### Mechanical Structure of the RCC
The RCC device is mounted at the end of the arm, between the robot flange and the final tool. 
It is composed of:
* Two parallel metal plates.
* Rods or flexible elastic elements connecting the two plates.

This specific elastic geometry allows the outer plate to translate laterally and rotate with respect to the fixed one in response to received external forces.

```
       [ Robot Flange ]
             |||
       +---------------+  <-- Fixed Plate
        \   \   \   \   \ <-- Elastic / Flexible Elements
         +-------------+  <-- Movable Plate
                |
          [ Tool/Peg ]
```

##### Application Example: The Peg-in-Hole Insertion Task

Let us consider a classic industrial task of inserting a peg into a cylindrical hole (*Peg-in-Hole task*):

* **Ideal Case**: If we had perfect knowledge of the hole geometry and the robot's position, we could plan a purely kinematic trajectory. The peg would enter perfectly without touching the edges. In reality, this is impossible due to positioning uncertainties, machining tolerances, and deformations.
* **Behavior Without RCC (Rigid Robot)**: If the robot approaches the hole with a small misalignment and attempts to follow the set rigid trajectory, the peg touches the edge. Generating rigid contact, the robot continues to push to track the desired trajectory: this causes mechanical jamming (*jamming*), high contact forces, and potential component damage.
* **Behavior With RCC**: When the peg impacts the edge of the hole, a reaction force $F$ is generated. The structural compliance of the RCC causes this force to deform the elastic elements. The device converts the contact force into passive sliding or rotation of the tool, spontaneously "guiding" the peg toward the center of the hole. 

> **Key Concept**: The RCC device makes the manipulation system **robust against small geometric misalignments**, adapting the position of the end-effector in a purely mechanical and instantaneous manner, without requiring any software feedback loop or computational calculation.

##### Limitations of Passive Control
Although the RCC works perfectly for repetitive and well-defined operations (such as component insertion on assembly lines), it shows obvious limitations for complex or variable tasks. If the application requires:
* Performing different tasks with the same tool,
* Following surfaces with unknown curvatures (*contour following*),
* Interacting with highly dynamic and unstructured environments (e.g., opening a door, turning a handle),

passive control is no longer sufficient and it is necessary to move to active control.

---

#### Introduction to Active Control and Force Sensors

To implement active control strategies, the robot structure must be capable of perceiving the dynamic effect of contact.

For this purpose, **Force/Torque Sensors** are used, typically mounted at the robot wrist (*wrist force sensors*). These are multi-axis sensors capable of simultaneously measuring:
* The linear force vector $\mathbf{f} = [F_x, F_y, F_z]^T$
* The moment/torque vector $\boldsymbol{\tau} = [\tau_x, \tau_y, \tau_z]^T$

In future lectures, we will develop the theory of active interaction control (such as *Impedance Control* and *Hybrid Force/Position Control*), assuming the presence of a wrist force sensor capable of closing the feedback loop on dynamic interaction with the environment.