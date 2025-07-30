### Chapter 7: The Algebra of Incidence

Traditional computational geometry implements intersection algorithms as a proliferating collection of special cases. Line-line intersection uses parametric equations or Plücker coordinates. Line-plane intersection solves linear systems. Line-sphere intersection substitutes into quadratic equations. Sphere-sphere intersection compares center distances. A complete geometric kernel handling 10 primitive types requires approximately 45 distinct intersection algorithms, each with its own numerical characteristics, degeneracy conditions, and failure modes.

Geometric algebra provides a single operation—the meet—that computes any intersection. This unification requires approximately 160 floating-point operations compared to ~10 for specialized line-plane intersection. This chapter analyzes when architectural simplification justifies the 16× computational overhead.

### Dual Representations: Construction vs Constraint

Every geometric object in conformal GA admits two complementary representations:

**Outer Product Null Space (OPNS)**: Constructs objects from constituent elements
**Inner Product Null Space (IPNS)**: Defines objects through constraint equations

Consider a circle in 3D space. The OPNS representation constructs it from three points:
$$C = P_1 \wedge P_2 \wedge P_3$$

The IPNS representation defines it as the intersection of a sphere and plane:
$$C^* = S \wedge \pi$$

These dual views provide computational flexibility. Use OPNS when building objects from known constituents. Use IPNS when testing membership or constraints.

### The Outer Product Null Space

In OPNS, the fundamental principle states:

**A point $X$ lies on object $A$ if and only if $X \wedge A = 0$**

This algebraic condition unifies all incidence tests. The outer product with an object that already contains the point produces zero because no new geometric span is created.

#### Constructing Geometric Objects

**Line through two points:**
$$L = P_1 \wedge P_2 \wedge \mathbf{n}_\infty$$

The inclusion of $\mathbf{n}_\infty$ ensures we get a line (infinite radius circle) rather than a finite circle through the two points. Computational cost: 32 FLOPs for the 3-blade outer product in 5D conformal space.

**Circle through three points:**
$$C = P_1 \wedge P_2 \wedge P_3$$

Three points naturally define a circle. No infinity term needed. Cost: 48 FLOPs accounting for the three-way product.

**Plane through three points:**
$$\pi^* = P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty$$

Note this gives the dual form (grade 4). Apply the dual operation to get the IPNS plane representation.

**Sphere through four points:**
$$S^* = P_1 \wedge P_2 \wedge P_3 \wedge P_4$$

Again, this yields the dual form requiring transformation for IPNS use.

### The Inner Product Null Space

In IPNS, the dual principle applies:

**A point $X$ lies on object $A$ if and only if $X \cdot A = 0$**

This enables efficient membership testing through a single inner product operation.

#### Direct Object Representation

**Plane representation:**
$$\pi = \mathbf{n} + d\mathbf{n}_\infty$$

where $\mathbf{n}$ is the unit normal and $d$ is the signed distance from origin. The constraint $\pi^2 = 1$ holds for unit normals.

**Sphere representation:**
$$S = C - \frac{1}{2}r^2\mathbf{n}_\infty$$

where $C$ is the conformal center point. The sphere satisfies $S^2 = r^2$, enabling direct radius extraction.

**Point-on-plane test:**
$$P \cdot \pi = P \cdot (\mathbf{n} + d\mathbf{n}_\infty)$$

For a normalized conformal point where $P \cdot \mathbf{n}_\infty = -1$, this simplifies to the signed distance from plane.

**Point-in-sphere test:**
$$P \cdot S = \frac{1}{2}(d_{PC}^2 - r^2)$$

Negative values indicate interior points, zero lies on the sphere, positive values are exterior.

### The Duality Transform

The duality principle connects OPNS and IPNS representations through multiplication by the pseudoscalar inverse:

$$A^* = AI^{-1}$$

where $I = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ is the unit pseudoscalar of 5D conformal space.

#### Computational Analysis

The dual operation requires:
- 32 multiplications for general multivector-pseudoscalar product
- Sign determination based on grade and metric signature
- Result lives in complementary grade: grade($A^*$) = 5 - grade($A$)

For frequently-used objects, caching both representations trades memory for computation:

```
Algorithm: Lazy Dual Evaluation
Input: Geometric object with OPNS representation
Output: IPNS representation when needed

Store OPNS representation
Set IPNS_cached = false

When IPNS needed:
    if not IPNS_cached:
        IPNS = dual(OPNS)
        IPNS_cached = true
    return IPNS
```

This pattern avoids recomputing duals for objects used in multiple intersections.

### The Meet Operation

The meet computes intersections through the elegant formula:

$$A \vee B = (A^* \wedge B^*)^*$$

This three-step process unifies all intersection types:

1. **Transform to constraints**: Convert both objects to IPNS representation
2. **Combine constraints**: The wedge product creates the joint constraint system
3. **Extract result**: Transform back to standard representation

#### Detailed Computational Breakdown

For typical object pairs:

**Step 1: Dual operations** (64 FLOPs total)
- $A^*$: 32 FLOPs
- $B^*$: 32 FLOPs

**Step 2: Wedge product** (varies by grade)
- Grade 1 ∧ Grade 1 → Grade 2: 15 FLOPs
- Grade 1 ∧ Grade 2 → Grade 3: 30 FLOPs
- Grade 2 ∧ Grade 2 → Grade 4: 45 FLOPs
- Average for common cases: 64 FLOPs

**Step 3: Final dual** (32 FLOPs)
- Result extraction and normalization

Total: approximately 160 FLOPs for general intersection.

#### Intersection Results by Type

| First Object | Second Object | Meet Result | Interpretation |
|--------------|---------------|-------------|----------------|
| Plane | Plane | Line (grade 2) | Intersection line |
| Line | Plane | Point (grade 1) | Pierce point |
| Sphere | Plane | Circle (grade 2) | Intersection circle |
| Line | Line | Point or ∅ | Intersection if not skew |
| Sphere | Sphere | Circle (grade 2) | Real if intersecting |
| Line | Sphere | Point pair | Entry/exit points |

The result's grade indicates the geometric type. Zero magnitude indicates no intersection.

#### Grade Arithmetic in Higher Dimensions

In 5D conformal space, grades range from 0 to 5. The wedge product of grades $p$ and $q$ normally produces grade $p + q$. When $p + q > 5$, the Hodge dual relationship creates a "wrap-around" effect:

$$\text{grade}(A \wedge B) = |p + q - 5| \text{ when } p + q > 5$$

This occurs because the wedge of a $k$-blade with a $(5-k)$-blade produces a pseudoscalar (grade 5), and further wedging wraps to lower grades through duality.

### Numerical Conditioning Analysis

The meet operation exhibits superior numerical conditioning compared to traditional intersection methods, particularly near geometric degeneracies.

#### Parallel Plane Intersection

Consider two planes with angle $\theta$ between their normals:

**Traditional approach** (cross product of normals):
- Direction vector: $\mathbf{d} = \mathbf{n}_1 \times \mathbf{n}_2$
- Magnitude: $|\mathbf{d}| = \sin\theta$
- Relative error amplification: $\kappa \sim 1/\sin^2\theta$

**GA meet operation**:
- Bivector magnitude in $\pi_1 \wedge \pi_2$ proportional to $\sin\theta$
- Single occurrence in denominator after dualization
- Relative error amplification: $\kappa \sim 1/\sin\theta$

When $\theta = 0.01$ radians (0.57°):
- Traditional: $\kappa \approx 10,000$
- GA meet: $\kappa \approx 100$

This 100× improvement in conditioning transforms near-singular configurations from numerical disasters to manageable computations.

#### Error Propagation Through Meet

For input objects with relative error $\epsilon$:

1. **First dual**: Error amplification ≤ $\|I^{-1}\| \approx 1$ (pseudoscalar is well-conditioned)
2. **Wedge product**: Error grows by factor $1/\sin\alpha$ where $\alpha$ measures object separation
3. **Second dual**: Additional factor ≤ $\|I^{-1}\| \approx 1$

Total relative error: $O(\epsilon/\sin\alpha)$

Compare to traditional methods where squared terms appear: $O(\epsilon/\sin^2\alpha)$

### Degeneracy Detection

Geometric algebra detects degeneracies through systematic grade and magnitude analysis:

#### Collinearity Testing

Three points are collinear when their wedge product drops grade:

```
Algorithm: Collinearity Detection
Input: Points P₁, P₂, P₃
Output: Boolean indicating collinearity

blade = P₁ ∧ P₂ ∧ P₃
expected_grade = 3  // Circle
actual_grade = highest_nonzero_grade(blade)

if actual_grade < expected_grade:
    return COLLINEAR
else:
    return NOT_COLLINEAR
```

Numerical threshold: $\|\text{grade-2 component}\| > 10^{-10}$ indicates collinearity.

#### General Degeneracy Patterns

| Configuration | Operation | Normal Result | Degenerate Signal |
|---------------|-----------|---------------|-------------------|
| Coplanar lines | $L_1 \vee L_2$ | Point | Grade 2 (line) |
| Tangent spheres | $S_1 \vee S_2$ | Circle | Grade 1 (point) |
| Parallel planes | $\pi_1 \vee \pi_2$ | Line | Zero magnitude |
| Concentric spheres | $S_1 \vee S_2$ | Circle | Imaginary radius |

### The Join Operation

The join constructs the smallest geometric object containing the given elements:

$$\text{join}(A, B) = A \wedge B \text{ when } A \cap B = \emptyset$$

For disjoint objects, join equals the outer product. When objects overlap, the result requires careful interpretation based on grade analysis.

#### Join Catalog

| First Object | Second Object | Join Result | Geometric Meaning |
|--------------|---------------|-------------|-------------------|
| Point | Point | Line | Unique line through both |
| Point | Line | Plane | Unique plane containing both |
| Point | Plane | 3-space | Entire 3D space |
| Line | Line (skew) | 4-blade | No common 3D subspace |
| Line | Line (intersecting) | Plane | Unique plane containing both |

### Lattice Structure and Computational Applications

The meet and join operations endow geometric objects with a lattice structure, enabling systematic geometric reasoning:

$$A \leq B \iff A \vee B = B \iff A \wedge B = A$$

This partial ordering has direct computational applications:

**Constraint Systems**: Objects form a hierarchy where $A \leq B$ means every point satisfying constraint $A$ also satisfies $B$. Automated theorem provers exploit this structure.

**Geometric Queries**: Testing whether a line lies in a plane reduces to checking $L \leq \pi$ via the meet operation.

**Boolean Operations**: For objects $A$ and $B$:
- Intersection: $A \cap B \sim A \vee B$
- Union: $A \cup B \sim A \wedge B$ (when applicable)
- Containment: $A \subseteq B \iff A \leq B$

These operations enable CSG (Constructive Solid Geometry) implementations using GA primitives.

### Implementation Strategies

#### When to Use Meet

The meet operation provides value when:
- System handles >5 geometric primitive types
- Uniform degeneracy handling prevents bugs
- Code maintainability outweighs performance
- Numerical robustness near singularities matters

#### When to Use Specialized Algorithms

Traditional methods excel when:
- Performance dominates (ray tracing inner loops)
- System uses 2-3 primitive types only
- Existing optimized implementations available
- Memory constraints prohibit dual caching

#### Hybrid Architecture

Mature systems often combine approaches:
- GA for high-level geometric reasoning
- Specialized algorithms for performance-critical paths
- Meet operation for complex/rare intersections
- Traditional methods for simple/frequent cases

### Summary

The algebra of incidence unifies intersection computation through the meet operation at 16× typical computational cost. For systems where architectural complexity from algorithm proliferation exceeds performance concerns, this unification provides genuine engineering value through:

- Single algorithm replacing dozens
- Systematic degeneracy detection
- Superior numerical conditioning near singularities
- Uniform treatment of all geometric primitives

The lattice structure enables automated geometric reasoning, while the duality between construction (OPNS) and constraint (IPNS) representations provides computational flexibility. Engineers must evaluate whether their specific system's complexity justifies the computational overhead of universal geometric operations.

---

### TODO: Tables for Appendix

The following comprehensive reference tables should be moved to Appendix C:

1. **Complete OPNS/IPNS Correspondence Table** (originally Table 25)
   - All geometric objects in both representations
   - Grade relationships and duality mappings
   - Storage requirements for each form

2. **Extended Meet Operation Results** (originally Table 26)
   - All possible object pair combinations
   - Degenerate case outcomes
   - Computational complexity for each case
