# 3D Pose Estimation and the Perspective-n-Point (PnP) Problem in Visual Control

## Educational Overview

In this lecture, the problem of three-dimensional pose estimation (*Pose Estimation*) of an object relative to a camera mounted on a robotic manipulator in an *eye-in-hand* configuration (camera rigidly attached to the end-effector) is addressed. The primary objective is to show how visual information extracted from 2D images can be used to reconstruct the 3D spatial structure, enabling the transition from joint-level control (*Joint Level Control*) to Cartesian-space control (*Cartesian Level Control*).

The main topics covered include:
* **Kinematic Relations and Pose Constraints**: Demonstration of how, assuming both the camera rigidly attached to the end-effector and the object static relative to the base frame (*Base Frame*), determining the relative pose between camera and object allows directly obtaining the pose of the end-effector relative to the robot base.
* **Formulation of the Perspective-n-Point (PnP) Problem**: Analytical modeling of the $PnP$ problem, whose goal is to estimate the homogeneous transformation matrix $T_O^C$ (comprising the rotation $R_O^C$ and the translation $t_O^C$) from the known 3D coordinates of $n$ points in the object frame and their corresponding normalized image coordinates (*Normalized Image Coordinates*).
* **Geometric Conditions and Multiplicity of Solutions**: Analysis of the impact of the spatial arrangement of points on the number of solutions to the $PnP$ problem (e.g., the $P3P$ case with 3 non-collinear points which admits 4 solutions, and the uniqueness conditions for 4 or more coplanar points).
* **Introduction to the Feature Jacobian**: Introduction to the concept of interaction matrix or Feature Jacobian (*Feature Jacobian / Interaction Matrix*), a tool that links the camera velocity in Cartesian space to the time variations of points in the image.

---

### PnP Problem Formulation and Pose Estimation

#### Contextualization: Pose Estimation for Visual Control

Let us recall the general framework: we have a robotic manipulator on which a camera is mounted in an *eye-in-hand* configuration (i.e., rigidly attached to the end-effector). During motion, the camera continuously acquires images of the surrounding environment. The goal is to use exclusively this visual information to guide the robot from an initial position to a desired final configuration.

A first strategy consists in using the images to reconstruct the relative **Pose Estimation** (*Pose Estimation*) between the end-effector and the robot base. 

The kinematic chain of the system is based on several fundamental geometric relationships:
1. **Camera - End-Effector**: The camera is rigidly fixed to the end-effector. Therefore, the relative pose between the camera frame and the end-effector frame is constant and known a priori.
2. **Object - Base**: The observed object and the robot base are both stationary in the environment. Consequently, the relative pose between the object frame and the base frame is constant and known.
3. **Camera - Object**: This is the unknown variable that varies with the robot's motion. 

If we are able to estimate the relative pose between the camera and the observed object, we can directly reconstruct the pose of the end-effector relative to the base through simple compositions of homogeneous transformations.

---

#### Mathematical Formulation of the Problem

Consider an object in the environment on which $n$ points of interest (*feature points*) are identified. 

The information available to us is:
* **Known 3D coordinates**: The coordinates of the points with respect to the object reference frame $\mathbf{r}_i^O = [x_i^O, y_i^O, z_i^O]^T$ (with $i = 1, \dots, n$), known a priori from the geometry of the object.
* **Measured 2D coordinates**: The positions of the same points measured on the camera image plane in pixel coordinates, from which we obtain the **Normalized Coordinates** (*Normalized Coordinates*) by inverting the matrix of **Intrinsic Parameters** (*Intrinsic Parameters*) $\mathbf{K}$:

$$\tilde{\mathbf{s}}_i = \mathbf{K}^{-1} \begin{bmatrix} u_i \\ v_i \\ 1 \end{bmatrix}$$

The projective relationship linking the 3D object points to their projections on the image plane in **Homogeneous Coordinates** (*Homogeneous Coordinates*) is given by:

$$\lambda_i \tilde{\mathbf{s}}_i = \mathbf{\Pi} \mathbf{T}_O^C \tilde{\mathbf{r}}_i^O$$

