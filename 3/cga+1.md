# Notation at a Glance

## Fundamental Elements

The algebra is built upon a basis of vectors that define the properties of the space.

| Symbol | Name | Description | Properties |
| :--- | :--- | :--- | :--- |
| $a, b, c$ | Scalar | A real number, grade-0 element | Commutes with all elements |
| $\mathbf{a}, \mathbf{b}, \mathbf{v}$ | Euclidean Vector | A standard vector in $\mathbb{R}^n$, grade-1 | $\mathbf{v} = \sum_{i=1}^n v_i\mathbf{e}_i$ |
| $\mathbf{e}_i$ | Euclidean Basis Vector | Orthonormal basis vectors of Euclidean space | $\mathbf{e}_i \cdot \mathbf{e}_j = \delta_{ij}$ |
| $\mathbf{n}_0$ | Origin Null Vector | Represents the point at the Euclidean origin | $\mathbf{n}_0^2 = 0$ |
| $\mathbf{n}_\infty$ | Infinity Null Vector | Represents the point at infinity; the conformal point | $\mathbf{n}_\infty^2 = 0$ |
| – | Null Vector Inner Product | Defines the metric of the conformal space | $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ |
| $I_n$ | Euclidean Pseudoscalar | The highest-grade element of $\mathbb{R}^n$; oriented unit volume | $I_n = \mathbf{e}_1 \wedge \mathbf{e}_2 \wedge \cdots \wedge \mathbf{e}_n$; $I_n^2 = (-1)^{n(n-1)/2}$ |
| $I_c$ | Conformal Pseudoscalar | Pseudoscalar for the $(n+2)$-D conformal space, $\mathcal{G}_{n+1,1}$ | $I_c = I_n \wedge \mathbf{n}_\infty \wedge \mathbf{n}_0$ |

## Multivector Structure

A **multivector** is a linear combination of blades of different grades. A **blade** is the outer product of linearly independent vectors.

| Concept | Notation | Description |
| :--- | :--- | :--- |
| General Multivector | $A, B, M$ | A linear combination of basis blades: $A = \sum_k \langle A \rangle_k$ |
| Basis Blade | $\mathbf{e}_{i_1...i_k}$ | The outer product of $k$ distinct basis vectors |
| Grade Projection | $\langle A \rangle_k$ | Extracts the grade-$k$ part of the multivector $A$ |
| Grade Set | $\mathcal{G}(A)$ | The set of all grades $k$ where $\langle A \rangle_k \neq 0$ |

The grade of an element indicates its geometric dimension. In $n$-D Conformal GA, grades range from 0 to $n+2$:

| Grade | Name | Dimension | Geometric Interpretation (Examples in 3D CGA) |
| :--- | :--- | :--- | :--- |
| 0 | Scalar | 1 | Magnitudes, scale factors |
| 1 | Vector | $n+2$ | Points, IPNS hyperspheres, IPNS hyperplanes |
| 2 | Bivector | $\binom{n+2}{2}$ | Rotations; OPNS lines and circles (3D) |
| $k$ | $k$-vector | $\binom{n+2}{k}$ | $k$-dimensional subspaces and their duals |
| $n+1$ | $(n+1)$-vector | $n+2$ | OPNS hyperplanes |
| $n+2$ | Pseudoscalar | 1 | The oriented hypervolume of the entire space |

## Fundamental Operations

These operations define the algebra and its geometric meaning. For vectors $\mathbf{a}$ and $\mathbf{b}$, the geometric product is $\mathbf{ab} = \mathbf{a} \cdot \mathbf{b} + \mathbf{a} \wedge \mathbf{b}$.

| Operation | Symbol | Definition | Grade Effect (for blades A, B) |
| :--- | :--- | :--- | :--- |
| **Geometric Product** | $AB$ | The fundamental, associative, non-commutative product | Mixed grades |
| **Outer Product** | $A \wedge B$ | Antisymmetric product; constructs higher-grade objects (span) | $\text{grade}(A) + \text{grade}(B)$ if linearly independent |
| **Inner Product** | $A \cdot B$ | Symmetric product; projects one object onto another | $\|\text{grade}(A) - \text{grade}(B)\|$ |
| **Left Contraction** | $A \lrcorner B$ | Generalization of the inner product | $\text{grade}(B) - \text{grade}(A)$ if $\text{grade}(A) \leq \text{grade}(B)$, else 0 |
| **Scalar Product** | $\langle AB \rangle_0$ | The scalar (grade-0) part of the geometric product | Grade 0 |
| **Commutator** | $[A, B]$ | Lie bracket; describes infinitesimal transformations | $\frac{1}{2}(AB - BA)$ |

