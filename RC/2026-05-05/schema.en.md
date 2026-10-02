# Homework Assignment on Modeling and Trajectories & Introduction to Advanced Control Strategies for Manipulators

## Educational Overview

In this lecture, two main blocks are addressed: guidelines for completing the third practical *homework* and a methodological review of control architectures for robotic manipulators. 

The main topics covered include:
* **Kinematic Modeling and Reference Frame Assignment:** Correct procedure for assigning reference frames (*frames*) to manipulator joints, essential for deriving the kinematic model without resorting to arbitrary assumptions.
* **Trajectory Planning in Joint and Operational Space:** Design of a straight-line trajectory for the end-effector in operational space (*task space*) and its conversion into joint space (*joint space*), satisfying constraints on zero initial and final velocities.
* **Error Dynamics Analysis and Controller Simulation:** Study of system response via a feedback linearization controller (*Feedback Linearization*). In this context, joint error evolution is reduced to a system of second-order ordinary differential equations, allowing trajectory tracking convergence (*trajectory tracking*) to be analyzed without simulating the full non-linear robot dynamics.
* **Overview of Control Strategies for Manipulators:** Synthetic review of the evolution of control techniques, from the classic *Feedforward* + *Feedback* combination approach (local convergence) to exact feedback linearization (global convergence), introducing the transition toward adaptive control (*Adaptive Control*).

---

3. Homework Organization, Logistics, and Deadlines

 This section details the requirements, scoring structure, and logistics for completing Homework 3 (and a brief mention of the subsequent Homework 4). The objective of the homework is to consolidate the modeling and control of robotic manipulators through the rigorous application of the methodologies covered in class.

---

#### Homework 3 Structure and Point Breakdown

The homework is divided into three main parts for a total of **2 overall points** (plus any quality bonuses):

1. **Robot Modeling (1.0 Point - Mandatory)**
2. **Trajectory Planning (0.5 Points)**
3. **Trajectory Tracking Control Design (0.5 Points - Optional)**

---

#### Part 1: Kinematic Modeling and Frame Assignment (1 Point)

In this first part, you are required to derive the kinematic/dynamic model of the provided robotic structure.

> **KEY CONCEPT: Reference Frame Assignment Rules (Frame Assignment)**
>
> To get full points, it is **essential** to follow the systematic procedure for reference frame assignment (*Frame Assignment*) explained in class (e.g., Denavit-Hartenberg convention).
> 
> Do not position the axes arbitrarily or randomly: even if a different frame choice generates a mathematically valid model, the exercise aims to verify mastery of the standard method taught in the course. Based on past history from previous years, about one third of students make a mistake or customize the frame assignment, resulting in 0 points for this part.

---

#### Part 2: Trajectory Planning (0.5 Points)

The second part requires designing a trajectory in joint space (*Joint Space*) starting from specifications defined in operational/Cartesian space (*Operational Space*):

* **Motion Geometry:** The end-effector (*End-Effector*) must move along a **straight line** in the Cartesian plane between the initial and final configuration.
* **Timing and Boundary Conditions:** The motion must be completed in time $T = 5\text{ s}$, starting from zero velocity and arriving at zero velocity ($\dot{q}(0) = 0$ and $\dot{q}(T) = 0$).

##### Recommended Solution Strategy:
1. Design the straight-line trajectory in Cartesian space for the position of the end-effector.
2. Map the Cartesian trajectory into the joint space $q(t)$ using *Inverse Kinematics*.
3. Consider the kinematic evolution: during the straight-line motion of the end-effector, the individual joints will rotate/translate in a non-linear manner, potentially requiring local motion reversals to maintain the straight Cartesian trajectory.

---

#### Part 3: Tracking Control Design and Simulation (0.5 Optional Points)

In this optional section, the goal is to design a trajectory tracking controller (*Trajectory Tracking Controller*) and verify its ability to converge the error to zero starting from perturbed initial conditions (e.g., placing the robot in an initial configuration slightly different from the nominal one $q(0) \neq q_d(0)$).

