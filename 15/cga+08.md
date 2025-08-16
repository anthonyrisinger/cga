### Chapter 8: Conformal Model Linearizes Translation

Translation breaks geometric algebra's elegant multiplicative structure. While rotations compose through multiplication—R₂R₁ represents "first rotate by R₁, then by R₂"—translations stubbornly require addition. This fundamental incompatibility forces hybrid representations: quaternions for rotation, vectors for translation, careful bookkeeping to maintain consistency. The conformal model dissolves this barrier through a geometric insight: translation becomes rotation in a carefully constructed 5-dimensional null space.

#### The Fixed Point Problem

A rotation leaves points on its axis unchanged. These fixed points anchor the transformation, enabling the sandwich product R**x**R† to work. Translation has no fixed points—every point moves. This geometric difference drives the algebraic incompatibility.

Consider transforming point **x** by rotation R then translation **t**:

$$\mathbf{x}' = R\mathbf{x}R^{\dagger} + \mathbf{t}$$

That plus sign breaks the multiplicative chain. We cannot express this as M**x**M† for any multivector M because the additive and multiplicative structures don't mix. Homogeneous coordinates patch around this by embedding 3D points as 4D vectors [x, y, z, 1], enabling matrix representation:

$$\begin{bmatrix}
R_{11} & R_{12} & R_{13} & t_x \\
R_{21} & R_{22} & R_{23} & t_y \\
R_{31} & R_{32} & R_{33} & t_z \\
0 & 0 & 0 & 1
\end{bmatrix}$$

This works for graphics pipelines but sacrifices metric information. The projective embedding treats all points on a ray as equivalent—distances and angles lose meaning. We need a different approach.

#### The Null Cone Insight

The conformal model's key insight: embed Euclidean space onto a null cone in higher dimensions where translations become rotations "toward infinity." This isn't mystical—it's precise geometry that exploits the properties of null vectors (vectors with zero length) to linearize translation.

Start with 3D Euclidean space {**e₁**, **e₂**, **e₃**}. Add two extra dimensions with mixed signature:
- **e₊** with **e₊**² = +1
- **e₋** with **e₋**² = -1

This creates a (4,1) metric space—four positive directions, one negative. The mixed signature is crucial; it enables null vectors that make the construction work. Define:

$$\mathbf{n}_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+) \quad \text{(origin)}$$
$$\mathbf{n}_\infty = \mathbf{e}_- + \mathbf{e}_+ \quad \text{(point at infinity)}$$

These are null vectors: **n₀**² = ¼(1 - 0 - 0 + 1) = 0 and **n∞**² = 1 + 0 + 0 + 1 = 0, where we used **e₋** · **e₊** = 0 (orthogonality). Crucially, **n₀** · **n∞** = -1.

Now embed 3D point **p** into this 5D space:

$$P = \mathbf{p} + \frac{1}{2}|\mathbf{p}|^2\mathbf{n}_\infty + \mathbf{n}_0$$

The quadratic term ½|**p**|² creates a paraboloid in the **n∞** direction. Every Euclidean point maps to a unique point on this paraboloid, and critically, P² = 0 for all embedded points. They lie on the null cone—the locus of all null vectors in the 5D space.

#### Why This Specific Embedding Works

The embedding formula achieves three critical properties:

1. **Null constraint**: P² = 0 ensures all points lie on the null cone
2. **Metric preservation**: P₁ · P₂ = -½|**p₁** - **p₂**|²
3. **Linearization**: Translations become multiplicative operations

Let's verify the null property:

$$P^2 = \left(\mathbf{p} + \frac{1}{2}|\mathbf{p}|^2\mathbf{n}_\infty + \mathbf{n}_0\right)^2$$

Expanding systematically:
$$P^2 = \mathbf{p}^2 + 2\mathbf{p} \cdot \left(\frac{1}{2}|\mathbf{p}|^2\mathbf{n}_\infty\right) + 2\mathbf{p} \cdot \mathbf{n}_0 + \left(\frac{1}{2}|\mathbf{p}|^2\right)^2\mathbf{n}_\infty^2 + \mathbf{n}_0^2 + 2\mathbf{n}_0 \cdot \left(\frac{1}{2}|\mathbf{p}|^2\mathbf{n}_\infty\right)$$

Since **p** ⊥ **n₀**, **p** ⊥ **n∞**, **n∞**² = 0, **n₀**² = 0, and **n₀** · **n∞** = -1:

$$P^2 = |\mathbf{p}|^2 + 0 + 0 + 0 + 0 + |\mathbf{p}|^2(-1) = 0$$

Every embedded point is null. This constraint maintains itself to first order under small perturbations—numerical errors produce O(ε²) deviations, not O(ε).

#### Translation Through Null Rotation

In the conformal space, translation by vector **t** becomes:

$$T = \exp\left(-\frac{1}{2}\mathbf{t} \wedge \mathbf{n}_\infty\right) = 1 - \frac{1}{2}\mathbf{t} \wedge \mathbf{n}_\infty$$