## Special Operations

These operations are involutions or mappings critical for duality and inversion.

| Operation | Symbol | Definition | Notes |
| :--- | :--- | :--- | :--- |
| **Reverse** | $\tilde{A}$ | Reverses the order of all vector factors in the blades of $A$ | For grade-$k$ blade: $\tilde{A}_k = (-1)^{k(k-1)/2}A_k$ |
| **Grade Involution** | $\hat{A}$ | Multiplies each grade-$k$ part of $A$ by $(-1)^k$ | $\hat{A} = \sum_k (-1)^k \langle A \rangle_k$ |
| **Clifford Conjugation** | $\bar{A}$ | Composition of the reverse and grade involution | $\bar{A} = \widetilde{\hat{A}} = \hat{\tilde{A}}$ |
| **Dual** | $A^*$ | Maps an object to its orthogonal complement via the pseudoscalar | $A^* = AI_c^{-1}$. Maps between OPNS and IPNS |
| **Inverse** | $A^{-1}$ | The multiplicative inverse, if it exists | $AA^{-1} = 1$. For a versor: $A^{-1} = \tilde{A} / \langle A\tilde{A} \rangle_0$ |

## Geometric Objects in CGA

Objects have a direct **Outer Product Null Space (OPNS)** representation (the object as a wedge of points) and a dual **Inner Product Null Space (IPNS)** representation (the object as a constraint).

| Object | Symbol | OPNS Representation (Grade) | IPNS Representation (Grade) | Geometric Properties |
| :--- | :--- | :--- | :--- | :--- |
| **Point** | $P$ | $P$ (1) | Same as OPNS (1) | A null vector: $P^2 = 0$ |
| **Hypersphere** | $S$ | $P_1 \wedge P_2 \wedge \cdots \wedge P_{n+1}$ ($n+1$) | $P_c - \frac{1}{2}r^2\mathbf{n}_\infty$ (1) | $S_{IPNS}^2 = r^2 > 0$ |
| **Hyperplane** | $\pi$ | $P_1 \wedge P_2 \wedge \cdots \wedge P_n \wedge \mathbf{n}_\infty$ ($n+1$) | $\mathbf{n} + d\mathbf{n}_\infty$ (1) | $\pi_{IPNS}^2 = 1$ for unit normal $\mathbf{n}$ |
| **$(n-1)$-sphere** | $C$ | $P_1 \wedge P_2 \wedge \cdots \wedge P_n$ ($n$) | $(S_1 \wedge S_2)^*$ (2) | Intersection of two hyperspheres |
| **Line** | $L$ | $P_1 \wedge P_2 \wedge \mathbf{n}_\infty$ (3) | $(\pi_1 \wedge \pi_2 \wedge \cdots \wedge \pi_{n-1})^*$ ($n$) | $(n-1)$ hyperplanes intersection |
| **Point Pair** | $PP$ | $P_1 \wedge P_2$ (2) | $(S_1 \wedge S_2 \wedge \pi_1 \wedge \cdots \wedge \pi_{n-1})^*$ (2) | $PP_{OPNS}^2 \geq 0$ |

## Versors (Transformation Operators)

A versor $V$ is a multivector that can be expressed as a product of vectors and transforms any object $X$ via the sandwich product: $X' = VX\tilde{V}$.

