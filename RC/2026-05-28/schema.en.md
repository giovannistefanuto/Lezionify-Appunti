# Robot-Environment Interaction Control: Natural Constraints, Artificial Constraints, and Formalization of Specifications

## Educational Overview

In this concluding lecture of the course, the analysis of control strategies for robots interacting with the surrounding environment is completed. The main goal is the formalization of control specifications through the rigorous definition of two complementary sets of geometric and kinematic constraints:

- **Natural Constraints**: Represent the physical limitations imposed by the environment and the kinematics of the system. They define the directions along which motion (linear or angular velocities) is prevented and the directions along which no constraint reaction forces (forces or torques) are generated.
- **Artificial Constraints**: Represent the design specifications determined by the controller. They define the desired velocity trajectories along the directions of free motion and the desired force or torque values along the constrained directions.

The lecture illustrates the practical application of this formalism through the detailed analysis of the rotation operation of a crank. Furthermore, it shows the mathematical parameterization required to transform kinematic quantities from the local reference frame (*Compliance Frame*) to the base frame (*Base Frame*) using selection matrices (*Selection Matrices*) and nodal rotation matrices ($R_z(\alpha)$).

---

### Natural and Artificial Constraints: Practical Example of the Crank

In modeling the interaction between a robot and the surrounding environment, the choice of variables to control (forces or velocities) is dictated by the geometry of the contact. Constraints describe how the 6-degrees-of-freedom (DOF) operational space is divided into two complementary subspaces:
* **Natural Constraints**: Constraints imposed by the kinematics and geometry of the environment.
* **Artificial Constraints**: Constraints imposed by the control system to achieve the task objective.

If the directions along which motion is blocked are $k$, there will be $6 - k$ directions along which motion is free. Consequently, reaction forces appear along the $k$ constrained directions, while they are zero along the $6 - k$ free directions.

---

#### Geometric Modeling of the "Crank" System

Consider the problem of rotating a crank using the robot's end-effector.

1. **Definition of Reference Frames**:
   * **Fixed/Base Frame $\Sigma_0$**: The frame solidary with the chassis on which the crank is mounted, not in motion.
   * **Reference/Compliant Frame $\Sigma_c$**: A frame chosen intuitively, positioned on the crank handle:
     * The $y_c$ axis is chosen **tangent** to the circular trajectory (direction of allowed linear motion).
     * The $x_c$ axis is oriented along the crank arm (radial direction).
     * The $z_c$ axis is parallel to the rotation axis of the crank.

2. **Rotation between Reference Systems**:
   The angular position of the crank is defined by the angle $\alpha$. The rotation to transform from the base frame $\Sigma_0$ to the mobile frame $\Sigma_c$ is represented by the elementary rotation matrix about the $z$-axis:
   $$R_z(\alpha) = \begin{bmatrix} \cos\alpha & -\sin\alpha & 0 \\ \sin\alpha & \cos\alpha & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

---

#### Analysis of Natural Constraints

Natural constraints define what the environment **prevents** or **allows** to do in terms of velocities and what reaction forces arise as a result.

##### Velocities (Kinematics)
* **Linear Velocities**: The handle is constrained to move only along the tangent to the circle ($y_c$). Translating along $x_c$ or $z_c$ is not possible.
  $$v_x = 0, \quad v_z = 0$$
* **Angular Velocities**: The crank rotates freely about the $z_c$ axis, but the mechanical structure prevents rotations about $x_c$ and $y_c$.
  $$\omega_x = 0, \quad \omega_y = 0$$

##### Reaction Forces and Torques
Since there are no rigid constraints along the free directions (assuming ideal absence of friction):
* The constraint reaction along $y_c$ is zero: $f_y = 0$.
* The reaction torque about the $z_c$ axis is zero: $\tau_z = 0$.

In summary, the vector of natural constraints consists of:
$$\text{Natural Constraints:} \quad \{ v_x = 0, \; v_z = 0, \; \omega_x = 0, \; \omega_y = 0, \; f_y = 0, \; \tau_z = 0 \}$$

---

#### Analysis of Artificial Constraints

Artificial constraints represent the specifications assigned to the controller for the free variables.

##### Desired Velocities
Along the directions where motion is natural, a velocity trajectory is imposed:
* **Desired Linear Velocity**: $v_y = v_{y,d}$ (tangential velocity set to turn the crank).
* **Desired Angular Velocity**: $\omega_z = \omega_{z,d}$ (often set to $0$ if rotation of the handle joint around itself is not desired).