> **KEY CONCEPT: Simplification of Controller Simulation**
>
> To verify convergence and demonstrate the controller's behavior, it is **NOT necessary to implement a full non-linear dynamic model of the robot** (e.g., via complex dynamic simulation). 
> 
> Since the feedback linearization (*Feedback Linearization*) or dynamic inversion controller ideally cancels the non-linear dynamics of the robot, the closed-loop error dynamics reduces to a **second-order linear differential system**.

##### Derivation of the Error Equation:
*(Note: Step integrated for pedagogical clarity)*

We define the joint position error as:
$$e(t) = q_d(t) - q(t)$$

Applying the exact feedback linearization technique, the time evolution of the joint error satisfies the following second-order matrix differential equation:

$$\ddot{e}(t) + K_d \dot{e}(t) + K_p e(t) = 0$$

Where $K_p$ and $K_d$ are the proportional and derivative gain matrices. 

If the gain matrices are chosen to be diagonal:
$$K_p = \text{diag}(k_{p1}, k_{p2}), \quad K_d = \text{diag}(k_{d1}, k_{d2})$$

The matrix equation of the system decouples into independent scalar differential equations for each joint $i$:

$$\ddot{e}_i(t) + k_{di} \dot{e}_i(t) + k_{pi} e_i(t) = 0, \quad i=1, 2$$

##### What to present in the solution:
* Analytically or numerically solve the linear error ordinary differential equations (ODEs) starting from the perturbed initial condition $e(0) \neq 0$.
* Plot the time histories of the joint error evolution $e_q(t) = [e_1(t), e_2(t)]^T$ or Cartesian space error $e_x(t), e_y(t)$.
* Show the effect of varying gains $K_p$ and $K_d$ on system convergence performance.

---

#### Bonus Evaluation and Quality Criteria

In addition to the 2 nominal points, bonus point fractions ($\sim 0.5$ random points) can be awarded for assignments completed in an exceptionally clear manner, with well-formatted graphs and free of generic responses passively generated by AI tools (e.g., ChatGPT).

---

#### Deadlines Overview

| Activity / Homework | Description | Agreed Deadline |
| :--- | :--- | :--- |
| **Homework 3** | Modeling, Trajectory, and Control (with Adaptive part) | **End of May** |
| **Homework 4** | Numerical exercise/simulation | **Beyond June** (after the end of classes) |

---

### Adaptive Control Based on Modified Reference Trajectory

In previous lectures we have seen that the classical approach to controller design for a robotic manipulator involves a feedback and feedforward structure (*Feedforward + Feedback*). However, feedforward compensation based solely on the desired trajectory $(q_d, \dot{q}_d, \ddot{q}_d)$ guarantees only local error convergence. Although *Feedback Linearization* allows achieving global convergence, it requires exact knowledge of the system dynamic parameters.

When model knowledge is approximate, standard feedforward control may show good performance in velocity tracking, but often generates a steady-state error (*offset*) in position. To overcome this limitation, the nominal trajectory is modified by introducing a modified **reference velocity** $q_r$.

#### From Desired Velocity to Reference Velocity ($q_r$)

We define the position tracking error as $\tilde{q} = q_d - q$. The key idea is to add a term proportional to the position error to the desired velocity $\dot{q}_d$:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

where $\Lambda$ is a diagonal, positive-definite gain matrix.
Taking the time derivative, the corresponding reference acceleration is:

$$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$

We now define the composite error variable $\sigma$ (i.e., the reference velocity error):

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

If $\sigma \to 0$, the differential equation $\dot{\tilde{q}} + \Lambda \tilde{q} = 0$ guarantees that the position error $\tilde{q}$ and velocity error $\dot{\tilde{q}}$ also converge asymptotically to zero.

---

### Ideal Case: Lyapunov Stability Analysis with Perfect Model

Before addressing parameter adaptation, we prove global convergence considering the robot dynamics to be known, described by:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + F\dot{q} + g(q) = u$$

Substituting the desired terms with the reference quantities $(\dot{q}_r, \ddot{q}_r)$, we propose the following control law:

