# Interaction Matrix and Image Jacobian for a Point Feature

## Teaching Overview

In this lecture, the study of the **Interaction Matrix** ($L_s$), also known as the **Image Jacobian**, is explored in depth. It is fundamental in visual servoing to relate the time variations of feature characteristics in the image to the kinematics of the system.

The key points covered include:
* **Fundamental Kinematic Relationship**: Formalization of the mathematical link between the time derivative of the feature vector (Feature Vector Velocity $\dot{s}$) and the absolute and relative velocity between the object and the camera. The common operational hypothesis where the object is stationary (object velocity equal to zero) is highlighted.
* **Properties of the Matrix $\Gamma$**: Analysis of the block structure of the matrix $\Gamma$ (containing skew-symmetric matrices). It is shown that the matrix has 6 eigenvalues all equal to $-1$, always guaranteeing its invertibility and facilitating the indirect calculation of the Jacobian.
* **Derivation for a Point Feature**: Case study concerning a single point in 3D space projected onto the image plane.
* **Dimensional Analysis and Coordinate Transformations**: Definition of the vector of normalized image coordinates of dimension 2, operating with a velocity vector in 3D space of dimension 6, from which an interaction matrix $L_s \in \mathbb{R}^{2 \times 6}$ is derived. Finally, coordinate transformations from the Base Frame to the Camera Frame are reviewed.

*(Note: At the beginning of the lecture, brief mention was made of the suspension of upcoming lectures and the completion of the course by the end of May).*

---

### Introduction and Organizational Information

The end of the course lectures is scheduled by the end of the month of May. 

In this lecture, we continue the analysis of the **Interaction Jacobian Matrix** and its algebraic and geometric properties.

---

### Kinematics of the Visual Feature and Relative Velocity

The main objective is to mathematically describe how the projection onto the image plane of some points of interest related to an object varies over time.

* **Feature Vector** $\mathbf{s}$: contains the image plane coordinates of the characteristic points extracted from the object.
* **Feature Vector Velocity** $\dot{\mathbf{s}}$: represents the time variation of the feature positions on the image plane.

When the camera moves relative to the object (or vice versa), a relative velocity is generated between the object and the camera frame. Since a kinematic relationship exists between the variation of the features and the velocities involved, the fundamental mathematical tool to describe it is a **Jacobian**.

#### From Absolute Velocities to the Interaction Matrix

In a general context, we define the absolute velocities expressed in the camera frame:
* $\mathbf{v}_c$: absolute velocity of the camera;
* $\mathbf{v}_o$: absolute velocity of the object.

Through appropriate algebraic steps, it is possible to relate the time variation of the features $\dot{\mathbf{s}}$ to the absolute velocities via a new matrix structure:

$$\dot{\mathbf{s}} = N_s \cdot \begin{bmatrix} \mathbf{v}_o \\ \mathbf{v}_c \end{bmatrix}$$

Where the matrix $N_s$ is directly connected to the Image Jacobian $L_s$ via a block matrix of kinematic transformation $\Gamma$:

$$N_s = L_s \cdot \Gamma$$

#### Structure of the Matrix $\Gamma$

The matrix $\Gamma$ is a block matrix incorporating a **Skew-Symmetric Matrix** constructed from the vector $\mathbf{o}_c$:

$$\mathbf{o}_c$$

The vector $\mathbf{o}_c$ represents the vector joining the origin of the camera frame to the origin of the object frame, expressed in camera coordinates.

> **Key Concept: Invertibility of $\Gamma$**
> 
> The matrix $\Gamma$ has all $6$ eigenvalues equal to $-1$. Since no eigenvalue is zero ($\lambda_i \neq 0$), the matrix $\Gamma$ is **always strictly invertible** ($\det(\Gamma) \neq 0$).
>
> This result is of fundamental practical importance: in many problems, it is extremely simpler to directly compute the matrix $N_s$ rather than the Image Jacobian $L_s$. Thanks to the full invertibility of $\Gamma$, it is always possible to obtain $L_s$ by inversion:
> 
> $$L_s = N_s \cdot \Gamma^{-1}$$

---

### Special Case: Stationary Object

In most practical visual servoing applications (e.g., eye-in-hand configuration with a fixed target in the environment), the observed object is stationary with respect to the world.

Assuming therefore that the absolute velocity of the object is zero:

$$\mathbf{v}_o = \mathbf{0}$$

The general relationship simplifies considerably, reducing to a direct and exclusive link between the feature velocity on the image plane and the absolute velocity of the camera $\mathbf{v}_c$ alone:

$$\dot{\mathbf{s}} = L_s \cdot \mathbf{v}_c$$

In this context, the Interaction Matrix acts in all respects as a system Jacobian, mapping directly the Cartesian velocity space of the camera into the velocity space of the visual features.

---

3. Analytical Calculation of the Interaction Matrix for Points and Segments

Let us now see how to practically and analytically calculate the **Interaction Matrix** in the fundamental case where the visual feature vector is represented by the coordinates of a single point in space. Understanding this derivation is essential for extending the result to more complex geometries.

---

### 3.1 Point Kinematics and Feature Vector

Consider a fixed point $P$ belonging to the observed object. Its position is described by the vector $\boldsymbol{p}$ with respect to the base frame. 
The camera moves in space: its reference frame originates at point $O_c$.

```
    [Base Frame] 
         |
         +-------------------->  P (Fixed point on the object, p)
         \                    /
          \                  /  r_C/C (Coordinates in camera frame)
           v                v
      [Camera Frame (O_c)]
```

We define the vector $\boldsymbol{r}_{C/C} = [x_c, y_c, z_c]^T$ as the position vector going from the camera origin $O_c$ to point $P$, expressed in the **camera frame coordinates**.

> **Key Concept: Dimensions of Vectors and Matrix**
> * Feature vector $\boldsymbol{s}$: formed by the normalized image coordinates of the point, so $\dim(\boldsymbol{s}) = 2$.
> * Spatial velocity vector of the camera $\boldsymbol{\nu}_c$: composed of 3 translational velocities and 3 angular velocities, so $\dim(\boldsymbol{\nu}_c) = 6$.
> * Interaction Matrix $L_s$: relates $\dot{\boldsymbol{s}}$ to $\boldsymbol{\nu}_c$ ($\dot{\boldsymbol{s}} = L_s \boldsymbol{\nu}_c$), therefore its dimension must be strictly **$2 \times 6$**.

#### Point Coordinates in the Camera Frame
In the base frame, the relative position is given by:
$$\boldsymbol{r}_C = \boldsymbol{p} - \boldsymbol{o}_c$$

To express this vector relative to the camera frame, we apply the rotation matrix $\boldsymbol{R}_c$ (which describes the orientation of the camera frame with respect to the base):
$$\boldsymbol{r}_{C/C} = \boldsymbol{R}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$$

where we use the transpose $\boldsymbol{R}_c^T$ since $\boldsymbol{R}_c^{-1} = \boldsymbol{R}_c^T$ for orthogonal rotation matrices.

#### Perspective Transformation and Normalized Coordinates
Exploiting the **perspective transformation** model, we set the focal length $f = 1$ to obtain the normalized image coordinates that constitute our feature vector $\boldsymbol{s}$:

$$\boldsymbol{s} = \begin{bmatrix} X \\ Y \end{bmatrix} = \begin{bmatrix} \frac{x_c}{z_c} \\ \frac{y_c}{z_c} \end{bmatrix}$$

---

### 3.2 Derivation of the Interaction Matrix $L_s$

To find the relationship $\dot{\boldsymbol{s}} = L_s \boldsymbol{\nu}_c$, we calculate the time derivative of $\boldsymbol{s}$ applying the **chain rule**:

$$\dot{\boldsymbol{s}} = \frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} \cdot \dot{\boldsymbol{r}}_{C/C}$$

This formulation decomposes the problem into two blocks:
1. The **spatial Jacobian** $\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}}$ (dimension $2 \times 3$).
2. The **time velocity of the point in the camera frame** $\dot{\boldsymbol{r}}_{C/C}$ (dimension $3 \times 1$).

---

#### First Block: Calculation of the Partial Jacobian $\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}}$

We calculate the partial derivatives of coordinates $X$ and $Y$ with respect to components $x_c, y_c, z_c$:

$$\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} = \begin{bmatrix} \frac{\partial X}{\partial x_c} & \frac{\partial X}{\partial y_c} & \frac{\partial X}{\partial z_c} \\[6pt] \frac{\partial Y}{\partial x_c} & \frac{\partial Y}{\partial y_c} & \frac{\partial Y}{\partial z_c} \end{bmatrix}$$