##### Desired Forces and Torques
Along the directions constrained by the environment, the robot applies or controls reaction forces/torques. To avoid unnecessary mechanical stress on the structure, desired values are set to zero:
* **Desired Forces**: $f_x = f_{x,d} = 0$, $f_z = f_{z,d} = 0$.
* **Desired Torques**: $\tau_x = \tau_{x,d} = 0$, $\tau_y = \tau_{y,d} = 0$.

In summary, the artificial constraints imposed on the controller are:
$$\text{Artificial Constraints:} \quad \{ v_y = v_{y,d}, \; \omega_z = \omega_{z,d}, \; f_x = f_{x,d}, \; f_z = f_{z,d}, \; \tau_x = \tau_{x,d}, \; \tau_y = \tau_{y,d} \}$$

> **Key Concept**
> Natural and artificial constraints are **perfectly complementary**. The directions in which an artificial velocity constraint is imposed correspond to directions with zero natural force, and vice versa. The principle of Virtual Work ensures that reaction forces perform no work along the directions of allowed motion:
> $$v^T h = 0$$

---

#### Parameterization and Frame Transformation

To use this formalism within a controller expressed relative to the fixed base frame $\Sigma_0$, selection and transformation matrices are introduced.

*(Note: Step integrated with educational clarity to make the matrix structure explicit)*

##### 1. Velocity Parameterization
Isolate the two free velocities in the vector $\mathbf{\nu} = \begin{bmatrix} v_y \\ \omega_z \end{bmatrix} \in \mathbb{R}^2$. The 6-DOF velocity vector in the local frame $\Sigma_c$, denoted by $v_c$, is obtained via a selection matrix $S_v \in \mathbb{R}^{6 \times 2}$:

$$v_c = \begin{bmatrix} v_x \\ v_y \\ v_z \\ \omega_x \\ \omega_y \\ \omega_z \end{bmatrix} = S_v \mathbf{\nu} = \begin{bmatrix} 0 & 0 \\ 1 & 0 \\ 0 & 0 \\ 0 & 0 \\ 0 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} v_y \\ \omega_z \end{bmatrix}$$

To express the velocity $v_0$ relative to the base frame $\Sigma_0$, apply the block rotation matrix $T(R_z(\alpha))$:

$$v_0 = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} v_c = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} S_v \mathbf{\nu}$$

##### 2. Force Parameterization
Isolate the four reactive force/torque components in the vector $\mathbf{\lambda} = \begin{bmatrix} f_x \\ f_z \\ \tau_x \\ \tau_y \end{bmatrix} \in \mathbb{R}^4$. The generalized force vector $h_c \in \mathbb{R}^6$ is expressed using the selection matrix $S_f \in \mathbb{R}^{6 \times 4}$:

$$h_c = \begin{bmatrix} f_x \\ f_y \\ f_z \\ \tau_x \\ \tau_y \\ \tau_z \end{bmatrix} = S_f \mathbf{\lambda} = \begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & 0 & 0 & 0 \end{bmatrix} \begin{bmatrix} f_x \\ f_z \\ \tau_x \\ \tau_y \end{bmatrix}$$

Similarly, to express it relative to the base frame $\Sigma_0$:

$$h_0 = \begin{bmatrix} R_z(\alpha) & \mathbf{0}_{3\times3} \\ \mathbf{0}_{3\times3} & R_z(\alpha) \end{bmatrix} S_f \mathbf{\lambda}$$

##### 3. Orthogonality Checks
By performing the transposed product between the local selection matrices, the orthogonal complementarity of the system is confirmed:

$$S_v^T S_f = \mathbf{0}_{2 \times 4}$$

This guarantees that the work produced by constraint forces on allowable velocities is always strictly zero:

$$v_c^T h_c = (S_v \mathbf{\nu})^T (S_f \mathbf{\lambda}) = \mathbf{\nu}^T S_v^T S_f \mathbf{\lambda} = 0$$

---

### Main Reference Frames

In order to design the controller in the presence of environmental constraints, it is necessary to clearly define the different reference frames involved in the analysis:

