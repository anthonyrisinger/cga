### Appendix C: Computational Reference Tables

#### Metric Signatures and Algebras

| Space | Signature $(p,q,r)$ | Notation | Basis Squares | Applications |
|-------|---------------------|----------|---------------|--------------|
| Euclidean 2D | $(2,0,0)$ | $\mathbb{R}^2$ | $(+1,+1)$ | Complex numbers, planar rotations |
| Euclidean 3D | $(3,0,0)$ | $\mathbb{R}^3$ | $(+1,+1,+1)$ | Quaternions, spatial rotations |
| Minkowski | $(1,3,0)$ | $\mathbb{R}^{1,3}$ | $(+1,-1,-1,-1)$ | Special relativity† |
| Minkowski | $(3,1,0)$ | $\mathbb{R}^{3,1}$ | $(+1,+1,+1,-1)$ | Spacetime algebra† |
| Conformal 2D | $(3,1,0)$ | $\mathbb{R}^{3,1}$ | $(+1,+1,+1,-1)$ | Inversive plane geometry |
| Conformal 3D | $(4,1,0)$ | $\mathbb{R}^{4,1}$ | $(+1,+1,+1,+1,-1)$ | CGA for 3D space |
| Projective 3D | $(3,0,1)$ | $\mathbb{R}^{3,0,1}$ | $(+1,+1,+1,0)$ | Homogeneous coordinates |

†Particle physics uses $(1,3,0)$; general relativity uses $(3,1,0)$

#### Geometric Product Tables

**2D Euclidean Algebra $\mathcal{G}(\mathbb{R}^2)$**

| | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
|---|-----|----------------|----------------|-------------------|
| $1$ | $1$ | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_{12}$ |
| $\mathbf{e}_1$ | $\mathbf{e}_1$ | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_2$ |
| $\mathbf{e}_2$ | $\mathbf{e}_2$ | $-\mathbf{e}_{12}$ | $1$ | $-\mathbf{e}_1$ |
| $\mathbf{e}_{12}$ | $\mathbf{e}_{12}$ | $-\mathbf{e}_2$ | $\mathbf{e}_1$ | $-1$ |

The even subalgebra $\{1, \mathbf{e}_{12}\}$ forms the complex numbers: $\mathbf{e}_{12} \leftrightarrow i$.

**3D Vector Products**

| | $\mathbf{e}_1$ | $\mathbf{e}_2$ | $\mathbf{e}_3$ |
|---|----------------|----------------|----------------|
| $\mathbf{e}_1$ | $1$ | $\mathbf{e}_{12}$ | $\mathbf{e}_{13}$ |
| $\mathbf{e}_2$ | $-\mathbf{e}_{12}$ | $1$ | $\mathbf{e}_{23}$ |
| $\mathbf{e}_3$ | $-\mathbf{e}_{13}$ | $-\mathbf{e}_{23}$ | $1$ |

**3D Bivector Products**

| | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|---|-------------------|-------------------|-------------------|
| $\mathbf{e}_{23}$ | $-1$ | $-\mathbf{e}_{12}$ | $\mathbf{e}_{31}$ |
| $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ | $-1$ | $-\mathbf{e}_{23}$ |
| $\mathbf{e}_{12}$ | $-\mathbf{e}_{31}$ | $\mathbf{e}_{23}$ | $-1$ |

The even subalgebra $\{1, \mathbf{e}_{23}, \mathbf{e}_{31}, \mathbf{e}_{12}\}$ forms the quaternions.

#### Conformal Basis Structure

**Null Vector Relations**

| Product | Result | Type | Significance |
|---------|--------|------|--------------|
| $\mathbf{n}_0^2$ | $0$ | Scalar | Null at origin |
| $\mathbf{n}_\infty^2$ | $0$ | Scalar | Null at infinity |
| $\mathbf{n}_0 \cdot \mathbf{n}_\infty$ | $-1$ | Scalar | Metric normalization |
| $\mathbf{n}_0 \wedge \mathbf{n}_\infty$ | $\mathbf{n}_0\mathbf{n}_\infty$ | Bivector | Dilation generator |
| $\mathbf{n}_0\mathbf{n}_\infty$ | $-1 + \mathbf{n}_0\mathbf{n}_\infty$ | Mixed | Geometric product |

