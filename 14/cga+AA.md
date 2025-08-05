## Appendices

These appendices serve as dense technical references for the practicing engineer. Unlike the pedagogical development in the main text, these sections optimize for rapid lookup and comprehensive coverage. Every formula appears with its computational cost. Every notation includes its dimensional context. Every conversion shows its exact mapping. The appendices acknowledge that real implementation requires precision beyond conceptual understanding—the difference between knowing that motors compose through multiplication and knowing that this requires exactly 48 floating-point operations in conformal geometric algebra.

### Appendix A: Symbol and Notation Reference

#### Fundamental Products and Their Decomposition

The geometric product unifies all geometric relationships:

$$\mathbf{ab} = \mathbf{a} \cdot \mathbf{b} + \mathbf{a} \wedge \mathbf{b}$$

where the symmetric part captures alignment (scalar) and the antisymmetric part captures orientation (bivector). This decomposition preserves all geometric information—neither product alone suffices. The geometric product exists because we demand both metric and orientational information in a single operation.

#### Core Notation Conventions

| Symbol | Description | Grade | Context |
|--------|-------------|-------|---------|
| $a, b, c$ | Scalars | 0 | Elements of $\mathbb{R}$ |
| $\mathbf{a}, \mathbf{b}, \mathbf{v}$ | Vectors | 1 | Bold lowercase throughout |
| $\mathbf{ab}$ or $\mathbf{a} * \mathbf{b}$ | Geometric product | Mixed | Juxtaposition preferred in theory, explicit in code |
| $B$, $\mathbf{a} \wedge \mathbf{b}$ | Bivectors | 2 | Oriented area elements |
| $T$ | Trivectors | 3 | Oriented volume elements |
| $A, B, M$ | General multivectors | Mixed | Sums of multiple grades |

#### Euclidean and Extended Basis Vectors

| Space | Basis | Properties | Geometric Role |
|-------|-------|------------|----------------|
| $\mathbb{R}^3$ | $\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3$ | $\mathbf{e}_i^2 = 1$, $\mathbf{e}_i \cdot \mathbf{e}_j = \delta_{ij}$ | Standard orthonormal frame |
| PGA $\mathbb{R}^{3,0,1}$ | Above + $\mathbf{e}_0$ | $\mathbf{e}_0^2 = 0$ | Projective direction (plane at infinity) |
| CGA $\mathbb{R}^{4,1}$ | Above + $\mathbf{e}_+, \mathbf{e}_-$ | $\mathbf{e}_+^2 = +1$, $\mathbf{e}_-^2 = -1$ | Minkowski basis for null cone |

#### Conformal Model Construction

The null basis vectors enable linearization of translation by creating a paraboloid embedding:

$$n_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+) \quad \text{(origin)}, \quad n_0^2 = 0$$

$$n_\infty = \mathbf{e}_- + \mathbf{e}_+ \quad \text{(point at infinity)}, \quad n_\infty^2 = 0$$

$$n_0 \cdot n_\infty = -1 \quad \text{(normalization constraint)}$$

Point embedding maps Euclidean to conformal space, with the quadratic term creating the null cone:

$$P = \mathbf{p} + \frac{1}{2}|\mathbf{p}|^2 n_\infty + n_0, \quad P^2 = 0 \text{ (always)}$$

Distance emerges from the inner product: $P_1 \cdot P_2 = -\frac{1}{2}|\mathbf{p}_1 - \mathbf{p}_2|^2$

#### Grade Structure and Projections

Grade projection extracts components of specific dimension:

$$\langle A \rangle_k = \frac{1}{2^n} \sum_{B_k} B_k A B_k^{-1}$$

where the sum runs over all grade-$k$ basis blades.

| Notation | Projection | Components | Computational Use |
|----------|------------|------------|-------------------|
| $\langle A \rangle_k$ | Grade-$k$ part | $\binom{n}{k}$ dimensions | Type checking |
| $\langle A \rangle_+$ | Even grades | $2^{n-1}$ components | Rotor extraction |
| $\langle A \rangle_-$ | Odd grades | $2^{n-1}$ components | Vector/trivector parts |
| $\langle A \rangle$ | Scalar part | 1 component | Trace operations |

#### Versors and Transformations

All orthogonal transformations decompose into reflections via versors:

| Type | Form | Constraint | Action | Minimum Reflections |
|------|------|------------|--------|---------------------|
| Reflection | $\mathbf{n}$ (unit vector) | $\mathbf{n}^2 = 1$ | $\mathbf{v}' = -\mathbf{n}\mathbf{v}\mathbf{n}$ | 1 |
| Rotor | $R = \exp(-\frac{\theta}{2}B)$ | $R\tilde{R} = 1$ | $\mathbf{v}' = R\mathbf{v}R^{\dagger}$ | 2 |
| Translator | $T = \exp(-\frac{1}{2}\mathbf{t} \cdot n_\infty)$ | $T\tilde{T} = 1$ | $P' = TPT^{-1}$ | 2 (parallel) |
| Motor | $M = TR$ | $M\tilde{M} = 1$ | Screw motion | 4 |

For rotors in Euclidean space: $R^{\dagger} = \tilde{R} = R^{-1}$ (all equivalent due to $R\tilde{R} = 1$)

#### Rotor-Quaternion Correspondence

Direct mapping preserves Hamilton's convention with adjusted signs:

$$R = q_0 - q_1\mathbf{e}_{23} - q_2\mathbf{e}_{31} - q_3\mathbf{e}_{12}$$

