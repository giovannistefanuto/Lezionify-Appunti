# Camera Modeling and Perspective Projection for Visual Servoing

## Teaching Overview

In this lecture, the geometric and optical modeling of vision sensors is introduced, a fundamental preparatory element for **Visual Control** (*Visual Servoing*) techniques. After a brief contextualization on how visual information is used for estimating the manipulator's 3D pose, the analysis moves to the analytical formalization of the image formation process.

Key concepts covered include:
- **Thin Lens Model**: description of the behavior of light rays passing through the lens, definition of the optical axis (*Optical Axis*), focal length (*Focal Length*), and the fundamental lens equation.
- **Camera Reference Frames**: geometric definition of the frame attached to the camera (*Camera Frame*), positioned at the optical center, and its homogeneous transformations relative to the base frame (*Base Frame*).
- **Image Plane and Perspective Projection**: definition of the planar reference system and derivation of the projection equations from $3D$ spatial coordinates onto the $2D$ image plane using geometric similarity relationships.

---

### Introduction to Visual Servoing and Camera Model

In the context of **Visual Servoing**, there are mainly two methodological approaches:
1. **3D Pose Reconstruction:** Information extracted from the cameras is used to reconstruct the three-dimensional pose of the manipulator relative to the world (or target). Once the 3D pose is estimated, conventional kinematic or dynamic control techniques are applied directly.
2. **Direct Image-Based Control:** Features extracted from the image plane are used to generate the control law without an intermediate explicit 3D reconstruction step.

To fully understand both methods, it is necessary to formalize the mathematical model by which three-dimensional reality is projected onto the two-dimensional plane of the sensor.

#### What is a Camera?
A camera is an optoelectronic device whose fundamental purpose is to measure the intensity of light reflected by objects in the scene. 
* **Pixel:** The basic photosensitive element that converts incident light intensity into an electrical signal/vector.
* **Lens:** Optical element tasked with focusing light rays reflected from the object onto the **Image Plane**, i.e., the physical plane where the pixels are arranged.

---

### The Thin Lens Model

To analyze the basic optical behavior of the camera, the ideal **Thin Lens Model** is adopted.

```
                 Asse Ottico (Zc)
        \               |               /
         \              |              /
          \             |             /
-----------+------------O------------+-----------  Lente
            \           |           /
             \          |          /
              \         |         /
               *--------+--------*
                  Fuoco |  Fuoco
                    λ   |    λ
```

Geometry of the thin lens:
* **Optical Axis:** The axis perpendicular to the lens and passing through its geometric center.
* **Optical Center ($O_c$):** The center of the lens. Light rays that pass exactly through $O_c$ **do not undergo any deflection** and continue straight.
* **Focal Length ($\lambda$ or $f$):** All rays parallel to the optical axis passing through the lens are deflected and converge at a point located on the optical axis at a distance $\lambda$ from the lens, called the **Focal Point**.

#### Fundamental Equation of the Thin Lens
The fundamental equation relating the object's distance from the lens, the image plane distance, and the focal length is defined as:

$$\frac{1}{Z} + \frac{1}{z} = \frac{1}{\lambda}$$

Where:
* $Z$: Physical distance of the real object from the lens (along the optical axis).
* $z$: Distance of the image plane (where the focused image is formed) from the lens.
* $\lambda$: Focal length of the lens.

---

### Reference Frames and Projection Geometry

To analytically express the projection of scene points onto the image, we define the following orthonormal reference frames:

1. **Camera Reference Frame $\{C\}$ ($O_c - X_c Y_c Z_c$):**
   * **Origin ($O_c$):** Coincides with the optical center of the lens.
   * **$Z_c$ Axis:** Oriented along the optical axis, pointing outward toward the scene.
   * **$X_c, Y_c$ Axes:** Lie on the lens plane and form a right-handed frame.

2. **Base Reference Frame $\{B\}$:** The fixed world/robot reference frame. The relationship between a point expressed in base coordinates $P_b$ and in camera coordinates $P_c$ is described by the homogeneous transformation matrix $T_c^b$:

   $$\tilde{P}_c = T_c^b \tilde{P}_b$$

   where $\tilde{P} = [X, Y, Z, 1]^T$ represents the representation in homogeneous coordinates.