Carrying out the analytical calculations:
* For $X = \frac{x_c}{z_c}$:
  $$\frac{\partial X}{\partial x_c} = \frac{1}{z_c}, \quad \frac{\partial X}{\partial y_c} = 0, \quad \frac{\partial X}{\partial z_c} = -\frac{x_c}{z_c^2} = -\frac{X}{z_c}$$

* For $Y = \frac{y_c}{z_c}$:
  $$\frac{\partial Y}{\partial x_c} = 0, \quad \frac{\partial Y}{\partial y_c} = \frac{1}{z_c}, \quad \frac{\partial Y}{\partial z_c} = -\frac{y_c}{z_c^2} = -\frac{Y}{z_c}$$

Substituting the relationships $X = \frac{x_c}{z_c}$ and $Y = \frac{y_c}{z_c}$, the matrix assumes an elegant form:

$$\frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} = \begin{bmatrix} \frac{1}{z_c} & 0 & -\frac{X}{z_c} \\ 0 & \frac{1}{z_c} & -\frac{Y}{z_c} \end{bmatrix} = \frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix}$$

---

#### Second Block: Calculation of $\dot{\boldsymbol{r}}_{C/C}$

We now differentiate $\boldsymbol{r}_{C/C} = \boldsymbol{R}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$ with respect to time. Recalling that point $P$ is fixed in the world ($\dot{\boldsymbol{p}} = \mathbf{0}$):

$$\dot{\boldsymbol{r}}_{C/C} = \boldsymbol{R}_c^T (-\dot{\boldsymbol{o}}_c) + \dot{\boldsymbol{R}}_c^T (\boldsymbol{p} - \boldsymbol{o}_c)$$

We note that:
* $\boldsymbol{R}_c^T \dot{\boldsymbol{o}}_c = \boldsymbol{v}_c$ represents the translational velocity of the camera expressed in the camera reference frame.
* The derivative of the rotation matrix introduces the angular velocity vector $\boldsymbol{\omega}_c$ via the skew-symmetric operator $[\boldsymbol{r}_{C/C}]_\times$:

$$\dot{\boldsymbol{r}}_{C/C} = -\boldsymbol{v}_c - [\boldsymbol{\omega}_c]_\times \boldsymbol{r}_{C/C} = -\boldsymbol{v}_c + [\boldsymbol{r}_{C/C}]_\times \boldsymbol{\omega}_c$$

In compact matrix form with respect to the camera velocity vector $\boldsymbol{\nu}_c = \begin{bmatrix} \boldsymbol{v}_c \\ \boldsymbol{\omega}_c \end{bmatrix}$:

$$\dot{\boldsymbol{r}}_{C/C} = \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix} \begin{bmatrix} \boldsymbol{v}_c \\ \boldsymbol{\omega}_c \end{bmatrix}$$

where $\boldsymbol{I}_3$ is the $3 \times 3$ identity matrix and $[\boldsymbol{r}_{C/C}]_\times$ is the skew-symmetric matrix associated with $\boldsymbol{r}_{C/C} = [x_c, y_c, z_c]^T$:

$$[\boldsymbol{r}_{C/C}]_\times = \begin{bmatrix} 0 & -z_c & y_c \\ z_c & 0 & -x_c \\ -y_c & x_c & 0 \end{bmatrix}$$

---

#### Assembly and Final Result

Multiplying the two blocks obtained:

$$\dot{\boldsymbol{s}} = \frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix} \boldsymbol{\nu}_c$$

*Note: Step integrated with teaching clarity.* We perform the explicit matrix product $L_s = \frac{\partial \boldsymbol{s}}{\partial \boldsymbol{r}_{C/C}} \begin{bmatrix} -\boldsymbol{I}_3 & [\boldsymbol{r}_{C/C}]_\times \end{bmatrix}$:

1. **Translational Part (first 3 columns):**
   $$\frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} -1 & 0 & 0 \\ 0 & -1 & 0 \\ 0 & 0 & -1 \end{bmatrix} = \begin{bmatrix} -\frac{1}{z_c} & 0 & \frac{X}{z_c} \\ 0 & -\frac{1}{z_c} & \frac{Y}{z_c} \end{bmatrix}$$

