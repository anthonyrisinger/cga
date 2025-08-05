### Appendix B: Essential Formulas with Geometric Motivation

Geometric algebra's formulas encode geometric truths. This reference presents each operation's mathematical form alongside its geometric necessity—why the formula must take its specific shape given the constraints of space and transformation.

#### Foundation: Products and Their Geometric Roles

##### The Geometric Product

For vectors $\mathbf{a}, \mathbf{b}$ separated by angle $\theta$:

$$\mathbf{ab} = \mathbf{a} \cdot \mathbf{b} + \mathbf{a} \wedge \mathbf{b} = |\mathbf{a}||\mathbf{b}|(\cos\theta + \sin\theta \mathbf{B})$$

where $\mathbf{B}$ is the unit bivector in their plane.

**Geometric Necessity**: Information preservation demands both alignment (scalar) and orientation (bivector). The symmetric part $\frac{1}{2}(\mathbf{ab} + \mathbf{ba}) = \mathbf{a} \cdot \mathbf{b}$ captures projection; the antisymmetric part $\frac{1}{2}(\mathbf{ab} - \mathbf{ba}) = \mathbf{a} \wedge \mathbf{b}$ captures circulation.

**Coordinate Expansion** (orthonormal basis):
$$\mathbf{e}_i \mathbf{e}_j = \begin{cases}
1 & i = j \\
\mathbf{e}_i \wedge \mathbf{e}_j = \mathbf{e}_{ij} & i < j \\
-\mathbf{e}_{ji} & i > j
\end{cases}$$

##### Inner Product: Dimensional Reduction

For blades $A_r$ (grade $r$), $B_s$ (grade $s$):

$$A_r \cdot B_s = \langle A_r B_s \rangle_{|r-s|}$$

**Geometric Necessity**: The inner product finds the largest common subspace. A plane (grade 2) intersected with a line (grade 1) yields their common line (grade 1). The grade difference $|r-s|$ emerges because we remove the $s$-dimensional probe from the $r$-dimensional space.

**Explicit Cases**:
- $\mathbf{a} \cdot \mathbf{b} = |\mathbf{a}||\mathbf{b}|\cos\theta$ (scalar projection)
- $\mathbf{a} \cdot (\mathbf{b} \wedge \mathbf{c}) = (\mathbf{a} \cdot \mathbf{b})\mathbf{c} - (\mathbf{a} \cdot \mathbf{c})\mathbf{b}$ (vector in plane)
- $(A \wedge B) \cdot C = A \cdot (B \cdot C)$ (associativity across grades)

##### Outer Product: Dimensional Extension

$$A_r \wedge B_s = \langle A_r B_s \rangle_{r+s}$$

**Geometric Necessity**: Independent directions combine additively. Sweeping a line (1D) along an independent line creates a plane (2D). The wedge vanishes when inputs share directions—parallel lines span no area.

**Basis Expansion**:
$$(\mathbf{e}_{i_1} \wedge \cdots \wedge \mathbf{e}_{i_r}) \wedge (\mathbf{e}_{j_1} \wedge \cdots \wedge \mathbf{e}_{j_s}) = \begin{cases}
\pm \mathbf{e}_{i_1 \cdots i_r j_1 \cdots j_s} & \text{indices distinct} \\
0 & \text{otherwise}
\end{cases}$$

Sign determined by permutation parity to reach ascending order.

#### Transformations via Sandwich Products

##### Reflection: The Generator of All Isometries

For unit normal $\mathbf{n}$:

$$\mathbf{v}' = -\mathbf{n}\mathbf{v}\mathbf{n}$$

**Geometric Necessity**: Reflection reverses the perpendicular component while preserving the parallel:
- Parallel: $\mathbf{n}(\mathbf{v} \cdot \mathbf{n})\mathbf{n} = (\mathbf{v} \cdot \mathbf{n})\mathbf{n}^2 = (\mathbf{v} \cdot \mathbf{n})\mathbf{n}$
- Perpendicular: $\mathbf{n}\mathbf{v}_\perp = -\mathbf{v}_\perp\mathbf{n}$ (anticommutation)

The sandwich ensures the result remains a vector. For non-unit $\mathbf{m}$: multiply by $1/|\mathbf{m}|^2$.

##### Rotation: Composed Reflections

Rotor for angle $\theta$ in plane $B$ (unit bivector):

