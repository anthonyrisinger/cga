## Part II: The Conformal Model

The geometric product unifies rotations through multiplication. Any sequence of rotations composes into a single rotor. The sandwich product $RXR^{-1}$ transforms any geometric object consistently. But translation breaks this unity—it remains stubbornly additive:

$$\mathbf{x}' = \mathbf{x} + \mathbf{t}$$

This forces hybrid representations: quaternions for rotation, vectors for translation, different composition rules for each. The architectural friction compounds with every transformation type added to a system.

The conformal model eliminates this friction by embedding Euclidean space in a larger geometric framework where all transformations—including translation—become multiplicative. This unification requires 5D space and increases computational cost by 2-10×. The following chapters develop the mathematics and analyze when this tradeoff proves worthwhile.

### Chapter 4: The Conformal Model

#### The Translation Problem

Consider a robotic arm performing a screw motion—rotating while translating along its axis. Traditional representations fragment this natural movement:

```
Rotation: q = cos(θ/2) + sin(θ/2)(ai + bj + ck)  [4 floats]
Translation: t = (tx, ty, tz)                      [3 floats]
Composition: First rotate, then translate (order matters!)
```

Each representation uses different mathematics. Quaternions compose through multiplication: $q_{total} = q_2 q_1$. Translations compose through addition: $\mathbf{t}_{total} = \mathbf{t}_1 + \mathbf{t}_2$. The boundary between them requires constant conversion and careful order tracking.

The root cause: translation has no fixed points. Rotation fixes an axis. Reflection fixes a plane. These fixed points enable the sandwich product $VXV^{-1}$ to work. Translation moves every point—there's no geometric anchor for multiplicative representation.

#### The Conformal Solution

The insight comes from projective geometry. Parallel lines "meet at infinity"—a concrete mathematical construction that eliminates special cases. Apply this thinking to transformations: translation is rotation around an axis at infinity.

Making this precise requires embedding 3D Euclidean space in a 5D space with specific properties:

1. **Angle preservation** (conformal property)
2. **Linearized Euclidean transformations**
3. **Recoverable distances**
4. **Null cone structure** for the embedding

The minimal space satisfying these requirements has signature (4,1,0)—four positive directions, one negative. To see why, count parameters: the conformal group in 3D has 10 parameters (3 rotations + 3 translations + 1 scaling + 3 inversions). The orthogonal group O(p,q) has dimension (p+q)(p+q-1)/2. Setting this equal to 10 with p+q=5 gives our signature.

#### Building Conformal Space

Start with Euclidean basis vectors $\{\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3\}$ where $\mathbf{e}_i^2 = 1$. Add two null vectors constructed from a Minkowski-like pair:

$$\mathbf{e}_+ \text{ with } \mathbf{e}_+^2 = +1$$
$$\mathbf{e}_- \text{ with } \mathbf{e}_-^2 = -1$$

The null vectors are:
$$\mathbf{n}_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+) \quad \text{(origin)}$$
$$\mathbf{n}_\infty = \mathbf{e}_- + \mathbf{e}_+ \quad \text{(infinity)}$$

These satisfy:
- $\mathbf{n}_0^2 = \frac{1}{4}(1 - 2 + 1) = 0$ (null)
- $\mathbf{n}_\infty^2 = 1 - 1 = 0$ (null)
- $\mathbf{n}_0 \cdot \mathbf{n}_\infty = \frac{1}{2}(-1 - 1) = -1$ (normalization)

The -1 inner product is crucial—it establishes the metric that makes distance calculations work.

#### The Conformal Embedding

A Euclidean point $\mathbf{p} = (x, y, z)$ embeds as:

$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$$

**Worked Example**: Embed the point $\mathbf{p} = (2, 1, 3)$:
```
||p||² = 4 + 1 + 9 = 14
P = (2, 1, 3) + 7n∞ + n₀
  = 2e₁ + e₂ + 3e₃ + 7n∞ + n₀

Verify null property:
P² = (2e₁ + e₂ + 3e₃)² + 2(2e₁ + e₂ + 3e₃)·(7n∞) + 2(2e₁ + e₂ + 3e₃)·n₀
     + 2(7n∞)·n₀ + (7n∞)² + n₀²
   = 14 + 0 + 0 + 2(7)(-1) + 0 + 0
   = 14 - 14 = 0 ✓
```