2. **Rotational Part (last 3 columns):**
   $$\frac{1}{z_c} \begin{bmatrix} 1 & 0 & -X \\ 0 & 1 & -Y \end{bmatrix} \begin{bmatrix} 0 & -z_c & y_c \\ z_c & 0 & -x_c \\ -y_c & x_c & 0 \end{bmatrix} = \begin{bmatrix} \frac{x_c Y}{z_c} & -1 - \frac{x_c X}{z_c} & \frac{y_c}{z_c} \\ 1 + \frac{y_c Y}{z_c} & -\frac{x_c Y}{z_c} & -\frac{x_c}{z_c} \end{bmatrix} = \begin{bmatrix} XY & -(1+X^2) & Y \\ 1+Y^2 & -XY & -X \end{bmatrix}$$

Combining the results, we obtain the **analytical Interaction Matrix for a single point**:

$$L_s = \begin{bmatrix} -\frac{1}{z_c} & 0 & \frac{X}{z_c} & XY & -(1+X^2) & Y \\[6pt] 0 & -\frac{1}{z_c} & \frac{Y}{z_c} & 1+Y^2 & -XY & -X \end{bmatrix}$$

> **Key Concept: The Role of Depth $z_c$**
> Look closely at the matrix $L_s$: the terms measurable on the image plane are the coordinates $X$ and $Y$. However, to compute the interaction matrix it is also necessary to know $z_c$, which is the **3D depth** of the point relative to the camera. 
> This information cannot be read directly from a single pixel, but can be estimated through analytical pose estimation algorithms (e.g., PnP algorithms based on 4 non-collinear coplanar points).

---

### 3.3 Extensions: Systems of Points and Aligned Segments

#### Case 1: Set of $N$ Points
If the object is described by a set of $N$ physical points, the feature vector $\boldsymbol{s}$ is the stacking of the coordinates of each point:

$$\boldsymbol{s} = \begin{bmatrix} \boldsymbol{s}_1 \\ \boldsymbol{s}_2 \\ \vdots \\ \boldsymbol{s}_N \end{bmatrix} \in \mathbb{R}^{2N}$$

The overall interaction matrix is obtained simply by stacking the interaction matrices of the individual points:

$$L_s = \begin{bmatrix} L_{s1} \\ L_{s2} \\ \vdots \\ L_{sN} \end{bmatrix} \in \mathbb{R}^{2N \times 6}$$

---

#### Case 2: Aligned Segment
An alternative approach consists in characterizing a richer geometry, such as a line segment in space.

```
       P1 (x1, y1)
        \
         \     P_bar (X_bar, Y_bar) -> Centroid
          \
           P2 (x2, y2)
```

We can represent the segment by defining a 4-element feature vector:

$$\boldsymbol{s}_{seg} = \begin{bmatrix} \bar{X} \\ \bar{Y} \\ L \\ \alpha \end{bmatrix}$$

where:
* $\bar{X} = \frac{X_1 + X_2}{2}$ and $\bar{Y} = \frac{Y_1 + Y_2}{2}$ are the coordinates of the midpoint.
* $L = \sqrt{\Delta X^2 + \Delta Y^2}$ is the total length of the segment on the image plane (with $\Delta X = X_1 - X_2$ and $\Delta Y = Y_1 - Y_2$).
* $\alpha = \arctan\left(\frac{\Delta Y}{\Delta X}\right)$ is the slope of the segment.

To compute the interaction matrix of the segment $L_{s\_seg}$, the chain rule is applied again considering that $\boldsymbol{s}_{seg}$ depends on the positions of the two endpoints $\boldsymbol{s}_1 = [X_1, Y_1]^T$ and $\boldsymbol{s}_2 = [X_2, Y_2]^T$:

$$\dot{\boldsymbol{s}}_{seg} = \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} \dot{\boldsymbol{s}}_1 + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} \dot{\boldsymbol{s}}_2 = \left( \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} L_{s1} + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} L_{s2} \right) \boldsymbol{\nu}_c$$

Therefore:
$$L_{s\_seg} = \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_1} L_{s1} + \frac{\partial \boldsymbol{s}_{seg}}{\partial \boldsymbol{s}_2} L_{s2}$$

This shows the extreme flexibility of the analytical calculation: knowing the interaction matrix of the base point, it is possible to derive the Jacobian for any more complex geometric primitive.

---

### Introduction to Image-Based Visual Servoing (IBVS)