Where:
* $\tilde{\mathbf{s}}_i = [u_i, v_i, 1]^T$ is the homogeneous normalized coordinate vector of the $i$-th point in the image.
* $\tilde{\mathbf{r}}_i^O = [x_i^O, y_i^O, z_i^O, 1]^T$ is the homogeneous representation of the $i$-th point in the object frame.
* $\lambda_i$ is an unknown scale factor (depth of the point).
* $\mathbf{\Pi} = \begin{bmatrix} \mathbf{I}_{3\times3} & \mathbf{0}_{3\times1} \end{bmatrix}$ is the canonical projection matrix.
* $\mathbf{T}_O^C \in SE(3)$ is the unknown homogeneous transformation matrix describing the pose of the object frame relative to the camera frame:

$$\mathbf{T}_O^C = \begin{bmatrix} \mathbf{R}_O^C & \mathbf{t}_O^C \\ \mathbf{0}_{1\times3} & 1 \end{bmatrix}$$

The algebraic objective consists in obtaining the elements of matrix $\mathbf{T}_O^C$ (composed of the rotation matrix $\mathbf{R}_O^C \in SO(3)$ and translation vector $\mathbf{t}_O^C \in \mathbb{R}^3$) starting from a set of $n$ projection equations.

---

#### The Perspective-n-Point (PnP) Problem

> **KEY CONCEPT: The Perspective-n-Point (PnP) Problem**
> The problem of pose estimation from $n$ correspondences between 3D points and their respective 2D projections is known in the literature as the **Perspective-n-Point (PnP) Problem**. The uniqueness and number of admissible solutions depend strictly on the number of points $n$ and on the geometric configuration of the points themselves.

The solutions to the PnP problem vary according to the following geometric conditions:

* **$n = 3$ points (non-collinear P3P)**: The problem admits up to **4 mathematically distinct solutions**.
* **$n = 4$ or $n = 5$ points (generic non-collinear)**: Typically at least **2 solutions** are obtained.
* **$n = 4$ coplanar points (with non-collinearity constraint)**: If all 4 points lie on the same plane and no triplet of points is collinear (i.e., no group of 3 points lies on the same line), **the solution is unique**.
* **$n \ge 6$ points (non-coplanar)**: With at least 6 points in generic non-coplanar spatial configuration, **the solution is unique** and can be obtained analytically.

---

#### Analytical Simplification: Coplanarity Assumption ($z = 0$)

To derive an elegant analytical solution, we will focus on the case of **4 coplanar points**. 

Without loss of generality, we can place the object reference frame $\mathcal{F}_O$ with its origin on the plane containing the points and the $Z_O$ axis orthogonal to it. Consequently, the third coordinate of all object points vanishes:

$$z_i^O = 0, \quad \forall i = 1, \dots, 4$$

The position vectors of the points in the object frame thus reduce to:

$$\tilde{\mathbf{r}}_i^O = \begin{bmatrix} x_i^O \\ y_i^O \\ 0 \\ 1 \end{bmatrix}$$

*Note*: If the points did not originally lie on the $z=0$ plane, it would simply be a matter of applying a prior rigid transformation (roto-translation) to the object reference frame to fall exactly into this simplified algebraic model. This assumption significantly simplifies the analytical development of the subsequent steps.

---

### Analytical Solution and Linearization of the PnP Problem

---

#### 1. Problem Formulation and Planar Simplification

To analytically solve the *Perspective-n-Point* (PnP) problem, the primary objective is to convert a set of non-linear projection relations into a linear system of equations in classical form:

$$A x = 0$$

Indeed, linear algebra provides us with extremely efficient tools to solve systems of this type.

##### Use of the Skew-Symmetric Matrix
The first step consists in pre-multiplying the geometric relations by a *Skew-Symmetric Matrix*, denoted by $[s]_\times$ or $S$, constructed from a three-dimensional vector. 

Recall that for a generic vector $a = [a_x, a_y, a_z]^T \in \mathbb{R}^3$, the corresponding skew-symmetric matrix $[a]_\times \in \mathbb{R}^{3 \times 3}$ is defined as:

$$[a]_\times = \begin{bmatrix} 0 & -a_z & a_y \\ a_z & 0 & -a_x \\ -a_y & a_x & 0 \end{bmatrix}$$

