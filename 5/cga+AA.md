## Appendices

### Appendix A: Symbol and Notation Glossary

This glossary provides an unambiguous reference for every mathematical symbol and notational convention used throughout the text. Entries are organized thematically for efficient lookup, with computational context included where relevant.

#### Scalar and Vector Notation

| Symbol | Definition | Grade | Computational Notes |
|--------|------------|-------|---------------------|
| $a, b, c$ | Scalar values | 0 | Real numbers; commute with all elements |
| $\alpha, \beta, \gamma$ | Scalar parameters | 0 | Often angles, scales, or coefficients |
| $\lambda, \mu, \nu$ | Scalar indices or parameters | 0 | Eigenvalues, indices, or scale factors |
| $\mathbf{a}, \mathbf{b}, \mathbf{v}$ | Euclidean vectors | 1 | Bold denotes spatial vectors in $\mathbb{R}^n$ |
| $\mathbf{e}_i$ | Euclidean basis vectors | 1 | Orthonormal: $\mathbf{e}_i \cdot \mathbf{e}_j = \delta_{ij}$ |
| $\mathbf{e}_{i_1i_2...i_k}$ | Basis blade | $k$ | Wedge product of $k$ distinct basis vectors |
| $\mathbf{p}, \mathbf{q}, \mathbf{r}$ | Position vectors | 1 | Points in Euclidean space |
| $\mathbf{n}$ | Unit normal vector | 1 | Used for planes and reflections |
| $\mathbf{t}$ | Translation vector | 1 | Displacement in Euclidean space |
| $\mathbf{d}$ | Direction vector | 1 | Often normalized for lines and rays |
| $\mathbf{u}, \mathbf{w}$ | General vectors | 1 | Arbitrary vector quantities |

#### Multivector Notation

| Symbol | Definition | Grade Structure | Storage Considerations |
|--------|------------|-----------------|------------------------|
| $A, B, C, M$ | General multivectors | Mixed | Up to $2^n$ components in $n$-D space |
| $\langle A \rangle_k$ | Grade-$k$ projection | Extracts grade $k$ | Returns zero if grade $k$ absent |
| $A^{(k)}$ | Alternative grade notation | Pure grade $k$ | Equivalent to $\langle A \rangle_k$ |
| $\mathcal{G}(A)$ | Grade set of $A$ | Set notation | $\{k : \langle A \rangle_k \neq 0\}$ |
| $X, Y, Z$ | Geometric objects | Context-dependent | Points, lines, planes, spheres, etc. |
| $A_k$ | $k$-vector | Pure grade $k$ | Only grade-$k$ components non-zero |

#### Conformal Basis Elements

| Symbol | Name | Properties | Construction |
|--------|------|------------|--------------|
| $\mathbf{n}_0$ | Origin null vector | $\mathbf{n}_0^2 = 0$ | $\frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+)$ |
| $\mathbf{n}_\infty$ | Infinity null vector | $\mathbf{n}_\infty^2 = 0$ | $\mathbf{e}_- + \mathbf{e}_+$ |
| $\mathbf{e}_+$ | Positive null generator | $\mathbf{e}_+^2 = +1$ | Fourth spatial dimension |
| $\mathbf{e}_-$ | Negative null generator | $\mathbf{e}_-^2 = -1$ | Fifth dimension (timelike) |

Key relation: $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ (defines conformal structure)

#### Fundamental Operations

| Operation | Symbol | Definition | Grade Result |
|-----------|--------|------------|--------------|
| Geometric Product | $AB$ | Fundamental product | Mixed grades |
| Inner Product | $A \cdot B$ | Symmetric contraction | $\lvert\text{grade}(A) - \text{grade}(B)\rvert$ |
| Outer Product | $A \wedge B$ | Antisymmetric extension | $\text{grade}(A) + \text{grade}(B)$ if independent |
| Left Contraction | $A \lrcorner B$ | Generalized projection | $\text{grade}(B) - \text{grade}(A)$ if $\text{grade}(A) \leq \text{grade}(B)$ |
| Right Contraction | $A \llcorner B$ | Alternative contraction | $\text{grade}(A) - \text{grade}(B)$ if $\text{grade}(B) \leq \text{grade}(A)$ |
| Scalar Product | $\langle AB \rangle_0$ or $\langle AB \rangle$ | Grade-0 part | Always scalar |
| Fat Dot Product | $A \bullet B$ | Symmetric grade selection | $\langle AB \rangle_{\lvert\text{grade}(A)-\text{grade}(B)\rvert}$ |
| Commutator | $[A, B]$ | Lie bracket | $\frac{1}{2}(AB - BA)$ |
| Anticommutator | $\{A, B\}$ | Jordan product | $\frac{1}{2}(AB + BA)$ |

#### Geometric Objects in CGA

Objects possess dual representations: **OPNS** (Outer Product Null Space) defines objects constructively, while **IPNS** (Inner Product Null Space) defines objects as constraints.