3. **Image Plane Reference Frame ($X_f - Y_f$):**
   * Positioned at a distance $f$ (focal length) from the lens along the $Z_c$ optical axis.
   * The origin is located at the intersection of the $Z_c$ optical axis and the image plane.
   * The $X_f$ and $Y_f$ axes are chosen parallel to the $X_c$ and $Y_c$ axes, respectively.

---

### Perspective Projection

Consider a generic point $P$ in 3D space, whose coordinates in the camera system are $P_c = [X_c, Y_c, Z_c]^T$. To determine the coordinates of the corresponding point projected onto the image plane $(x_f, y_f)$, the optical ray passing through $P_c$ and through the optical center $O_c$ is drawn.

Using the similarity of geometric triangles formed by the ray with the optical axis, we obtain the **Perspective Transformation**:

$$x_f = -f \frac{X_c}{Z_c}$$

$$y_f = -f \frac{Y_c}{Z_c}$$

#### The Virtual Image Plane
The minus sign in the previous equations reflects the physical fact that images projected behind the lens are inverted (along both the $X$ and $Y$ axes).

To simplify the mathematical treatment and eliminate the negative sign, the model of the **Virtual Image Plane** is conventionally adopted, assuming that the image plane is located *in front of* the optical center at a distance $+f$ along the $Z_c$ axis.

The perspective projection equations then become:

$$x_f = f \frac{X_c}{Z_c}$$

$$y_f = f \frac{Y_c}{Z_c}$$

---

### Fundamental Properties and Singularities

From the analysis of the perspective projection equations, two fundamental properties for visual control emerge:

> **KEY CONCEPT: Loss of Depth Information**
> The projected coordinates $(x_f, y_f)$ depend solely on the ratio between the transverse coordinates ($X_c, Y_c$) and the depth ($Z_c$). 
> Consequently, all points in 3D space lying along the same line passing through the optical center $O_c$ are projected onto the exact **same point** on the image plane. This phenomenon results in the irreversible loss of information about the third dimension (depth $Z_c$) within a single 2D image.

> **KEY CONCEPT: Singularity of the Perspective Transformation**
> The perspective transformation is undefined for points where $Z_c = 0$. 
> If a point $P$ lies on the $X_c - Y_c$ plane (i.e., lies on the lens plane passing through the origin $O_c$), the ray passing through $P$ and the optical center lies entirely on this plane and is parallel to the image plane. Since no intersection point exists, the perspective transformation becomes mathematically **singular**.

---

### Pixel Coordinates and Intrinsic Parameters

When moving from the theoretical camera model to the real sensor, one must consider that the image is digitized. In a real sensor, the projection surface is discretized into pixels (Pixel Coordinates). Therefore, the coordinates of a point on the image plane are not continuous numbers, but quantized values identified by $(X_i, Y_i)$.

To convert from the continuous image plane coordinates $(x_f, y_f)$ to the discrete pixel coordinates $(X_i, Y_i)$, we introduce two physical factors:
1. **Scale factors ($\alpha_x, \alpha_y$):** Represent the number of pixels per unit length (or the inverse of the physical pixel size) along the $X$ and $Y$ axes.
2. **Offset or Principal Point ($x_0, y_0$):** In a real image, the origin of the pixel coordinate reference system $(0,0)$ is not located at the center of the optical axis, but typically in a corner of the sensor (e.g., top-left or bottom-left). The offset $(x_0, y_0)$ thus represents the position of the optical center expressed in the pixel reference system.

The transformation equations from metric coordinates $(x_f, y_f)$ to pixel coordinates $(X_i, Y_i)$ are:

$$X_i = \alpha_x x_f + x_0$$
$$Y_i = \alpha_y y_f + y_0$$

Recalling the ideal perspective transformation where $x_f = f \frac{X_c}{Z_c}$ and $y_f = f \frac{Y_c}{Z_c}$ (with $f$ focal length, and $P^c = [X_c, Y_c, Z_c]^T$ coordinates of the point in the camera reference system), we can rewrite the pixel coordinates as a non-linear relationship:

$$X_i = \alpha_x f \frac{X_c}{Z_c} + x_0$$
$$Y_i = \alpha_y f \frac{Y_c}{Z_c} + y_0$$

