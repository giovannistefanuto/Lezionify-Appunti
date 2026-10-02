# Direct Adaptive Control and Linear Parameterization of Dynamics

## Educational Overview

In this lecture, the family of **adaptive controllers** is introduced, focusing in particular on **Direct Adaptive Control**. The main problem addressed is trajectory tracking of a robotic manipulator in the presence of uncertainty in the dynamic parameters of the system (such as masses, moments of inertia, and center of mass positions).

The key points covered in the lecture include:
* **Objective of Direct Adaptive Control:** The primary goal is to drive the tracking error to zero ($e \to 0$) by updating the estimates of the dynamic parameters in real time. The distinction from *Indirect Adaptive Control* is highlighted: in direct control, the tracking error drops to zero even if the estimated parameters do not necessarily converge to their true values.
* **First Ingredient — Linear Parameterization of Dynamics:** Decomposition of the robot's dynamic model into the product between the **Regressor Matrix** $Y(q, \dot{q}, \ddot{q})$, which depends on the kinematic state of the robot and has a known structure, and the **Dynamic Parameter Vector** $\pi$, for which only an estimate $\hat{\pi}$ is available.
* **Second Ingredient — Recall of Model-Based Control Techniques:** Analysis of the behavior of **Feedforward + PD** and **Feedback Linearization** (*Inverse Dynamics*) controllers. It is shown how, in the presence of parametric uncertainties, imperfect nonlinear cancellation results in the confinement of trajectories within a bounded neighborhood (an error "tube") around the desired trajectory.

---

### Introduction to Direct Adaptive Control

In the control of robotic manipulators, precise and perfect knowledge of the system's dynamic model is often unavailable, having only an estimate instead. The goal of **Adaptive Control** is to manage these parametric uncertainties by updating the model parameters in real time during task execution.

In this context, we distinguish two main approaches:

1. **Direct Adaptive Control:** Our primary purpose is to make the tracking error tend to zero ($e(t) \to 0$). We continuously update the estimate of the dynamic parameters during operation, but it is *not necessary* for the estimated parameters to converge to their true values. It is sufficient to guarantee that the trajectory error goes to zero.
2. **Indirect Adaptive Control:** Requires a more refined formulation with the explicit objective of accurately estimating the true physical dynamic parameters of the robot.

> **Key Concept**
> In **Direct Adaptive Control**, the main objective is driving the trajectory tracking error to zero ($e \to 0$), **not** the exact convergence of the dynamic parameters to their true values. We can achieve perfect tracking even with stationary, incorrect parameter estimates.

---

### The Two Fundamental Ingredients

To design a direct adaptive control law, we make use of two fundamental conceptual "ingredients".

#### Ingredient 1: Linear Parameterization of Dynamics

The dynamics of an $n$-joint manipulator can be rewritten by exploiting the property of **Linear Parameterization**:

$$
\tau = Y(q, \dot{q}, \ddot{ddot{q}}) \pi
$$

Where:
* $Y(q, \dot{q}, \ddot{q}) \in \mathbb{R}^{n \times p}$ is the **Regressor Matrix**. It depends exclusively on the kinematic variables (joint positions, velocities, and accelerations) and the robot geometry (link lengths), which we assume to be known.
* $\pi \in \mathbb{R}^{p}$ is the **Dynamic Parameter Vector**. It contains the combination of the physical properties of the masses, moments of inertia, and the positions of the centers of mass of the various links.

**Properties of the Regressor and Parameters:**
* **Reduced dimension of $\pi$:** Theoretically, we would have 10 dynamic parameters per link ($10n$). However, not all parameters actively enter the dynamics, and many combine linearly with each other. The effective dimension $p$ of $\pi$ is therefore appropriately reduced by eliminating negligible terms or combining them.
* **Structure of $Y$:** The regressor matrix $Y$ has a dependence that is:
  * *Linear* with respect to joint accelerations $\ddot{q}$.
  * *Quadratic* with respect to joint velocities $\dot{q}$.
  * *Nonlinear* (trigonometric functions such as sine and cosine) with respect to joint positions $q$.

