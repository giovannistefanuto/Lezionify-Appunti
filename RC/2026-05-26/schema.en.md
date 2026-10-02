# Dynamic Modeling and Adaptive Control for Robotic Manipulators

## Educational Overview
In this lecture, guidelines for the dynamic modeling and control of a robotic arm (*Robotic Arm*) are presented, taking the specifications of the third educational assignment as a reference. 

Key concepts addressed include:

*   **Denavit-Hartenberg Convention (*Denavit-Hartenberg Convention - DH*):** Analysis and verification of the correctness of the definition of joint variables — in particular for a first prismatic joint and a second revolute joint ($q_2$) — according to conventional rules and modeling best practices (*Rule of Thumb*).
*   **Dynamic Model of the Manipulator (*Dynamic Model of the Manipulator*):** Formulation of the equations of motion in standard form:
    $$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = \tau$$
*   **PD Control with Constant Gravity Compensation (*PD Control with Constant Gravity Compensation*):** Design of a Proportional-Derivative control law combined with a constant gravity compensation term, evaluated at the desired final configuration $q_d$, namely $g(q_d)$.
*   **Linear Parameterization of Dynamic Model (*Linear Parameterization of Dynamic Model*):** Proof of linearity of the dynamic model with respect to a set of physical parameters and extraction of the regressor matrix (*Regressor Matrix*) $Y(q, \dot{q}, \ddot{q})$ and parameter vector $\theta$, such that:
    $$Y(q, \dot{q}, \ddot{q})\theta = \tau$$
*   **Adaptive Controller Design (*Adaptive Controller Design*):** Introduction of the reference velocity (*Reference Velocity*) $\dot{q}_r$ and formulation of the regressor matrix redefined as a function of the desired trajectories and the reference state of the system.

---

### Assignment of the Third Homework and Organizational Announcements

#### Deadlines and Grading
Due to delays in the assignment, the **Third Homework** will be worth **2 points** (instead of the usual single point). 

*   **Deadline:** Submission for the Third Homework (and for the subsequent Fourth Homework) is strictly set for **June 25th**. Submissions must take place before the exam session.
*   **Optionality:** Homeworks remain optional.

#### Details and Structure of the Third Homework
The central objective of the homework is the implementation of the dynamic model and the control scheme for a robotic manipulator (mechanical arm).

1. **Kinematic Analysis and Denavit-Hartenberg Conventions:**
   * The first joint is prismatic (so the variable $q_1$ represents a length), while the second joint is revolute ($q_2$ represents an angle).
   * Verify the compatibility of the definition of the variables $q_1$ and $q_2$ provided in the text with the Denavit-Hartenberg procedure (*Denavit-Hartenberg Procedure*) and the practical rules covered in class.
   * If deemed appropriate to redefine $q_2$ in a manner more consistent with the convention, it is possible to do so provided the choice is explicitly discussed and justified. Any variation in the angle $q_2$ will consequently modify the reference numerical values (e.g. angular values like $\pi$).

2. **Proportional-Derivative Control with Constant Gravity Compensation:**
   * Design a Proportional-Derivative (PD) control law coupled with a constant gravity compensation term:
     $$\tau = K_p e + K_d \dot{e} + g(q_d)$$
   * *Educational note:* Gravity compensation is calculated solely at the desired final configuration $q_d$, and not along the entire instantaneous dynamic state $g(q)$.

3. **Linear Parameterization of the Dynamic Model:**
   * Prove that the dynamic model of the robot can be expressed in linearly parameterized form with respect to a vector of unknown dynamic parameters $\pi$:
     $$\tau = Y(q, \dot{q}, \ddot{ddot{q}}) \pi$$
   * Where $Y(q, \dot{q}, \ddot{q})$ represents the **Regressor Matrix** (*Regressor Matrix*).

4. **Adaptive Controller Design (*Adaptive Controller*):**
   * Introduce the reference velocity $g_r$ (or sliding variable/synthetic velocity error $\dot{q}_r$).
   * Compute the extended regressor, which will no longer depend on real accelerations but on reference trajectories and velocities:
     $$Y_r = Y(q, \dot{q}, \dot{q}_r, \ddot{q}_r)$$

