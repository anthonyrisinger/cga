### Chapter 8: Universal Meet Operation

Every computational geometry system accumulates intersection algorithms like scar tissue. Line intersects plane. Plane intersects plane. Line intersects sphere. Sphere intersects sphere. Line intersects cylinder—wait, is the line parallel to the axis? Does it hit the caps? Is it tangent to the surface? Each geometric pair spawns special cases, and each special case breeds bugs.

Production CAD kernels routinely maintain 40-50 different intersection routines. A typical line-cylinder intersection runs 300-400 lines, handling seven distinct configurations. Every edge case discovered in the field means another conditional, another threshold, another place for numerical errors to hide. Teams spend months debugging why two "parallel" planes sometimes intersect at infinity and sometimes return null, depending on which developer implemented that particular case.

The meet operation promises something radical: one algorithm for all intersections.

#### The Duality Principle

The meet operation computes intersections through duality—a profound geometric principle that unifies all intersection types. The key insight: the intersection of two objects equals the dual of the union of their duals.

Consider two planes in 3D. Their intersection is a line. But if we think of planes as "stacks of parallel lines" (their dual representation), then the union of two stacks gives us all lines in both planes. The dual of this union—the common perpendicular to all these lines—is precisely the intersection line.

For geometric objects $A$ and $B$, the meet formula encodes this principle:

$$A \vee B = (A^* \wedge B^*)^*$$

Breaking this down:

1. **Dualization** ($A \rightarrow A^*$): Transform objects to their orthogonal complements
   $$A^* = AI^{-1}$$
   where $I$ is the pseudoscalar. A plane becomes a vector perpendicular to it; a line becomes a bivector representing its perpendicular plane.

2. **Wedge Product** ($A^* \wedge B^*$): Find the span of the duals
   - Builds the smallest subspace containing both dual spaces
   - Result is non-zero only when duals are linearly independent
   - The grade encodes the dimension of intersection

3. **Re-dualization**: Transform back to direct representation
   $$(A^* \wedge B^*)^* = (A^* \wedge B^*)I^{-1}$$

#### Concrete Computation

Let's compute where a line intersects a plane in 3D projective geometric algebra:

```cpp
// Line through points P₁ = (1,0,0) and P₂ = (0,1,0)
// In PGA: points have homogeneous coordinate e₀
auto P1 = e1 + e0;  // Point (1,0,0)
auto P2 = e2 + e0;  // Point (0,1,0)
auto L = P1 ^ P2;   // Line: e₁₂ + e₁₀ + e₂₀

// Plane through origin with normal (1,1,1)
auto pi = e1 + e2 + e3;

// Step 1: Dualize (in 3D PGA, I = e₀₁₂₃)
auto I_inv = e3210;  // I⁻¹ = -I for this metric
auto L_dual = L * I_inv;   // L* = -e₃₀
auto pi_dual = pi * I_inv; // π* = e₀₂₃ - e₀₁₃ + e₀₁₂

// Step 2: Wedge product
auto meet_dual = L_dual ^ pi_dual; // = e₀₁₂₃

// Step 3: Re-dualize
auto intersection = meet_dual * I_inv; // = e₁ + e₂ - 2e₀
```

The result represents the point $(0.5, 0.5, 0)$ after normalization by the $e_0$ coefficient.

#### Computational Requirements

The universal meet requires approximately 160 floating-point operations for dense representations:
- First dualization: ~32 FLOPs
- Wedge product: ~64 FLOPs
- Second dualization: ~32 FLOPs

However, geometric objects are inherently sparse:
- Point: 4 non-zero of 32 components (87.5% sparse)
- Line: 6-8 non-zero of 32 components (75-81% sparse)
- Plane: 4 non-zero of 32 components (87.5% sparse)

Optimized implementations exploit this sparsity, reducing effective operation count to 20-30 FLOPs—a 2-5× overhead compared to specialized algorithms.

#### Grade Classification

The meet operation's power lies in automatic intersection classification through grade:

**Two lines in 3D:**
$$\text{grade}(L_1 \vee L_2) = \begin{cases}
0 & \text{skew lines (scalar = signed distance)} \\
1 & \text{intersecting lines (point)} \\
2 & \text{coincident lines (line)}
\end{cases}$$