In our uncertainty scenario, we consider $Y$ to be perfectly known (since the kinematics is known), whereas for the dynamic parameter vector we only possess an uncertain estimate, denoted by $\hat{\pi}$.

#### Ingredient 2: Selection of the Control Structure and Stability Issues

Let us review the nonlinear control strategies for trajectory tracking:

1. **Feedback Linearization:** 
   Tries to cancel the nonlinear dynamics exactly by inserting the estimated matrices $\hat{B}(q)$, $\hat{C}(q, \dot{q})$, and $\hat{g}(q)$. 
   * *Problem with the adaptive approach:* Making it adaptive online is problematic. The estimated inertia matrix $\hat{B}(q)$ must always remain positive definite (all eigenvalues $\lambda > 0$). During high-frequency online updates (e.g., $1 \text{ kHz}$), small estimation oscillations can cause even a very small eigenvalue to become negative (e.g., from $+0.001$ to $-0.001$). This sign change destroys the stability of the system, causing severe instabilities.
   
2. **Advanced Control Law (Hybrid Feedforward + PD Feedback):**
   To avoid positive definiteness issues, a globally asymptotically stable control law that does not invert the inertia matrix is preferred. The starting structure (in case of uncertain parameters $\hat{\pi}$) evaluates the dynamic terms not on the desired trajectory, but on the current state:

$$
\tau = \hat{B}(q)\ddot{q}_d + \hat{C}(q, \dot{q})\dot{q}_d + \hat{g}(q) + K_d (\dot{q}_d - \dot{q}) + K_p (q_d - q)
$$

Exploiting the regressor $Y$, the structural part of the model can be expressed directly as a function of the parameter estimate $\hat{\pi}$:

$$
\tau = Y(q, \dot{q}, \dot{q}_d, \ddot{q}_d) \hat{\pi} + K_d \dot{e} + K_p e
$$

---

### Parametric Uncertainty and Modification of Reference Trajectory

When working with the adaptive law, the goal is to provide a time update law for the derivative of the parameter estimate, $\dot{\hat{\pi}}(t)$, which, when integrated, gives us the evolution of $\hat{\pi}(t)$.

However, applying the law indicated above directly presents a known practical drawback:

> **Caution (Position Drift Issue):**
> Direct adaptation based solely on velocity error tends to drive the velocity error perfectly to zero ($\dot{e} \to 0$), but can produce a drift phenomenon on the position error ($e \ne 0$), taking the robot far from the desired trajectory in certain configurations.

#### Robustification via Reference Velocity

To overcome this drawback and guarantee robustness in both position and velocity, we introduce a **Reference Velocity** $\dot{q}_r$, defined by modifying the desired velocity $\dot{q}_d$ with a corrective term on the position error:

$$
\dot{q}_r = \dot{q}_d + \Lambda e = \dot{q}_d + \Lambda (q_d - q)
$$

Where $\Lambda$ (or $\Gamma$) is a positive definite gain matrix. 
Consequently, the reference acceleration becomes:

$$
\ddot{q}_r = \ddot{q}_d + \Lambda \dot{e}
$$

We also define the **Filtered Tracking Error** $s$:

$$
s = \dot{q}_r - \dot{q} = \dot{e} + \Lambda e
$$

Substituting $\dot{q}_d$ and $\ddot{q}_d$ with the corresponding reference quantities $\dot{q}_r$ and $\ddot{q}_r$ inside the regressor matrix $Y$, we obtain the robust formulation upon which the adaptive parameter update mechanism for $\hat{\pi}$ will be triggered.

---

### Modification of the Reference Trajectory: Insertion of Position Error

To understand the need to modify the reference trajectory, consider a simple conceptual example: tracking an ideal mass (represented in green) moving at a desired velocity $\dot{q}_d$. Our goal is to ensure that the actuated mass (represented in yellow), whose position is $q$, overlaps perfectly with the green one.

If we were to provide the controller solely with the desired velocity $\dot{q}_d$ as the reference to track, the strategy would fail.

Suppose that the yellow mass is behind the green mass by a distance $e > 0$ (where the position error is $\tilde{q} = q_d - q$), but is moving at the exact same velocity ($\dot{q} = \dot{q}_d$). In this scenario:
* The velocity error is zero ($\dot{q}_d - \dot{q} = 0$).
* The actuator applies no force ($u = 0$).

