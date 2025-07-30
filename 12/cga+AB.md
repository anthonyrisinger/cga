### Appendix B: Essential Formulas with Geometric Motivation

This appendix presents GA formulas alongside their geometric meaning, computational cost, and traditional alternatives. Each formula includes victory conditions—when GA's approach provides engineering value.

#### B.1 The Conformal Embedding: Points on a Paraboloid

**Geometric Motivation**: Embedding Euclidean points onto a paraboloid in 5D linearizes distance computation. Imagine lifting each point vertically by half its squared distance from origin—this height encoding enables unified treatment of all isometries.

**The Embedding**:
$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$$

- $\mathbf{p}$: Original Euclidean position (3 floats)
- $\mathbf{n}_0$: Origin of conformal space (null vector)
- $\mathbf{n}_\infty$: Point at infinity (null vector)
- Result: 5D null vector where $P^2 = 0$

**Cost**: 8 FLOPs (3 squares + 2 adds + 1 multiply + 2 basis additions)

**The Null Basis**:
Starting from $\mathbf{e}_+^2 = +1$ and $\mathbf{e}_-^2 = -1$:
$$\mathbf{n}_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+), \quad \mathbf{n}_\infty = \mathbf{e}_- + \mathbf{e}_+$$

Properties ensuring correct distance encoding:
- $\mathbf{n}_0^2 = 0$ (null at origin)
- $\mathbf{n}_\infty^2 = 0$ (null at infinity)
- $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ (normalization)

**Extraction** (Conformal to Euclidean):
$$\mathbf{p} = \frac{P - (P \cdot \mathbf{n}_\infty)\mathbf{n}_0}{-P \cdot \mathbf{n}_\infty}$$

**Cost**: 10 FLOPs versus 0 for direct storage

**Victory Condition**: Use when unified transformation handling (rotation + translation + scaling) saves more than embedding/extraction overhead.

#### B.2 Distance as Inner Product

**Geometric Motivation**: On the conformal paraboloid, the inner product between points encodes their squared Euclidean distance—no square roots needed for distance comparisons.

**Distance Formula**:
$$d^2 = -2P_1 \cdot P_2$$

**Derivation Sketch**: The parabolic lifting ensures:
$$P_1 \cdot P_2 = \mathbf{p}_1 \cdot \mathbf{p}_2 - \frac{1}{2}(\|\mathbf{p}_1\|^2 + \|\mathbf{p}_2\|^2) = -\frac{1}{2}\|\mathbf{p}_1 - \mathbf{p}_2\|^2$$

**Cost Comparison**:
- Traditional: $d^2 = \|\mathbf{p}_1 - \mathbf{p}_2\|^2$ requires 6 FLOPs
- Conformal: $-2P_1 \cdot P_2$ requires 6 FLOPs (5 multiplies + 1 add)
- Equal cost but GA keeps points in unified framework

**Victory Condition**: When distances feed into further GA operations avoiding extraction cost.

#### B.3 Transformations as Versors

**Geometric Motivation**: Every Euclidean transformation (rotation, translation, scaling) becomes a sandwich product with appropriate versor—unifying what traditionally requires different mathematical objects.

##### Reflection
**Traditional**: $\mathbf{v}' = \mathbf{v} - 2(\mathbf{v} \cdot \mathbf{n})\mathbf{n}$ (10 FLOPs)
**GA**: $V' = -\pi V \pi$ where $\pi$ is unit plane (6 FLOPs)

##### Rotation
**Rotor Construction**:
$$R = \exp\left(-\frac{\theta}{2}B\right) = \cos\frac{\theta}{2} - \sin\frac{\theta}{2}B$$

- $B$: Unit bivector (rotation plane)
- Application: $V' = RVR^{-1} = RV\tilde{R}$ for unit rotor

**Cost**: 54 FLOPs versus 15 for 3×3 matrix
**Victory**: When composing many rotations (28 FLOPs per composition vs 64)

##### Translation
**The Translator**:
$$T = 1 - \frac{\mathbf{t} \cdot \mathbf{n}_\infty}{2}$$

Note: $(\mathbf{t} \cdot \mathbf{n}_\infty)^2 = 0$ ensures exponential series terminates

**Cost**: 54 FLOPs versus 3 for vector addition
**Victory**: When translation combines with rotation in single motor

##### Motor (Rotation + Translation)
**Construction**: $M = TR$ (translator times rotor)
**Screw Motion**:
$$M = \exp\left(-\frac{1}{2}(\theta L^* + d\mathbf{n}_\infty)\right)$$