*   **Base Frame:** The fixed inertial reference frame relative to which the general kinematic quantities of the robot are described.
*   **End-Effector Frame:** Solidary with the manipulator's end-effector.
*   **Sensor Frame:** Solidary with the Force/Torque sensor, if present. It typically coincides with or is placed up to a known rigid transformation relative to the end-effector frame. It is used both for direct measurements and for estimating contact forces via soft sensors.
*   **Task Frame:** A frame introduced ad hoc to describe the geometry of the contact with the environment. For example, in the case of a surface, it is natural to define an axis orthogonal to it (force constraint direction) and the remaining axes tangent (directions allowed for motion). The complete determination of the frame follows the right-hand rule.

All physical quantities and matrices used for the control algorithm must be appropriately expressed relative to the common reference frame in order to be processed correctly.

---

### Dynamic and Kinematic Model in Joint Space

The formulation of the problem starts from the expression of the robot's dynamic model in **Joint Space**:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau - J^T(q) h_e$$

Where:
*   $B(q)$ is the robot's inertia matrix.
*   $C(q,\dot{q})$ represents the Coriolis and centrifugal terms.
*   $g(q)$ is the vector of gravitational terms.
*   $\tau$ is the vector of joint torques, i.e., the control input to be designed.
*   $h_e = \begin{bmatrix} f_e \\ \mu_e \end{bmatrix}$ is the end-effector force/torque vector exerted by the end-effector on the environment.
*   $J^T(q)$ is the transpose of the Jacobian matrix, required to project operational space forces onto equivalent joint torques.

> **Key Concept: Geometric vs. Analytical Jacobian**  
> The forward kinematics of the system relates joint velocities $\dot{q}$ to the end-effector's linear velocities $\dot{p}$ and angular velocities $\omega$ through the **Geometric Jacobian** $J(q)$:
> $$v = \begin{bmatrix} \dot{p} \\ \omega \end{bmatrix} = J(q)\dot{q}$$
> It is essential to emphasize that by directly dealing with physical angular velocities $\omega$ rather than the time derivatives of an attitude parameterization (such as Euler angles), explicit use is being made of the *Geometric Jacobian*.

---

### Constraint Formalism and Motion/Force Parameterization

In the case of rigid contact between robot and environment (assuming no deformation or compliance), the motion of the end-effector is not completely free, but is constrained to specific subspaces defined by the contact surfaces.

#### Motion Parameterization (Motion Degrees of Freedom)
We define a vector of independent coordinates $s$ (of dimension $k$, equal to the motion degrees of freedom allowed by the constraint) that parameterizes the allowable trajectory on the surface. The velocity of the end-effector $v$ can be expressed as:

$$v = D(s)\dot{s}$$

Where:
*   $s$ represents the curvilinear coordinate or generalized coordinate along the direction of the constraint (for example, the position along a guide or the rotation angle of a crank).
*   $\dot{s}$ is the time derivative of the parameterization.
*   $D(s)$ (related to the derivative of the kinematic map of the constraint $\frac{\partial \alpha}{\partial y}$) is the matrix mapping variations of parameter $s$ into linear and angular velocities of the end-effector expressed in the base frame.

#### Reaction Force Parameterization (Force Degrees of Freedom)
Similarly, reaction forces $h_e$ arising from environmental constraints are not arbitrary, but lie in the space orthogonal to the directions allowed for motion. They can be parameterized via the vector $\lambda$:

$$h_e = Y(s)\lambda$$

Where:
*   $\lambda$ is the vector of Lagrange multipliers (of dimension $6-k$), representing the independent components of the reaction force.
*   $Y(s)$ is the matrix of constraint force directions.

Since the subspaces of constraint forces and allowed motions are mutually orthogonal, the orthogonality condition holds strictly:

$$Y^T(s) D(s) = 0$$

> **Key Concept: Advantage of Constraint Parameterization**  
> Adopting the $(s, \lambda)$ parameterization allows implicitly incorporating kinematic and natural constraints into the model. Using the variables $s$ is equivalent to representing the dynamic system projected directly onto the subspace where motion is physically possible, mathematically "absorbing" the presence of the constraint itself.

---

### Definition of Control Objectives

The primary objective in constrained interaction is the design of the control action $\tau$ (joint torque input) to simultaneously satisfy two distinct tasks:

1.  **Motion Tracking:** Ensure that the end-effector follows a desired position, velocity, and acceleration profile expressed via the parameterization variable:
    $$s(t) \to s_d(t), \quad \dot{s}(t) \to \dot{s}_d(t), \quad \ddot{s}(t) \to \ddot{s}_d(t)$$
    *(Example: sliding the end-effector along a straight guide according to a specific time profile).*

2.  **Force Tracking:** Ensure that the forces exerted by the end-effector on the environment follow a desired force profile $\lambda_d(t)$:
    $$\lambda(t) \to \lambda_d(t)$$
    *(Example: pressing on the surface while moving along it to remove a layer of glue or material).*

Unlike free space (where all $6$ dimensions of operational space are used for position/orientation tracking), in constrained motion the presence of the environment requires simultaneous and decoupled control of $k$ motion variables ($s$) and $6-k$ force variables ($\lambda$).

---

### Synthesis of Hybrid Control: Two-Phase Approach

The main goal of hybrid position/force control synthesis is to enable the manipulator to simultaneously track two desired trajectories:
1. A kinematic trajectory in the space of allowable positions/velocities, parameterized by the variable $s(t)$.
2. A constraint reaction force/torque trajectory, parameterized by the variable $\lambda(t)$.

To achieve this goal, a cascade approach based on **Feedback Linearization** and decoupling is used, structured in two sequential phases:

1. **Decoupling and Linearization:** Design the control law for joint torques $u$ as a function of two auxiliary accelerations ($a_s$ for position and $a_\lambda$ for force), obtaining a simplified and decoupled dynamics.
2. **Error Stabilization:** Design the auxiliary accelerations $a_s$ and $a_\lambda$ to guarantee the convergence to zero of tracking errors $e_s = s_d - s$ and $e_\lambda = \lambda_d - \lambda$.

---

### Step 1: Decoupling and Linearization via Feedback

Assume that the system operates away from kinematic singularity points and that the robot's number of degrees of freedom equals that of the operational space ($n = m = 6$).

Recall that operational space velocity $V_\Omega$ and contact forces $f_m$ can be expressed through the new parameterizations tied to the constraints:
$$V_\Omega = J(q)\dot{q} = S \dot{s}$$
$$f_m = Y(s)\lambda$$

Differentiating the kinematic relations with respect to time and substituting the robot dynamics equations in the joint domain, joint acceleration $\ddot{q}$ can be expressed as a function of the derivatives of the new parameterized variables $\ddot{s}$ and the force variable $\lambda$.

> **Note: Step integrated with educational clarity**  
> Substituting the reaction force relation $f_m = Y(s)\lambda$ and the expression for $\ddot{q}(s, \dot{s}, \ddot{s})$ into the manipulator's dynamic equations yields a structure in which the vector of joint accelerations and reaction forces appears in block-composite matrix form:

$$A(q) \begin{bmatrix} \ddot{s} \\ \lambda \end{bmatrix} + b(q, \dot{q}) = u$$

Where:
* $A(q)$ is an $n \times n$ matrix (with $n=6$) formed by two column blocks: the first block multiplies the auxiliary acceleration $\ddot{s}$, while the second block (derived from the $-J^T Y$ term) multiplies the Lagrange multipliers vector $\lambda$.
* $b(q, \dot{q})$ is a vector gathering all nonlinear coupling terms (Coriolis, centrifugal force, gravity, and derivatives of parameterization matrices).
* $u$ is the vector of torques applied to the robot joints.

To linearize and completely decouple the system, the control law $u$ is chosen as:

$$u = A(q) \begin{bmatrix} a_s \\ a_\lambda \end{bmatrix} + b(q, \dot{q})$$

Substituting this control law $u$ into the system equation yields the desired perfect decoupling:

$$\begin{bmatrix} \ddot{s} \\ \lambda \end{bmatrix} = \begin{bmatrix} a_s \\ a_\lambda \end{bmatrix} \implies \begin{cases} \ddot{s} = a_s \\ \lambda = a_\lambda \end{cases}$$

With this first step, the complex nonlinear system has been reduced to:
* A **Double Integrator** along the directions of allowable motion ($s$).
* A direct algebraic relationship along the force constraint directions ($\lambda$).

---

### Step 2: Tracking Error Stabilization

Once the decoupled system is obtained, the auxiliary inputs $a_s$ and $a_\lambda$ must be designed.