Consequently, the system will continue to move at velocity $\dot{q}_d$, while keeping the position error $e > 0$ constant over time without ever zeroing it.

#### Solution: Introduction of Modified Reference Velocity

To overcome this limitation, we redefine the velocity provided to the controller. Instead of directly using $\dot{q}_d$, we introduce a **Reference Velocity** $\dot{q}_r$ that includes a corrective term proportional to the **Position Tracking Error** $\tilde{q}$:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

where:
* $\tilde{q} = q_d - q$ is the position error.
* $\Lambda$ (Lambda) is a **Positive Definite Gain Matrix**, conventionally set as $\Lambda = K_D^{-1} K_P > 0$.

In this way, even if the nominal velocity error were zero, the presence of a position error $\tilde{q} \neq 0$ would generate a larger reference velocity $\dot{q}_r$, imposing an actuation force that drives the system to reduce the error to zero.

#### Common Definitions: Filtered Error $\sigma$

We can define a new synthetic state variable, called the **Filtered Tracking Error** $\sigma$, expressed as:

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

Note that $\sigma$ directly represents the difference between the new reference velocity and the actual velocity of the system.

---

### Control Law Formulation and Closed-Loop Dynamics

We now replace the nominal velocity $\dot{q}_d$ and acceleration $\ddot{q}_d$ terms with their respective reference terms $\dot{q}_r$ and $\ddot{q}_r$ inside the control law.

By differentiating $\dot{q}_r$ with respect to time, we obtain the **Reference Acceleration** $\ddot{q}_r$:

$$\ddot{q}_r = \ddot{q}_d + \Lambda \dot{\tilde{q}}$$

#### Control Law in the Ideal Case
Assuming **perfect knowledge of the system's dynamic parameters** ($B(q)$ inertia matrix, $C(q, \dot{q})$ Coriolis and centrifugal matrix, $g(q)$ gravity vector), the proposed control law is:

$$u = B(q)\ddot{q}_r + C(q,\dot{q})\dot{q}_r + g(q) + K_D \sigma$$

This structure represents a hybrid combination of a **Feedforward Term** based on the modified reference trajectories and a **Feedback Term** on the filtered error $\sigma$.

#### Closed-Loop System Equation
Substituting the control action $u$ into the robot dynamic model $B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = u$, we obtain:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = B(q)\ddot{q}_r + C(q,\dot{q})\dot{q}_r + g(q) + K_D \sigma$$

Simplifying $g(q)$ and rearranging the terms:

$$B(q)(\ddot{q}_r - \ddot{q}) + C(q,\dot{q})(\dot{q}_r - \dot{q}) + K_D \sigma = 0$$

Since $\dot{q}_r - \dot{q} = \sigma$ and $\ddot{q}_r - \ddot{q} = \dot{\sigma}$, the differential equation governing the **Closed-loop Error Dynamics** simply becomes:

$$B(q)\dot{\sigma} + C(q,\dot{q})\sigma + K_D \sigma = 0$$

---

### Lyapunov Stability Analysis in the Ideal Case

> **KEY CONCEPT**
> Prove that the modified control law guarantees **Global Asymptotic Stability (GAS)** of the system under the hypothesis of perfect knowledge of the dynamic parameters. The convergence of the filtered error $\sigma \to 0$ directly implies that both the position error and the velocity error asymptotically tend to zero ($\tilde{q} \to 0, \dot{\tilde{q}} \to 0$).

To carry out the stability proof, we apply the **Lyapunov Direct Method**.

#### 1. Choice of the Lyapunov Function Candidate
We define the scalar function $V(\sigma, \tilde{q})$ as the sum of a kinetic energy term expressed with respect to $\sigma$ and a quadratic form depending on the position error $\tilde{q}$:

$$V(\sigma, \tilde{q}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{q}^T M \tilde{q}$$

where $M$ is a positive definite matrix to be designed ($M > 0$).

* **Properties of $V$:** Since $B(q) > 0$ and $M > 0$, the function $V(\sigma, \tilde{q})$ is strictly positive definite ($V > 0$ for every $(\sigma, \tilde{q}) \neq (0,0)$) and vanishes only at the origin $(\sigma = 0, \tilde{q} = 0)$.

#### 2. Computation of the Time Derivative $\dot{V}$
We compute the time derivative along the trajectories of the system:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

From the error dynamic equation we know that $B(q)\dot{\sigma} = -C(q,\dot{q})\sigma - K_D \sigma$. Substituting this relation into the derivative:

$$\dot{V} = \sigma^T \left( -C(q,\dot{q})\sigma - K_D \sigma \right) + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

Grouping the quadratic terms in $\sigma$:

$$\dot{V} = \frac{1}{2} \sigma^T \left( \dot{B}(q) - 2C(q,\dot{q}) \right) \sigma - \sigma^T K_D \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

Exploiting the fundamental **Skew-Symmetry** property of the matrix $(\dot{B}(q) - 2C(q,\dot{q}))$, the first term vanishes identically:

$$\frac{1}{2} \sigma^T \left( \dot{B}(q) - 2C(q,\dot{q}) \right) \sigma = 0$$

The time derivative thus reduces to:

$$\dot{V} = -\sigma^T K_D \sigma + \tilde{q}^T M \dot{\tilde{q}}$$

#### 3. Substitution and Elimination of Cross Terms
*(Note: Step integrated with pedagogical clarity to make the algebraic steps conducted in class explicit).*

We substitute the definition $\sigma = \dot{\tilde{q}} + \Lambda \tilde{q}$ inside the term $-\sigma^T K_D \sigma$:

$$-\sigma^T K_D \sigma = -(\dot{\tilde{q}} + \Lambda \tilde{q})^T K_D (\dot{\tilde{q}} + \Lambda \tilde{q}) = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q}$$

Substituting this expansion into the expression for $\dot{V}$:

$$\dot{V} = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q} + \tilde{q}^T M \dot{\tilde{q}}$$

Exploiting the degree of freedom in choosing the positive definite matrix $M$, we specifically set:

$$M = 2 \Lambda K_D$$

With this suitable choice, the term $+ \tilde{q}^T M \dot{\tilde{q}} = 2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}}$ exactly cancels the cross term $-2 \tilde{q}^T \Lambda K_D \dot{\tilde{q}}$.

The final expression of the Lyapunov derivative becomes:

$$\dot{V} = -\dot{\tilde{q}}^T K_D \dot{\tilde{q}} - \tilde{q}^T \Lambda K_D \Lambda \tilde{q}$$

#### 4. Conclusion on Stability
Since $K_D > 0$ and $\Lambda > 0$, both matrix $K_D$ and matrix $\Lambda K_D \Lambda$ are positive definite.

Consequently, $\dot{V}$ turns out to be **strictly negative definite** ($\dot{V} < 0$) for any error configuration other than the origin:

$$\dot{V} < 0 \quad \forall (\tilde{q}, \dot{\tilde{q}}) \neq (0,0)$$

This formally proves that the error state converges asymptotically and globally to the origin:

$$\lim_{t \to \infty} \tilde{q}(t) = 0 \quad \text{and} \quad \lim_{t \to \infty} \dot{\tilde{q}}(t) = 0$$

With this result, we have mathematically confirmed that the modification of the reference trajectory guarantees perfect tracking without position drift in the ideal case. The next step will be to extend this formulation to the case where knowledge of the dynamic parameters is imperfect, introducing **Adaptive Control** laws.

---

### Model-Based Adaptive Control

#### Motivation and Problem Formulation
In previous lectures, the control law was analyzed under the assumption of perfect knowledge of the system's dynamic parameters. In reality, however, one almost always possesses an approximate knowledge of the model.

The goal of **Adaptive Control** is to update the estimate of the system parameters in real time during the operation of the algorithm, so as to guarantee that the tracking error is zeroed, compensating for modeling uncertainties.

Exploiting the property of **linear parameterization**, the actual dynamics of the system and the approximate model can be expressed through the regressor matrix $Y$ and the dynamic parameter vector $\pi$:

*   **Actual Model:** $B(q)\ddot{q} + C(q, \dot{q})\dot{q} + F\dot{q} + g(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\pi$
*   **Estimated Model:** $\hat{B}(q)\ddot{q}_r + \hat{C}(q, \dot{q})\dot{q}_r + \hat{F}\dot{q}_r + \hat{g}(q) = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi}$

Where $\hat{\pi}$ represents the vector of estimated parameters, and the reference variable for velocity $\dot{q}_r$ is defined as a function of the position error $\tilde{q} = q_d - q$ and the gain matrix $\Lambda$:

$$\dot{q}_r = \dot{q}_d + \Lambda \tilde{q}$$

The filtered error variable $\sigma$ is defined as:

$$\sigma = \dot{q}_r - \dot{q} = \dot{\tilde{q}} + \Lambda \tilde{q}$$

We propose the following adaptive control law:

$$u = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\hat{\pi} + K_D \sigma$$

with $K_D$ a positive definite matrix.

---

#### Control Law and Error Dynamics
Substituting the control law $u$ into the actual system dynamics, the closed-loop error dynamics is obtained.

> **Note:** *Step integrated with pedagogical clarity.*
> Adding and subtracting the terms of the actual dynamics evaluated at $\dot{q}_r$ and $\ddot{q}_r$, we can express the system equation as a function of the filtered error $\sigma$ and the **model mismatch** $\tilde{\pi} = \hat{\pi} - \pi$:

$$B(q)\dot{\sigma} + C(q, \dot{q})\sigma + F\sigma + K_D \sigma = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)\tilde{\pi}$$

Where $\tilde{\pi} = \hat{\pi} - \pi$ represents the parameter estimation error (noting that $\dot{\tilde{\pi}} = \dot{\hat{\pi}}$, as the true parameter values $\pi$ are considered constant over time).

---

#### Lyapunov Stability Analysis and Update Law
To derive the continuous-time **update law** for the estimated parameter vector $\hat{\pi}$, we use Lyapunov stability theory.

We choose a positive definite **Lyapunov Function Candidate**, consisting of the sum of the kinetic energy associated with the error and a quadratic term linked to the parameter mismatch:

$$V(\sigma, \tilde{\pi}) = \frac{1}{2} \sigma^T B(q) \sigma + \frac{1}{2} \tilde{\pi}^T K_\pi \tilde{\pi}$$

where $K_\pi$ is a customizable positive definite matrix.

We compute the time derivative of $V$:

$$\dot{V} = \sigma^T B(q) \dot{\sigma} + \frac{1}{2} \sigma^T \dot{B}(q) \sigma + \tilde{\pi}^T K_\pi \dot{\tilde{\pi}}$$

Substituting the error dynamics $B(q)\dot{\sigma} = - C(q, \dot{q})\sigma - F\sigma - K_D \sigma + Y\tilde{\pi}$ and exploiting the skew-symmetry property of the matrix $(\dot{B} - 2C)$, the $C(q, \dot{q})$ terms cancel out:

$$\dot{V} = -\sigma^T (K_D + F) \sigma + \sigma^T Y \tilde{\pi} + \tilde{\pi}^T K_\pi \dot{\hat{\pi}}$$

Grouping the terms containing the parameter error $\tilde{\pi}$:

$$\dot{V} = -\sigma^T (K_D + F) \sigma + \tilde{\pi}^T \left( Y^T \sigma + K_\pi \dot{\hat{\pi}} \right)$$

To make the Lyapunov derivative negative definite or negative semi-definite, we zero the term inside the parentheses by imposing the following **adaptive update law**:

$$Y^T \sigma + K_\pi \dot{\hat{\pi}} = 0 \implies \dot{\hat{\pi}} = - K_\pi^{-1} Y^T(q, \dot{q}, \dot{q}_r, \ddot{q}_r) \sigma$$

Substituting this update law, the time derivative becomes:

$$\dot{V} = -\sigma^T (K_D + F) \sigma \le 0$$

Since $\dot{V} \le 0$, the system is stable in the sense of Lyapunov and, by Barbalat's Lemma, the filtered error $\sigma \to 0$ as $t \to \infty$. Consequently, it is guaranteed that:

$$\tilde{q} \to 0 \quad \text{and} \quad \dot{\tilde{q}} \to 0 \quad \text{for } t \to \infty$$

