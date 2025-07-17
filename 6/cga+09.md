### Chapter 9: Visual Computing Unified: Graphics and Vision as One

Visual computing spans two traditionally separate disciplines. Computer graphics synthesizes images from geometric models—transforming 3D scenes into 2D projections through careful orchestration of matrices, lighting equations, and rasterization algorithms. Computer vision reverses this process, extracting 3D structure from 2D images using different mathematical tools: homogeneous coordinates for projection, Plücker coordinates for lines, quaternions for rotation, and Lie algebras for optimization.

Consider implementing a visual SLAM (Simultaneous Localization and Mapping) system. The camera projection employs 4×4 matrices. Feature detection operates in pixel coordinates. Triangulation uses either linear least squares or Plücker line intersections. Bundle adjustment optimizes over rotation manifolds using specialized parameterizations. Each component speaks a different mathematical dialect, necessitating error-prone translations between representations.

These translations aren't just inconvenient—they're lossy. Projecting a 3D line loses its spatial orientation. Triangulating a point discards uncertainty information. Interpolating camera poses by separately handling rotations and translations violates rigid motion constraints. The mathematical fragmentation of visual computing obscures a fundamental truth: rendering and reconstruction are inverse operations that should share the same geometric language.

#### Camera Models: Projection as Incidence

Traditional computer graphics builds cameras from projection matrices—carefully constructed 4×4 arrays encoding focal length, principal point, aspect ratio, and pose. The standard pipeline concatenates modeling, viewing, and projection transformations, obscuring the underlying geometric relationships.

CGA reconceptualizes cameras as geometric entities: an optical center (conformal point) and an image surface (conformal plane, sphere, or other manifold). Projection becomes pure incidence:

$$\text{Project}(P) = (C \wedge P \wedge \mathbf{n}_\infty) \vee \Sigma$$

Let's deconstruct this formula to reveal its geometric story. We begin with the camera center $C$ and a world point $P$. The wedge product $C \wedge P$ creates a bivector representing the point pair—the two endpoints of a line segment. But we need more than a segment; we need the infinite ray that passes through both points. This is where $\mathbf{n}_\infty$ enters: wedging with the point at infinity extends our finite segment into an infinite ray. The expression $C \wedge P \wedge \mathbf{n}_\infty$ thus constructs the unique ray emanating from the camera center through the world point.

The second half of the formula—the meet with surface $\Sigma$—finds where this ray pierces the image manifold. Whether $\Sigma$ is a plane (traditional pinhole camera), a sphere (fisheye lens), or even a non-Euclidean surface, the same formula applies. The meet operation automatically handles all geometric cases: rays that miss the surface, tangent rays, and normal intersections.

This unification extends to all camera types:

> **Implementation Blueprint: Unified Camera Projection**
> ```
> FUNCTION PROJECT_POINT(camera, world_point):
>     // Input: Camera with center C and surface Σ, world point P
>     // Output: Image coordinates or NULL if no intersection
>
>     // Step 1: Construct the projection ray
>     ray = camera.center ∧ world_point ∧ CGA5D::N_INFINITY
>
>     // Step 2: Handle any reflective elements (mirrors, prisms)
>     FOR EACH optical_element IN camera.optical_path:
>         IF optical_element.type == MIRROR:
>             ray = optical_element.versor * ray * REVERSE(optical_element.versor)
>
>     // Step 3: Find intersection with image surface
>     intersection = MEET(ray, camera.image_surface)
>
>     // Step 4: Validate the intersection
>     IF GRADE(intersection) != EXPECTED_GRADE(camera.image_surface):
>         RETURN NULL  // Ray misses the image surface
>
>     // Step 5: Extract coordinates appropriate to surface type
>     IF camera.surface_type == PLANAR:
>         RETURN EXTRACT_2D_COORDS(intersection, camera.image_plane)
>     ELSE IF camera.surface_type == SPHERICAL:
>         RETURN EXTRACT_SPHERICAL_COORDS(intersection, camera.center)
>     ELSE:
>         RETURN EXTRACT_PARAMETRIC_COORDS(intersection, camera.surface)
> ```

**Table 32: Camera Model Unification**