| Transformation | Versor | Construction | Action | Parameters |
| :--- | :--- | :--- | :--- | :--- |
| **Reflection** | $\mathbf{n}$ | A unit vector: $\mathbf{n}^2 = \pm 1$ | $X' = -\mathbf{n} X \mathbf{n}$ | Reflection across hyperplane $\perp$ to $\mathbf{n}$ |
| **Inversion** | $S$ | A hypersphere $S$ | $X' = S X S^{-1}$ | Inversion w.r.t. the hypersphere $S$ |
| **Rotation** | $R$ | $R = \exp\left(-\frac{\theta}{2}B\right) = \cos\frac{\theta}{2} - B\sin\frac{\theta}{2}$ | $X' = RX\tilde{R}$ | Angle $\theta$, unit bivector $B$ |
| **Translation** | $T$ | $T = \exp\left(-\frac{1}{2}\mathbf{t}\mathbf{n}_\infty\right) = 1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$ | $X' = TX\tilde{T}$ | Displacement vector $\mathbf{t} \in \mathbb{R}^n$ |
| **Scaling** | $D$ | $D = \exp\left(\frac{\alpha}{2}(\mathbf{n}_0 \wedge \mathbf{n}_\infty)\right)$ | $X' = DX\tilde{D}$ | Scale factor $s = e^\alpha$ |
| **Motor** | $M$ | Screw motion: $M = TR$ | $X' = MX\tilde{M}$ | Combines rotation and translation |
| **Transversion** | $K$ | $K = 1 + \mathbf{k}\mathbf{n}_0$ | $X' = KX\tilde{K}$ | Special conformal transformation, $\mathbf{k} \in \mathbb{R}^n$ |

## Key Relations and Formulas

| Relation | Formula | Interpretation |
| :--- | :--- | :--- |
| **Incidence (OPNS)** | $P \wedge A = 0$ | Point $P$ lies on the OPNS object $A$ |
| **Incidence (IPNS)** | $P \cdot A = 0$ | Point $P$ lies on the IPNS object $A$ |
| **Intersection (Meet)** | $A \vee B = (A^* \wedge B^*)^*$ | Finds the geometric object common to both $A$ and $B$ |
| **Span (Join)** | $A \wedge B$ | Constructs the smallest object containing both $A$ and $B$ |
| **Orthogonality** | $A \cdot B = 0$ | The objects are perpendicular |
| **Distance (Points)** | $d^2 = -2(P_1 \cdot P_2)$ | Squared Euclidean distance (for normalized points) |
| **Distance (Point-Hyperplane)** | $d = \frac{P \cdot \pi}{-P \cdot \mathbf{n}_\infty}$ | Signed distance from normalized point to normalized hyperplane |
| **Embedding** | $P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | Maps Euclidean vector $\mathbf{p} \in \mathbb{R}^n$ to conformal point $P$ |
| **Sphere Radius** | $r^2 = S_{IPNS}^2$ | The squared radius of IPNS hypersphere $S$ |
| **Sphere Center** | $P_c = S_{IPNS}\mathbf{n}_\infty S_{IPNS}$ | The center point of IPNS hypersphere $S$ |
| **Rotor Interpolation** | $R(t) = R_1(R_1^{-1}R_2)^t$ | Spherical Linear Interpolation (SLERP) between rotors |

## Algebraic and Computational Notation

| Concept | Notation | Description |
| :--- | :--- | :--- |
| **Associativity** | $(AB)C = A(BC)$ | The geometric product is associative |
| **Non-commutativity** | $AB \neq BA$ | Product order matters; encodes geometric relationships |
| **Outermorphism** | $f(A \wedge B) = f(A) \wedge f(B)$ | Linear transformations preserve the outer product structure |
| **Computational Complexity** | $\mathcal{O}(f(n))$ | Asymptotic complexity for algorithmic analysis |
| **Numerical Tolerance** | $\epsilon$ | Machine epsilon for floating-point comparisons |
| **Norm** | $\|A\|$ | For blade $A$: $\|A\| = \sqrt{\|\langle A\tilde{A}\rangle_0\|}$ where $\langle A\tilde{A}\rangle_0$ is scalar |
| **Blade Indexing** | Binary/bit representation | Basis blade indexed by integer with bits indicating basis vectors. E.g., $\mathbf{e}_1 \wedge \mathbf{e}_3 \leftrightarrow 101_2 = 5$ |
| **Sparse Representation** | Sum of non-zero terms | Multivectors stored as (coefficient, blade index) pairs for efficiency |

---

*This notation guide provides immediate access to the symbols and operations used throughout Conformal Geometric Algebra. Each entry includes enough context for quick comprehension while maintaining mathematical precision. For detailed explanations and derivations, consult the corresponding chapters.*