$$u = B(q)\ddot{q}_r + C(q, \dot{q})\dot{q}_r + F\dot{q}_r + g(q) + K_d \sigma$$

Substituting the control $u$ into the system dynamics equation, we obtain the closed-loop error dynamics:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_d \sigma = 0$$

> **Key Concept**: Proving stability using the composite variable $\sigma$ is mathematically simpler than using classical state variables directly, and still guarantees global convergence of the state to the origin.

#### Stability Proof (Lyapunov)

We choose the following candidate Lyapunov function $V(q, \sigma, \tilde{q})$:

$$V = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T H \tilde{q}$$

where $H$ is a symmetric, positive-definite matrix appropriately chosen as $H = 2 \Lambda K_d$. Since $B(q)$ is the inertia matrix (positive-definite), $V > 0$ for all non-zero states.

We compute the time derivative $\dot{V}$ along system trajectories:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

Substituting the error dynamics $B(q)\dot{\sigma} = -C(q, \dot{q})\sigma - F\sigma - K_d \sigma$:

$$\dot{V} = \sigma^T \left( -C(q, \dot{q})\sigma - F\sigma - K_d \sigma \right) + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

Grouping the terms and exploiting the skew-symmetry property of the matrix $(\dot{B} - 2C)$, whereby $\sigma^T (\dot{B}(q) - 2C(q,\dot{q})) \sigma = 0$:

$$\dot{V} = -\sigma^T K_d \sigma - \sigma^T F \sigma + \tilde{q}^T H \dot{\tilde{q}}$$

*(Note: Step integrated for pedagogical clarity)*
Since $F$ represents friction (positive semi-definite matrix) and choosing $H = 2 \Lambda K_d$ with suitable algebraic simplification of cross-terms, the derivative reduces to the negative semi-definite form:

$$\dot{V} \le -\sigma^T K_d \sigma \le 0$$

Applying **LaSalle's Invariance Principle** (*LaSalle's Invariance Principle*), the only invariant set where $\dot{V} = 0$ is the one for which $\sigma = 0$. This directly implies that:

$$\lim_{t \to \infty} \tilde{q}(t) = 0 \quad \text{and} \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

The tracking error therefore globally converges to zero.

---

### Real Case: Adaptive Control in the Presence of Parameter Uncertainty

In the real case, we do not know the physical parameters of the system (masses, centers of mass, inertias, friction) precisely. We replace the real matrices with their nominal estimates $\hat{B}, \hat{C}, \hat{F}, \hat{G}$.

Exploiting the property of linearity in dynamic parameters (*Linearity in Parameters*), we can rewrite the dynamic model with respect to the **parameter vector** $\pi \in \mathbb{R}^p$ and the **regressor matrix** $Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \in \mathbb{R}^{n \times p}$:

$$B(q)\ddot{q}_r + C(q, \dot{q})\dot{q}_r + F\dot{q}_r + g(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \pi$$

> **Key Concept**: In the Regressor $Y$, the reference accelerations and velocities ($\ddot{q}_r, \dot{q}_r$) replace the desired terms, while the actual state of the system ($q, \dot{q}$) continues to be used where necessary. Identifying which terms to replace is a crucial step in controller design.

The actual applied control law becomes:

$$u = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi} + K_d \sigma$$

where $\hat{\pi}$ is the instantaneous estimate of the dynamic parameters.
We define the parameter estimation error as:

$$\tilde{\pi} = \hat{\pi} - \pi$$

Substituting this control law into the real robot dynamics, we obtain the error dynamics perturbed by the parameter error:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_d \sigma = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \tilde{\pi}$$

If the estimated parameter vector $\hat{\pi}$ were to remain constant ($\hat{\pi} = \text{constant}$), the presence of $\tilde{\pi} \neq 0$ would prevent the error from converging to zero, confining the state inside a neighborhood of the origin (an error "tube" proportional to parameter uncertainty).

---

### Parameter Adaptation Law and Global Stability

To guarantee that tracking error converges to zero, the estimate $\hat{\pi}(t)$ must be updated dynamically along the trajectory.

