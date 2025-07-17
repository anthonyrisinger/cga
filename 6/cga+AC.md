### Appendix C: Multiplication and Reference Tables

This appendix provides the computational foundation for implementing geometric algebra operations through comprehensive tables and reference matrices.

#### Metric Signatures of Common Geometric Algebras

| Space | Signature $(p,q,r)$ | Notation | Basis Squares | Applications |
|-------|---------------------|----------|---------------|--------------|
| Euclidean 2D | $(2,0,0)$ | $\mathbb{R}^2$ | All $+1$ | Plane geometry |
| Euclidean 3D | $(3,0,0)$ | $\mathbb{R}^3$ | All $+1$ | Classical 3D geometry |
| Conformal 2D | $(3,1,0)$ | $\mathbb{R}^{3,1}$ | $(+1,+1,+1,-1)$ | 2D CGA |
| Conformal 3D | $(4,1,0)$ | $\mathbb{R}^{4,1}$ | $(+1,+1,+1,+1,-1)$ | 3D CGA |
| Spacetime | $(1,3,0)$ or $(3,1,0)$ | $\mathbb{R}^{1,3}$ | Mixed signs | Special relativity |
| Projective 3D | $(3,0,1)$ | $\mathbb{R}^{3,0,1}$ | $(+1,+1,+1,0)$ | Computer vision |
| Spacetime Conformal | $(4,2,0)$ | $\mathbb{R}^{4,2}$ | $(+1,+1,+1,+1,-1,-1)$ | Conformal relativity |

#### Geometric Product Tables

**Table C.1: 2D Euclidean Geometric Algebra $\mathbb{R}^2$**

| × | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
|---|-----|----------------|----------------|-------------------|
| **$1$** | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
| **$\mathbf{e}_1$** | $\mathbf{e}_1$ | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_2$ |
| **$\mathbf{e}_2$** | $\mathbf{e}_2$ | $-\mathbf{e}_{12}$ | $1$ | $-\mathbf{e}_1$ |
| **$\mathbf{e}_{12}$** | $\mathbf{e}_{12}$ | $-\mathbf{e}_2$ | $\mathbf{e}_1$ | $-1$ |

**Table C.2: 3D Euclidean Basis Vector Products**

| × | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_3$ |
|---|----------------|----------------|----------------|
| **$\mathbf{e}_1$** | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_{13}$ |
| **$\mathbf{e}_2$** | $-\mathbf{e}_{12}$ | $1$ | $\mathbf{e}_{23}$ |
| **$\mathbf{e}_3$** | $-\mathbf{e}_{13}$ | $-\mathbf{e}_{23}$ | $1$ |

**Table C.3: 3D Euclidean Bivector Products**

| × | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|---|-------------------|-------------------|-------------------|
| **$\mathbf{e}_{23}$** | $-1$ | $-\mathbf{e}_{12}$ | $\mathbf{e}_{31}$ |
| **$\mathbf{e}_{31}$** | $\mathbf{e}_{12}$ | $-1$ | $-\mathbf{e}_{23}$ |
| **$\mathbf{e}_{12}$** | $-\mathbf{e}_{31}$ | $\mathbf{e}_{23}$ | $-1$ |

Note: $\mathbf{e}_{31} = \mathbf{e}_{13}$ with appropriate sign.

#### Conformal Basis Products

**Table C.4: Null Vector Products in CGA**

| Product | Result | Type | Notes |
|---------|--------|------|-------|
| $\mathbf{n}_0 \cdot \mathbf{n}_0$ | $0$ | Scalar | Null property |
| $\mathbf{n}_\infty \cdot \mathbf{n}_\infty$ | $0$ | Scalar | Null property |
| $\mathbf{n}_0 \cdot \mathbf{n}_\infty$ | $-1$ | Scalar | Defines metric |
| $\mathbf{n}_0 \wedge \mathbf{n}_\infty$ | $\mathbf{n}_0\mathbf{n}_\infty$ | Bivector | Basis bivector |
| $\mathbf{n}_0\mathbf{n}_\infty$ | $-1 + \mathbf{n}_0 \wedge \mathbf{n}_\infty$ | Mixed | Geometric product |
| $\mathbf{n}_\infty\mathbf{n}_0$ | $-1 - \mathbf{n}_0 \wedge \mathbf{n}_\infty$ | Mixed | Note sign change |

**Table C.5: Euclidean-Null Interactions**