This creates a paraboloid in 5D space where height (the $\mathbf{n}_\infty$ coefficient) encodes distance from origin. Points farther from origin sit higher on the paraboloid—a beautiful geometric relationship that linearizes distance calculations.

#### Translation as Rotation

In conformal space, translation by vector $\mathbf{t}$ uses the translator:

$$T = \exp\left(-\frac{1}{2}\mathbf{t}\mathbf{n}_\infty\right) = 1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$$

The exponential series terminates because $(\mathbf{t}\mathbf{n}_\infty)^2 = 0$. Apply via sandwich product:

$$P' = TPT^{-1}$$

**Verification** (sketch):
```
T = 1 - ½tn∞
T⁻¹ = 1 + ½tn∞
P' = (1 - ½tn∞)P(1 + ½tn∞)
   = P + [terms that evaluate to t]
```

The result shifts the Euclidean part by $\mathbf{t}$ while maintaining the null property. Translation has become a multiplicative operation—specifically, a rotation in the plane containing $\mathbf{n}_\infty$.

#### Performance Analysis

**Storage Overhead**:
| Component | Euclidean | Conformal | Factor |
|-----------|-----------|-----------|--------|
| Point | 3 floats | 5 floats (4 sparse) | 1.67× |
| Line | 6 floats (Plücker) | 10 floats (8 sparse) | 1.67× |
| Transformation | 7 floats (quat+vec) | 8 floats (motor) | 1.14× |

**Computational Overhead**:
| Operation | Traditional | Conformal | Factor |
|-----------|------------|------------|--------|
| Translate point | 3 adds | ~30 FLOPs | 10× |
| Compose translations | 3 adds | ~50 FLOPs | 17× |
| Rotate + translate | Convert, apply separately | One motor product | ~0.8× |

The key insight: individual operations cost more, but transformation composition and unified handling often compensate in real systems.

#### What Conformal Space Provides

Beyond making translation multiplicative, the conformal model enables:

1. **Unified Primitives**: Spheres and planes both become vectors. Circles and lines both become bivectors.

2. **Universal Intersection**: The meet operation $A \vee B = (A^* \wedge B^*)^*$ works for any geometric objects.

3. **Extended Transformations**: Scaling and inversion join the transformation group as versors.

4. **Numerical Robustness**: Coordinate-free formulations improve conditioning near degeneracies.

#### When to Use Conformal GA

**Conformal GA excels when**:
- Systems handle mixed transformation types (rotation + translation + scaling)
- Geometric primitives vary widely (points, lines, planes, spheres, circles)
- Numerical robustness matters more than raw speed
- Code clarity and maintainability are priorities

**Traditional methods win when**:
- Only simple transformations are needed
- Performance requirements are stringent
- Memory is severely constrained
- The team lacks GA expertise

**Fundamental Limitation**: The conformal model maps each point to a single, precise null vector. It provides no mechanism for representing probability distributions, measurement uncertainty, or stochastic processes. Applications requiring sensor fusion, SLAM, or belief propagation must maintain separate probabilistic representations alongside the geometric ones.

#### The Mathematical Depth

Felix Klein's Erlangen program revealed that each geometry is characterized by its transformation group. Conformal geometry preserves angles while allowing distance and shape to vary. By embedding Euclidean geometry in conformal space, we gain access to a richer transformation group while maintaining the ability to recover Euclidean relationships.

The specific choice of (4,1) signature and null cone embedding isn't arbitrary—it's the unique construction that linearizes the conformal group while preserving the structure needed for practical computation. This mathematical sophistication underlies the engineering benefits.

The next chapter develops the complete catalog of conformal representations, showing how points, lines, planes, circles, and spheres all find natural expression in this unified framework.

---

**TODO**: Table disposition for Appendix integration:
- Embedding dimension comparison table (various geometries and their properties)
- Detailed signature analysis table