We extend the candidate Lyapunov function by inserting a quadratic term linked to the parameter error:

$$V(q, \sigma, \tilde{q}, \tilde{\pi}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T H \tilde{q} + \frac{1}{2} \tilde{\pi}^T \Gamma^{-1} \tilde{\pi}$$

where $\Gamma = \Gamma^T > 0$ is the **adaptation rate matrix** (*adaptation rate matrix*).

Since the real system parameters $\pi$ are constant over time, we have $\dot{\tilde{\pi}} = \dot{\hat{\pi}}$. Computing the time derivative of $V$:

$$\dot{V} = -\sigma^T K_d \sigma + \tilde{\pi}^T Y^T \sigma + \tilde{\pi}^T \Gamma^{-1} \dot{\hat{\pi}}$$

Grouping terms that depend on the parameter error $\tilde{\pi}$:

$$\dot{V} = -\sigma^T K_d \sigma + \tilde{\pi}^T \left( Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma + \Gamma^{-1} \dot{\hat{\pi}} \right)$$

To cancel the effect of parameter uncertainty on the Lyapunov derivative, we require the term in parentheses to be zero:

$$Y^T \sigma + \Gamma^{-1} \dot{\hat{\pi}} = 0 \implies \dot{\hat{\pi}} = -\Gamma Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$

Substituting the **adaptation law** thus designed, the derivative of the Lyapunov function becomes:

$$\dot{V} = -\sigma^T K_d \sigma \le 0$$

#### Convergence Properties of the Adaptive System

1. **Tracking Error**: Since $\dot{V} \le 0$, the variables $\sigma, \tilde{q}, \dot{\tilde{q}}$ are bounded and will asymptotically converge to zero:

$$\lim_{t \to \infty} \tilde{q}(t) = 0, \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

2. **Parameter Estimation**: Since $\dot{V} \le 0$, the parameter error $\tilde{\pi}$ also remains bounded. Since $\sigma \to 0$, the update law ensures that $\dot{\hat{\pi}} \to 0$, which means that $\hat{\pi}$ converges to a constant value.

> **Key Concept**: The primary objective of this adaptive control scheme is to drive the trajectory tracking error to zero ($\tilde{q} \to 0$), **not** to guarantee that the parameter estimate converges to the real values ($\hat{\pi} \to \pi$). The convergence of $\hat{\pi} \to \pi$ occurs only if the trajectory guarantees *Persistent Excitation* conditions (*Persistent Excitation*), i.e., if the regressor $Y$ has full rank along the trajectory. If $\tilde{\pi} \in \text{ker}(Y)$, the dynamic error will still be zero even with incorrect estimated parameters.

---

### Adaptive Control Scheme Architecture

The overall architecture of the adaptive control algorithm consists of two integrated loops:
1. **Real-Time Control Loop**: Calculates the input signal $u(t)$ based on current estimates $\hat{\pi}(t)$ and the robot state $(q, \dot{q})$.
2. **Adaptation Loop**: Continuously updates the parameter estimates vector $\hat{\pi}(t)$ by integrating the differential law $\dot{\hat{\pi}}$.

```
Desired Trajectory (qd, qd_dot, qd_ddot)
       │
       ▼
 ┌───────────┐      qr_dot, qr_ddot      ┌─────────────────────┐
 ├─► q_ref ──┼──────────────────────────►│  Regressor Matrix   │
 │ └─────────┘                           │   Y(q,q_dot,qr,qr)  │
 │     ▲                                 └──────────┬──────────┘
 │     │ q, q_dot                                   │
 │     │                                            ▼
 │  ┌──┴────────┐   sigma   ┌───────────┐     Y^T * sigma     ┌─────────────────────┐
 │  │Calculation├──────────►│   Gain    ├────────────────────►│   Adaptation Law    │
 │  │ of sigma  │           │    Kd     │                     │  pi_hat_dot = -Γ Yᵀσ│
 │  └──┬────────┘           └─────┬─────┘                     └──────────┬──────────┘
 │     ▲                          │                                      │
 │     │                          ▼                                      ▼
 │     │                       ┌──┴──┐                                ┌──┴──┐
 │     │                       │  +  │◄─── Y * pi_hat ───────────────┤ ∫dt │ (pi_hat)
 │     │                       └──┬──┘                                └─────┘
 │     │                          │ u(t)
 │     │                          ▼
 │     │                   ┌─────────────┐
 └─────┴───────────────────┤ Manipulator │
        q, q_dot           │   (Robot)   │
                           └─────────────┘
```

#### Logical Calculation Steps:

1. **Reference Signal Generation**:
   $$\tilde{q} = q_d - q$$
   $$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$
   $$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$
   $$\sigma = \dot{q}_r - \dot{q}$$

2. **Parameter Update**:
   $$\dot{\hat{\pi}}(t) = -\Gamma Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$
   $$\hat{\pi}(t) = \hat{\pi}(0) + \int_{0}^{t} \dot{\hat{\pi}}(\tau) \, d\tau$$

3. **Control Law Calculation**:
   $$u(t) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi}(t) + K_d \sigma(t)$$

