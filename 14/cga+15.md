### Chapter 15: Graphics—Architecture Over Performance

Modern graphics hardware enforces mathematical monoculture. Every GPU manufactured since the late 1990s optimizes for 4×4 matrix operations. Vertex shaders expect them. Fragment shaders assume them. Ray tracing cores accelerate them. This isn't mere convention—it's silicon.

Against this reality, geometric algebra offers not speed but structural clarity. The trade-off is stark: accept performance penalties for architectural benefits, or stay with matrices. For most graphics applications, the choice is predetermined by hardware. But specific niches reveal where GA's clarity justifies bucking the system.

#### The Lock-In Reality

The modern graphics pipeline crystallized around homogeneous coordinates and matrix transformations:

$$\mathbf{v}_{\text{clip}} = \mathbf{P} \cdot \mathbf{V} \cdot \mathbf{M} \cdot \mathbf{v}_{\text{model}}$$

This equation drives billions of GPU calculations per second. The lock-in extends beyond mathematical convention:

- **Silicon Design**: Texture interpolation units process 4-component vectors, not 32-component multivectors
- **Memory Architecture**: 64-byte cache lines align perfectly with 4×4 matrices, not CGA's 128-byte points
- **Instruction Sets**: GPU SIMD operates on float4 types; no native support for bivector operations
- **API Design**: Vulkan's push constants, DirectX's constant buffers—all sized for matrices

Consider a simple reflection. Traditional graphics computes:

$$\mathbf{v}' = \mathbf{v} - 2(\mathbf{v} \cdot \mathbf{n})\mathbf{n}$$

This requires 9 floating-point operations: 3 for the dot product, 3 for the scale, 3 for the subtraction. GPUs execute this in 2-3 cycles.

The GA formulation is algebraically cleaner:

$$\mathbf{v}' = -\mathbf{n}\mathbf{v}\mathbf{n}$$

But computing this sandwich product requires ~18 operations without specialized hardware. On current GPUs, that's 6-8 cycles—a 3× penalty for elegance. **Why does this matter?** For a shader executing millions of times per frame, 3× slower means dropping from 60 FPS to 20 FPS. Elegance doesn't justify unplayable games.

#### Projective Geometric Algebra's Natural Fit

While conformal geometric algebra offers rich structure for general geometry, graphics benefits more from projective geometric algebra (PGA). The key insight: graphics already uses homogeneous coordinates, making P(ℝ³) a natural evolution rather than revolution.

In traditional homogeneous coordinates, a 3D point is represented as a 4-vector (x, y, z, w). PGA embeds this same point as:

$$P = x\mathbf{e}_{032} + y\mathbf{e}_{013} + z\mathbf{e}_{021} + w\mathbf{e}_{123}$$

**Why this exotic basis?** Each basis element represents a plane:
- $\mathbf{e}_{032}$: the x = 0 plane (yz-plane through origin)
- $\mathbf{e}_{013}$: the y = 0 plane (xz-plane through origin)
- $\mathbf{e}_{021}$: the z = 0 plane (xy-plane through origin)
- $\mathbf{e}_{123}$: the plane at infinity

A point is the intersection of these four planes—its coordinates tell you how far it is from each. This isn't just mathematical cleverness; it means operations like "line through two points" become algebraic:

$$L = P_1 \vee P_2$$

**Why does this matter?** Traditional graphics handles special cases with code branches:

```cpp
// Traditional approach - special case handling
bool intersect_planes(const Plane& p1, const Plane& p2, Line& result) {
    Vector3 dir = cross(p1.normal, p2.normal);
    float det = dot(dir, dir);

    if (det < EPSILON) {  // Parallel planes - special case!
        return false;      // No finite intersection
    }

    // Complex formula for finite intersection line
    Point origin = (cross(dir, p2.normal) * p1.d +
                    cross(p1.normal, dir) * p2.d) / det;
    result = Line(origin, dir);
    return true;
}
```

In PGA, parallel planes meet at an ideal line—no special case:

$$\pi_1 \vee \pi_2 = \begin{cases}
L & \text{if planes intersect} \\
L_\infty & \text{if planes parallel}
\end{cases}$$