> **Key Concept: Pay Attention to the Regressor in Adaptive Control**
> The correct definition of the regressor matrix $Y_r$ as a function of reference velocities and accelerations ($\dot{q}_r, \ddot{q}_r$) is historically one of the most critical and complex points of the entire course. It is essential to pay maximum attention to the substitutions of state terms within the dynamic structure.

---

### Outline of the Final Lectures: Robot-Environment Interaction

The final part of the course is structured as follows:

1. **Theoretical Framework (Current Lecture):** Mathematical modeling of robot dynamics in the presence of rigid kinematic constraints (*Constrained Dynamics*).
2. **Practical Applications (Subsequent Lectures):** Study of operational techniques for contact management:
   * Impedance Control (*Impedance Control*)
   * Admittance Control / Augmented Control (*Admittance Control / Augmented Control*)

---

### Interaction Modeling with the Environment: Introduction to Constraints

To understand how a robot interacts with the external environment, the classical Lagrangian dynamic formulation must be extended by inserting the constraint reaction forces generated by the contact surfaces.

#### Fundamental Example: Point Mass on Rigid Wall
Consider the elementary physical system of a mass $m$ in a plane $(x, y)$, pushed by a horizontal force $F_x$ and a vertical force $F_y$. At a horizontal position $x = c$, there is an infinitely rigid wall (*Infinitely Stiff Environment*).

```
          y ^
            |       | Rigid Wall
            |   m   | (x = c)
            +--->   |
           F_y |    |
               o--->| F_x
               |    |
  -------------+----+--------> x
               |    |
```

##### 1. Free Motion ($x < c$)
As long as the mass does not touch the wall, the environment reaction force $F_e$ is zero ($F_e = 0$). The equations of motion directly follow Newton's second law:
$$m \ddot{x} = F_x$$
$$m \ddot{y} = F_y$$

##### 2. Contact with the Rigid Wall ($x = c$)
When the mass reaches the coordinate $x = c$, the environment prevents any further penetration. Assuming no deformations (infinitely rigid environment):
* Horizontal velocity vanishes: $\dot{x} = 0$
* Horizontal acceleration vanishes: $\ddot{x} = 0$

By the principle of action and reaction, the wall exerts a constraint reaction force $F_e$ along the $x$-axis equal and opposite to the applied force:
$$F_e = -F_x$$

The only admissible dynamics for the mass becomes a sliding motion along the vertical direction $y$ (*Constrained Motion*):
$$m \ddot{y} = F_y$$

#### Need for a Generalized Formulation
In the Cartesian orthogonal example, the constraint is trivial. However, if the robot end-effector (*End-Effector*) were constrained to move along a complex surface or a circular trajectory described by a non-linear equation of the form:
$$\phi(x, y) = 0$$

It is no longer possible to separate the equations of motion with an intuitive scalar analysis. It thus becomes necessary to develop an **Extended Lagrangian Formulation** (*Extended Lagrangian Theory*) capable of formally integrating kinematic constraints through the use of Lagrange multipliers.

---

### Theoretical Formulation of Constrained Dynamics via Augmented Lagrangian and Lagrange Multipliers

---

### Introduction to Geometric Constraints in Task and Joint Space

To describe the dynamics of a manipulator whose movements are restricted by an environmental constraint, we start from the standard unconstrained dynamic model in joint space:

$$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u$$

where $q \in \mathbb{R}^n$ is the vector of generalized coordinates (joint positions), $B(q)$ is the inertia matrix, $C(q, \dot{q})\dot{q}$ represents the Coriolis and centrifugal contributions, $g(q)$ is the gravity vector, and $u$ is the vector of forces/torques applied to the joints.

Assume that the task output function (*task output function*) is described by a forward kinematics relationship of the form $r = f(q) \in \mathbb{R}^p$. If the end-effector (*end-effector*) is subject to $M$ geometric constraints (with $M < n$), these constraints can be expressed in implicit form in operational space as $k(r) = 0$.

Substituting forward kinematics into the constraint equation, we can define a generic function $h(q)$ expressed directly in the generalized joint coordinates:

$$h(q) = 0$$

where $h(q): \mathbb{R}^n \to \mathbb{R}^M$ compactly describes the $M$ geometric constraint equations that the robot must satisfy during motion.

---

### The Augmented Lagrangian and Lagrange Multipliers

To derive the constrained equations of motion systematically, the classical formalism of Lagrangian mechanics is extended. The idea is to incorporate the presence of the constraint directly inside the original Lagrangian function $\mathcal{L}(q, \dot{q}) = K(q, \dot{q}) - P(q)$ (where $K$ is the kinetic energy and $P$ is the potential energy).

We define the **Augmented Lagrangian** (*Augmented Lagrangian*) $\mathcal{L}_a$ as:

$$\mathcal{L}_a(q, \dot{q}, \lambda) = \mathcal{L}(q, \dot{q}) + \lambda^T h(q)$$

where $\lambda \in \mathbb{R}^M$ is the vector of **Lagrange Multipliers** (*Lagrange Multipliers*).

> **Key Concept: Physical Meaning of Lagrange Multipliers**
> In mathematical optimization, $\lambda$ represents the dual variable introduced to handle constraints. In robotics and physics, $\lambda$ possesses a very precise physical meaning: it represents the magnitude of the **generalized reaction forces** (*generalized reaction forces*) generated by the environment when the robot attempts to violate the geometric constraint $h(q) = 0$.

Note that the Augmented Lagrangian $\mathcal{L}_a$ is no longer merely a function of positions $q$ and velocities $\dot{q}$, but also depends on the set of dual variables $\lambda$.

---

### Derivation of the Constrained Equations of Motion

Applying the Euler-Lagrange equations to the Augmented Lagrangian $\mathcal{L}_a$, we must calculate the partial derivatives with respect to $q$, $\dot{q}$, and $\lambda$:

1. **Derivative with respect to $q$ and $\dot{q}$:**
   The classical Euler-Lagrange equations take the form:
   $$\frac{d}{dt}\left( \frac{\partial \mathcal{L}_a}{\partial \dot{q}} \right) - \frac{\partial \mathcal{L}_a}{\partial q} = u$$

   Expanding the individual terms:
   - Since $h(q)$ does not depend on $\dot{q}$, we have: $\frac{\partial \mathcal{L}_a}{\partial \dot{q}} = \frac{\partial \mathcal{L}}{\partial \dot{q}}$.
   - Differentiating with respect to $q$, one must consider that $q$ appears both in the classical Lagrangian and in the constraint function $h(q)$:
     $$\frac{\partial \mathcal{L}_a}{\partial q} = \frac{\partial \mathcal{L}}{\partial q} + \frac{\partial}{\partial q}\left(\lambda^T h(q)\right) = \frac{\partial \mathcal{L}}{\partial q} + A^T(q)\lambda$$
     where $A(q) = \frac{\partial h(q)}{\partial q} \in \mathbb{R}^{M \times n}$ is the **Constraint Jacobian** (*Constraint Jacobian*).

   Substituting these terms yields the first equation of motion:
   $$B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u + A^T(q)\lambda$$

2. **Derivative with respect to $\lambda$:**
   Considering the derivative with respect to the dual variables:
   $$\frac{d}{dt}\left( \frac{\partial \mathcal{L}_a}{\partial \dot{\lambda}} \right) - \frac{\partial \mathcal{L}_a}{\partial \lambda} = 0$$
   Since $\dot{\lambda}$ does not appear in the Augmented Lagrangian, the first term is zero. Since $\mathcal{L}_a$ is linear with respect to $\lambda$, the partial derivative with respect to $\lambda$ directly yields the constraint equation:
   $$h(q) = 0$$

> **Key Concept: Zero Virtual Work of Reaction Forces**
> The right-hand side of the equation with respect to $\lambda$ is set to $0$ because the constraint reaction forces associated with $\lambda$ do not perform non-conservative work. The reaction force always acts orthogonal to the constraint surface (along the normal), while the elementary displacement of the robot occurs along the direction tangent to the constraint. Their scalar product is therefore zero:
> $$\delta W = F_{vincolo}^T \cdot \delta r = 0$$