---

### Practical Adaptive Control Example: The Single-Link Pendulum

To concretely understand the application of adaptive control, let us consider a simple mechanical system: a single-link pendulum (Single Link Pendulum).

#### Dynamic Model of the Pendulum
The dynamics of this system is described by the following non-linear differential equation:

$$I \ddot{\theta} + m g d \sin(\theta) + f_v \dot{\theta} = u$$

Where:
*   $\theta$ represents the angular displacement of the pendulum.
*   $\dot{\theta}$ and $\ddot{\theta}$ are the angular velocity and angular acceleration, respectively.
*   $I$ is the total moment of inertia relative to the axis of rotation. By the parallel axis theorem (Huygens-Steiner Theorem), $I = I_G + m d^2$, where $I_G$ is the inertia relative to the center of mass, $m$ is the mass, and $d$ is the distance between the center of mass and the axis of rotation.
*   $m g d \sin(\theta)$ is the gravitational term.
*   $f_v$ is the viscous friction coefficient (Viscous Friction Coefficient).
*   $u$ is the total applied control torque (Total Applied Torque).

---

#### Linear Parameterization of the Model
A key ingredient for adaptive control design is **Linear Parameterization** (*Linear Parameterization*). It consists of separating the state variables (known and measurable) from the uncertain dynamic parameters, expressing the dynamics in the form:

$$Y(\theta, \dot{\theta}, \ddot{\theta}) \, \pi = u$$

Where $Y$ is the **Regressor Matrix** (*Regressor Matrix*) and $\pi$ is the **Dynamic Parameter Vector** (*Dynamic Parameter Vector*). 

In our specific case, we can rewrite the dynamics as the scalar product between the row vector $Y$ and the column vector $\pi$:

$$Y(\theta, \dot{\theta}, \ddot{\theta}) = \begin{bmatrix} \ddot{\theta} & \sin(\theta) & \dot{\theta} \end{bmatrix}$$

$$\pi = \begin{bmatrix} I \\ m g d \\ f_v \end{bmatrix}$$

Multiplying $Y \cdot \pi$, we obtain exactly the equation of motion:

$$I \ddot{\theta} + m g d \sin(\theta) + f_v \dot{\theta} = u$$

---

#### Adaptive Control Law Design
Let us assume that we do not know the exact real dynamic parameters ($I$, $m g d$, $f_v$), but only have an estimate of them at the current time, denoted by $\hat{\pi} = \begin{bmatrix} \hat{I} & \widehat{m g d} & \hat{f}_v \end{bmatrix}^T$.

First, we define reference variables and errors:
1.  **Position Error**: $e = \theta_d - \theta$ (where $\theta_d$ is the desired trajectory).
2.  **Virtual Reference Velocity**: $\dot{\theta}_r = \dot{\theta}_d + \lambda e$, with $\lambda = \frac{K_p}{K_d} > 0$ (ratio of proportional to derivative gains).
3.  **Filtered Error (or Sliding) Signal**: $\sigma = \dot{\theta}_r - \dot{\theta} = \dot{e} + \lambda e$.

