### Appendix C: Multiplication and Reference Tables

This appendix provides the computational foundation for implementing geometric algebra operations through comprehensive tables and reference matrices. Each table captures essential patterns that enable efficient implementation while revealing the deep algebraic structure underlying geometric computation.

#### Metric Signatures of Common Geometric Algebras

The choice of metric signature fundamentally determines the algebra's computational properties. Each signature $(p,q,r)$ specifies the number of basis vectors squaring to $+1$, $-1$, and $0$ respectively.

| Space | Signature $(p,q,r)$ | Notation | Basis Squares | Applications |
|-------|---------------------|----------|---------------|--------------|
| Euclidean 2D | $(2,0,0)$ | $\mathbb{R}^2$ | All $+1$ | Plane geometry, complex numbers |
| Euclidean 3D | $(3,0,0)$ | $\mathbb{R}^3$ | All $+1$ | Classical 3D geometry, quaternions |
| Conformal 2D | $(3,1,0)$ | $\mathbb{R}^{3,1}$ | $(+1,+1,+1,-1)$ | 2D CGA, inversive geometry |
| Conformal 3D | $(4,1,0)$ | $\mathbb{R}^{4,1}$ | $(+1,+1,+1,+1,-1)$ | 3D CGA, sphere geometry |
| Spacetime | $(1,3,0)$ or $(3,1,0)$ | $\mathbb{R}^{1,3}$ | Mixed signs | Special relativity, electromagnetism |
| Projective 3D | $(3,0,1)$ | $\mathbb{R}^{3,0,1}$ | $(+1,+1,+1,0)$ | Computer vision, homogeneous coords |
| Spacetime Conformal | $(4,2,0)$ | $\mathbb{R}^{4,2}$ | $(+1,+1,+1,+1,-1,-1)$ | Conformal relativity, twistors |

#### Geometric Product Tables

These tables encode the complete multiplication rules. The geometric product's non-commutativity appears in the sign patterns, while its information-preserving nature shows in how products span multiple grades.

**Table C.1: 2D Euclidean Geometric Algebra $\mathbb{R}^2$**

| $\times$ | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
|----------|-----|----------------|----------------|-------------------|
| **$1$** | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
| **$\mathbf{e}_1$** | $\mathbf{e}_1$ | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_2$ |
| **$\mathbf{e}_2$** | $\mathbf{e}_2$ | $-\mathbf{e}_{12}$ | $1$ | $-\mathbf{e}_1$ |
| **$\mathbf{e}_{12}$** | $\mathbf{e}_{12}$ | $-\mathbf{e}_2$ | $\mathbf{e}_1$ | $-1$ |

Note the isomorphism with complex numbers: $\mathbf{e}_{12} \leftrightarrow i$, revealing how $\mathbb{C}$ embeds naturally in GA.

**Table C.2: 3D Euclidean Basis Vector Products**

| $\times$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_3$ |
|----------|----------------|----------------|----------------|
| **$\mathbf{e}_1$** | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_{13}$ |
| **$\mathbf{e}_2$** | $-\mathbf{e}_{12}$ | $1$ | $\mathbf{e}_{23}$ |
| **$\mathbf{e}_3$** | $-\mathbf{e}_{13}$ | $-\mathbf{e}_{23}$ | $1$ |

The diagonal gives the metric, while off-diagonal elements encode orientation via bivectors.

**Table C.3: 3D Euclidean Bivector Products**

| $\times$ | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|----------|-------------------|-------------------|-------------------|
| **$\mathbf{e}_{23}$** | $-1$ | $-\mathbf{e}_{12}$ | $\mathbf{e}_{31}$ |
| **$\mathbf{e}_{31}$** | $\mathbf{e}_{12}$ | $-1$ | $-\mathbf{e}_{23}$ |
| **$\mathbf{e}_{12}$** | $-\mathbf{e}_{31}$ | $\mathbf{e}_{23}$ | $-1$ |

Note: $\mathbf{e}_{ji} = -\mathbf{e}_{ij}$ for $i < j$. This table reveals the quaternion subalgebra structure.

#### Conformal Basis Products