---

### Elimination of the Lagrange Multiplier and Dynamically Consistent Projection Matrix

The resulting constrained dynamic equations constitute a differential-algebraic system (DAE):

$$\begin{cases} B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = u + A^T(q)\lambda \\ h(q) = 0 \end{cases}$$

The goal is to eliminate the explicit dependence on $\lambda$ to express the constrained motion using a single differential equation.

#### 1. Time derivative of the constraint
To relate the joint accelerations $\ddot{q}$ to the geometric constraint, we differentiate $h(q) = 0$ twice with respect to time:
1. First time derivative (chain rule):
   $$\dot{h}(q) = A(q)\dot{q} = 0$$
2. Second time derivative:
   $$\ddot{h}(q) = A(q)\ddot{q} + \dot{A}(q, \dot{q})\dot{q} = 0 \implies A(q)\ddot{q} = -\dot{A}(q, \dot{q})\dot{q}$$

#### 2. Explicit computation of $\lambda$
From the dynamics equation, we isolate the acceleration vector $\ddot{q}$:

$$\ddot{q} = B^{-1}(q) \left( u - C(q,\dot{q})\dot{q} - g(q) + A^T(q)\lambda \right)$$

Premultiplying both sides by the constraint Jacobian matrix $A(q)$ and substituting the acceleration condition $A(q)\ddot{q} = -\dot{A}(q)\dot{q}$, we obtain:

$$A(q) B^{-1}(q) \left( u - C(q,\dot{q})\dot{q} - g(q) + A^T(q)\lambda \right) = -\dot{A}(q)\dot{q}$$

Rearranging terms as a function of $\lambda$:

$$\left( A(q) B^{-1}(q) A^T(q) \right) \lambda = -\dot{A}(q)\dot{q} + A(q) B^{-1}(q) \left( C(q,\dot{q})\dot{q} + g(q) - u \right)$$

Assuming that $A(q)$ has full row rank ($rank(A) = M$, which requires $M < n$) and knowing that the inertia matrix $B(q)$ is always positive definite and invertible, the matrix $(A B^{-1} A^T) \in \mathbb{R}^{M \times M}$ is invertible.

We define the **Inertia-Weighted Pseudo-Inverse** (*Inertia-Weighted Pseudo-Inverse*) of the constraint Jacobian as:

$$A_B^\dagger(q) = B^{-1}(q) A^T(q) \left( A(q) B^{-1}(q) A^T(q) \right)^{-1}$$

We can thus express the vector of constraint reaction forces $\lambda$ in closed form:

$$\lambda = (A B^{-1} A^T)^{-1} \left( A B^{-1} (C\dot{q} + g - u) - \dot{A}\dot{q} \right)$$

#### 3. Projected equation of motion
Substituting the expression for $\lambda$ into the robot dynamic equation and collecting terms, we obtain the compact form of constrained motion:

$$B(q)\ddot{q} + P_B^T(q) \left( C(q,\dot{q})\dot{q} + g(q) \right) = P_B^T(q) u - B(q) A_B^\dagger(q) \dot{A}(q)\dot{q}$$

where $P_B(q)$ is the **Dynamically Consistent Projection Matrix** (*Dynamically Consistent Projection Matrix*), defined as:

$$P_B(q) = I - A_B^\dagger(q) A(q)$$

> **Physical Interpretation:** The matrix $P_B^T(q)$ acts as a projective filter that selects and eliminates all force components (both the applied external forces $u$ and fictitious or gravity forces) that would violate the constraint, transmitting to the system solely those forces that produce motion along the subspace permitted by the constraint surfaces.

---

### Consistency Verification and Motion Simulation

To guarantee that the numerical simulation of the constrained model is physically consistent, the initial conditions of the system $(q(0), \dot{q}(0))$ must strictly satisfy the kinematic constraints:

$$\begin{cases} h(q(0)) = 0 \\ A(q(0))\dot{q}(0) = 0 \end{cases}$$

If these initial conditions are respected, time integration of the projected dynamics guarantees that the resulting trajectory remains constrained to the surface $h(q) = 0$ for any torque profile $u(t)$ applied to the joints.