A fundamental property exploited in this step is the effect of the cross product of a vector with itself: for any vector $\tilde{s}_i$, the cross product is zero, i.e.:

$$[\tilde{s}_i]_\times \tilde{s}_i = 0$$

This identity allows zeroing out and significantly simplifying the terms inside the equations.

##### Planarity Assumption for Model Points
We assume that all reference points belonging to the model lie on a plane. Without loss of generality, we can set the third coordinate of each point equal to zero ($r_{i,z} = 0$). 

In homogeneous coordinates, the position vector of the $i$-th point becomes:

$$\tilde{r}_i = \begin{bmatrix} r_{i,x} \\ r_{i,y} \\ 0 \\ 1 \end{bmatrix}$$

Multiplying this vector by the transformation matrix (composed of the rotation matrix $R = [r_1 \quad r_2 \quad r_3]$ and the translation vector $t$), the third column of the rotation matrix $r_3$ is canceled out by the value $r_{i,z} = 0$.

We thus obtain a reduced matrix $H \in \mathbb{R}^{3 \times 3}$, known in Computer Vision as the *Planar Homography Matrix*:

$$H = \begin{bmatrix} r_1 & r_2 & t \end{bmatrix}$$

where:
* $r_1 \in \mathbb{R}^3$ is the first column of the rotation matrix;
* $r_2 \in \mathbb{R}^3$ is the second column of the rotation matrix;
* $t \in \mathbb{R}^3$ is the translation vector $o_C$.

The points on the plane can thus be expressed in a reduced $2\text{D}$ homogeneous coordinate system:

$$\tilde{r}_i^{2D} = \begin{bmatrix} r_{i,x} \\ r_{i,y} \\ 1 \end{bmatrix}$$

> **Note:** The temporary loss of the third column $r_3$ is not a problem: since $R$ is an orthogonal rotation matrix, the third column can be recovered later via the cross product of the first two: $r_3 = r_1 \times r_2$.

---

#### 2. Linearization via Vectorization and Kronecker Product

To transform the matrix relationship into a linear problem $A x = 0$, we use the vectorization operator and the Kronecker product.

##### Vectorization Operator
The *Vectorization Operator (Vectorization Map)*, denoted by $\text{vec}(A)$, takes a matrix $A \in \mathbb{R}^{m \times n}$ and converts it into a column vector by stacking its columns one underneath the other:

$$\text{if } A = \begin{bmatrix} a_1 & a_2 & \dots & a_n \end{bmatrix} \implies \text{vec}(A) = \begin{bmatrix} a_1 \\ a_2 \\ \vdots \\ a_n \end{bmatrix} \in \mathbb{R}^{mn \times 1}$$

##### Kronecker Product
Given two matrices $A \in \mathbb{R}^{m \times n}$ and $B \in \mathbb{R}^{p \times q}$, the *Kronecker Product* $A \otimes B \in \mathbb{R}^{mp \times nq}$ is a block matrix defined as:

$$A \otimes B = \begin{bmatrix} a_{11} B & a_{12} B & \dots & a_{1n} B \\ a_{21} B & a_{22} B & \dots & a_{2n} B \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} B & a_{m2} B & \dots & a_{mn} B \end{bmatrix}$$

##### Fundamental Property of Vectorization
We exploit the following algebraic identity for the product of three matrices $A$, $B$, and $C$:

$$\text{vec}(A B C) = (C^T \otimes A) \, \text{vec}(B)$$

*Note: Step integrated with didactic clarity.*

In our problem, the equation for the $i$-th point is expressed by:

$$S_i H \tilde{r}_i = 0$$

Applying the vectorization operator to both sides and setting $A = S_i$, $B = H$, and $C = \tilde{r}_i$ (where $\tilde{r}_i$ is treated as a matrix with a single column):

$$\text{vec}(S_i H \tilde{r}_i) = (\tilde{r}_i^T \otimes S_i) \, \text{vec}(H) = 0$$

We define:
1. The vector of unknowns $h \in \mathbb{R}^{9 \times 1}$ as the vectorization of the homography matrix $H$:
   $$h = \text{vec}(H) = \begin{bmatrix} r_1 \\ r_2 \\ t \end{bmatrix}$$