$$q = (R)_0 + (R)_{23}\mathbf{i} + (R)_{31}\mathbf{j} + (R)_{12}\mathbf{k}$$

where $\mathbf{e}_{ij} = \mathbf{e}_i \wedge \mathbf{e}_j$ and signs ensure $\mathbf{i}\mathbf{j} = \mathbf{k}$.

#### Duality and Incidence Operations

The dual operation provides orthogonal complement via pseudoscalar multiplication:

$$A^* = AI^{-1}$$

| Space | Pseudoscalar $I$ | $I^2$ | Dual Properties | Key Mappings |
|-------|------------------|-------|-----------------|--------------|
| 2D | $\mathbf{e}_{12}$ | $-1$ | Complex structure | Vector ↔ Bivector |
| 3D | $\mathbf{e}_{123}$ | $-1$ | Hodge dual | Cross product link |
| 3D PGA | $\mathbf{e}_{0123}$ | $0$ | $I^{-1} = I$ | Points ↔ Planes |
| 3D CGA | $\mathbf{e}_{12345}$ | $+1$ | Standard inverse | Spheres ↔ Points |

Meet (intersection) and Join (span) achieve computational unification:

$$A \vee B = (A^* \wedge B^*)^* \quad \text{(intersection via dual-wedge-dual)}$$

#### Binary Blade Indexing

Basis blades map to binary indices enabling bitwise algorithms:

$$\mathbf{e}_{\{i_1, i_2, \ldots, i_k\}} \leftrightarrow \sum_{j} 2^{i_j-1}$$

| Blade | Binary | Decimal | Grade | Sign Rule |
|-------|--------|---------|-------|-----------|
| $1$ | $0000$ | 0 | 0 | Always $+$ |
| $\mathbf{e}_1$ | $0001$ | 1 | 1 | Base vector |
| $\mathbf{e}_2$ | $0010$ | 2 | 1 | Base vector |
| $\mathbf{e}_{12}$ | $0011$ | 3 | 2 | One swap |
| $\mathbf{e}_{123}$ | $0111$ | 7 | 3 | Three swaps |

Computational patterns:
- Grade: $\text{popcount(index)}$ (number of 1-bits)
- Product indices: $\text{index}_c = \text{index}_a \oplus \text{index}_b$ (XOR)
- Sign: $(-1)^{\text{swap count}}$ via sorting algorithm

#### Performance Reality

| Operation | Naive FLOPs | Optimized | Sparsity Factor | Memory Access |
|-----------|-------------|-----------|-----------------|---------------|
| 3D geometric product | 15 | 9 | 0.6× | Sequential |
| 3D rotor application | 54 | 28 | 0.5× | Scattered |
| CGA motor application | 220 | ~100 | 0.45× | Scattered |
| General meet operation | 160 | ~50 | 0.3× | Random |

#### Specialized Products

| Symbol | Name | Definition | Primary Use | Efficiency Note |
|--------|------|------------|-------------|-----------------|
| $\times$ | Commutator | $\frac{1}{2}(AB - BA)$ | Lie brackets | Often zero |
| $\bullet$ | Anticommutator | $\frac{1}{2}(AB + BA)$ | Quantum analogs | Symmetric only |
| $\tilde{A}$ | Reverse | Reverse blade factors | Versor inverse | $O(n)$ operation |
| $\hat{A}$ | Grade involution | $(-1)^{\text{grade}}$ | Parity tests | Sign flips only |

#### Null Space Constraints

Conformal objects satisfy strict algebraic constraints:

| Object | Primary Constraint | Normalization | Geometric Meaning |
|--------|-------------------|---------------|-------------------|
| Point $P$ | $P^2 = 0$ | $P \cdot n_\infty = -1$ | On null cone |
| Line $L$ | $L \cdot n_\infty = 0$ | $\|L\|^2 = \text{length}^2$ | No infinite point |
| Circle $C$ | $C^2 < 0$ | Grade 3 in CGA | Real circle |
| Sphere $S$ | $S^2 < 0$ | Grade 4 in CGA | Real sphere |

#### Matrix-Motor Bridge

For $4 \times 4$ homogeneous matrix $\begin{bmatrix} R & \mathbf{t} \\ 0 & 1 \end{bmatrix}$:

1. Extract rotation matrix $R_{3 \times 3}$
2. Convert to rotor via eigenaxis: $R = \exp(-\frac{\theta}{2}B)$
3. Form translator: $T = 1 - \frac{1}{2}\mathbf{t} \cdot n_\infty$
4. Compose: $M = TR$

Inverse path uses logarithm and grade projection.

#### Sparsity Exploitation

| Type | Non-zero/Total | Pattern | Cache Behavior |
|------|----------------|---------|----------------|
| 3D Vector | 3/8 | Contiguous | Excellent |
| 3D Rotor | 4/8 | Even grades | Good |
| CGA Point | 5/32 | Scattered | Poor |
| CGA Line | 8/32 | Two clusters | Medium |
| CGA Motor | 8/32 | Even grades | Poor |

#### Critical Implementation Notes

1. **Normalization frequency**: Versors drift as $\epsilon^2$ per operation
2. **Coordinate extraction**: Always check $P \cdot n_\infty \neq 0$ before dividing
3. **Dual degeneracy**: PGA dual requires special handling when $I^2 = 0$
4. **Grade projection**: Numerical zeros need threshold $\approx \sqrt{\text{machine epsilon}}$
5. **Binary index mapping**: Blade order must be consistent across entire codebase