The exponential truncates because (**t** ∧ **n∞**)² = 0. This translator acts through the sandwich product:

$$P' = TPT^{-1}$$

where T⁻¹ = 1 + ½**t** ∧ **n∞**. The key insight: this "rotation" involves the point at infinity. In the limit where two reflection planes become parallel, their intersection line recedes to infinity. Reflection in parallel planes separated by distance d produces translation by 2d—the conformal model makes this limiting process algebraically precise.

#### Numerical Example

Point **p** = (3, 4, 0), translation **t** = (2, 0, 0):

Embedding:
$$P = 3\mathbf{e}_1 + 4\mathbf{e}_2 + 12.5\mathbf{n}_\infty + \mathbf{n}_0$$

Translator:
$$T = 1 - \mathbf{e}_1 \wedge \mathbf{n}_\infty$$

Application:
$$P' = TPT^{-1} = 5\mathbf{e}_1 + 4\mathbf{e}_2 + 20.5\mathbf{n}_\infty + \mathbf{n}_0$$

Extraction:
$$\mathbf{p}' = \frac{P' - (P' \cdot \mathbf{n}_\infty)\mathbf{n}_0}{-P' \cdot \mathbf{n}_\infty} = (5, 4, 0) = \mathbf{p} + \mathbf{t} \checkmark$$

#### Computational Reality

The elegance costs dearly:

**Storage**:
- Euclidean point: 3 floats
- Conformal point: 5 floats (4 independent due to null constraint)

**Translation**:
- Traditional: 3 additions (~3 FLOPs)
- Conformal: Full sandwich product (~50 FLOPs)
- Overhead: ~17×

**Distance computation**:
- Traditional: 6 FLOPs + sqrt for |**p₁** - **p₂**|
- Conformal: 9 FLOPs for -2(P₁ · P₂) = |**p₁** - **p₂**|²

The conformal approach avoids square roots for distance comparisons—valuable for collision detection where actual distances aren't needed.

#### Practical Implementation

```cpp
struct ConformalPoint {
    float data[5];  // [e1, e2, e3, n_inf, n_0]

    static ConformalPoint embed(float x, float y, float z) {
        float norm_sq = x*x + y*y + z*z;
        return {x, y, z, 0.5f * norm_sq, 1.0f};
    }

    Vec3 extract() const {
        float w = -dot_with_ninf();  // -P·n∞
        if (fabs(w) < 1e-10f) return {0, 0, 0};  // at infinity
        float inv_w = 1.0f / w;
        return {data[0] * inv_w, data[1] * inv_w, data[2] * inv_w};
    }
};

struct Translator {
    float s;         // scalar part
    float e1_inf;    // e1∧n∞ coefficient
    float e2_inf;    // e2∧n∞ coefficient
    float e3_inf;    // e3∧n∞ coefficient

    static Translator from_vector(const Vec3& t) {
        return {1.0f, -0.5f * t.x, -0.5f * t.y, -0.5f * t.z};
    }

    ConformalPoint apply(const ConformalPoint& p) const {
        // Optimized sandwich product for translator
        // Exploits sparsity: only 4 non-zero components
        return sandwich_sparse(*this, p);
    }
};
```

#### When Linearization Justifies Its Cost

The 17× overhead for single translations is prohibitive. Conformal wins when:

**Transformation Composition**: Long chains of mixed rotations and translations. Traditional: carefully track quaternion and vector separately, worry about order. Conformal: motors multiply.

**Unified Operations**: One code path for all primitives. The same sandwich product transforms points, lines, planes, circles, and spheres. No special cases.

**Interpolation Quality**: Natural screw motion paths. Motor interpolation produces helical motion that minimizes kinetic energy—crucial for robotics and animation.

**Numerical Stability**: The null constraint self-corrects to first order. After 10,000 operations, conformal points drift by ~10⁻¹² while traditional matrix chains drift by ~10⁻⁶.

**Example**: A surgical robot with 7-DOF operating for 8 hours. Traditional: accumulates numerical errors requiring frequent renormalization, gimbal lock requires special handling, separate code paths for different geometric primitives. Conformal: motors compose stably, no gimbal lock possible, unified geometric operations. The 17× computational overhead is acceptable when precision and robustness matter more than microseconds.

#### The Bottom Line

The conformal model trades substantial computational overhead for architectural benefits. It transforms the fundamental incompatibility between rotation and translation into a unified multiplicative framework. This isn't mathematical mysticism—it's a concrete engineering trade-off.

The null cone embedding exemplifies a deeper principle: geometric relationships that seem incompatible in one space often unify naturally in a higher-dimensional space with the right structure. Understanding when this dimensional lifting justifies its computational cost separates geometric algebra practitioners from enthusiasts. When geometric complexity dominates your system architecture, when numerical robustness matters more than microseconds, when unified operations simplify maintenance, the conformal model provides a powerful tool. Otherwise, stick with traditional representations.