The algebra handles both cases uniformly. **The engineering value**: fewer code paths means fewer bugs. When your clipping algorithm handles parallel planes without special cases, it's more robust.

#### Real Performance Numbers

The Klein library (archived in 2024 but instructive for its design) demonstrated what specialized PGA can achieve:

- **Rotor composition**: 4.1ms for 1M operations (vs GLM quaternion: 4.2ms)
- **Point transformation**: 4.5ms for 1M operations (vs GLM matrix: 4.0ms)
- **Ray-plane intersection**: 2.1ms for 1M operations (vs traditional: 1.9ms)

These near-parity results required:
- Restriction to 3D PGA only (no general multivectors)
- Hand-written SSE4 intrinsics
- Structure-of-arrays memory layout
- Compile-time knowledge of operation types

**Why these optimizations matter**: Klein achieved parity by sacrificing GA's generality. You can't use Klein for 2D graphics, or 4D simulations, or conformal geometry. It does one thing—3D PGA—extremely well.

A general GA library shows different performance:
- Generic geometric product: 5-10× slower than specialized operations
- Dense 32-component storage: 4× more cache misses
- Runtime grade detection: Additional overhead per operation

**The lesson**: GA can match traditional performance only through extreme specialization that sacrifices its unifying power.

#### Why Motors Preserve Volume in Skinning

Linear blend skinning suffers from the "candy wrapper" effect—volumes collapse during twisting. **Why?** Linear interpolation of transformation matrices doesn't preserve geometric constraints:

$$M_{\text{blend}} = \sum_i w_i M_i \quad \text{(not a valid transformation!)}$$

The blended result isn't even a rigid transformation anymore. Traditional solutions add complexity—dual quaternions, optimized centers of rotation, corrective blend shapes.

GA motors unify rotation and translation as a single screw motion:

$$M = \exp\left(-\frac{1}{2}(\theta\mathbf{L} + d\mathbf{n}_\infty)\right)$$

Where $\mathbf{L}$ is the screw axis bivector and $d$ is translation along it. **Why is this better?** The exponential map ensures interpolation follows the screw motion manifold:

$$M(t) = M_0 \exp(t\log(M_0^{-1}M_1))$$

Intermediate poses follow helical paths—the natural motion of rigid bodies. No candy wrapper collapse because the motion remains rigid throughout interpolation.

**The practical result**: GA-Unity's implementation demonstrated 16% runtime improvement over dual quaternion skinning. Not from faster math—motors require more operations—but from needing fewer corrective iterations to maintain volume. One correct calculation beats multiple corrections.

#### The Shader Impossibility

GA's architectural benefits evaporate inside GPU shaders. **Why?** Shaders lack:
- Multivector types (only float, float2, float3, float4)
- Geometric product operations (only dot, cross)
- Dynamic memory allocation (fixed registers)
- Complex control flow (divergence kills performance)

Consider implementing bivector rotation in a shader:

```cpp
// CPU-side GA: clean, abstract
Rotor R = exp(-angle/2 * e12);
Point p_rotated = R * p * ~R;

// GPU shader reality: manual expansion
float3 rotate_point_shader(float3 p, float angle) {
    float c = cos(angle/2);
    float s = sin(angle/2);

    // R * p * ~R expanded symbolically for e12 rotation
    return float3(
        p.x * (c*c - s*s) - p.y * (2*c*s),
        p.x * (2*c*s) + p.y * (c*c - s*s),
        p.z
    );
}
```

**Why this matters**: Every different rotation requires a different expansion. GA's promise of unified operations breaks down where computation actually happens—in shaders processing millions of pixels.

#### Successful Hybrid Architectures

Three patterns enable GA benefits while respecting graphics reality:

**Pattern 1: Scene Graph GA, Rendering Traditional**

Build your scene hierarchy with motors for elegant transformation composition:

```cpp
// Compose transformations without gimbal lock
Motor world_to_camera = camera.get_motor();
Motor model_to_world = model.get_motor();
Motor model_to_camera = world_to_camera * model_to_world;

// Convert once for GPU submission
Matrix4 mvp_matrix = to_matrix(projection * model_to_camera);
shader.set_uniform("mvpMatrix", mvp_matrix);
```

**Why this works**: Scene graph updates happen once per frame per object. Rendering happens millions of times. Paying GA overhead where it's amortized makes sense.

**Pattern 2: Offline GA Tools, Runtime Traditional**

RoboBlend uses GA for CSG operations in their modeling pipeline. Complex boolean operations reduce to simple meets:

$$\text{A} \cap \text{B} \cap \text{C} = S_A \vee S_B \vee S_C$$

**Why use GA here?** Tools can be 10× slower if they're 10× more robust. Artists care about results, not milliseconds. Edge cases that crash traditional CSG algorithms are handled uniformly by GA's meet operation.

**Pattern 3: Motor Interpolation for Advanced Skinning**

```cpp
// Blend influences using motor logarithms
Motor blended = Motor::identity();
for (auto& influence : influences) {
    Motor bone_motor = bones[influence.index].get_motor();
    // Blend in logarithm space for proper interpolation
    blended = blended * exp(influence.weight * log(bone_motor));
}

// Convert to dual quaternion for GPU
DualQuat dq = motor_to_dual_quat(blended);
```

**Why motors help**: They interpolate along screw paths, preserving volume naturally. Worth the CPU overhead for hero characters where quality matters.

#### Where GA Fails Completely

**Real-time Ray Tracing**: RTX cores accelerate specific operations:
- Ray-AABB intersection: 1 cycle hardware
- Ray-triangle intersection: 1 cycle hardware

Replacing these with GA meet operations means:
- Ray-AABB meet: ~160 FLOPs software
- Ray-triangle meet: ~200 FLOPs software

**The brutal math**: 200× slower for core operations. No architectural elegance justifies this.

**Mobile Graphics**: Bandwidth constraints dominate:
- Traditional point: 12 bytes
- PGA point: 16 bytes (33% more)
- CGA point: 128 bytes (10× more!)

**Why this kills performance**: Mobile GPUs are bandwidth-limited. Every byte matters. GA's richer representations become liabilities.

#### Future Opportunities

**VR/AR Pose Streaming**: ORamaVR's medical training system streams surgeon hand poses at 90Hz. Traditional approach:
- Position: 3 floats (12 bytes)
- Orientation: 4 floats (16 bytes)
- Total: 28 bytes per pose

Motor approach:
- Single motor: 8 floats (32 bytes)

**Wait, that's MORE bytes!** But motors compress better:
- Predictable sparsity patterns
- Temporal coherence in screw parameters
- A15 crystallographic symmetry for repeated elements

**Result**: 50% bandwidth reduction after compression. For wireless VR with multiple users, this enables more participants per access point.

**Non-Euclidean Rendering**: Hyperbolic space breaks traditional matrices. Points at infinity cause numerical explosions. PGA handles ideal points algebraically:

$$\text{Hyperbolic isometry} = \text{PGA versor in } P(\mathbb{R}^{3,1})$$

**Why GA wins here**: No special cases for points approaching infinity. CodeParade's Hyperbolica could eliminate chunks of numerical fixup code using PGA's uniform treatment.

#### Making the Graphics Decision

Use GA in graphics when:
- Building tools where robustness beats performance
- Handling non-Euclidean geometries
- Streaming geometric data over networks
- CPU-bound by scene management
- Prototyping new algorithms

Avoid GA in graphics when:
- Shaders do the heavy lifting
- Chasing maximum frame rates
- Targeting mobile/embedded platforms
- Working with established engines
- Bandwidth limited

**The engineering reality**: Graphics is about feeding GPUs efficiently. GA's theoretical elegance means nothing to an RTX core expecting triangles. But in the gaps—tools, networking, CPU-side scene management—GA's unifying architecture can reduce bugs and development time.

Choose GA when architectural clarity prevents more bugs than performance overhead causes dropped frames. For most graphics applications, that's a narrow window. But when it fits, the elegance is worth the cost.