**Euclidean-Conformal Orthogonality**

All Euclidean basis vectors are orthogonal to both null vectors:
- $\mathbf{e}_i \cdot \mathbf{n}_0 = 0$
- $\mathbf{e}_i \cdot \mathbf{n}_\infty = 0$
- $\mathbf{e}_i\mathbf{n}_0 = \mathbf{e}_i \wedge \mathbf{n}_0$ (pure bivector)
- $\mathbf{e}_i\mathbf{n}_\infty = \mathbf{e}_i \wedge \mathbf{n}_\infty$ (pure bivector)

#### Dimension and Grade Structure

| Space | Dimension | Grade Distribution | Total Elements |
|-------|-----------|-------------------|----------------|
| $\mathbb{R}^2$ | 2 | $[1, 2, 1]$ | $2^2 = 4$ |
| $\mathbb{R}^3$ | 3 | $[1, 3, 3, 1]$ | $2^3 = 8$ |
| $\mathbb{R}^4$ | 4 | $[1, 4, 6, 4, 1]$ | $2^4 = 16$ |
| $\mathbb{R}^{4,1}$ | 5 | $[1, 5, 10, 10, 5, 1]$ | $2^5 = 32$ |

Grade-$k$ dimension: $\binom{n}{k}$. The symmetry in dimensions reflects Hodge duality.

#### Binary Blade Representation

**Encoding Scheme**

| Blade | Binary | Decimal | Grade | Parity |
|-------|--------|---------|-------|--------|
| $1$ | 00000 | 0 | 0 | even |
| $\mathbf{e}_1$ | 00001 | 1 | 1 | odd |
| $\mathbf{e}_2$ | 00010 | 2 | 1 | odd |
| $\mathbf{e}_{12}$ | 00011 | 3 | 2 | even |
| $\mathbf{e}_3$ | 00100 | 4 | 1 | odd |
| $\mathbf{e}_{123}$ | 00111 | 7 | 3 | odd |
| $\mathbf{n}_0$ | 01000 | 8 | 1 | odd |
| $\mathbf{n}_\infty$ | 10000 | 16 | 1 | odd |
| $I_c$ | 11111 | 31 | 5 | odd |

**Computational Algorithms**

Grade extraction: `grade(blade) = popcount(blade.index)`

Product of orthogonal blades: `(a ∧ b).index = a.index XOR b.index`

Sign computation: Count inversions in basis vector sequence

Storage optimization: Group by grade, sort by index within grade

#### Sign Computation Rules

For product $\mathbf{e}_{i_1}\mathbf{e}_{i_2}\cdots\mathbf{e}_{i_k}$:

1. Count swaps to achieve ascending order
2. Sign = $(-1)^{\text{swaps}}$
3. Apply metric signature for repeated indices

**Examples**

| Product | Canonical Form | Swaps | Result |
|---------|----------------|-------|--------|
| $\mathbf{e}_2\mathbf{e}_1$ | $\mathbf{e}_1\mathbf{e}_2$ | 1 | $-\mathbf{e}_{12}$ |
| $\mathbf{e}_3\mathbf{e}_1\mathbf{e}_2$ | $\mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ | 2 | $+\mathbf{e}_{123}$ |
| $\mathbf{e}_2\mathbf{e}_3\mathbf{e}_1$ | $\mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ | 2 | $+\mathbf{e}_{123}$ |

#### Duality and Pseudoscalars