---

### Application Examples

#### Example 1: Point Mass Constrained on a Vertical Wall
Consider a point mass of mass $m$ moving in the $xy$ plane, subject to a rigid geometric constraint located at $x = c$.

- **Generalized coordinates:** $q = \begin{bmatrix} x \\ y \end{bmatrix}$
- **Inertia matrix:** $B = \begin{bmatrix} m & 0 \\ 0 & m \end{bmatrix}$
- **Applied forces:** $u = \begin{bmatrix} f_x \\ f_y \end{bmatrix}$
- **Constraint equation:** $h(q) = x - c = 0 \implies A = \frac{\partial h}{\partial q} = \begin{bmatrix} 1 & 0 \end{bmatrix}$

*(Note: Step integrated with educational clarity to complete the explicit calculation).*

Let us calculate the terms of the projective operator:
$$A B^{-1} A^T = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} \frac{1}{m} & 0 \\ 0 & \frac{1}{m} \end{bmatrix} \begin{bmatrix} 1 \\ 0 \end{bmatrix} = \frac{1}{m}$$

$$A_B^\dagger = B^{-1} A^T (A B^{-1} A^T)^{-1} = \begin{bmatrix} \frac{1}{m} \\ 0 \end{bmatrix} m = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

The Lagrange multiplier $\lambda$ reduces to:
$$\lambda = (A B^{-1} A^T)^{-1} A B^{-1} (-u) = m \cdot \left( -\frac{f_x}{m} \right) = -f_x$$

The value $\lambda = -f_x$ confirms the physical intuition: the constraint reaction exerted by the wall cancels exactly the force $f_x$ pushed against it.

The projection matrix $P_B$ results in:
$$P_B = I - A_B^\dagger A = \begin{bmatrix} 1 & 0 \\ 0 & 1 \end{bmatrix} - \begin{bmatrix} 1 \\ 0 \end{bmatrix} \begin{bmatrix} 1 & 0 \end{bmatrix} = \begin{bmatrix} 0 & 0 \\ 0 & 1 \end{bmatrix}$$

The filtered dynamics of the system becomes:
$$\begin{bmatrix} \ddot{x} \\ \ddot{y} \end{bmatrix} = \begin{bmatrix} 0 & 0 \\ 0 & \frac{1}{m} \end{bmatrix} \begin{bmatrix} f_x \\ f_y \end{bmatrix} \implies \begin{cases} \ddot{x} = 0 \\ m\ddot{y} = f_y \end{cases}$$

The motion along $x$ is suppressed, while the dynamics along $y$ remains free.

#### Example 2: End-Effector Constrained to Move on a Circle
Imagine a two-link planar manipulator whose end-effector is constrained to slide along a circular guide of radius $R$ and center $(x_0, y_0)$.

1. **Constraint in operational space:**
   $$k(r) = (x - x_0)^2 + (y - y_0)^2 - R^2 = 0$$

2. **Conversion to joint space:**
   Substituting forward kinematics $r = \begin{bmatrix} x(q) \\ y(q) \end{bmatrix} = f(q)$, the constraint function $h(q)$ is expressed:
   $$h(q) = (x(q) - x_0)^2 + (y(q) - y_0)^2 - R^2 = 0$$

3. **Calculation of the constraint Jacobian $A(q)$:**
   $$A(q) = \frac{\partial h(q)}{\partial q} = 2(x(q) - x_0)\frac{\partial x(q)}{\partial q} + 2(y(q) - y_0)\frac{\partial y(q)}{\partial q}$$

Inserting $A(q)$ into the general projection formulas ($P_B$ and $A_B^\dagger$), it is possible to accurately simulate the constrained dynamics of the robot, taking into account the constraint reactions $A^T(q)\lambda$ arising along the circle.

---

3. Reduced Dynamic Model and Pseudo-Velocities

#### Mathematical Formulation of the Reduced Space

When a mechanical system presents $n$ generalized coordinates and $m$ kinematic constraints of the form $A(q)\dot{q} = 0$, its effective dynamics possesses $n - m$ free degrees of freedom. From a mathematical and execution point of view (for example in interactive simulation environments such as MuJoCo), it is advantageous to describe the evolution of the system directly in the reduced-dimensional space $(n - m)$.

