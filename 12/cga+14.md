### Chapter 14: Graphics—Architecture Over Performance

The graphics pipeline represents geometric algebra's most challenging battlefield. Every GPU manufactured optimizes for 4×4 matrix operations. Every shader language assumes vector-matrix mathematics. Every graphics engineer learned quaternions and homogeneous coordinates. Against this entrenched ecosystem, geometric algebra offers not speed but clarity—a trade worth making only when architectural benefits justify disrupting decades of optimization.

#### Matrix Lock-In and Its Discontents

Modern graphics hardware enforces a mathematical monoculture. The vertex shader receives a cascade of matrices:

$$\mathbf{v}_{\text{clip}} = \mathbf{P} \cdot \mathbf{V} \cdot \mathbf{M} \cdot \mathbf{v}_{\text{model}}$$

where $\mathbf{M}$ transforms from model to world space, $\mathbf{V}$ from world to view space, and $\mathbf{P}$ projects to clip coordinates. This pipeline achieved ubiquity through hardware acceleration—modern GPUs process these 4×4 operations in parallel across thousands of cores.

Yet this optimization comes with architectural costs. Consider implementing a simple reflection:

**Traditional approach:**
- Construct reflection matrix (16 floats)
- Ensure orthogonality through Gram-Schmidt
- Apply to all vertex types identically
- Hope numerical errors don't accumulate

**GA approach:**
$$\mathbf{v}' = -\mathbf{n}\mathbf{v}\mathbf{n}$$

The plane normal $\mathbf{n}$ directly encodes the reflection. No matrix construction. No orthogonality concerns. The same operation reflects points, lines, planes, and more complex geometric entities consistently.

#### Projective Geometric Algebra for Graphics

While the book emphasizes conformal geometric algebra, graphics applications often benefit more from projective geometric algebra (PGA). The key insight: graphics already uses homogeneous coordinates, making PGA a natural fit.

In 3D PGA with signature $(3,0,1)$:
- Points: $P = x\mathbf{e}_1 + y\mathbf{e}_2 + z\mathbf{e}_3 + w\mathbf{e}_0$
- Planes: $\pi = a\mathbf{e}_{023} + b\mathbf{e}_{031} + c\mathbf{e}_{012} + d\mathbf{e}_{123}$
- Lines: Bivectors with 6 Plücker coordinates

The join of two points yields their connecting line:
$$L = P_1 \wedge P_2$$

The meet of two planes yields their intersection line:
$$L = \pi_1 \vee \pi_2$$

These operations naturally handle "points at infinity" that cause special cases in traditional formulations. When two planes are parallel, their meet yields a line at infinity—a perfectly valid PGA object—rather than numerical instability.

#### Real Performance Numbers

The klein library demonstrates that specialized PGA implementations can compete with traditional graphics math. Benchmarks against GLM (a widely-used C++ graphics math library) show:

- Rotor composition: **1.2× faster** than quaternion multiplication
- Point transformation: **0.9× speed** (10% slower)
- Ray-plane intersection: **1.1× speed** (comparable)

These numbers assume SSE4 vectorization for both libraries. The key: klein focuses exclusively on 3D PGA, enabling hand-optimized SIMD implementations. A general-purpose GA library would be 5-10× slower.

#### Where Architecture Wins

Despite raw performance parity being achievable only in specialized cases, GA provides architectural advantages that justify its use in specific graphics scenarios:

**Unified Intersection Testing**

A game engine might contain:
```
intersectRayTriangle(...)
intersectRaySphere(...)
intersectRayBox(...)
intersectLinePlane(...)
// ... dozens more
```

In GA, these collapse to variations of the meet operation. The code complexity reduction is dramatic—one algorithm kernel replaces dozens. This matters when:
- Building a new engine from scratch
- Debugging intersection edge cases
- Adding novel primitive types

**Robust Clipping**

The Sutherland-Hodgman polygon clipping algorithm struggles with numerical edge cases. In PGA, clipping against a plane $\pi$ reduces to:
1. Compute signed distance: $d = P \cdot \pi$
2. Vertices with $d < 0$ lie outside
3. Edge intersections via: $(P_1 \wedge P_2) \vee \pi$