$$R = \exp\left(-\frac{\theta}{2}B\right) = \cos\frac{\theta}{2} - \sin\frac{\theta}{2}B$$

Applied via: $\mathbf{v}' = R\mathbf{v}\tilde{R}$ where $\tilde{R} = \cos\frac{\theta}{2} + \sin\frac{\theta}{2}B$

**Geometric Necessity**: Two reflections compose into rotation. Reflecting in planes separated by angle $\alpha$ rotates by $2\alpha$. The half-angle appears because the sandwich applies the rotor twice. The exponential form emerges because bivectors satisfy $B^2 = -|B|^2$, mimicking imaginary units.

**Composition**: $R_{total} = R_2 R_1$ (right-to-left order)

#### Conformal Model: Where Translation Becomes Rotation

##### The Null Embedding

Euclidean point $\mathbf{p}$ embeds as:

$$P = \mathbf{p} + \frac{1}{2}|\mathbf{p}|^2 n_\infty + n_0$$

where null basis vectors satisfy:
- $n_0^2 = n_\infty^2 = 0$
- $n_0 \cdot n_\infty = -1$
- $n_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+)$, $n_\infty = \mathbf{e}_- + \mathbf{e}_+$

**Geometric Necessity**: Points map to null rays (light-like) in 5D. The quadratic term creates a paraboloid—distance from origin becomes height. This particular embedding ensures:

$$P_1 \cdot P_2 = -\frac{1}{2}|\mathbf{p}_1 - \mathbf{p}_2|^2$$

Distance emerges from the inner product's mixed-signature geometry.

##### Translation as Null Rotation

Translator for displacement $\mathbf{t}$:

$$T = 1 - \frac{1}{2}\mathbf{t} \cdot n_\infty$$

**Geometric Necessity**: Translation has no fixed point in Euclidean space but fixes the point at infinity in conformal space. The translator rotates in the null plane containing $n_\infty$. Series truncates because $(n_\infty)^2 = 0$.

##### Motor: Screw Motion Unified

For rotation $R$ and translation $\mathbf{t}$:

$$M = TR = \left(1 - \frac{1}{2}\mathbf{t} \cdot n_\infty\right)R$$

General screw with axis $L$, angle $\theta$, pitch $d$:

$$M = \exp\left(-\frac{1}{2}(\theta L^* + d \cdot n_\infty)\right)$$

**Geometric Necessity**: Chasles' theorem—every rigid motion is a screw. Motors make this algebraic. The dual line $L^*$ ensures the exponent has consistent grade.

#### Meet and Join: Intersection Through Duality

##### Universal Meet Formula

$$A \vee B = ((AI^{-1}) \wedge (BI^{-1}))I$$

where $I$ is the pseudoscalar.

**Geometric Necessity**: In projective geometry, points on a line dualize to lines through a point. Intersection and span are dual operations. The algorithm exploits this:
1. Map objects to orthogonal complements (dual)
2. Find their span (wedge)
3. Map back (dual again)

This works because: intersection$(A,B)$ = dual(span(dual$(A)$, dual$(B)$))

**Grade Arithmetic**:
$$\text{grade}(A \vee B) = \text{grade}(A) + \text{grade}(B) - n$$

Lower grades signal degeneracy (parallel lines, tangent spheres).

#### Exponentials: From Algebra to Group

##### Bivector Exponential

For unit bivector $B$ (where $B^2 = -1$):

$$\exp(\theta B) = \cos\theta + \sin\theta B$$

General bivector with $B^2 = -\phi^2$:

$$\exp(B) = \cos\phi + \frac{\sin\phi}{\phi}B$$

**Geometric Necessity**: Bivectors generate rotations continuously. The exponential map connects the Lie algebra (bivectors) to the Lie group (rotors). The formula mirrors Euler's because bivectors square to negative scalars.

##### Logarithm Extraction

For rotor $R = a + bB$ with $|R| = 1$:

$$\log(R) = \arccos(a) \frac{B}{|B|}$$

Numerically stable form:
$$\theta = \begin{cases}
2\arctan(|B|/a) & a > 0 \\
\pi - 2\arctan(|B|/|a|) & a < 0
\end{cases}$$

#### Critical Identities