2. The matrix of known coefficients for a single point $A_i \in \mathbb{R}^{3 \times 9}$:
   $$A_i = \tilde{r}_i^T \otimes S_i = \begin{bmatrix} r_{i,x} S_i & r_{i,y} S_i & S_i \end{bmatrix}$$

The algebraic equation for a single point $i$ thus becomes:

$$A_i h = 0$$

---

#### 3. Construction of the Complete System and Rank Analysis

With a single point $i$, we obtain a matrix $A_i$ of size $3 \times 9$. However, the skew-symmetric matrix $S_i$ has rank equal to 2 ($\text{rank}(S_i) = 2$). Consequently, the rank of each block is also:

$$\text{rank}(A_i) = 2$$

A single point therefore provides only 2 linearly independent equations for 9 unknowns.

```
+--------------------------------------------------------------------------+
|                            KEY CONCEPT                                   |
| To solve the system for the 9 unknowns contained in the vector h,        |
| it is necessary to consider a set of at least 4 points (P4P Problem).    |
+--------------------------------------------------------------------------+
```

By stacking the equations for 4 distinct points ($i = 1, 2, 3, 4$), the overall linear system $A h = 0$ is constructed:

$$A = \begin{bmatrix} A_1 \\ A_2 \\ A_3 \\ A_4 \end{bmatrix} \in \mathbb{R}^{12 \times 9}$$

##### Rank Analysis of the System
* Each of the 4 points contributes a block of rank 2, bringing the theoretical maximum rank of matrix $A$ to $4 \times 2 = 8$.
* **Non-collinearity assumption:** For the 4 blocks to be mutually independent and guarantee $\text{rank}(A) = 8$, **no triplet of the 4 points must be aligned (collinear)**.

If the non-collinearity condition is satisfied:

$$\text{rank}(A) = 8$$

Since the number of unknowns is 9 and the rank is 8, linear algebra theory guarantees the existence of a 1-dimensional nullspace (*Nullspace*). The solution $h$ is determined up to an unknown scale factor $c \in \mathbb{R}$:

$$h = c \cdot h_0$$

where $h_0$ is any non-trivial solution in the nullspace of $A$.

---

#### 4. Scale Recovery and Rotation Reconstruction

To determine the correct value of the scale factor $c$, we apply the geometric constraints inherent to a rotation matrix.

##### Determination of the Scale Factor
Knowing that the column vectors $r_1$ and $r_2$ belong to a rotation matrix $R \in SO(3)$, their Euclidean norm must be unity:

$$\|r_1\|_2 = 1 \quad \text{and} \quad \|r_2\|_2 = 1$$

Having extracted the estimated vectors $\hat{r}_1$ and $\hat{r}_2$ from the solution vector $h_0$, the scale factor $c$ is calculated as:

$$c = \frac{1}{\|\hat{r}_1\|_2}$$

Multiplying the entire vector $h_0$ by $c$, we obtain the correctly scaled rotation vectors $r_1, r_2$ and the translation vector $t$.

##### Full Reconstruction of the Rotation Matrix
With the first two columns $r_1$ and $r_2$ known and properly scaled, the third column of the rotation matrix $R$ is computed via the cross product:

$$r_3 = r_1 \times r_2$$

Reassembling the complete rotation matrix:

$$R = \begin{bmatrix} r_1 & r_2 & r_3 \end{bmatrix}$$

---

#### 5. Computational Advantages of the Analytical Solution

The described analytical approach presents significant operational advantages:

* **Closed-form:** Does not require iterative optimization algorithms or initial guesses (*initial guess*).
* **Real-Time Efficiency:** Solving a $12 \times 9$ linear system via SVD (*Singular Value Decomposition*) or eigenvalue computation is a computationally lightweight and deterministic operation.
* **Applications:** It is ideal for high-performance robotic systems and for real-time pose estimation at high frame rates (*high frame-rate*).

---

### Handling Noise and Outliers in Computer Vision

In a laboratory environment or a structured industrial context, it is common and realistic to assume that the exact position of certain points in space is known, often identified via markers (*markers*). In unstructured environments (such as a drone flying outdoors), this assumption no longer holds and more complex methods must be used. However, exploiting the geometric structure of the problem allows elegant solutions to be obtained using techniques such as the **Direct Linear Transformation** (*Direct Linear Transformation - DLT*).