---

### Homogeneous Representation and Linearization of Projection

The presence of the depth $Z_c$ in the denominator makes the perspective projection relationship highly **non-linear**. To simplify analytical calculations, estimations, and control, these equations are converted into linear form using the **Homogeneous Representation**.

We define the homogeneous vector of pixel coordinates as $\tilde{m}_i = [X_i, Y_i, 1]^T$ and the point $P^c$ in homogeneous coordinates as $\bar{P}^c = [X_c, Y_c, Z_c, 1]^T$.

We can express the projection process via matrix multiplication by introducing a scale factor $\lambda$, where **$\lambda = Z_c$** represents the depth coordinate of the point in space with respect to the camera ($Z_c > 0$):

$$\lambda \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \Pi \bar{P}^c$$

Where:
* **$\Sigma$ (Intrinsic Parameters Matrix):** A $3 \times 3$ matrix encapsulating the internal geometry of the camera.
* **$\Pi$ (Canonical Projection Matrix):** A $3 \times 4$ matrix defined as $\Pi = \begin{bmatrix} I_{3 \times 3} & \mathbf{0}_{3 \times 1} \end{bmatrix}$.

The intrinsic parameters matrix $\Sigma$ has the following structure:

$$\Sigma = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix}$$

#### Derivation and Explicit Steps
*Note: Step included for educational clarity.*

We carry out the multiplication $\Sigma \cdot \Pi$:

$$\Sigma \Pi = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} = \begin{bmatrix} \alpha_x f & 0 & x_0 & 0 \\ 0 & \alpha_y f & y_0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix}$$

Multiplying now this $3 \times 4$ matrix by the point $\bar{P}^c = [X_c, Y_c, Z_c, 1]^T$:

$$\begin{bmatrix} \alpha_x f & 0 & x_0 & 0 \\ 0 & \alpha_y f & y_0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} X_c \\ Y_c \\ Z_c \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f X_c + x_0 Z_c \\ \alpha_y f Y_c + y_0 Z_c \\ Z_c \end{bmatrix}$$

Equating to the left-hand side $\lambda [X_i, Y_i, 1]^T$ and setting $\lambda = Z_c$:

$$\begin{bmatrix} Z_c X_i \\ Z_c Y_i \\ Z_c \end{bmatrix} = \begin{bmatrix} \alpha_x f X_c + x_0 Z_c \\ \alpha_y f Y_c + y_0 Z_c \\ Z_c \end{bmatrix}$$

Dividing the first and second rows by $Z_c$, we recover exactly the initial equations:
$$X_i = \alpha_x f \frac{X_c}{Z_c} + x_0$$
$$Y_i = \alpha_y f \frac{Y_c}{Z_c} + y_0$$

This confirms the formal validity of using homogeneous coordinates to linearize the projection model.

---

### The Camera Calibration Matrix

A point $P$ originally described relative to a base reference frame $P^b$ can be transformed into the camera frame $P^c$ via the homogeneous transformation matrix $T_b^c$:

$$\bar{P}^c = T_b^c \bar{P}^b = \begin{bmatrix} R_b^c & t_b^c \\ \mathbf{0}^T & 1 \end{bmatrix} \bar{P}^b$$

Substituting the rigid-body transformation relationship into the projection formula, we obtain the fundamental equation of vision:

$$\lambda \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \Pi T_b^c \bar{P}^b$$

The matrix resulting from the product is commonly defined as the **Camera Calibration Matrix**.

> **KEY CONCEPT: Intrinsic vs. Extrinsic Parameters**
>
> Two distinct types of information are summarized within the Calibration Matrix:
> * **Intrinsic Parameters ($\Sigma$):** Depend solely on the camera hardware (focal length $f$, pixel dimensions $\alpha_x, \alpha_y$, offset $x_0, y_0$). They do not change when the camera moves in space.
> * **Extrinsic Parameters ($T_b^c$):** Identify the relative pose (rotation $R_b^c$ and translation $t_b^c$) of the camera reference frame relative to the world/base reference frame.

---

### Normalized Coordinates

In the development of vision-based control algorithms and analytical analysis, extensive use is made of **Normalized Coordinates**.