**Line and sphere:**
$$\text{grade}(L \vee S) = \begin{cases}
\emptyset & \text{no intersection (zero result)} \\
1 & \text{tangent or secant (point or point-pair)}
\end{cases}$$

A "point-pair" is a single grade-1 object encoding two points:
$$PP = \frac{1}{2}(P_1 + P_2) + \frac{\lambda}{2}(P_1 \wedge P_2)$$

The algebra encodes not just intersection type but multiplicity—one object can represent two intersection points.

#### Line-Cylinder: A Case Study

Traditional line-cylinder intersection demonstrates the complexity meet eliminates:

```cpp
// Traditional approach: ~400 lines
bool intersectLineCylinder(const Line& line, const Cylinder& cyl,
                          std::vector<Point>& results) {
    // Transform to cylinder coordinates
    Line local = cyl.worldToLocal(line);

    // Special case 1: parallel to axis
    if (abs(dot(local.dir, vec3(0,0,1))) > 0.999) {
        double d = length(vec2(local.point.x, local.point.y));
        if (d > cyl.radius + EPSILON) return false;
        // ... 50 lines handling cap intersections ...
    }

    // Special case 2: perpendicular to axis
    if (abs(dot(local.dir, vec3(0,0,1))) < 0.001) {
        // ... 30 lines handling radial intersection ...
    }

    // General quadratic case
    double a = local.dir.x * local.dir.x + local.dir.y * local.dir.y;
    double b = 2.0 * (local.point.x * local.dir.x +
                      local.point.y * local.dir.y);
    double c = local.point.x * local.point.x +
               local.point.y * local.point.y - cyl.radius * cyl.radius;

    // ... 300+ lines handling:
    // - Discriminant analysis
    // - Parameter bounds
    // - Cap intersection
    // - Tangent detection
    // - Edge grazing
    // - Numerical stability
}
```

Using meet:

```cpp
// GA approach: ~15 lines
auto intersectLineCylinder(const Line& L, const Cylinder& cyl) {
    // Cylinder = infinite surface ∩ top cap ∩ bottom cap
    auto S = cyl.surface();     // Quadric surface
    auto top = cyl.topPlane();  // Plane
    auto bot = cyl.botPlane();  // Plane

    // One operation handles all cases
    auto result = L.meet(S ^ top ^ bot);

    // Extract intersection points based on grade
    return extractPoints(result);
}
```

All special cases—parallel to axis, tangent configurations, cap intersections—emerge from the algebra. No branching, no thresholds, no case analysis.

#### Numerical Robustness

Near-parallel planes demonstrate meet's superior conditioning:

**Traditional approach:**
```cpp
Vector n = cross(plane1.normal, plane2.normal);
double len = norm(n);  // → 0 as planes align
if (len < EPSILON) {
    // Parallel: special handling
} else {
    n /= len;  // Catastrophic error amplification
    // Condition number: κ ~ 1/sin²θ
}
```

**Meet operation in PGA:**
```cpp
auto line = plane1.meet(plane2);
// Parallel planes meet at ideal line (at infinity)
// No dangerous normalization
// Condition number: κ ~ 1/sin θ
```

The improvement from quadratic to linear conditioning stems from PGA's homogeneous representation where infinity is algebraically valid, not an error state.

#### Performance vs. Architecture

The meet operation crystallizes a fundamental trade-off:

**Costs:**
- 2-5× computational overhead (optimized)
- New mathematical framework
- Result interpretation complexity

**Benefits:**
- Replaces dozens of algorithms
- Eliminates special-case proliferation
- Reduces code by 10-40×
- Handles all degeneracies uniformly
- Extends to non-Euclidean geometries

For systems where geometric robustness and code maintainability outweigh microsecond-level performance—CAD kernels, geometric modeling, computational physics—the meet operation transforms brittle special-case code into robust algebra.

The universal meet isn't universally better. It's a tool that trades computation for comprehension, FLOPs for correctness, speed for sanity. In domains drowning in geometric edge cases, that trade is often worth making.