#### Standard Noise Model vs. Computer Vision

In measurement engineering and signal processing, a single measurement $y$ of an unknown quantity $x$ is typically modeled with **Additive Gaussian Noise** (*Additive Gaussian Noise*):

$$y = x + n, \quad n \sim \mathcal{N}(0, \sigma^2)$$

To reduce the variance of the estimate of the unknown $x$, the classic strategy consists in taking $N$ independent measurements $y_i$ and calculating their arithmetic mean:

$$\bar{x} = \frac{1}{N} \sum_{i=1}^{N} y_i$$

By the Central Limit Theorem, as $N$ approaches infinity ($N \to \infty$), the sample mean $\bar{x}$ converges exactly to the true value $x$.

#### The Outlier Problem in Computer Vision

In computer vision (*Computer Vision*), the purely Gaussian model fails due to the presence of **outliers** (*outliers*). An outlier can result from incorrect point correspondence (mismatching) or reflections, and does not follow any regular statistical distribution.

To handle this situation in practice, a two-stage approach is used:
1. **Outlier identification and filtering:** The points are analyzed to discard those that deviate significantly from the assumed geometric model.
2. **Least Squares Estimation (*Least Squares*):** On the subset of inliers/valid points (affected by residual noise reasonably assimilable to Gaussian noise), the least squares algorithm is applied to estimate the transformation matrix or parameter vector $h$.

---

### Reconstruction of the Rotation Matrix and Projection onto $SO(3)$

When applying the DLT method with real noisy data, we obtain an estimated vector $\hat{h}$ from which we extract the three candidate columns $R_1, R_2, R_3$ for the rotation matrix. 

> **KEY CONCEPT**
> Since the vector $\hat{h}$ is the result of an approximate estimation (due to the finite number of points and residual noise), the matrix constructed by side-by-side arrangement of the three column vectors $\tilde{R} = \begin{bmatrix} R_1 & R_2 & R_3 \end{bmatrix}$ **is NOT a true rotation matrix**. In particular, its column vectors will not be perfectly orthogonal to each other and its determinant may differ from $+1$.

#### Projection into the Space of Rotation Matrices $SO(3)$

To restore rigid geometric properties, a **projection** operation of the estimated matrix $\tilde{R}$ onto the special orthogonal group $SO(3)$ (the space of true rotation matrices) must be performed.

Mathematically, one seeks the true rotation matrix $R \in SO(3)$ that minimizes the distance from the non-orthogonal matrix $\tilde{R}$. Matrix distance is defined using the **Frobenius Norm** (*Frobenius Norm*):

$$\hat{R} = \arg\min_{R \in SO(3)} \| \tilde{R} - R \|_F$$

where for a generic matrix $A$, $\|A\|_F = \sqrt{\sum_{i}\sum_{j} a_{ij}^2}$.

This projection guarantees that the final matrix $\hat{R}$ respects the orthonormality constraints:

$$\hat{R}^T \hat{R} = I \quad \text{and} \quad \det(\hat{R}) = +1$$

---

### Frame Transformation Chains and Computation of Relative Poses

Once the rotation matrix is estimated and projected, and the translation vector is extracted, we have the **Homogeneous Transformation Matrix** (*Homogeneous Transformation Matrix*) between the camera and the object, indicated as $T_O^C$ (pose of object $O$ relative to camera $C$).

The ultimate goal of the robotic system is to determine the kinematic relationships among all frame reference systems (*frame*) involved:
* **Base ($B$):** Fixed reference frame of the robot.
* **Camera ($C$):** Reference frame of the camera.
* **End-Effector ($E$):** Reference frame of the robot tool.
* **Object ($O$):** Reference frame of the object to be manipulated.

```
      [ Base (B) ]
        /      \
       /        \
[ End-Effector (E) ] -- (Constant) -- [ Camera (C) ]
                                           |
                                 (DLT Reconstructed)
                                           |
                                     [ Object (O) ]
```

#### Frame Transformation Algebra

To derive the unknown relative positions, matrix transformation composition via multiplication and inversion is exploited.