The goal is to define a new set of unconstrained variables, called **Pseudo-velocities** (*Pseudo-velocities*), with dimension $n - m$.

The constraint matrix $A(q) \in \mathbb{R}^{m \times n}$ has full rank $m$ (with $m < n$). To complete the basis of the velocity space, we introduce an arbitrary matrix $D(q) \in \mathbb{R}^{(n-m) \times n}$ such that the resulting block matrix is square $(n \times n)$ and non-singular (invertible):

$$\begin{bmatrix} A(q) \\ D(q) \end{bmatrix} \in \mathbb{R}^{n \times n}$$

Since the matrix is invertible, we can define its inverse by partitioning it into two matrices $E(q) \in \mathbb{R}^{n \times m}$ and $F(q) \in \mathbb{R}^{n \times (n-m)}$:

$$\begin{bmatrix} A(q) \\ D(q) \end{bmatrix}^{-1} = \begin{bmatrix} E(q) & F(q) \end{bmatrix}$$

From the inverse property $\begin{bmatrix} A \\ D \end{bmatrix} \begin{bmatrix} E & F \end{bmatrix} = I_n$, the following fundamental algebraic identities directly follow:

$$A E = I_m, \quad D F = I_{n-m}, \quad A F = 0_{m \times (n-m)}, \quad D E = 0_{(n-m) \times m}$$

> **Key Concept**: The matrix $F(q)$ projects any vector into the null space (*null space*) of the constraint matrix $A(q)$, since $A(q)F(q) = 0$.

We define the vector of **pseudo-velocities** $v \in \mathbb{R}^{n-m}$ as:

$$v = D(q)\dot{q}$$

Exploiting the inverse matrix, the fundamental relation linking the real generalized velocities $\dot{q}$ to the pseudo-velocities $v$ is given by:

$$\dot{q} = E(q)A(q)\dot{q} + F(q)D(q)\dot{q}$$

Since the constraint is satisfied ($A(q)\dot{q} = 0$), the equation reduces to:

$$\dot{q} = F(q)v$$

Differentiating with respect to time, we obtain the expression for the generalized accelerations:

$$\ddot{q} = F(q)\dot{v} + \dot{F}(q)v$$

Although the pseudo-velocities $v$ may not have a direct physical meaning, they guarantee an equivalent and exact description of the system in the reduced-dimensional space.

---

#### Derivation of the Reduced Dynamics

We substitute the expressions for $\dot{q}$ and $\ddot{q}$ into the complete dynamic equation of the robot with constraints:

$$B(q)\ddot{q} + C(q,\dot{q})\dot{q} + g(q) = u + A^T(q)\lambda$$

$$B(q)(F\dot{v} + \dot{F}v) + C(q,\dot{q})F v + g(q) = u + A^T(q)\lambda$$

We left-multiply the entire equation by $F^T(q)$:

$$F^T B F \dot{v} + F^T (B \dot{F} v + C F v + g) = F^T u + F^T A^T \lambda$$

Since $A F = 0$, we have $F^T A^T = (A F)^T = 0$. Consequently, the term related to the constraint forces $A^T \lambda$ **disappears completely** from the projected equation.

The reduced dynamic system takes the form:

$$B_v(q)\dot{v} + n_v(q,v) = F^T(q)u$$

Where:
*   $B_v(q) = F^T(q) B(q) F(q) \in \mathbb{R}^{(n-m) \times (n-m)}$ represents the **Reduced Inertia Matrix** (*Reduced Inertia Matrix*).
*   $n_v(q,v) = F^T(q)\left(B(q)\dot{F}(q)v + C(q,Fv)F(q)v + g(q)\right) \in \mathbb{R}^{n-m}$ collects the Coriolis, centrifugal, and gravity terms.

> **Key Concept**: Solving and integrating the dynamics on the reduced system $(n-m)$ requires a significantly lower computational load compared to the full $n$-dimensional dynamic system with explicit constraints.