| Space | Pseudoscalar $I$ | $I^2$ | Grade | Duality Map |
|-------|------------------|-------|-------|-------------|
| $\mathbb{R}^2$ | $\mathbf{e}_{12}$ | $-1$ | 2 | $A^* = -AI$ |
| $\mathbb{R}^3$ | $\mathbf{e}_{123}$ | $-1$ | 3 | $A^* = -AI$ |
| $\mathbb{R}^4$ | $\mathbf{e}_{1234}$ | $+1$ | 4 | $A^* = AI$ |
| $\mathbb{R}^{4,1}$ | $\mathbf{e}_{12345}$ | $+1$ | 5 | $A^* = AI$ |

**Grade-Dual Correspondence (5D CGA)**

| Original | Dual | Example Transformation |
|----------|------|------------------------|
| 0 | 5 | Scalar ↔ Pseudoscalar |
| 1 | 4 | Point ↔ Hypersphere |
| 2 | 3 | Line ↔ Plane |
| 3 | 2 | Plane ↔ Line |
| 4 | 1 | Hypersphere ↔ Point |
| 5 | 0 | Pseudoscalar ↔ Scalar |

#### Lie Algebra Structure

**3D Bivector Commutators**

| $[A,B]$ | $\mathbf{e}_{23}$ | $\mathbf{e}_{31}$ | $\mathbf{e}_{12}$ |
|---------|-------------------|-------------------|-------------------|
| $\mathbf{e}_{23}$ | $0$ | $\mathbf{e}_{12}$ | $-\mathbf{e}_{31}$ |
| $\mathbf{e}_{31}$ | $-\mathbf{e}_{12}$ | $0$ | $\mathbf{e}_{23}$ |
| $\mathbf{e}_{12}$ | $\mathbf{e}_{31}$ | $-\mathbf{e}_{23}$ | $0$ |

Forms the Lie algebra $\mathfrak{so}(3)$. Structure constants match $\epsilon_{ijk}$.

#### Quaternion-Clifford Correspondence

| Quaternion | Clifford Element | Properties |
|------------|------------------|------------|
| $1$ | $1$ | Scalar identity |
| $i$ | $-\mathbf{e}_{23}$ | $i^2 = -1$ |
| $j$ | $-\mathbf{e}_{31}$ | $j^2 = -1$ |
| $k$ | $-\mathbf{e}_{12}$ | $k^2 = -1$ |

Hamilton relations verified:
- $ij = (-\mathbf{e}_{23})(-\mathbf{e}_{31}) = \mathbf{e}_{2331} = -\mathbf{e}_{12} = k$ ✓
- $jk = (-\mathbf{e}_{31})(-\mathbf{e}_{12}) = \mathbf{e}_{3112} = -\mathbf{e}_{23} = i$ ✓
- $ki = (-\mathbf{e}_{12})(-\mathbf{e}_{23}) = \mathbf{e}_{1223} = -\mathbf{e}_{31} = j$ ✓

#### Computational Complexity

| Operation | General Complexity | Optimized | Sparse Factor |
|-----------|-------------------|-----------|---------------|
| Geometric product | $O(4^n)$ | $O(g^2)$ for grade $g$ | $O(s^2)$ for $s$ non-zero |
| Grade projection | $O(2^n)$ scan | $O(\binom{n}{k})$ direct | — |
| Dual operation | $O(2^n)$ | $O(2^n)$ permutation | Index bit reversal |
| Rotor application | $O(8^n)$ naive | $O(n^2)$ targeted | Grade preservation |
| Meet operation | $O(2^{2n})$ | $O(n^3)$ structured | Dimension dependent |
| Exponential (bivector) | $O(n^3)$ series | $O(1)$ closed form | Exact for $B^2 = \pm\theta^2$ |
| Inverse (blade) | $O(2^{2n})$ general | $O(2^n)$ for versors | $O(1)$ for normalized |

**Storage Strategies**

Dense array: Direct indexing, $2^n$ elements, fast random access

Sparse map: (index, coefficient) pairs, $O(s)$ storage for $s$ non-zero

Graded storage: Separate arrays per grade, optimizes grade operations

Blade factorization: Store as product of vectors, $O(k)$ for $k$-blade