that is, the tracking error converges exactly to zero.

---

### Parameter Convergence and Model Adjustment

#### Tracking Error vs Parameter Convergence
A fundamental aspect of adaptive control lies in the distinction between tracking error convergence and parameter estimation error convergence.

> **Key Concept**
> Zeroing the tracking error ($\tilde{q} \to 0$) **DOES NOT guarantee** that the estimated parameter vector $\hat{\pi}$ converges to the true value $\pi$.
> Lyapunov theory only guarantees that $\hat{\pi}$ remains bounded and converges to a constant value $\bar{\pi}$, but it is not necessarily true that $\bar{\pi} = \pi$.

At equilibrium ($\sigma = 0$), the update law vanishes ($\dot{\hat{\pi}} = 0$). If the condition $Y\tilde{\pi} = 0$ is satisfied with a vector $\tilde{\pi} \neq 0$ belonging to the kernel (null space) of the regressor $Y$, the system will reach a zero tracking error while maintaining an imperfect knowledge of the dynamic parameters.

---

#### The Role of the Trajectory: Persistent Excitation
For the parameter estimate $\hat{\pi}$ to converge to the true value $\pi$, the desired reference trajectory $q_d(t)$ must be **sufficiently rich (persistently exciting)** to excite all dynamic modes of the system.

*   **Linear Systems:** Precise analytical conditions exist regarding the signal's frequency and waveform to guarantee the excitation of all dynamics.
*   **Nonlinear Systems:** Finding *a priori* theoretical conditions for persistent excitation is extremely complex. Generally, one resorts to injecting trajectories composed of harmonics at different frequencies (e.g., sums of sinusoids) to accurately estimate the system dynamics.

---

### Practical Example: Inverted Pendulum with Friction

#### Modeling and Linear Parameterization
Consider the model of an inverted pendulum with viscous friction at the joint:

$$I \ddot{\theta} + M g d \sin(\theta) + F_v \dot{\theta} = u$$

We can decompose the equation into the product between the regressor matrix $Y$ and the parameter vector $\pi \in \mathbb{R}^3$:

$$Y(\theta, \dot{\theta}, \dot{\theta}_r, \ddot{\theta}_r) = \begin{bmatrix} \ddot{\theta}_r & \sin(\theta) & \dot{\theta}_r \end{bmatrix}, \quad \pi = \begin{bmatrix} I \\ M g d \\ F_v \end{bmatrix}$$

The combined control and adaptation laws are:

$$u = Y \hat{\pi} + K_D \sigma, \quad \dot{\hat{\pi}} = - K_\pi^{-1} Y^T \sigma$$

with $\sigma = (\dot{\theta}_d - \dot{\theta}) + \lambda (\theta_d - \theta)$.

---

#### Comparison between Trajectories (Sinusoid vs Bang-Bang Trajectory)
Two different reference trajectories $q_d(t)$ are simulated to evaluate control performance and parameter estimation:

1.  **Single-Frequency Sinusoidal Trajectory:** $\theta_d(t) = A \sin(\omega t)$
2.  **Acceleration Bang-Bang Trajectory:** Alternating acceleration profile $\ddot{\theta}_d(t) \in \{+1, -1\}$ at regular intervals (rich in harmonic components).

| Performance Metric | Sinusoidal Trajectory (Single Frequency) | Bang-Bang Trajectory (Rich) |
| :--- | :--- | :--- |
| **Position/Velocity Error ($\tilde{q}, \dot{\tilde{q}}$)** | Converges to zero ($\to 0$) | Converges to zero ($\to 0$) with more aggressive transients |
| **Torque Trend ($u$)** | Smooth and quasi-sinusoidal | Highly dynamic |
| **Parameter Convergence ($\hat{\pi} \to \pi$)** | **Incomplete:** Friction $F_v$ is estimated, but errors remain on Inertia $I$ and mass $M g d$. | **Complete:** All 3 estimated parameters converge to true values. |

**Conclusion from the example:** In both cases, the main control objective (zeroing the tracking error) is achieved. However, only the *Bang-Bang* trajectory, thanks to its greater spectral richness (*Persistent Excitation*), allows for the complete identification of the system's true dynamic parameters.