##### 1. Computation of Object Pose relative to Base ($T_O^B$)
If the camera pose relative to the base $T_C^B$ is known, the object pose relative to the base is given by:

$$T_O^B = T_C^B \cdot T_O^C$$

Conversely, if the relationship between base and object $T_O^B$ is known, the position of the camera relative to the base $T_C^B$ can be derived:

$$T_C^B = T_O^B \cdot T_C^O = T_O^B \cdot (T_O^C)^{-1}$$

*Note: Step integrated with didactic clarity.*

##### 2. Computation of End-Effector Pose relative to Base ($T_E^B$)
In an *eye-in-hand* configuration (camera mounted on the end-effector), the transformation between the end-effector and the camera $T_C^E$ is fixed and known a priori via calibration.

To find the transformation between the object and the end-effector $T_O^E$:

$$T_O^E = T_C^E \cdot T_O^C$$

Consequently, by inverting the appropriate kinematic chains, it is guaranteed that the robot control system knows at every instant the exact position of object $O$ relative to base $B$ or end-effector $E$, enabling docking or tracking operations (*tracking*).

---

### Control in the Image Plane and the Image Jacobian

The objective is to guide a manipulator from its initial position to a final position by operating directly on the **Image Plane** (*Image Plane*). Instead of reconstructing the relative pose in 3D space to reuse traditional controllers, the visual information from the camera is exploited to evaluate trajectory and error directly on the 2D plane. 

Since the camera projects a three-dimensional world onto a two-dimensional plane, it is necessary to verify whether this information is sufficient to solve the control problem. To do so, a new type of Jacobian must be introduced.

The conventional Jacobian (geometric or analytical) maps joint-space velocities ($\dot{q}$) to end-effector velocities in Cartesian space. The **Image Jacobian** (*Image Jacobian*), on the other hand, is a kinematic relation between two different quantities:
1. The velocity at which the **Feature Vector** (*Feature Vector*) changes in the image plane ($\dot{s}$ or $\dot{x}$).
2. The relative velocity between the manipulator (or the camera attached to it) and the object.

#### Definition of Relative Velocities between Camera and Object

Consider an object in relative motion with respect to the camera. Such relative motion exists both if the object actually moves in space, and if the object is stationary and the camera is in motion.

We define the following three reference frames (*Frame*):
* Base frame: $\{b\}$ (or subscript-free)
* Camera frame: $\{c\}$
* Object frame: $\{o\}$

The vector $o_{c,o}^c$ connects the origin of the camera frame with the origin of the object frame, expressed in the camera reference frame:

$$o_{c,o}^c = R_c^T (o_o - o_c)$$

where $o_o$ and $o_c$ are the absolute positions of the object and camera relative to the base frame, while $R_c$ is the rotation matrix from the camera to the base frame ($R_c = R_c^b$).

The relative velocity $v_{c,o}^c$ consists of a linear part and an angular part:

$$v_{c,o}^c = \begin{bmatrix} \dot{o}_{c,o}^c \\ R_c^T (\omega_o - \omega_c) \end{bmatrix}$$

- **Linear part:** It is the time derivative of the position vector expressed in the camera frame $\dot{o}_{c,o}^c$.
- **Angular part:** Since angular velocity vectors belong to a vector space and can be algebraically added, the variation of relative orientation is given by the difference $(\omega_o - \omega_c)$ expressed relative to the base frame. To project it into the camera frame, it is pre-multiplied by the transposed rotation matrix $R_c^T$.

---

### Mathematical Derivation of the Jacobian and Interaction Matrix

The points of interest of the object are projected onto the image plane providing normalized coordinates, collected in the feature vector $s \in \mathbb{R}^k$. If $n$ points on the image plane are considered, each characterized by $2$ normalized coordinates, the dimension of the vector will be $k = 2n$.

The **Image Jacobian** $J_s$ (or $J_x$) is defined by the relation:

$$\dot{s} = J_s \, v_{c,o}^c$$

With $\dot{s} \in \mathbb{R}^k$ and $v_{c,o}^c \in \mathbb{R}^6$ (composed of 3 linear and 3 angular components), matrix $J_s$ has dimensions $k \times 6$.