The algorithm naturally handles edges parallel to the clipping plane—they produce points at infinity that subsequent operations handle correctly.

**Quaternion-Free Rotations**

Graphics programmers manage a complex dance between matrices (for GPUs), quaternions (for interpolation), and Euler angles (for artist interfaces). GA rotors unify these representations:

$$R = \cos\frac{\theta}{2} + \sin\frac{\theta}{2}B$$

where $B$ is a unit bivector. Rotor composition is simple multiplication. Interpolation uses the exponential map:

$$R(t) = \exp(t\log(R))$$

No gimbal lock. No quaternion double-cover confusion. No matrix orthogonalization.

#### The Shader Problem

GA's benefits evaporate inside GPU shaders. Modern shading languages provide no bivector types, no wedge products, no geometric algebra operations. Implementing GA in shader code would require:

- Storing multivectors as float arrays
- Implementing products via explicit index manipulation
- Fighting the optimizer that assumes matrix-vector semantics

The overhead makes this impractical for real-time rendering. GA must remain CPU-side, transforming to matrices before GPU submission. This boundary limits GA's penetration into the graphics pipeline.

#### Successful Hybrid Architectures

Production systems successfully using GA in graphics adopt hybrid architectures:

**Scene Graph with GA, Rendering with Matrices**
- Scene representation uses motors for poses
- Intersection queries use meet operations
- Final frame: convert to 4×4 matrices for GPU

**Offline GA, Online Traditional**
- Precompute complex geometric relationships with GA
- Bake results into traditional formats
- Runtime uses optimized matrix paths

**Example: Skinned Character Animation**

The ganja.js library demonstrates GA-based character skinning:
1. Skeleton joints as motors: $M_i = T_i R_i$
2. Blend motors for smooth deformation: $M = \sum w_i M_i$
3. Convert to dual quaternions for GPU skinning

This approach claims 16% performance improvement over pure dual quaternion skinning by eliminating conversion overhead in the animation pipeline.

#### When Not to Use GA in Graphics

Clarity requires acknowledging where GA fails in graphics:

**Ray Tracing Production Renderers**: When Intel Embree provides hand-optimized ray-triangle intersection using AVX-512 instructions, GA's 5× overhead is unacceptable. Billions of intersection tests demand maximum throughput.

**Mobile/WebGL Graphics**: Memory bandwidth limitations make 32-component multivectors prohibitive. The ecosystem assumes 16-float matrices maximum.

**Shader-Heavy Pipelines**: If most computation occurs in shaders, CPU-side GA provides minimal benefit while adding conversion overhead.

**Legacy System Integration**: Introducing GA into established engines requires extensive interface code. The architectural benefits rarely justify the migration cost.

#### Future Directions

GA in graphics may find renewed relevance through:

**Neural Rendering**: Differentiable rendering benefits from GA's unified derivatives. The meet operation has consistent gradients across all primitive types, unlike special-case intersection code.

**VR/AR Frameworks**: Head tracking naturally uses motors. Network protocols could transmit compact motor updates rather than separate position/orientation packets.

**Non-Euclidean Rendering**: Hyperbolic or spherical spaces for VR experiences map naturally to GA with appropriate signatures. Traditional matrix approaches require extensive special-casing.

#### Engineering Decision Framework

Choose GA for graphics when:
- Building new engines where architectural clarity matters
- Robustness near geometric degeneracies is critical
- Novel geometric primitives require unified handling
- Development velocity outweighs runtime performance

Avoid GA for graphics when:
- Integrating with existing matrix-based pipelines
- Performance requirements leave no overhead room
- GPU computation dominates the workload
- Team expertise centers on traditional graphics math

The graphics domain exemplifies GA's fundamental trade-off: elegant architecture versus raw performance. In a field where milliseconds matter, GA finds its niche not in the inner loops but in the outer structure—organizing scene graphs, handling complex intersections, and providing geometric insight that survives compilation to traditional forms.