| Operation | Identity | Geometric Meaning |
|-----------|----------|-------------------|
| **Reversion** | $\widetilde{AB} = \tilde{B}\tilde{A}$ | Reverses order like transpose |
| **Involution** | $\hat{A} = \sum_{k}(-1)^k\langle A\rangle_k$ | Flips odd grades |
| **Hodge dual** | $A^* = AI^{-1}$ | Maps to orthogonal complement |
| **Double dual** | $(A^*)^* = (-1)^{k(n-k)+s}A$ | Returns original (up to sign) |
| **Jacobi** | $[[A,B],C] + [[B,C],A] + [[C,A],B] = 0$ | Lie algebra structure |
| **Bianchi** | $\nabla \wedge F = 0$ | Electromagnetic conservation |

where $s$ is metric signature, $k$ is grade, $n$ is dimension.

#### Numerical Characteristics

| Configuration | Condition Number | Traditional | GA Improvement | Critical Range |
|---------------|------------------|-------------|----------------|----------------|
| **Near-parallel planes** | $O(1/\sin^2\theta)$ | $10^{16}$ at $\theta=10^{-8}$ | $O(1/\sin\theta)$: $10^8$ | $\theta < 10^{-6}$ |
| **Near-singular rotation** | $O(1/\|1-\mathbf{q}\|)$ | Gimbal lock | Graceful degradation | $\|\mathbf{q}\| \approx 1$ |
| **Distant points (CGA)** | $O(\|\mathbf{p}\|^2)$ | Linear | Quadratic growth | $\|\mathbf{p}\| > 10^3$ |
| **Long kinematic chains** | $O(n)$ matrix drift | $10^{-6}$ per 1000 ops | $10^{-12}$ per 10000 ops | $n > 7$ joints |

#### Conversion Formulas

| From | To | Formula | Notes |
|------|-----|---------|-------|
| **Quaternion** $q = w + xi + yj + zk$ | **Rotor** | $R = w - x\mathbf{e}_{23} - y\mathbf{e}_{31} - z\mathbf{e}_{12}$ | Sign convention |
| **Axis-angle** $(\mathbf{n}, \theta)$ | **Rotor** | $R = \cos(\theta/2) - \sin(\theta/2)\mathbf{n}^*$ | $\mathbf{n}^* = $ dual axis |
| **Matrix** $[R\|\mathbf{t}]$ | **Motor** | $M = (1 - \frac{1}{2}\mathbf{t} \cdot n_\infty) R_{quat}$ | Via quaternion |
| **Euler** $(\phi, \theta, \psi)$ | **Rotor** | $R = R_z(\psi)R_y(\theta)R_x(\phi)$ | Order-dependent |
| **Plücker** $(m, \mathbf{d})$ | **Line** | $L = \mathbf{d} + m \cdot n_\infty$ | PGA representation |

#### Implementation Constants

| Operation | FLOPs (Naive) | FLOPs (Sparse) | Memory | Cache Lines | Chapter |
|-----------|--------------|----------------|---------|-------------|---------|
| **Geometric product (3D)** | 27 | 15 | 32B | 1 | 1, 6 |
| **Rotor sandwich (3D)** | 54 | 28 | 48B | 1 | 2, 7 |
| **Motor sandwich (CGA)** | 220 | 54 | 256B | 4 | 7, 14 |
| **Meet operation (PGA)** | 160 | 40 | 128B | 2 | 9 |
| **Grade projection** | 32n | 8k | 32nB | n/2 | 4, 10 |
| **Dual operation** | 64 | 16 | 256B | 4 | 9 |

where $n$ = space dimension, $k$ = number of grades present.

#### Validity Domains and Failure Modes

| Formula | Valid Domain | Failure Mode | Mitigation | Reference |
|---------|--------------|--------------|------------|-----------|
| **$\log(R)$** | $\|R\| = 1 \pm 10^{-10}$ | Branch cut at $R = -1$ | Use atan2 form | App. F |
| **$P^{-1}$** | $P \cdot n_\infty \neq 0$ | Infinite points | Check before invert | Ch. 7 |
| **$(AI^{-1})$** | $A$ not parallel to $I$ | Dual undefined | Use Moore-Penrose | Ch. 8 |
| **$\exp(B)$** | $\|B\| < \pi$ | Aliasing beyond $\pi$ | Wrap angles | Ch. 6 |
| **Meet $(A \vee B)$** | Non-degenerate | Grade collapse | Check result grade | Ch. 3, 8 |