| Camera Type | Traditional Model | CGA Model | Unique Benefits |
|-------------|-------------------|-----------|-----------------|
| Pinhole | 3×4 projection matrix | Point $C$ + plane $\pi$ | Natural ray construction |
| Fisheye | Nonlinear distortion function | Point $C$ + sphere $S$ | No distortion parameters |
| Cylindrical panorama | Unwrapping formula | Point $C$ + cylinder | Direct ray intersection |
| Orthographic | Parallel projection matrix | Plane $\pi$ + direction $\mathbf{n}_\infty$ | Limit of perspective |
| Pushbroom | Line of projection centers | Line $L$ + plane $\pi$ | Natural for satellite imaging |
| Cross-slits | Two lines of projection | $L_1 \wedge L_2$ ray constraint | Geometrically impossible traditionally |
| Spherical | Equirectangular mapping | Point $C$ + sphere $S$ | No singularities |
| Light field | 4D ray parameterization | Ray space in CGA | Natural ray algebra |

The table reveals how exotic camera models—difficult or impossible to express with matrices—emerge naturally in CGA. Multi-perspective cameras used in artistic rendering simply vary the center $C$ across the image plane. Catadioptric systems chain reflections through sequential versor applications.

#### Ray Tracing: Universal Intersection Redux

Ray tracing exemplifies CGA's computational elegance. Traditional implementations maintain separate intersection routines for each primitive type: ray-sphere via quadratic formula, ray-plane through parametric substitution, ray-triangle using barycentric coordinates. Each routine handles its own numerical edge cases and floating-point subtleties.

CGA unifies all ray-primitive intersections through the meet operation. Consider the architectural contrast. A traditional ray tracer might contain:

```
// Traditional approach - branching complexity
switch(object.type) {
    case SPHERE:
        result = intersect_ray_sphere(ray, object);
        break;
    case PLANE:
        result = intersect_ray_plane(ray, object);
        break;
    case CYLINDER:
        result = intersect_ray_cylinder(ray, object);
        break;
    // ... dozens more cases
}
```

The CGA approach replaces this entire switch statement with a single line:

```
intersection = ray ∨ object
```

This isn't just notational convenience—it's a fundamental architectural simplification. The meet operation handles spheres, planes, cylinders, tori, and constructive solid geometry objects uniformly. The result's grade indicates the intersection type: points for ray-sphere, lines for grazing incidence, point pairs for ray-cylinder.

> **Implementation Blueprint: CGA Ray Tracer Core**
> ```
> FUNCTION TRACE_RAY(ray, scene):
>     // Input: Ray as bivector, scene as collection of CGA objects
>     // Output: Closest intersection and hit object
>
>     closest_t = INFINITY
>     hit_object = NULL
>     hit_point = NULL
>
>     FOR EACH object IN scene.objects:
>         // Universal intersection through meet
>         intersection = MEET(ray, object)
>
>         // Validate intersection based on expected grade
>         IF IS_VALID_INTERSECTION(intersection, object.type):
>             // Extract ray parameter t
>             t = COMPUTE_RAY_PARAMETER(ray.origin, intersection)
>
>             IF t > EPSILON AND t < closest_t:
>                 closest_t = t
>                 hit_object = object
>                 hit_point = intersection
>
>     RETURN (hit_point, hit_object, closest_t)
> ```

Beyond algorithmic unification, CGA enables novel rendering effects through conformal transformations. Gravitational lensing, atmospheric refraction, and artistic warping all become applications of the versor mechanism to rays during traversal.

#### Illumination Models: Light as Geometric Entity

Traditional rendering separates light into intensity, direction, color, and occasionally polarization—each handled by separate systems. Physical reality suggests a richer structure: electromagnetic fields are fundamentally bivector quantities, the same mathematical objects that generate rotations in CGA.

Consider Lambertian diffuse reflection, traditionally computed as $I = \mathbf{n} \cdot \mathbf{l}$. In CGA:

$$I = \langle N L \rangle_0$$

where $N$ and $L$ are conformal vectors. This appears identical until we recognize that light can be more than a direction—it can be a bivector encoding spatial extent:

$$L = L_0 + L_2$$

where $L_0$ represents the traditional direction vector and $L_2$ encodes the light's bivector distribution (area, orientation). The illumination integral becomes:

$$I = \langle N L_0 \rangle_0 + \frac{1}{2}\langle N L_2 N \rangle_0$$