The control law actually implemented is based on the regressor evaluated along the reference variables $Y_r$:

$$Y_r = Y(\theta, \dot{\theta}, \dot{\theta}_r, \ddot{\theta}_r) = \begin{bmatrix} \ddot{\theta}_r & \sin(\theta) & \dot{\theta}_r \end{bmatrix}$$

> **Note**: The positional term $\sin(\theta)$ does not change in the reference regressor since the position state $\theta$ is directly measured and does not require estimation or virtual replacement.

The **Control Torque** ($u$) thus consists of a dynamic compensation term based on estimated parameters and a proportional-derivative action term ($K_D \sigma$):

$$u = Y_r \hat{\pi} + K_D \sigma = \hat{I} \ddot{\theta}_r + \widehat{m g d} \sin(\theta) + \hat{f}_v \dot{\theta}_r + K_D \sigma$$

---

#### Parameter Update Law (Adaptation Law)
Estimated parameters must vary over time to reduce the tracking error. The adaptation law for the time derivative of the parameter estimate $\dot{\hat{\pi}}$ is defined as:

$$\dot{\hat{\pi}} = K_\pi^{-1} Y_r^T \sigma$$

Where $K_\pi^{-1}$ is a diagonal matrix of positive-definite adaptation gains:

$$K_\pi^{-1} = \begin{bmatrix} \gamma_1 & 0 & 0 \\ 0 & \gamma_2 & 0 \\ 0 & 0 & \gamma_3 \end{bmatrix}$$

Expanding the vector-matrix calculations:

$$\begin{bmatrix} \dot{\hat{I}} \\ \dot{\widehat{m g d}} \\ \dot{\hat{f}}_v \end{bmatrix} = \begin{bmatrix} \gamma_1 & 0 & 0 \\ 0 & \gamma_2 & 0 \\ 0 & 0 & \gamma_3 \end{bmatrix} \begin{bmatrix} \ddot{\theta}_r \\ \sin(\theta) \\ \dot{\theta}_r \end{bmatrix} \sigma$$

*(Note: Step integrated for pedagogical clarity to show individual parameter updates:)*

$$\begin{cases} \dot{\hat{I}} = \gamma_1 \ddot{\theta}_r \sigma \\ \dot{\widehat{m g d}} = \gamma_2 \sin(\theta) \sigma \\ \dot{\hat{f}}_v = \gamma_3 \dot{\theta}_r \sigma \end{cases}$$

---

### Performance Analysis and Simulation Examples

Let us analyze the system behavior under two different desired trajectories $\theta_d(t)$, starting from initial conditions $\theta(0) = 0$ and $\dot{\theta}(0) = 0$.

*   **Initial Position Error**: $e(0) = \theta_d(0) - \theta(0) = 0$.
*   **Initial Velocity Error**: If the desired trajectory starts with velocity $\dot{\theta}_d(0) = -1$, then $\dot{e}(0) = -1 - 0 = -1$. This explains why in graphical simulations the velocity error starts at $-1$ while position error starts at $0$.

#### Case 1: Sinusoidal Desired Trajectory
We feed the controller a sinusoidal position trajectory: $\theta_d(t) = -\sin(t)$.

*   **Trajectory tracking**: Both errors (position and velocity) asymptotically converge to zero ($e \to 0, \dot{e} \to 0$). This complies with the stability guaranteed by theory.
*   **Parameter estimation**: Looking at the evolution of the estimated parameters $\hat{\pi}$, we observe that the derivative $\dot{\hat{\pi}} \to 0$, which means that stable parameters settle to constant values. **However, the estimated parameters do not converge to the real system values!** Only one parameter ($\hat{f}_v$) comes close to the real value, while the other two converge to incorrect values that mutually self-compensate in the model.

#### Case 2: Bang-Bang Acceleration Trajectory
We now feed a trajectory where desired acceleration has a square wave ("Bang-Bang") profile, oscillating between $+1$ and $-1$ at constant frequency.