The Lagrange multipliers $\lambda$ (i.e. contact forces) can be computed independently by premultiplying the dynamic equation by $E^T(q)$.

---

#### Practical Application: Constrained Point Mass

Let us reconsider the simple example of a point mass $m$ in a 2D plane ($n=2$), constrained to move along the horizontal axis ($m=1$).

*Generalized coordinates*: $q = [x, y]^T$, $\dot{q} = [\dot{x}, \dot{y}]^T$.
*Constraint*: $y = c \implies \dot{y} = 0 \implies A = [0 \quad 1]$.

To complete the matrix, we choose $D = [1 \quad 0]$ (*Note: Step integrated with educational clarity*):

$$\begin{bmatrix} A \\ D \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix}$$

The inverse of this matrix is:

$$\begin{bmatrix} A \\ D \end{bmatrix}^{-1} = \begin{bmatrix} 0 & 1 \\ 1 & 0 \end{bmatrix} = \begin{bmatrix} E & F \end{bmatrix} \implies E = \begin{bmatrix} 0 \\ 1 \end{bmatrix}, \quad F = \begin{bmatrix} 1 \\ 0 \end{bmatrix}$$

The pseudo-velocity results in:

$$v = D\dot{q} = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} \dot{x} \\ \dot{y} \end{bmatrix} = \dot{x}$$

In this specific case, the pseudo-velocity has an immediate physical meaning: it represents the velocity along the free degree of freedom ($x$).

Applying the projection with $F = [1 \quad 0]^T$ and knowing that the inertia matrix is $B = \text{diag}(m, m)$:

$$B_v = F^T B F = \begin{bmatrix} 1 & 0 \end{bmatrix} \begin{bmatrix} m & 0 \\ 0 & m \end{bmatrix} \begin{bmatrix} 1 \\ 0 \end{bmatrix} = m$$

The one-dimensional reduced dynamic equation simply results in:

$$m \ddot{x} = u_1$$

While the constraint force component is uniquely obtained as $\lambda = -f_y$. The results agree perfectly with elementary dynamic analysis.

---

### Control Decoupling and Feedback Linearization

The expression of the reduced model and the explicit formulation of Lagrange multipliers $\lambda$ suggest a control design technique based on **Feedback Linearization** (*Feedback Linearization*).

By appropriately designing the control input $u$, it is possible to completely decouple the problem into two independent sub-problems:
1. **Motion Control** (*Motion Control*): management of the trajectory along the free degrees of freedom (tangential space to the constraint).
2. **Force Control** (*Force Control*): modulation of the force exerted perpendicularly against the environment.

$$\begin{cases} \dot{v} = a_v \quad \text{(Double integrator for motion)} \\ \lambda = f_\lambda \quad \text{(Algebraic/dynamic force control)} \end{cases}$$

#### Real Application Examples

*   **Writing with a Pen:**
    *   The action $u_1$ modulates the sliding velocity of the pen tip along the paper surface.
    *   The action $u_2$ modulates the vertical pressure force applied by the tip on the paper to ensure ink release without breaking the lead.

*   **Machining / Material Removal (*Material Removal*):**
    *   The action $u_1$ controls the feed rate of the tool along the workpiece profile.
    *   The action $u_2$ regulates the normal contact force required to penetrate the material according to the tool's technological law.

---

### Extension to Compliant Contacts (Overview)

In the previous discussions, infinite rigidity was assumed for both the robot structure and the surrounding environment (*Infinitely Rigid Assumption*).

In real-world applications, however, elastic deformations occur. To model such dynamics, the theory is extended by introducing **compliance** components (*Compliance Modeling*), represented mathematically as elastic elements (springs and dampers) positioned at the contact interface. This extended approach enables the design of compliant control schemes (*Compliant Control*) that are stable even in the presence of structural or environmental deformations.

---

> [!NOTE]
> ### Exam Notes and Instructor Announcements
> - **Homework Deadline**: The last homework has a strict deadline of June 25th (before the start of the exams) and homework submission is optional.
> - **Topics NOT required for the exam**: The discussion and theoretical derivation on constrained dynamics (augmented Lagrangian and Lagrange multipliers) is not part of the exam topics.