The null vectors $\mathbf{n}_0$ and $\mathbf{n}_\infty$ encode the conformal structure, enabling unified treatment of Euclidean and inversive geometry.

**Table C.4: Null Vector Products in CGA**

| Product | Result | Type | Notes |
|---------|--------|------|-------|
| $\mathbf{n}_0 \cdot \mathbf{n}_0$ | $0$ | Scalar | Null property |
| $\mathbf{n}_\infty \cdot \mathbf{n}_\infty$ | $0$ | Scalar | Null property |
| $\mathbf{n}_0 \cdot \mathbf{n}_\infty$ | $-1$ | Scalar | Defines metric |
| $\mathbf{n}_0 \wedge \mathbf{n}_\infty$ | $\mathbf{n}_0\mathbf{n}_\infty$ | Bivector | Scaling generator |
| $\mathbf{n}_0\mathbf{n}_\infty$ | $-1 + \mathbf{n}_0 \wedge \mathbf{n}_\infty$ | Mixed | Geometric product |
| $\mathbf{n}_\infty\mathbf{n}_0$ | $-1 - \mathbf{n}_0 \wedge \mathbf{n}_\infty$ | Mixed | Note sign change |

The relation $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ is fundamental—it ensures proper distance encoding in the conformal model.

**Table C.5: Euclidean-Null Interactions**

| Product | Result | Notes |
|---------|--------|-------|
| $\mathbf{e}_i \cdot \mathbf{n}_0$ | $0$ | Orthogonal subspaces |
| $\mathbf{e}_i \cdot \mathbf{n}_\infty$ | $0$ | Orthogonal subspaces |
| $\mathbf{e}_i\mathbf{n}_0$ | $\mathbf{e}_i \wedge \mathbf{n}_0$ | Pure outer product |
| $\mathbf{e}_i\mathbf{n}_\infty$ | $\mathbf{e}_i \wedge \mathbf{n}_\infty$ | Pure outer product |
| $\mathbf{n}_0\mathbf{e}_i$ | $-\mathbf{e}_i \wedge \mathbf{n}_0$ | Anticommutes |
| $\mathbf{n}_\infty\mathbf{e}_i$ | $-\mathbf{e}_i \wedge \mathbf{n}_\infty$ | Anticommutes |

This orthogonality enables the conformal split: Euclidean operations remain unchanged while gaining inversive capabilities.

#### Grade Structure Tables

Understanding grade dimensions guides efficient storage allocation and operation optimization.

**Table C.6: Grade Dimensions by Space**

| Space | Total Dim | Grade 0 | Grade 1 | Grade 2 | Grade 3 | Grade 4 | Grade 5 |
|-------|-----------|---------|---------|---------|---------|---------|---------|
| 2D | $2^2 = 4$ | 1 | 2 | 1 | - | - | - |
| 3D | $2^3 = 8$ | 1 | 3 | 3 | 1 | - | - |
| 4D | $2^4 = 16$ | 1 | 4 | 6 | 4 | 1 | - |
| 5D (CGA) | $2^5 = 32$ | 1 | 5 | 10 | 10 | 5 | 1 |

General formula: $\text{dim}(\Lambda^k(\mathbb{R}^n)) = \binom{n}{k}$

The symmetry in dimensions (e.g., 10 bivectors and 10 trivectors in 5D) reflects Hodge duality.

**Table C.7: CGA Basis Blades by Grade**

| Grade | Count | Basis Elements | Geometric Interpretation |
|-------|-------|----------------|--------------------------|
| 0 | 1 | $1$ | Scalar |
| 1 | 5 | $\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3, \mathbf{n}_0, \mathbf{n}_\infty$ | Points (IPNS), vectors |
| 2 | 10 | $\mathbf{e}_{ij}, \mathbf{e}_i\mathbf{n}_0, \mathbf{e}_i\mathbf{n}_\infty, \mathbf{n}_0\mathbf{n}_\infty$ | Lines (OPNS), bivectors |
| 3 | 10 | All 3-way combinations | Planes (IPNS), circles (OPNS) |
| 4 | 5 | All 4-way combinations | Spheres (IPNS) |
| 5 | 1 | $I_c = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ | Pseudoscalar |