*   **Trajectory tracking**: As in the previous case, position and velocity converge perfectly to zero.
*   **Parameter estimation**: In this case, **all three parameter estimates $\hat{\pi}$ converge exactly to the real values of the system ($\pi$)**.

---

### Persistent Excitation

> **KEY CONCEPT: Persistent Excitation**
> To guarantee that the estimated parameters $\hat{\pi}$ converge to their **real physical values** $\pi$ (and not just to numerical values that nullify tracking error), the desired trajectory must be **Persistently Exciting** (*Persistently Exciting*).
> 
> *   A **pure sinusoidal trajectory** excites too narrow a range of frequencies of the non-linear system dynamics. As a result, the controller "settles" for an incorrect parameter combination that is sufficient to compensate for that specific sinusoid.
> *   A **Bang-Bang trajectory** (or one rich in harmonics and acceleration jumps) excites the entire dynamics of the system. This broader spectral content forces the regressor $Y_r$ to explore the state space completely, eliminating ambiguities and guaranteeing the convergence of $\hat{\pi} \to \pi$.

In the case of non-linear systems, analytically determining whether a trajectory is persistently exciting is a complex task; however, the general principle remains tied to the frequency "richness" of the reference signal.

---

### Extension: Partial Parameter Knowledge

If we know certain dynamic parameters exactly a priori (for example, if the friction coefficient $f_v$ is precisely known), there is no need to adapt them.

We can split the linear parameterization into two distinct parts:

$$Y \pi = Y_u \pi_u + Y_k \pi_k$$

Where:
*   $\pi_u$ (Uncertain Parameters): Vector of uncertain parameters to adapt.
*   $Y_u$: Regressor part associated with uncertain parameters.
*   $\pi_k$ (Known Parameters): Vector of precisely known parameters.
*   $Y_k$: Regressor part associated with known parameters.

The control law reduces to:

$$u = Y_{u,r} \hat{\pi}_u + Y_{k,r} \pi_k + K_D \sigma$$

Adaptation will be applied solely to the uncertain component $\dot{\hat{\pi}}_u = K_{\pi,u}^{-1} Y_{u,r}^T \sigma$. 

**Practical advantage**: By reducing the number of uncertain parameters to be estimated in real time, a significantly faster transient dynamic and a vastly superior error convergence speed are achieved.

---

### Introduction to Visual Servoing

Up to this point, we have analyzed control schemes assuming we can directly measure the physical quantities of the robot, such as joint positions and velocities or their respective quantities in Cartesian space.

We now drop this assumption and consider a scenario where feedback information comes primarily from visual systems (cameras). The control of a robot guided by visual measurements is called **Visual Servoing** (*Visual Servoing*).

```
[Real World / Robot] ---> [Camera] ---> [Feature Extraction (2D)] ---> [Error Calculation] ---> [Control]
```

#### Data Flow Management and Information Reduction
When acquiring a real-time video stream, saving and processing the full image pixel by pixel for every frame is neither computationally sustainable nor useful. Control systems require fast responses; therefore, it is necessary to process the image on the fly using image processing algorithms (*Image Processing*) to extract only a synthesis of information useful for the task.

> **Example:** If the robot must track a green sphere, the high-resolution image is converted into a binary map (black and white) where only the object of interest stands out. From this map, a few synthetic parameters are extracted: the 2D coordinates of the center of the sphere and its radius in the image plane.

---

### Classification of Visual Control Approaches

There are two fundamental approaches to designing a *Visual Servoing* scheme:

#### 1. Position-Based Visual Servoing (PBVS)
In position-based control (3D):
1. Images acquired by one or more cameras are used (for example, via **Stereo Vision** techniques (*Stereo Vision*)) to reconstruct the 3D pose of the end-effector relative to a world/base reference frame.
2. Once the 3D pose $\boldsymbol{x}$ is estimated, the 3D Cartesian space error relative to the desired pose $\boldsymbol{x}_d$ is calculated.
3. The Cartesian control laws already studied are applied directly.