In **Image-Based Visual Servoing (IBVS)**, the fundamental idea is to directly use information extracted from the camera image plane to generate control commands for the robot, without going through a full three-dimensional reconstruction of the scene in Cartesian space.

#### Advantages and Disadvantages of the IBVS Approach

**Advantages:**
* **Keeping the Object in the Field of View (FOV):** 
  If we design a controller in the traditional **Operational Space** (looking only at the 3D position of the end-effector), the planned trajectory could cause the object to leave the camera's field of view.
  > **Key Concept:** By designing the controller directly on the image plane, we can impose explicit constraints on the feature coordinates to ensure that the object always remains inside the FOV throughout the entire motion.

**Disadvantages:**
* **Lack of Control over Kinematic Singularities:**
  By working only in the image space, one does not have direct perception of the robot's joint configuration. Consequently, the path generated in image space could push the robot toward singular configurations or outside joint limits.

> **Practical context note:** In real robotics, **hybrid control schemes** are often used, combining joint/operational space measurements with camera data to combine the advantages of both worlds. Nevertheless, it is fundamental to first understand the pure theory of IBVS.

---

### Definition of the Control Problem and Scenario Setup

Consider a **Point-to-Point Control** problem: we want to guide the end-effector (and consequently the camera attached to it) from an initial configuration to a desired final configuration.

1. **Desired Configuration:** The presence of a desired final position for the end-effector implies a desired final position for the camera.
2. **Desired Feature Vector ($s_d$):** When the camera is in the desired final pose, the object points projected onto the image plane form a desired visual feature vector $s_d$.
3. **Planning:** Since this is a point-to-point problem, $s_d$ is a **constant** vector in time (it represents a stationary reference that we want to reach).

```
   [ Initial Camera Pose ]  --->  Current Feature Vector: s(t)
                                              |
                                              v (Error Tracking)
                                              |
   [ Desired Final Pose ]   --->  Desired Feature Vector: s_d (Constant)
```

#### How is $s_d$ obtained?
In an offline phase (*learning or teaching phase*), the robot is manually moved to the desired final configuration and the image point coordinates are measured, storing the vector $s_d$.

---

### Depth Estimation and Interaction Matrix

In order to compute the control laws in IBVS, we need to know the **Interaction Matrix** $L_s$. 

As seen previously, the interaction matrix for a single point depends on its coordinates on the image plane and its depth $Z_c$ along the camera's optical axis.

#### Case Study: 4 Coplanar Points
Consider the standard scenario where we track $N = 4$ points on the surface of the object. We assume that:
* The 4 points are coplanar.
* No triplet of points is collinear (non-collinear).

Under these conditions, by solving the **Perspective-n-Point (PnP)** problem, it is possible to uniquely determine the relative pose between the object frame and the camera frame, represented by the transformation matrix $T_c^o$.

Exploiting the knowledge of $T_c^o$, we can analytically derive the depth coordinate $Z_{ci}$ for each of the 4 points ($i = 1, 2, 3, 4$):

$$Z_c = \begin{bmatrix} Z_{c1} \\ Z_{c2} \\ Z_{c3} \\ Z_{c4} \end{bmatrix}$$

#### Composition of the Composite Interaction Matrix
Since for each point $i$ the interaction matrix $L_{si}$ has dimension $2 \times 6$ (relating the camera velocities in 3D space to the variation of 2D image coordinates):

$$\dot{s}_i = L_{si}(s_i, Z_{ci}) \, v_c$$

Stacking the matrices of the 4 points, we obtain the overall interaction matrix $L_s$ of dimension $8 \times 6$:

$$L_s = \begin{bmatrix} L_{s1} \\ L_{s2} \\ L_{s3} \\ L_{s4} \end{bmatrix} \in \mathbb{R}^{8 \times 6}$$

*Note: Step integrated with teaching clarity to show the overall vector structure.*

---

### Definition of the Image Space Error

The goal of IBVS control is to cancel the difference between the current visual configuration and the desired one.

We define the **image space error vector** $e(t)$ as:

$$e(t) = s_d - s(t)$$

Where:
* $s_d \in \mathbb{R}^{2N}$ is the desired feature vector (constant).
* $s(t) \in \mathbb{R}^{2N}$ is the current feature vector measured at each time instant $t$.

For a system with $N = 4$ points, $e(t)$, $s_d$, and $s(t)$ are all vectors of dimension $8 \times 1$. 