The dual representations (IPNS/OPNS) provide computational flexibility for different operations.

#### Binary Blade Indexing Scheme

Binary representation enables efficient blade arithmetic through bit operations, crucial for performance.

**Table C.8: Binary Index Mapping for 5D CGA**

| Blade | Binary | Decimal | Grade | Storage Pattern |
|-------|--------|---------|-------|-----------------|
| $1$ | 00000 | 0 | 0 | Always present |
| $\mathbf{e}_1$ | 00001 | 1 | 1 | Spatial |
| $\mathbf{e}_2$ | 00010 | 2 | 1 | Spatial |
| $\mathbf{e}_{12}$ | 00011 | 3 | 2 | Rotation plane |
| $\mathbf{e}_3$ | 00100 | 4 | 1 | Spatial |
| $\mathbf{e}_{13}$ | 00101 | 5 | 2 | Rotation plane |
| $\mathbf{e}_{23}$ | 00110 | 6 | 2 | Rotation plane |
| $\mathbf{e}_{123}$ | 00111 | 7 | 3 | Volume element |
| $\mathbf{n}_0$ | 01000 | 8 | 1 | Conformal |
| $\mathbf{n}_\infty$ | 10000 | 16 | 1 | Conformal |
| $I_c$ | 11111 | 31 | 5 | Pseudoscalar |

**Computational Rules:**
- Grade extraction: `grade = popcount(index)`
- Blade product indices: `index_c = index_a XOR index_b` (for orthogonal bases)
- Sign computation requires counting basis vector swaps
- Sparse storage benefits from sorted index arrays

#### Sign Computation for Products

Correct sign handling is critical for geometric algebra implementation. These rules ensure products respect orientation.

**Table C.9: Reordering Sign Rules**

For product of basis vectors $\mathbf{e}_{i_1}\mathbf{e}_{i_2}\cdots\mathbf{e}_{i_k}$:

1. Count inversions needed to sort indices ascending
2. Apply metric signs for any repeated indices
3. Final sign: $(-1)^{\text{inversions}} \times \text{metric signs}$

Example calculations:

| Product | Canonical Form | Swaps | Sign | Verification |
|---------|----------------|-------|------|--------------|
| $\mathbf{e}_2\mathbf{e}_1$ | $\mathbf{e}_{12}$ | 1 | $-1$ | One transposition |
| $\mathbf{e}_3\mathbf{e}_1\mathbf{e}_2$ | $\mathbf{e}_{123}$ | 2 | $+1$ | Even permutation |
| $\mathbf{e}_2\mathbf{e}_3\mathbf{e}_1$ | $\mathbf{e}_{123}$ | 2 | $+1$ | $(2,3,1) \to (1,2,3)$ |

Implementation via merge sort provides $O(k \log k)$ sign computation for $k$-blades.

#### Duality Tables

Duality reveals deep structural relationships and enables computational shortcuts through the pseudoscalar.

**Table C.10: Pseudoscalar Properties**

| Space | Pseudoscalar $I$ | $I^2$ | Dual Formula | Implementation Note |
|-------|------------------|-------|--------------|---------------------|
| 2D | $\mathbf{e}_{12}$ | $-1$ | $A^* = -AI$ | Right multiply, negate |
| 3D | $\mathbf{e}_{123}$ | $-1$ | $A^* = -AI$ | Right multiply, negate |
| 4D | $\mathbf{e}_{1234}$ | $+1$ | $A^* = AI$ | Right multiply only |
| 5D (CGA) | $\mathbf{e}_{12345}$ | $-1$ | $A^* = -AI$ | Right multiply, negate |

where $\mathbf{e}_{12345} = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ in CGA with our ordering convention.

The sign of $I^2$ determines whether duality is an involution ($I^2 = \pm 1$) or more complex.

**Table C.11: Grade Duality Mapping in CGA**