**Cost**: 28 FLOPs to compose versus 64 for 4×4 matrices
**Victory**: Kinematic chains, interpolation, constraint preservation

#### B.4 The Universal Meet Operation

**Geometric Motivation**: Every geometric intersection—line∩plane, sphere∩sphere, plane∩plane—reduces to one formula computing the common subspace.

**Meet Formula**:
$$A \vee B = (A^* \wedge B^*)^*$$

where $*$ denotes dual with respect to pseudoscalar $I_c = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$

**Cost Structure**:
1. First dual: 32 FLOPs
2. Wedge product: 64 FLOPs
3. Second dual: 32 FLOPs
4. Total: ~128 FLOPs

**Traditional Comparison**:
- Line-plane intersection: 10 FLOPs (parametric substitution)
- Plane-plane intersection: 15 FLOPs (cross product method)
- Sphere-sphere intersection: 25 FLOPs (algebraic solution)

**GA Overhead**: 5-12× for universal handling

**Victory Conditions**:
- System handles >5 primitive types
- Robustness near degeneracy critical
- Code simplicity outweighs performance

#### B.5 Geometric Primitives

##### Sphere Representation (IPNS)
$$S = C - \frac{1}{2}r^2\mathbf{n}_\infty$$

- $C$: Conformal center point
- $r$: Radius
- Test point on sphere: $P \cdot S = 0$

**Construction Cost**: 10 FLOPs
**Traditional**: Store (center, radius) as 4 floats

##### Line Through Two Points
$$L = P_1 \wedge P_2 \wedge \mathbf{n}_\infty$$

**Cost**: 30 FLOPs versus 6 values for parametric line

##### Plane Through Three Points
$$\pi = (P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty)^*$$

**Cost**: ~60 FLOPs versus normal computation via cross products (15 FLOPs)

#### B.6 Interpolation and Paths

##### Linear Motor Interpolation
$$M(t) = M_0(M_0^{-1}M_1)^t$$

Implements screw motion between configurations.

**Cost**: 56 FLOPs per evaluation
**Traditional**: Separate position lerp + quaternion slerp requires 40 FLOPs
**Victory**: Natural screw paths, no synchronization issues

##### Logarithm for Interpolation
For motor $M = T(\mathbf{d})R(\theta, B)$:
$$\log(M) = -\frac{1}{2}(\theta B + \mathbf{d} \cdot \mathbf{n}_\infty)$$

Enables smooth interpolation in Lie algebra.

#### B.7 Numerical Stability Formulas

##### Null Cone Projection
For approximately null vector $P'$ with $P'^2 \approx \epsilon$:
$$P = P' - \frac{P'^2}{2(P' \cdot \mathbf{n}_\infty)}\mathbf{n}_\infty$$

Restores exact null constraint.

##### Versor Normalization
For approximate versor $V'$:
$$V = \frac{V'}{\sqrt{|V'\tilde{V'}|}}$$

**Cost**: 8 FLOPs (4 multiply + 1 sqrt + 1 divide + 2 reverse)
**Frequency**: Every 1000+ operations versus every operation for matrices

##### Precision Loss with Distance
Conformal embedding degrades with distance from origin:

| Distance | Quadratic Term | float32 Relative Error |
|----------|----------------|------------------------|
| 10 units | 50 | Negligible |
| 100 units | 5,000 | ~10⁻⁴ |
| 1,000 units | 500,000 | ~10⁻² |
| 10,000 units | 50,000,000 | Catastrophic |

**Mitigation**: Translate coordinate system to keep $\|\mathbf{p}\| < 100$

#### B.8 Special Forms for Efficiency

##### Bivector Exponential (Stable)
For $B^2 = -\alpha^2 < 0$:
$$\exp(B) = \begin{cases}
1 + B + \frac{B^2}{2} + O(B^3) & \text{if } \alpha < 10^{-4} \\
\cos\alpha + \frac{\sin\alpha}{\alpha}B & \text{otherwise}
\end{cases}$$

Avoids precision loss near identity.

##### Rotor from Two Vectors
Given unit vectors $\mathbf{a}, \mathbf{b}$:
$$R = \frac{1 + \mathbf{b}\mathbf{a}}{\|1 + \mathbf{b}\mathbf{a}\|}$$

Rotates $\mathbf{a}$ to $\mathbf{b}$ in their common plane.
**Cost**: 12 FLOPs versus quaternion construction (20+ FLOPs)

Each formula in this appendix emerged from geometric necessity, not algebraic abstraction. Use when problem structure aligns with GA's representation. Otherwise, traditional methods remain optimal.