#### Position/Velocity Control ($s$)
For the motion component, the objective is to drive the position error $e_s(t) = s_d(t) - s(t)$ to zero. Since the equivalent dynamics is that of a double integrator $\ddot{s} = a_s$, a control law with a feedforward term of the desired acceleration and a Proportional-Derivative (PD) corrective action is adopted:

$$a_s = \ddot{s}_d + K_{ds}(\dot{s}_d - \dot{s}) + K_{ps}(s_d - s)$$

Substituting $a_s = \ddot{s}$ into the double integrator equation gives the error dynamics:

$$\ddot{s}_d - \ddot{s} + K_{ds}(\dot{s}_d - \dot{s}) + K_{ps}(s_d - s) = 0$$

$$\ddot{e}_s + K_{ds}\dot{e}_s + K_{ps}e_s = 0$$

> **Key Concept: Position Error Stability**  
> Choosing positive-definite gain matrices $K_{ps}$ and $K_{ds}$, the second-order linear constant-coefficient differential equation is asymptotically and **exponentially stable**. The position error $e_s(t)$ converges to zero: $\lim_{t \to \infty} e_s(t) = 0$.

#### Force Control ($\lambda$)
For the force component, the objective is to drive the error $e_\lambda(t) = \lambda_d(t) - \lambda(t)$ to zero. Being a direct algebraic relation ($\lambda = a_\lambda$), theoretically setting $a_\lambda = \lambda_d$ would suffice. However, to guarantee robustness against model uncertainties or measurement noise, an **Integral Action** term is added:

$$a_\lambda = \lambda_d + K_{i\lambda} \int_{0}^{t} (\lambda_d(\tau) - \lambda(\tau)) d\tau$$

Since $\lambda = a_\lambda$, substituting the expression yields:

$$\lambda = \lambda_d + K_{i\lambda} \int_{0}^{t} e_\lambda(\tau) d\tau \implies e_\lambda(t) + K_{i\lambda} \int_{0}^{t} e_\lambda(\tau) d\tau = 0$$

Differentiating this equation with respect to time:

$$\dot{e}_\lambda + K_{i\lambda} e_\lambda = 0$$

> **Key Concept: Force Error Stability**  
> Adding integral action converts the force error dynamics into a **first-order** differential equation. If the integral gain $K_{i\lambda}$ is positive, the force error converges exponentially to zero, while providing robustness against sensor imperfections.

---

### Architecture of the Hybrid Control Scheme

The overall architecture of the Hybrid Control Scheme appears as a feedback structure organized into the following logical blocks:

* **Measurement Sensors:** The system requires real-time knowledge of state variables $s, \dot{s}$ (velocities and positions along allowable directions) and forces $\lambda$.
  * Motion variables are derived from joint kinematic sensors.
  * Reaction forces $\lambda$ are measured directly via **Force Sensors** located at the end-effector of the robot, or estimated via software algorithms called **Soft Sensors**.
* **Measurement Filter:** In industrial practice, signals acquired from force sensors are pre-filtered to damp high-frequency spikes and measurement noise before being sent to the feedback block.
* **Feedback Linearization Module:** Computes the matrix $A(q)$ and vector $b(q, \dot{q})$ based on natural and artificial task constraints (Task Reference Frame).

```
                      +-------------------+
                      |   Trajectories    |
                      |  s_d, lambda_d    |
                      +---------+---------+
                                |
                                v
+-------------+       +-------------------+       +--------------------+      +----------+
| Pos. & Force|------>|   Control Phases  |------>|      Feedback      |----->| Robot /  |
|   Sensors   |       |  (PD on s, I on λ)| (a_s, | Linearization u=A*a| (u)  |Environment
+-------------+       +-------------------+  a_λ) +--------------------+      +----------+
```

This approach guarantees stability and desired performance, especially in applications where both the manipulator structure and contact environment exhibit high mechanical stiffness (Stiff System).

---

### Practical Activities and Numerical Experiences

#### Structure of Computational Labs
To complete the applied pathway of the course, practical exercises based on numerical simulations are made available. The material is organized into modules covering two fundamental pillars of manipulator robotics:

*   **Kinematics:** Analysis of position and orientation of the end-effector for standard architectures such as **SCARA** robots and articulated manipulators of the **UR (Universal Robots)** type.
*   **Dynamic Control:** Implementation of joint torque control algorithms for trajectory tracking on full dynamic models.

The practical goal consists of manipulating the provided code files, completing the implementation of required control algorithms, and analyzing the performance of simulated systems.