> **Key Concept:** The primary goal of the control algorithm will be to design a camera velocity law $v_c$ such that the error asymptotically converges to zero:
> $$\lim_{t \to \infty} e(t) = 0 \implies \lim_{t \to \infty} s(t) = s_d$$

---

### PD Control with Gravity Compensation in Image Space

#### Formulation of the Candidate Lyapunov Function

We want to design a Proportional-Derivative (PD) controller with gravity compensation directly in **Image Space**. 

Recall the diagonal approach used in joint space: we defined a Lyapunov function equal to the sum of the kinetic energy and a quadratic term depending on the joint error. Not having the joint error available, we imitate this structure by defining a candidate Lyapunov function $V(q, \dot{q})$ composed of the manipulator kinetic energy and a quadratic term based on the visual feature error $e_s$:

$$V(q, \dot{q}) = \frac{1}{2} \dot{q}^T B(q) \dot{q} + \frac{1}{2} e_s^T K_p^s e_s$$

where:
* $B(q)$ is the robot inertia matrix (positive definite).
* $e_s = s_d - s$ represents the error on the image plane between the desired feature vector $s_d$ (constant, so $\dot{s}_d = 0$) and the measured one $s$.
* $K_p^s$ is a positive definite proportional gain matrix ($K_p^s > 0$).

Properties of $V(q, \dot{q})$:
* $V(q, \dot{q}) > 0$ for every non-zero state.
* $V(q, \dot{q}) = 0$ if and only if both the image space error and the joint velocity are zero ($e_s = 0$ and $\dot{q} = 0$).

The minimum point of this function corresponds exactly to the target configuration: we reach the destination with zero velocity ($\dot{q} = 0$) and remain there.

---

#### Time Derivative and Kinematic Relationships

We calculate the time derivative of $V(q, \dot{q})$:

$$\dot{V}(q, \dot{q}) = \dot{q}^T B(q) \ddot{q} + \frac{1}{2} \dot{q}^T \dot{B}(q) \dot{q} + e_s^T K_p^s \dot{e}_s$$

From the robot dynamics in joint space we know that:

$$B(q)\ddot{q} = u - C(q, \dot{q})\dot{q} - F\dot{q} - g(q)$$

Substituting $B(q)\ddot{q}$ into the derivative expression and exploiting the skew-symmetry property whereby the matrix $(\dot{B}(q) - 2C(q, \dot{q}))$ is skew-symmetric (i.e., $\dot{q}^T (\dot{B}(q) - 2C(q, \dot{q})) \dot{q} = 0$), we obtain:

$$\dot{V}(q, \dot{q}) = \dot{q}^T \left( u - g(q) - F\dot{q} \right) + e_s^T K_p^s \dot{e}_s$$

> *Note: Step integrated with teaching clarity to make explicit the link between $\dot{e}_s$ and $\dot{q}$.*

To proceed, we must express the error derivative $\dot{e}_s = -\dot{s}$ as a function of the joint velocity $\dot{q}$. 

1. Knowing that the object is stationary, the variation of the visual features is linked to the camera absolute velocity $v_c$ via the **Interaction Matrix** $L_s$:
   $$\dot{s} = L_s v_c$$

2. Since the camera is attached to the end-effector, its velocity $v_c$ (composed of linear and angular velocity) is directly linked to $\dot{q}$ via the robot geometric Jacobian $J_q(q)$, taking into account the rotation $R_c$ between the camera frame and the base frame:
   $$v_c = \begin{bmatrix} R_c^T & 0 \\ 0 & R_c^T \end{bmatrix} J_q(q) \dot{q}$$

3. Combining these relationships, we define the **Visual or Feature Jacobian** $J_m(q, s)$:
   $$J_m(q, s) = L_s(s, Z_c) \begin{bmatrix} R_c^T & 0 \\ 0 & R_c^T \end{bmatrix} J_q(q)$$

We thus obtain the fundamental kinematic relationship:

$$\dot{e}_s = -\dot{s} = -J_m(q, s) \dot{q}$$

Substituting $\dot{e}_s$ into the expression of the Lyapunov derivative:

$$\dot{V}(q, \dot{q}) = \dot{q}^T \left( u - g(q) - F\dot{q} - J_m^T(q, s) K_p^s e_s \right)$$

---

#### Synthesis of the Control Law