Since 3D reconstruction belongs to advanced *Computer Vision* courses, this approach does not constitute the main novel element of this section of the course.

#### 2. Image-Based Visual Servoing (IBVS)
In image-based control (2D):
1. Three-dimensional reconstruction of the scene is not performed.
2. The desired end-effector configuration is projected directly onto the two-dimensional **Image Plane** (*Image Plane*) of the camera.
3. The tracking/positioning error is defined directly in the 2D image plane as the difference between the actual visual feature positions and desired ones.
4. The control law is designed to evolve the robot working directly in image space $\mathbb{R}^2$.

> **KEY CONCEPT: Fundamental Difference between PBVS and IBVS**
> * **PBVS:** 2D Image $\rightarrow$ 3D Position Reconstruction $\rightarrow$ 3D Error Calculation $\rightarrow$ Cartesian Space Control.
> * **IBVS:** 2D Image $\rightarrow$ 2D Feature Extraction $\rightarrow$ 2D Error Calculation directly in Image Plane $\rightarrow$ Image Space Control.
> 
> The real scientific and methodological challenge of this part of the course consists in redesigning control laws (e.g., Lyapunov-based approaches) so that they work directly on 2D image plane measurements.

---

### Image Features and Simplified Modeling

In a *Visual Servoing* algorithm, the extracted information is called an **Image Feature** (*Image Feature*), with associated **Feature Parameters** (*Feature Parameters*):
* If the feature is a point, the parameter is given by its 2D coordinates $(u, v)$ on the image plane.
* If the feature is a line, the parameters can be the slope and intercept.
* If it is a region, geometric moments or principal components can be considered.

#### Working Assumptions for the Course
To keep the mathematical analysis accessible and focus on control synthesis:
1. We will assume that vision algorithms extract and track exclusively **visual feature points (*Point Features*)**.
2. We will denote by the vector $\boldsymbol{s} \in \mathbb{R}^{2k}$ the set of 2D coordinates of the $k$ tracked points in the image plane.

---

### Visual System Hardware Configurations

Depending on the physical location of the camera relative to the kinematic structure of the manipulator, three main architectures are identified:

1. **Eye-in-Hand (Camera on Manipulator):**
   The camera is rigidly attached to the robot's end-effector and moves in space along with it. The images change continuously as a function of the robot's motion.

2. **Eye-to-Hand / Eye-off-Hand (Camera Fixed in the Environment):**
   The camera is placed at a fixed location in the workspace and frames both the robot and the object to be manipulated.

3. **Hybrid Configurations (*Hybrid Configurations*):**
   Complex systems combining both mobile cameras mounted on the manipulator (*Eye-in-Hand*) and fixed cameras in the environment (*Eye-to-Hand*).

> **KEY CONCEPT: Course Reference Architecture**
> In upcoming lectures, we will refer exclusively to the **Single Eye-in-Hand** configuration (single camera mounted on the robot's end-effector).

---

### Preview: IBVS Control Design via Lyapunov

The objective of upcoming lectures will be to adapt control methods based on Lyapunov stability to the IBVS context. 

The candidate Lyapunov function $V$ will typically incorporate the contribution of the manipulator's kinetic energy and a quadratic term associated with the error calculated in the image plane:

$$V(\boldsymbol{q}, \dot{\boldsymbol{q}}, \boldsymbol{e}) = \frac{1}{2} \dot{\boldsymbol{q}}^T \boldsymbol{B}(\boldsymbol{q}) \dot{\boldsymbol{q}} + U(\boldsymbol{e})$$

where:
* $\boldsymbol{q}$ and $\dot{\boldsymbol{q}}$ are the joint positions and velocities.
* $\boldsymbol{B}(\boldsymbol{q})$ is the inertia matrix of the manipulator.
* $\boldsymbol{e} = \boldsymbol{s} - \boldsymbol{s}_d$ is the error expressed directly on the image plane between the observed features $\boldsymbol{s}$ and the desired ones $\boldsymbol{s}_d$.