| Original Grade | Dual Grade | Example | Geometric Meaning |
|----------------|------------|---------|-------------------|
| 0 (scalar) | 5 (pseudoscalar) | $1 \leftrightarrow I_c$ | Point at origin $\leftrightarrow$ entire space |
| 1 (vector) | 4 (4-vector) | Point $\leftrightarrow$ hypersphere | IPNS $\leftrightarrow$ OPNS |
| 2 (bivector) | 3 (trivector) | Line $\leftrightarrow$ line* | Plücker dual |
| 3 (trivector) | 2 (bivector) | Plane* $\leftrightarrow$ plane | Dual plane |
| 4 (4-vector) | 1 (vector) | Sphere* $\leftrightarrow$ sphere | Co-sphere |
| 5 (pseudoscalar) | 0 (scalar) | $I_c \leftrightarrow$ 1 | Entire space $\leftrightarrow$ point |

This duality enables the meet operation: $A \vee B = (A^* \wedge B^*)^*$.

#### Commutator and Lie Algebra Structure

The bivector commutators reveal the Lie algebra structure underlying rotational dynamics.

**Table C.12: Bivector Commutators in 3D**

The commutator $[A,B] = \frac{1}{2}(AB - BA)$ for unit bivectors:

| $[,]$ | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|-------|-------------------|-------------------|-------------------|
| **$\mathbf{e}_{23}$** | $0$ | $\mathbf{e}_{12}$ | $-\mathbf{e}_{31}$ |
| **$\mathbf{e}_{31}$** | $-\mathbf{e}_{12}$ | $0$ | $\mathbf{e}_{23}$ |
| **$\mathbf{e}_{12}$** | $\mathbf{e}_{31}$ | $-\mathbf{e}_{23}$ | $0$ |

This forms the Lie algebra $\mathfrak{so}(3)$ of the rotation group. The structure constants match the Levi-Civita symbol, connecting to cross products.

#### Quaternion-GA Correspondence

This correspondence bridges familiar quaternion algebra with GA's geometric clarity.

**Table C.13: Quaternion to Geometric Algebra Mapping**

| Quaternion | GA Element | Verification | Geometric Meaning |
|------------|------------|--------------|-------------------|
| $1$ | $1$ | Identity element | No rotation |
| $i$ | $-\mathbf{e}_{23}$ | $i^2 = (-\mathbf{e}_{23})^2 = -1$ | YZ-plane rotation |
| $j$ | $-\mathbf{e}_{31}$ | $j^2 = (-\mathbf{e}_{31})^2 = -1$ | ZX-plane rotation |
| $k$ | $-\mathbf{e}_{12}$ | $k^2 = (-\mathbf{e}_{12})^2 = -1$ | XY-plane rotation |

Hamilton's relation verification:
$$ij = (-\mathbf{e}_{23})(-\mathbf{e}_{31}) = \mathbf{e}_{23}\mathbf{e}_{31} = -\mathbf{e}_{12} = k$$

The negative signs arise from orientation conventions; quaternions use left-handed rotations while GA uses right-handed.

#### Computational Complexity Reference

These complexity bounds guide architectural decisions and optimization priorities.

**Table C.14: Operation Complexity Analysis**

| Operation | Naive | Optimized | Notes |
|-----------|-------|-----------|-------|
| Geometric product (general) | $O(4^n)$ | $O(2^n)$ sparse | Sparsity exploitation crucial |
| Vector-vector product | $O(n)$ | $O(n)$ | Already optimal |
| Bivector-vector product | $O(n^2)$ | $O(n)$ | Grade structure helps |
| Rotor application | $O(2^{3n})$ | $O(n^2)$ | Sandwich formula |
| Meet operation | $O(2^{2n})$ | $O(n^3)$ | Grade-targeted computation |
| Bivector exponential | $O(n^3)$ | $O(1)$ | Closed-form solution |
| Dual operation | $O(2^n)$ | $O(2^n)$ | Index permutation |
| Grade projection | $O(2^n)$ | $O(\binom{n}{k})$ where $k$ is target grade | Only compute grade $k$ |
| Inverse (general) | $O(2^{3n})$ | $O(2^n)$ for blades | Clifford conjugation |
| Batch operations | - | Amortized $O(1)$ per element | SIMD parallelism |

These bounds assume dense storage; sparse representations can dramatically improve performance for typical geometric objects where only 10-20% of components are non-zero.

---