To ensure that $\dot{V}(q, \dot{q}) \le 0$, we can choose the control input $u$ by equating the terms in parentheses and adding a derivative action to damp and speed up the transient response:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d \dot{q}$$

with $K_d > 0$. Substituting this control law $u$ into the equation for $\dot{V}$, we obtain:

$$\dot{V}(q, \dot{q}) = -\dot{q}^T F \dot{q} - \dot{q}^T K_d \dot{q} \le 0$$

Since $F > 0$ and $K_d > 0$, the derivative of the Lyapunov function is strictly negative for every $\dot{q} \neq 0$. This guarantees energy dissipation and convergence of the system toward an equilibrium configuration where $\dot{q} = 0$.

---

### Stability Analysis and Equilibrium Points

> **KEY CONCEPT: Presence of Singularities and Full Rank Condition**
> 
> The analysis of the Lyapunov function derivative guarantees that the system converges to an equilibrium state where $\dot{q} = 0$. However, this **does not automatically guarantee** that zero visual error ($e_s = 0$) has been reached.

To analyze the final equilibrium configuration, consider the dynamic model at equilibrium (where $\dot{q} = 0$ and $\ddot{q} = 0$) under the applied control law:

$$0 = u - g(q) \implies 0 = J_m^T(q, s) K_p^s e_s$$

From this relationship we can identify two scenarios:

1. **Ideal Case ($J_m$ Full Rank)**: If the matrix $J_m^T(q, s)$ is full rank, its null space (kernel) contains only the zero vector. Consequently, the only solution to the above equation is:
   $$e_s = 0$$
   In this case, convergence to the desired visual configuration is guaranteed.

2. **Critical Case (Presence of Singularities)**: If the product $K_p^s e_s$ lies within the null space of $J_m^T$ ($\text{null}(J_m^T) \neq \{0\}$), the system can get stuck in a stationary equilibrium point ($\dot{q} = 0$) exhibiting a **non-zero** visual error ($e_s \neq 0$).

---

### Practical Considerations, Trade-offs, and Implementation

#### Model Knowledge and Measurement Requirements

To practically implement the proposed control law:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d \dot{q}$$

it is necessary to have the following information:

* **Gravity Term $g(q)$**: The only part of the robot dynamics that must be known a priori.
* **Robot Jacobian $J_q(q)$**: Obtainable via the direct kinematics of the manipulator.
* **Interaction Matrix $L_s$**: Depends on the normalized image plane coordinates $s$ and the depth $Z_c$ of the image points.
* **3D Reconstruction (Depth $Z_c$)**: Since $L_s$ depends on $Z_c$, depth must be estimated in real time via 3D reconstruction algorithms (analytical or numerical).

```
   [Camera Sensor] ---> Measures e_s and s
                               |
                               v
[3D Reconstruction / Z_c Estimation] + [Robot Kinematics J_q(q)]
                               |
                               v
                     [Computation of J_m(q,s)]
                               |
                               v
  [Control u = g(q) + J_m^T K_p^s e_s - K_d q_dot] ---> [Robot]
```

#### Control Trade-offs

* **Joint Space vs. Image Space**:
  * *Joint Space Control*: Guarantees smooth trajectories and global convergence, but requires full knowledge of the state and does not guarantee that points of interest remain inside the Field of View (FOV).
  * *Image Space Control (IBVS)*: Guarantees that visual features remain well framed within the sensor, but pays the price of potential local minima (related to the rank of $J_m$) and less regular operational space trajectories.

* **Hybrid Solutions**: In industrial contexts, mixed architectures are often used that fuse joint space error measurements with image space measurements, combining the advantages of both solutions.

* **Special Cases (e.g., SCARA Manipulators)**: When the robot operates moving along parallel planes (such as SCARA robots), depth $Z_c$ remains constantly invariant. In these scenarios, the interaction matrix is simplified, eliminating the need for online 3D depth reconstruction.

#### Implementation Variants of Derivative Action

Whenever it is possible to directly measure the velocity of visual features in the image plane $\dot{s}$, a further practical formulation of derivative action is widespread, replacing the term $-K_d \dot{q}$ with a component proportional to $\dot{s}$:

$$u = g(q) + J_m^T(q, s) K_p^s e_s - K_d^s J_m^T(q, s) \dot{s}$$

This technical variant allows acting on damping directly at the image dynamics level.