| Object | Symbol | OPNS Representation | IPNS Representation | Incidence Test |
|--------|--------|---------------------|---------------------|----------------|
| Point | $P$ | $P$ (grade 1) | Same (grade 1) | $P^2 = 0$ |
| Line | $L$ | $P_1 \wedge P_2 \wedge \mathbf{n}_\infty$ (grade 2) | $(\pi_1 \wedge \pi_2)^*$ (grade 3) | Point on line: $P \wedge L = 0$ |
| Plane | $\pi$ | $(P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty)^*$ (grade 1) | $\mathbf{n} + d\mathbf{n}_\infty$ (grade 1) | Point on plane: $P \cdot \pi = 0$ |
| Sphere | $S$ | $(P_1 \wedge P_2 \wedge P_3 \wedge P_4)^*$ (grade 1) | $C - \frac{1}{2}r^2\mathbf{n}_\infty$ (grade 1) | Point on sphere: $P \cdot S = 0$ |
| Circle | $C$ | $P_1 \wedge P_2 \wedge P_3$ (grade 2) | $(S_1 \wedge S_2)^*$ or $(S \wedge \pi)^*$ (grade 3) | Point on circle: $P \wedge C = 0$ |
| Point Pair | $PP$ | $P_1 \wedge P_2$ (grade 2) | $(S_1 \wedge S_2 \wedge \pi)^*$ (grade 3) | $PP^2 \geq 0$ |

#### Transformations (Versors)

A versor $V$ transforms objects via the sandwich product: $X' = VXV^{-1}$ (or $X' = VX\tilde{V}$ for normalized versors).

| Transformation | Symbol | Versor Form | Application | Type |
|---------------|--------|-------------|-------------|------|
| Reflection | $\sigma$ | Unit vector/hyperplane | $X' = -\sigma X \sigma$ | Odd versor |
| Rotation | $R$ | $\exp(-\frac{\theta}{2}B) = \cos\frac{\theta}{2} - B\sin\frac{\theta}{2}$ | $X' = RXR^{-1}$ | Even versor (rotor) |
| Translation | $T$ | $1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$ | $X' = TXT^{-1}$ | Even versor |
| Scaling | $D$ | $\exp(\frac{\lambda}{2}\mathbf{n}_0\mathbf{n}_\infty)$ | $X' = DXD^{-1}$ | Even versor, scale $e^\lambda$ |
| Inversion | $S$ | Sphere $S$ | $X' = SXS^{-1}$ | Odd versor |
| Motor | $M$ | $TR$ (screw motion) | $X' = MXM^{-1}$ | Even versor |
| Transversion | $K$ | $1 + \mathbf{k}\mathbf{n}_0$ | $X' = KXK^{-1}$ | Special conformal |

#### Special Operations and Relations

| Operation | Symbol | Definition | Properties |
|-----------|--------|------------|------------|
| Reverse | $\tilde{A}$ | Reverses vector order in blades | $\widetilde{AB} = \tilde{B}\tilde{A}$ |
| Grade Involution | $\hat{A}$ | Alternates sign by grade | $\hat{A} = \sum_k (-1)^k \langle A \rangle_k$ |
| Clifford Conjugation | $\bar{A}$ | Combined operations | $\bar{A} = \widetilde{\hat{A}} = \hat{\tilde{A}}$ |
| Dual | $A^*$ | Hodge dual | $A^* = AI^{-1}$ where $I$ is pseudoscalar |
| Meet | $A \vee B$ | Intersection operation | $(A^* \wedge B^*)^*$ |
| Join | $A \wedge B$ | Direct wedge if disjoint | Span/union operation |
| Inverse | $A^{-1}$ | Multiplicative inverse | $\tilde{A}/\langle A\tilde{A}\rangle_0$ for versors |
| Magnitude | $\lvert A \rvert$ or $\|A\|$ | Norm of multivector | $\sqrt{\lvert\langle A\tilde{A} \rangle_0\rvert}$ for blades |

#### Computational and Implementation Notation

| Symbol | Meaning | Context |
|--------|---------|---------|
| $O(f(n))$ | Big-O complexity | Algorithm analysis upper bound |
| $\Theta(f(n))$ | Theta complexity | Tight complexity bound |
| $\epsilon$ | Machine epsilon | Floating-point tolerance (~$2.22 \times 10^{-16}$ for double) |
| $\kappa$ | Condition number | Numerical stability measure |
| $\nabla$ | Vector derivative | $\nabla = \sum_i \mathbf{e}^i\partial_i$ |
| $\partial_i$ | Partial derivative | With respect to coordinate $i$ |
| $I_n$ | Euclidean pseudoscalar | $I_n = \mathbf{e}_1 \wedge \cdots \wedge \mathbf{e}_n$ |
| $I_c$ | Conformal pseudoscalar | $I_c = I_n \wedge \mathbf{n}_\infty \wedge \mathbf{n}_0$ |
| $\text{grade}(A)$ | Grade extraction | Returns set of active grades |
| $\text{popcount}(n)$ | Population count | Number of set bits in binary representation |
| $\oplus$ | Direct sum | $V = V_0 \oplus V_1 \oplus ... \oplus V_n$ |
| $\otimes$ | Tensor product | Multilinear constructions |

#### Index Notation for Binary Blade Representation

| Concept | Binary Representation | Example |
|---------|---------------------|---------|
| Blade index | Bits indicate basis vectors | $\mathbf{e}_1\mathbf{e}_3 \rightarrow 101_2 = 5$ |
| Grade computation | Population count of index | $\text{grade}(101_2) = \text{popcount}(5) = 2$ |
| Blade product sign | Swap count parity | Count transpositions for canonical ordering |
| Sparse storage | (coefficient, index) pairs | Only non-zero components stored |