#### Definition
Normalized coordinates $(x, y)$ are the coordinates that would be obtained on the image plane if the camera had a **unit focal length ($f = 1$)** and if the sensor introduced no distortions, origin shifts, or scale factors ($\alpha_x = \alpha_y = 1$, $x_0 = y_0 = 0$).

Geometrically, they correspond to the coordinates of the point projected onto a plane located at unit distance ($Z=1$) in front of the camera's optical center:

$$x = \frac{X_c}{Z_c}$$
$$y = \frac{Y_c}{Z_c}$$

In homogeneous coordinates, the normalized coordinate vector is defined as:

$$\tilde{m}_n = \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$

#### Relationship between Pixel Coordinates and Normalized Coordinates
Using the intrinsic parameters matrix $\Sigma$, it is possible to directly link the measured pixel coordinates $(X_i, Y_i)$ to the normalized coordinates $(x, y)$:

$$\begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \Sigma \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}$$

Expanding the product:

$$\begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f & 0 & x_0 \\ 0 & \alpha_y f & y_0 \\ 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \begin{bmatrix} \alpha_x f x + x_0 \\ \alpha_y f y + y_0 \\ 1 \end{bmatrix}$$

#### Practical Use
In a real application, the camera directly measures points on the image in the form of **pixel coordinates** $(X_i, Y_i)$. Knowing the intrinsic parameters of the camera (through a prior calibration process providing the matrix $\Sigma$), we can invert the relationship to compute the corresponding **normalized coordinates**:

$$\begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = \Sigma^{-1} \begin{bmatrix} X_i \\ Y_i \\ 1 \end{bmatrix}$$

This step is fundamental: it allows us to decouple the control algorithm and data processing from the specific physical characteristics of the sensor used, working with purely geometric coordinates.

---

### System Configuration and Visual Servoing Control Approaches

In visual servoing control, the main objective is to guide the end-effector of a manipulator from an initial configuration to a desired final position using exclusively information extracted from vision sensors.

The reference configuration discussed is the **Eye-in-Hand** type (Camera on End-Effector), in which the camera is rigidly mounted on the robot's end-effector.

```
 [ Base Frame {b} ]
        |
        v (Cinematica Robot)
 [ End-Effector {e} ] === (Vincolo Rigido Costante) ===> [ Camera Frame {c} ]
                                                               |
                                                        (Piani di Visione)
                                                               v
                                                      [ Object Frame {o} ]
```

From this rigid arrangement, several fundamental properties derive:
* The camera reference frame $\{c\}$ moves and rotates in a perfectly rigid manner with the end-effector frame $\{e\}$.
* The relative pose between the camera frame and the end-effector frame (expressed by the homogeneous transformation matrix $\mathbf{T}_c^e$) is **constant over time**.
* The axes of the two frames do not necessarily need to be parallel; it is sufficient that their spatial relationship is rigid and known.

To solve the positioning problem via vision, two main philosophies exist:

1. **Position-Based Visual Servoing (PBVS):**
   Uses images acquired by the camera to analytically reconstruct the relative 3D pose between the camera frame and a world or object reference frame. Once the 3D position and orientation in space are estimated, classical kinematic control algorithms are applied.
2. **Image-Based Visual Servoing (IBVS):**
   The control error is computed and eliminated directly on the 2D image plane, without passing through three-dimensional pose reconstruction.

---

### Formulation of the Pose Reconstruction Problem (PBVS)

> **KEY CONCEPT**
> The primary objective of the PBVS approach described analytically is to reconstruct the relative pose between the camera reference frame $\{c\}$ and the reference frame fixed to the object $\{o\}$, relying exclusively on the 2D projections of known points (*features*) on the normalized image plane.

#### Work Scenario Model
Consider the following operational scenario:
* A static (non-moving) object present in the environment.
* On the same object, a set of geometric points of interest (*keypoints* or 3D *features*) are defined and extracted.
* Through point detection algorithms (*Point Detectors* / *Feature Extractors*), the projections of these points onto the image plane are identified.
* The features used by the system are the **normalized coordinates** ($\tilde{x}, \tilde{y}$) of these points on the camera image plane.