| Product | Result | Notes |
|---------|--------|-------|
| $\mathbf{e}_i \cdot \mathbf{n}_0$ | $0$ | Orthogonal subspaces |
| $\mathbf{e}_i \cdot \mathbf{n}_\infty$ | $0$ | Orthogonal subspaces |
| $\mathbf{e}_i\mathbf{n}_0$ | $\mathbf{e}_i \wedge \mathbf{n}_0$ | Pure outer product |
| $\mathbf{e}_i\mathbf{n}_\infty$ | $\mathbf{e}_i \wedge \mathbf{n}_\infty$ | Pure outer product |
| $\mathbf{n}_0\mathbf{e}_i$ | $-\mathbf{e}_i \wedge \mathbf{n}_0$ | Anticommutes |
| $\mathbf{n}_\infty\mathbf{e}_i$ | $-\mathbf{e}_i \wedge \mathbf{n}_\infty$ | Anticommutes |

#### Grade Structure Tables

**Table C.6: Grade Dimensions by Space**

| Space | Total Dim | Grade 0 | Grade 1 | Grade 2 | Grade 3 | Grade 4 | Grade 5 |
|-------|-----------|---------|---------|---------|---------|---------|---------|
| 2D | $2^2 = 4$ | 1 | 2 | 1 | - | - | - |
| 3D | $2^3 = 8$ | 1 | 3 | 3 | 1 | - | - |
| 4D | $2^4 = 16$ | 1 | 4 | 6 | 4 | 1 | - |
| 5D (CGA) | $2^5 = 32$ | 1 | 5 | 10 | 10 | 5 | 1 |

General formula: $\text{dim}(\Lambda^k(\mathbb{R}^n)) = \binom{n}{k}$

**Table C.7: CGA Basis Blades by Grade**

| Grade | Count | Basis Elements |
|-------|-------|----------------|
| 0 | 1 | $1$ |
| 1 | 5 | $\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3, \mathbf{n}_0, \mathbf{n}_\infty$ |
| 2 | 10 | $\mathbf{e}_{12}, \mathbf{e}_{13}, \mathbf{e}_{23}, \mathbf{e}_1\mathbf{n}_0, \mathbf{e}_2\mathbf{n}_0, \mathbf{e}_3\mathbf{n}_0, \mathbf{e}_1\mathbf{n}_\infty, \mathbf{e}_2\mathbf{n}_\infty, \mathbf{e}_3\mathbf{n}_\infty, \mathbf{n}_0\mathbf{n}_\infty$ |
| 3 | 10 | All 3-way combinations |
| 4 | 5 | All 4-way combinations |
| 5 | 1 | $I_c = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ |

#### Binary Blade Indexing Scheme

**Table C.8: Binary Index Mapping for 5D CGA**

| Blade | Binary | Decimal | Grade | Sign Computation |
|-------|--------|---------|-------|------------------|
| $1$ | 00000 | 0 | 0 | Always +1 |
| $\mathbf{e}_1$ | 00001 | 1 | 1 | +1 |
| $\mathbf{e}_2$ | 00010 | 2 | 1 | +1 |
| $\mathbf{e}_{12}$ | 00011 | 3 | 2 | +1 |
| $\mathbf{e}_3$ | 00100 | 4 | 1 | +1 |
| $\mathbf{e}_{13}$ | 00101 | 5 | 2 | +1 |
| $\mathbf{e}_{23}$ | 00110 | 6 | 2 | +1 |
| $\mathbf{e}_{123}$ | 00111 | 7 | 3 | +1 |
| $\mathbf{n}_0$ | 01000 | 8 | 1 | +1 |
| $\mathbf{n}_\infty$ | 10000 | 16 | 1 | +1 |
| $I_c$ | 11111 | 31 | 5 | Depends on ordering |

**Computational Rules:**
- Grade extraction: `grade = popcount(index)`
- Blade product indices: `index_c = index_a XOR index_b` (for orthogonal bases)
- Sign computation requires counting basis vector swaps

#### Sign Computation for Products

**Table C.9: Reordering Sign Rules**

For product of basis vectors $\mathbf{e}_{i_1}\mathbf{e}_{i_2}\cdots\mathbf{e}_{i_k}$:

1. Count inversions needed to sort indices ascending
2. Apply metric signs for any repeated indices
3. Final sign: $(-1)^{\text{inversions}} \times \text{metric signs}$

Example calculations:

| Product | Canonical Form | Swaps | Sign |
|---------|----------------|-------|------|
| $\mathbf{e}_2\mathbf{e}_1$ | $\mathbf{e}_{12}$ | 1 | $-1$ |
| $\mathbf{e}_3\mathbf{e}_1\mathbf{e}_2$ | $\mathbf{e}_{123}$ | 2 | $+1$ |
| $\mathbf{e}_2\mathbf{e}_3\mathbf{e}_1$ | $\mathbf{e}_{123}$ | 2 | $+1$ |

#### Duality Tables

**Table C.10: Pseudoscalar Properties**

| Space | Pseudoscalar $I$ | $I^2$ | Dual Formula |
|-------|------------------|-------|--------------|
| 2D | $\mathbf{e}_{12}$ | $-1$ | $A^* = -AI$ |
| 3D | $\mathbf{e}_{123}$ | $-1$ | $A^* = -AI$ |
| 4D | $\mathbf{e}_{1234}$ | $+1$ | $A^* = AI$ |
| 5D (CGA) | $\mathbf{e}_{12345}$ | $-1$ | $A^* = -AI$ |

where $\mathbf{e}_{12345} = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ in CGA with our ordering convention.

**Table C.11: Grade Duality Mapping in CGA**

| Original Grade | Dual Grade | Example |
|----------------|------------|---------|
| 0 (scalar) | 5 (pseudoscalar) | $1 \leftrightarrow I_c$ |
| 1 (vector) | 4 (4-vector) | Point $\leftrightarrow$ 4-blade |
| 2 (bivector) | 3 (trivector) | Line $\leftrightarrow$ Line* |
| 3 (trivector) | 2 (bivector) | Plane* $\leftrightarrow$ Plane |
| 4 (4-vector) | 1 (vector) | 3-sphere* $\leftrightarrow$ 3-sphere |
| 5 (pseudoscalar) | 0 (scalar) | $I_c \leftrightarrow$ 1 |

#### Commutator and Lie Algebra Structure

**Table C.12: Bivector Commutators in 3D**

The commutator $[A,B] = \frac{1}{2}(AB - BA)$ for unit bivectors:

| $[,]$ | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|-------|-------------------|-------------------|-------------------|
| **$\mathbf{e}_{23}$** | $0$ | $\mathbf{e}_{12}$ | $-\mathbf{e}_{31}$ |
| **$\mathbf{e}_{31}$** | $-\mathbf{e}_{12}$ | $0$ | $\mathbf{e}_{23}$ |
| **$\mathbf{e}_{12}$** | $\mathbf{e}_{31}$ | $-\mathbf{e}_{23}$ | $0$ |

This forms the Lie algebra $\mathfrak{so}(3)$ of the rotation group.

#### Quaternion-GA Correspondence

**Table C.13: Quaternion to Geometric Algebra Mapping**

| Quaternion | GA Element | Verification |
|------------|------------|--------------|
| $1$ | $1$ | Identity element |
| $i$ | $-\mathbf{e}_{23}$ | $i^2 = (-\mathbf{e}_{23})^2 = -1$ |
| $j$ | $-\mathbf{e}_{31}$ | $j^2 = (-\mathbf{e}_{31})^2 = -1$ |
| $k$ | $-\mathbf{e}_{12}$ | $k^2 = (-\mathbf{e}_{12})^2 = -1$ |

Hamilton's relation verification:
$$ij = (-\mathbf{e}_{23})(-\mathbf{e}_{31}) = \mathbf{e}_{23}\mathbf{e}_{31} = -\mathbf{e}_{12} = k$$

#### Computational Complexity Reference

**Table C.14: Operation Complexity Analysis**

| Operation | Naive | Optimized | Notes |
|-----------|-------|-----------|-------|
| Geometric product (general) | $O(4^n)$ | $O(2^n)$ | Using sparsity |
| Vector-vector product | $O(n)$ | $O(n)$ | Direct formula |
| Bivector-vector product | $O(n^2)$ | $O(n)$ | Structure exploitation |
| Rotor application | $O(2^{3n})$ | $O(n^2)$ | Optimized sandwich |
| Meet operation | $O(2^{2n})$ | $O(n^3)$ | Grade-targeted |
| Bivector exponential | $O(n^3)$ | $O(1)$ | Closed form |
| Dual operation | $O(2^n)$ | $O(2^n)$ | Index permutation |
| Grade projection | $O(2^n)$ | $O(\binom{n}{k})$ | Only compute grade $k$ |

---