The second term—impossible to express in vector algebra—captures how surface orientation interacts with the light source's spatial extent, naturally producing soft shadows and area light effects. This isn't a mathematical curiosity; it's the gateway to physically accurate rendering of extended light sources.

**Table 33: Illumination Models Enhanced**

| Traditional Model | Vector Formula | CGA Enhancement | Physical Meaning |
|------------------|----------------|-----------------|------------------|
| Lambert diffuse | $\mathbf{n} \cdot \mathbf{l}$ | $\langle NL \rangle_0$ with bivector $L$ | Area light sources |
| Phong specular | $(\mathbf{r} \cdot \mathbf{v})^n$ | Rotor-based reflection | Anisotropic highlights |
| Blinn-Phong | $(\mathbf{n} \cdot \mathbf{h})^n$ | Half-vector as rotation axis | Energy conservation |
| Fresnel reflection | Complex formulae | Spinor coefficients | Natural polarization |
| Oren-Nayar rough | Statistical model | Bivector microfacets | Geometric roughness |
| Cook-Torrance | Microfacet BRDF | Versor per microfacet | Coherent framework |

The striking advancement comes in polarization handling. Traditional rendering either ignores polarization or implements it as a separate layer. In CGA, the electromagnetic field's bivector components naturally encode polarization state. Reflection and refraction transform these components through versor operations, automatically handling polarization rotation and phase shifts.

#### Structure from Motion: Inverse Rendering

Computer vision's structure-from-motion problem inverts the rendering process: given 2D images, recover 3D geometry and camera poses. Traditional pipelines fragment into feature detection, matching, fundamental matrix estimation, triangulation, and bundle adjustment—each using different mathematical frameworks.

CGA unifies this pipeline by recognizing that image features correspond to rays in conformal space. The key insight: all optimization occurs in motor space, which naturally parameterizes rigid motions without singularities, gimbal lock, or normalization constraints.

> **Implementation Blueprint: CGA Structure from Motion**
> ```
> FUNCTION ESTIMATE_STRUCTURE_AND_MOTION(image_sequence):
>     // Input: Sequence of images with extracted features
>     // Output: Camera motors and 3D points
>
>     // Initialize with first two views
>     cameras[0] = CGA5D::IDENTITY_MOTOR
>     features_1 = EXTRACT_FEATURES(image_sequence[0])
>     features_2 = EXTRACT_FEATURES(image_sequence[1])
>     matches = MATCH_FEATURES(features_1, features_2)
>
>     // Estimate relative pose as motor
>     cameras[1] = ESTIMATE_RELATIVE_MOTOR(matches)
>
>     // Triangulate initial structure
>     points = []
>     FOR EACH match IN matches:
>         ray_1 = CONSTRUCT_RAY(cameras[0], match.feature_1)
>         ray_2 = CONSTRUCT_RAY(cameras[1], match.feature_2)
>
>         // Intersection gives 3D point
>         point = MEET(ray_1, ray_2)
>         IF IS_VALID_3D_POINT(point):
>             points.APPEND(point)
>
>     // Add remaining views
>     FOR i = 2 TO LENGTH(image_sequence) - 1:
>         // PnP in motor space
>         cameras[i] = SOLVE_PNP_MOTOR(image_sequence[i], points)
>
>         // Extend structure
>         new_points = TRIANGULATE_NEW_FEATURES(image_sequence[i], cameras)
>         points.EXTEND(new_points)
>
>     // Global refinement on motor manifold
>     BUNDLE_ADJUST_MOTORS(cameras, points)
>
>     RETURN (cameras, points)
> ```

#### Bundle Adjustment: Optimization on the Motor Manifold

Bundle adjustment jointly optimizes camera poses and 3D points to minimize reprojection error. Traditional implementations struggle with rotation parameterization—quaternions require normalization constraints, Euler angles suffer from gimbal lock, and rotation matrices need orthogonality enforcement.

CGA's motor representation elegantly solves these challenges. Motors form a Lie group with corresponding Lie algebra of bivectors, enabling unconstrained optimization. The key advantage: we optimize directly in the space of rigid motions without ever leaving the constraint manifold.

Consider the contrast in parameterization:

Traditional approach with quaternions:
- 7 parameters (4 for rotation + 3 for translation)
- Constraint: $|q| = 1$ must be enforced
- Optimization requires constrained methods or frequent renormalization
- Composition is awkward: $T_2R_2(T_1R_1(x))$

Motor approach:
- 6 parameters (bivector in Lie algebra)
- No constraints—the exponential map automatically produces valid motors
- Optimization uses standard unconstrained methods
- Composition is natural: $M_2M_1$

**Table 34: Bundle Adjustment Comparison**

| Aspect | Traditional Approach | CGA Approach | Advantage |
|--------|---------------------|--------------|-----------|
| Rotation parameterization | Quaternions + normalization | Bivectors (unconstrained) | Natural manifold |
| Translation handling | Separate 3-vector | Integrated in motor | Unified transform |
| Jacobian structure | Block-sparse with coupling | Natural block structure | Simpler derivatives |
| Gauge freedom | Fix arbitrary camera | Natural gauge in CGA | No arbitrary choice |
| Planar degeneracy | Special detection needed | Grade indicates degeneracy | Automatic handling |
| Initialization | Careful rotation averaging | Motor interpolation | Geometrically valid |

The bivector parameterization provides several advantages:
- Natural manifold structure without constraints
- Simple chain rule for derivatives
- Numerically stable exp/log maps
- Direct interpolation for motion regularization

#### Real-Time Visual SLAM

Visual SLAM demands real-time performance while maintaining accuracy. Traditional systems achieve this through approximations: constant velocity models, fixed landmarks, sliding window optimization. CGA enables novel approaches that maintain geometric accuracy at high frame rates.

The bivector velocity model naturally handles screw motions—combined rotation and translation along an axis—common in handheld and vehicle-mounted cameras. As established in Chapter 10, motors compose multiplicatively, making prediction and update steps geometrically consistent:

```
// Predict next pose using velocity in Lie algebra
velocity_bivector = LOG(INVERSE(M_previous) * M_current) / dt
M_predicted = M_current * EXP(velocity_bivector * dt)
```

This prediction naturally extrapolates the screw motion without separating rotation and translation components.

#### Advanced Visual Effects

CGA enables visual effects difficult or impossible with traditional methods:

**Non-Euclidean Rendering**: By modifying the underlying geometric algebra signature (as explored in Chapter 12), we can visualize hyperbolic, spherical, or custom geometries. The same ray-tracing algorithm works across all geometries—only the meet operation changes.

**Spinor Field Visualization**: Quantum wavefunctions and electromagnetic fields are naturally spinor-valued (as shown in Chapter 11). Direct visualization becomes possible by mapping spinor properties to visual attributes through geometric operations.

**Conformal Lens Distortions**: Real camera lenses introduce complex distortions traditionally modeled through polynomial coefficients. In CGA, these become conformal transformations—versors that warp space while preserving angles locally.

#### Neural Geometric Vision

The convergence of deep learning with geometric algebra opens new frontiers. Traditional neural networks learn abstract feature representations disconnected from geometric reality. Geometric neural networks (introduced in Chapter 13) preserve and exploit geometric structure.

For visual computing, this means:
- Convolutions that respect the versor mechanism
- Pooling operations using geometric meet and join
- Outputs that are geometric entities (motors for pose, points for structure)
- Natural equivariance under rigid transformations

#### Performance Optimization

Achieving real-time performance requires careful implementation:

**GPU Acceleration**: Modern GPUs excel at the regular computations of geometric algebra:
- Geometric products map efficiently to tensor cores
- Meet operations parallelize naturally across rays
- Bivector exponentials leverage special function units

**Sparse Multivector Storage**: As discussed in Chapter 15, visual computing objects exhibit natural sparsity:
- Rays are grade-2 bivectors (10 non-zero components)
- Points are grade-1 vectors (5 non-zero components)
- Motors combine grades 0 and 2 (8 non-zero components)

Exploiting this sparsity provides order-of-magnitude speedups over dense representations.

#### The Unified Vision

This chapter began with the artificial separation between computer graphics and computer vision—two fields solving inverse problems with incompatible mathematical tools. Through CGA, we've discovered this separation was never fundamental. Both fields manipulate the same geometric entities: rays, transformations, and incidence relationships.