#### Reference Frames and Homogeneous Transformation Chain
To formalize the problem, four main coordinate systems are defined:
* **$\{b\}$ - Base Frame:** Fixed reference frame of the robot.
* **$\{o\}$ - Object Frame:** Reference frame attached to the object to be tracked.
* **$\{c\}$ - Camera Frame:** Reference frame moving with the optics.
* **$\{e\}$ - End-Effector Frame:** Reference frame moving with the tool.

Since the object is static in the workspace, the relative pose between the base frame $\{b\}$ and the object frame $\{o\}$, represented by the homogeneous transformation matrix $\mathbf{T}_o^b$, is **constant and known a priori**.

The analytical objective unfolds in the following steps:

1. Estimation of the homogeneous transformation $\mathbf{T}_o^c$ (pose of the object relative to the camera) starting from the normalized coordinates of the points on the image plane.
2. Calculation of the camera pose relative to the base $\mathbf{T}_c^b$ by inverting the kinematic chain:
   $$\mathbf{T}_c^b = \mathbf{T}_o^b \cdot (\mathbf{T}_o^c)^{-1}$$
3. Determination of the end-effector pose relative to the base $\mathbf{T}_e^b$, knowing the constant rigid relationship $\mathbf{T}_c^e$:
   $$\mathbf{T}_e^b = \mathbf{T}_c^b \cdot (\mathbf{T}_c^e)^{-1}$$

Once $\mathbf{T}_e^b$ is obtained at each instant, the trajectory of the end-effector can be controlled using standard kinematic control techniques in 3D operational space.

---

### Definition of the Feature Vector and Normalized Coordinates

To formally define the pose estimation problem from images, it is necessary to introduce a *Feature Vector*, denoted by $s$. This vector gathers the geometric information extracted from the image.

Rather than using the pixel coordinates $(u, v)$ directly, **normalized coordinates** $(x, y)$ on the image plane are used. 

$$\begin{bmatrix} x \\ y \\ 1 \end{bmatrix} = K^{-1} \begin{bmatrix} u \\ v \\ 1 \end{bmatrix}$$

The use of normalized coordinates is a fundamental choice: assuming the *Intrinsic Parameters Matrix* $K$ (or $\Sigma$) is known, it is possible to invert the relationship between pixels and physical coordinates. Using normalized coordinates greatly simplifies the analytical derivation of solutions.

#### Structure of the Feature Vector
* **For a single point $i$**: the feature vector $s_i$ has dimension 2:
  $$s_i = \begin{bmatrix} x_i \\ y_i \end{bmatrix} \in \mathbb{R}^2$$

* **For a set of $n$ points**: the global feature vector $s$ is obtained by stacking the vectors of the individual points. Its dimension $k$ will therefore be $k = 2n$:
  $$s = \begin{bmatrix} s_1 \\ s_2 \\ \vdots \\ s_n \end{bmatrix} = \begin{bmatrix} x_1 \\ y_1 \\ x_2 \\ y_2 \\ \vdots \\ x_n \\ y_n \end{bmatrix} \in \mathbb{R}^{2n}$$

For geometric calculations and perspective projections, it is useful to represent the point in **homogeneous coordinates**, adding a third unit coordinate:

$$\tilde{s}_i = \begin{bmatrix} x_i \\ y_i \\ 1 \end{bmatrix} \in \mathbb{R}^3$$

---

### Reference Frames and Vector Notation

To link the 3D geometry of the object to the camera's image plane, we define three main reference frames:
1. **Object Frame ($F_o$)**: fixed to the object, with origin $O_o$.
2. **Camera Frame ($F_c$)**: fixed at the optical center of the camera, with origin $O_c$.
3. **Base Frame ($F_b$)**: absolute/world reference frame, with origin $O_b$.

The spatial relationship between the object and the camera is expressed by the *Homogeneous Transformation Matrix* $T_o^c \in \mathbb{R}^{4 \times 4}$:

$$T_o^c = \begin{bmatrix} R_o^c & o_{c,o}^c \\ 0_{1 \times 3} & 1 \end{bmatrix}$$

Where:
* $R_o^c \in \mathbb{R}^{3 \times 3}$ is the rotation matrix describing the relative orientation of the object with respect to the camera.
* $o_{c,o}^c \in \mathbb{R}^3$ is the translation vector connecting the camera origin $O_c$ to the object origin $O_o$, expressed with respect to the camera frame.