> **KEY CONCEPT**
> The matrix $J_s$ depends locally on both the current feature vector $s$ and the relative pose (in particular the point depth) between the camera and the object.

```
 Relative Velocity (Object/Camera)          Image Jacobian (J_s)             Feature Variation (Image Plane)
           v_{c,o}^c               ------------------------------------>                  \dot{s}
        (dimension 6x1)                     (matrix k x 6)                            (dimension k x 1)
```

#### From Relative Velocity to Absolute Velocities (Matrix $\Gamma$)

For control purposes, it is useful to express the derivative of the feature vector $\dot{s}$ as a function of the **absolute velocities** of the camera and object, expressed however in the camera frame.

We define the absolute velocity vector of the camera expressed in the camera frame $v_{c,c}^c$ and that of the object $v_{o,c}^c$:

$$v_{c,c}^c = \begin{bmatrix} R_c^T \dot{o}_c \\ R_c^T \omega_c \end{bmatrix}, \quad v_{o,c}^c = \begin{bmatrix} R_c^T \dot{o}_o \\ R_c^T \omega_o \end{bmatrix}$$

We expand the time derivative of the vector $o_{c,o}^c$:

$$\dot{o}_{c,o}^c = \frac{d}{dt} \left( R_c^T (o_o - o_c) \right) = R_c^T (\dot{o}_o - \dot{o}_c) + \dot{R}_c^T (o_o - o_c)$$

Exploiting the properties of rotation matrices and the skew-symmetric operator (*Skew-symmetric matrix*) $S(\cdot)$, it can be shown that the term due to the derivative of the rotation matrix satisfies the following relationship:

$$\dot{R}_c^T (o_o - o_c) = S(o_{c,o}^c) R_c^T \omega_c$$

*(Note: Step integrated with didactic clarity to reconstruct the underlying algebraic steps).*

Rearranging the terms in matrix form, we obtain the relationship linking relative velocity $v_{c,o}^c$ to absolute velocities $v_{o,c}^c$ and $v_{c,c}^c$:

$$v_{c,o}^c = v_{o,c}^c + \Gamma \, v_{c,c}^c$$

where $\Gamma$ is a $6 \times 6$ block matrix defined as:

$$\Gamma = \begin{bmatrix} -I_3 & S(o_{c,o}^c) \\ 0_3 & -I_3 \end{bmatrix}$$

where $I_3$ is the $3 \times 3$ identity matrix, $0_3$ is the $3 \times 3$ zero matrix, and $S(o_{c,o}^c)$ is the skew-symmetric matrix associated with the position vector $o_{c,o}^c$.

#### Interaction Matrix ($L_s$) and Stationary Object Case

Substituting the expression of $v_{c,o}^c$ into the fundamental relationship of the image Jacobian, we obtain:

$$\dot{s} = J_s \left( v_{o,c}^c + \Gamma v_{c,c}^c \right) = J_s v_{o,c}^c + L_s v_{c,c}^c$$

where the **Interaction Matrix** (*Interaction Matrix*) $L_s$ is defined as the product:

$$L_s = J_s \, \Gamma$$

> **KEY CONCEPT**
> In most practical engineering applications (e.g., **Image-Based Visual Servoing** / *Image-Based Visual Servoing - IBVS*), the object to be tracked or grasped is stationary in space ($v_{o,c}^c = 0$). In this realistic case, the relationship simplifies drastically:
> $$\dot{s} = L_s \, v_{c,c}^c$$
> The interaction matrix $L_s$ directly maps the absolute velocity of the camera (expressed in its own frame) to the rate of change of features on the image plane.

#### Invertibility Property of Matrix $\Gamma$

In practice, it is often easier to compute the interaction matrix $L_s$ and then invert the relation to obtain the image Jacobian $J_s = L_s \Gamma^{-1}$. 

The matrix $\Gamma$ is **always invertible**. Being a $6 \times 6$ block triangular matrix:

$$\Gamma = \begin{bmatrix} -I_3 & S(o_{c,o}^c) \\ 0_3 & -I_3 \end{bmatrix}$$

Its $6$ eigenvalues are all exactly equal to $-1$. Since there are no zero eigenvalues, $\Gamma$ has full rank and its invertibility is guaranteed in any spatial configuration.