---

### Guide to Exam Exercises and Analytical Shortcuts

#### Kinematics and Reference Frame Assignment
In exercises dedicated to kinematics, a specific mechanical structure of a robotic arm is usually provided along with joint and link (*limb*) indications. The exam task requires:
1.  Orienting and assigning coordinate reference frame axes (according to known conventions, such as Denavit-Hartenberg).
2.  Analyzing or calculating the reachable **Workspace** of the manipulator.

#### Calculation Simplification and Physical Intuition

> **KEY CONCEPT: Using Physical Intuition vs. Automatic Calculation**
>
> When solving dynamic analytical exercises, **do not rush headlong into complex matrix calculations**. Concept shortcuts based on system physics often exist that drastically simplify equations.

Consider the general dynamics equation of a manipulator:

$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = \tau$$

Where $M(q)$ is the inertia matrix, $C(q, \dot{q})$ gathers Coriolis and centrifugal terms, $g(q)$ is the gravity vector, and $\tau$ is the joint torque vector.

*   **Practical Example:** If a part of the robot or a specific joint performs a purely horizontal translational movement (or moves within a plane perpendicular to the gravity vector $\vec{g}$), the system's gravitational potential energy $U$ remains constant ($\Delta U = 0$).
*   **Analytical Consequence:** In this case, the derivative of potential energy with respect to free coordinates $q$ is zero, directly implying that the gravity force vector is zero:

$$g(q) = \frac{\partial U(q)}{\partial q} = 0$$

Recognizing this physical condition immediately avoids unnecessarily computing coordinate transformations or complex matrices to determine the gravity component.

#### Theory Section: Controller Design
The theoretical portion of the exam focuses heavily on **Controller Design**. Questions require precise, structured, and targeted answers regarding specific control architectures (e.g., Computed Torque Control, PD Control with gravity compensation, Operational Space Control).

---

### Frontiers of Robotics Research (Advanced Research Topics)

#### Reinforcement Learning in Robotics
The integration of **Reinforcement Learning (RL)** and classical robotics aims to increase robot autonomy in executing complex tasks within uncertain environments. Research develops on both simulation platforms and real hardware (e.g., laboratory robotic manipulators), addressing challenges such as:
*   Transitioning from simulation to the real world (*Sim-to-Real transfer*).
*   Stability and safety of mechanical interaction during learning.

#### Large Language Models & Large-Scale Software
Another advanced research line concerns combining **Large Language Models (LLMs)** with robotic control software systems.

*   **Use Case (High-Level Interface):** A user interacts with a robot (e.g., a quadrotor) via a high-level natural language command: *"I lost my backpack in the park, can you send me the coordinates of where it is?"*.
*   **Technological Challenge:** The model must translate this abstract prompt into a logical and executable sequence of atomic actions (e.g., exploration planning, visual object detection, GPS coordinate extraction).
*   **Operational Constraint:** Standard language models are huge and computationally heavy. Research aims to create lightweight models (*Small Language Models*) capable of running onboard the robot while respecting strict **computational and energy resource** constraints of embedded systems.

---

### Clarifications on Trajectory Generation

In dynamic control lab exercises, the primary goal is tracking a reference. If the desired trajectory generation law $q_d(t)$ is not explicitly constrained or defined in the exercise text:
*   Any **standard trajectory generation approach** found in literature may be used (e.g., trapezoidal velocity profiles, cubic or quintic polynomials).
*   Main focus must remain on the correct synthesis of the control law $\tau$ capable of driving the tracking error $e(t) = q_d(t) - q(t)$ to zero.

---

> [!NOTE]
> ### Exam Notes and Instructor Announcements
> - **Dates and deadlines**: The indicated exam date is December 29. The deadline for submitting the final assignment/numerical experience is set for June 25.
> - **Exam syllabus**: The exam will cover almost all material in the course PDF files (with a few exceptions to be specified later), explicitly including the section on hybrid control.
> - **Practical exercises in the exam**: Practical exercises will most likely concern **kinematics** (e.g., given a robotic arm structure, orienting axes or analyzing/designing the workspace).
> - **Theory section of the exam**: The theoretical part will focus mainly on **controller design**. The instructor specified requiring precise and prompt answers to direct questions.
> - **Proofs and derivations**: Some starting equations might be provided during the exam with the request to carry out the steps and final mathematical derivations.