> **Key Concept: Clarification on Vector Notation**
> 
> A common mistake is evaluating the vector $o_c^c$ as a null vector $(0,0,0)^T$. 
> * $o_c^c$ represents the vector going from the origin of the **Base Frame** ($O_b$) to the origin of the **Camera Frame** ($O_c$), expressed in the coordinates of the camera frame.
> * Similarly, $o_o^c$ is the vector from the origin of the Base Frame ($O_b$) to the origin of the Object ($O_o$), expressed in the camera frame.
> 
> Indeed, by the vector composition property, the following relation holds:
> $$o_{c,o}^c = o_o^c - o_c^c$$

---

### Formulation of the Pose Estimation Problem (PnP)

The fundamental problem consists in determining the homogeneous transformation matrix $T_o^c$ starting from 2D visual measurements and 3D geometric information known a priori.

```
       [ Oggetto 3D ]  ---> Coordinate note a priori: r_{o,i}^o
             |
             v  (Proiezione Prospettica)
       [ Immagine 2D ] ---> Coordinate misurate in real-time: s_i = [x_i, y_i]^T
             |
             v
 [ OBIETTIVO: Reconstruct T_o^c (12 parametri incogniti) ]
```

#### Available Data:
1. **Known 3D coordinates of the object** (*A priori*): We know the 3D position of $n$ points $P_i$ relative to the object frame $F_o$, indicated by the vector $r_{o,i}^o \in \mathbb{R}^3$.
2. **2D coordinates measured in the image** (*Real-time*): Through computer vision algorithms, we measure the normalized coordinates $s_i = [x_i, y_i]^T$ for each of the $n$ points in the image.

#### Counting Unknowns:
The matrix $T_o^c$ contains 12 unknown elements to be calculated (9 for the rotation matrix $R_o^c$ and 3 for the translation $o_{c,o}^c$). Consequently, the information provided by a single point ($2$ scalar equations) is not sufficient to determine the object's pose.

#### Mathematical Projection Model
For a generic $i$-th point, the rigid transformation of point $P_i$ from the object frame to the camera frame is expressed in homogeneous coordinates as:

$$\tilde{r}_{o,i}^c = T_o^c \tilde{r}_{o,i}^o$$

Multiplying by the perspective projection model yields the fundamental relationship linking the measured features in the image with the 3D coordinates of the object:

$$\lambda_i \tilde{s}_i = \begin{bmatrix} I_{3 \times 3} & 0_{3 \times 1} \end{bmatrix} T_o^c \tilde{r}_{o,i}^o$$

*(Note: Step included for educational clarity to explicitly show the canonical perspective projection matrix $\Pi = [I_{3 \times 3} \mid 0_{3 \times 1}]$).*

Where:
* $\tilde{s}_i = [x_i, y_i, 1]^T$ is the homogeneous vector of measured features for point $i$.
* $\lambda_i$ represents the unknown scale factor, corresponding to the depth $z_i^c$ of the point relative to the optical center of the camera.
* $\tilde{r}_{o,i}^o = [x_{o,i}, y_{o,i}, z_{o,i}, 1]^T$ is the known homogeneous position of point $i$ in the object frame.

Stacking these equations for all $n$ available points ($i = 1, \dots, n$), we obtain a system of equations whose solution allows estimating the matrix $T_o^c$.

---

### The Perspective-n-Point (PnP) Problem and the Coplanar Case

Solving the system to calculate $T_o^c$ is known in the literature as the **Perspective-n-Point (PnP)** problem.

The possibility of finding an analytical or numerical solution and its uniqueness depend on the number of points $n$ and their geometric configuration in space (for example, whether the points are collinear or lie on the same plane).

> **Key Concept: Conditions for Unique Analytical Solution (Coplanar P4P)**
> 
> In the analytical case with **$n = 4$ points**, the solution to the PnP problem is **unique** under the following mandatory geometric assumptions:
> 1. All 4 points belong to the **same plane** (coplanar points).
> 2. **No triplet** of points among the 4 considered is collinear (pairwise non-collinearity of any three points).
> 
> If these conditions are met, it is possible to determine the homogeneous transformation matrix $T_o^c$ exactly and analytically.