The unification transcends theoretical elegance. When rendering and reconstruction share the same algebraic framework:
- Differentiable rendering enables learning from images
- Uncertainty propagates naturally through geometric operations
- Novel view synthesis preserves geometric consistency
- Sensor fusion becomes geometric aggregation

The projection formula $(C \wedge P \wedge \mathbf{n}_\infty) \vee \Sigma$ isn't just mathematics—it's a lens through which graphics and vision reveal their essential unity. One constructs rays through outer products; the other finds their intersections through meets. One builds geometric models; the other infers them from observations. Both are dialects of the same geometric language.

As visual computing increasingly drives robotics, augmented reality, and human-computer interaction, the need for unified geometric reasoning becomes critical. CGA provides the mathematical foundation where light, geometry, and computation speak the same language.

#### Exercises

**Conceptual Questions**

1. Explain why the projection formula $(C \wedge P \wedge \mathbf{n}_\infty) \vee \Sigma$ works identically for planar, spherical, and arbitrary image surfaces. What role does each operation play in the geometric story?

2. Traditional ray tracers require separate intersection algorithms for each primitive type. How does the meet operation unify these while automatically handling special cases like tangent rays?

3. The bivector representation of light enables area light sources and polarization effects. Explain geometrically why bivectors are the natural representation for electromagnetic phenomena.

**Mathematical Derivations**

1. Starting from a pinhole camera at origin looking along the z-axis, derive the CGA representation of its projection operation. Show that it reduces to the traditional perspective division in the limit.

2. Given two rays $R_1$ and $R_2$ from cameras with centers $C_1$ and $C_2$, derive the condition for the rays to intersect (i.e., when triangulation succeeds). Express this condition using the meet operation.

3. Prove that optimizing camera poses in motor space (bivector parameterization) eliminates the need for quaternion normalization constraints.

4. Show that the second term in the bivector illumination model $\frac{1}{2}\langle N L_2 N \rangle_0$ correctly accounts for the projected solid angle of an area light source.

**Computational Exercises**

1. Implement the unified camera projection for three cases:
   - Pinhole camera (planar image surface)
   - Fisheye camera (spherical image surface)
   - Cylindrical panorama camera

   Verify that the same projection formula handles all three.

2. Create a minimal ray tracer that handles spheres, planes, and cylinders using only the meet operation. Compare the code complexity with a traditional implementation.

3. Implement motor-based camera interpolation for a virtual camera path. Compare the smoothness of motion with separate rotation/translation interpolation.

4. Given a set of 2D feature correspondences between two images, implement the motor estimation algorithm. Verify that the recovered motor correctly transforms 3D points between camera frames.

**Implementation Challenges**

1. **Unified Visual SLAM System**
   Design and implement a complete visual SLAM pipeline using CGA representations throughout.
   - Input: Video sequence from a moving camera
   - Output: Reconstructed 3D map and camera trajectory
   - Requirements:
     - Use motors for all camera poses
     - Represent map points as conformal points
     - Implement feature tracking and matching
     - Use bivector-based prediction for camera motion
     - Perform bundle adjustment on the motor manifold
     - Handle both indoor and outdoor sequences
     - Achieve real-time performance (30+ fps)

2. **Non-Euclidean Ray Tracer**
   Extend the CGA ray tracer to handle non-Euclidean geometries.
   - Input: Scene description with geometry type specification
   - Output: Rendered images showing the geometric distortion
   - Requirements:
     - Support Euclidean, spherical, and hyperbolic geometries
     - Use the same ray-tracing algorithm for all geometries
     - Only modify the underlying geometric algebra
     - Implement at least three test scenes that showcase the differences
     - Include refractive objects that bend light according to the geometry

3. **Geometric Neural Renderer**
   Create a differentiable renderer using geometric algebra operations.
   - Input: 3D scene with geometric primitives and target images
   - Output: Optimized scene parameters that best match the target
   - Requirements:
     - Represent all geometry using CGA objects
     - Implement differentiable versions of meet and join operations
     - Use gradient descent on the motor manifold for camera optimization
     - Support both shape and appearance optimization
     - Demonstrate inverse rendering on at least three different scenes

---

*The geometric principles of visual computing extend directly to robotics, where virtual models meet physical reality through precise geometric control...*
