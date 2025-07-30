### Chapter 5: The Conformal Model

The conformal model embeds Euclidean space into a carefully constructed 5D space where all conformal transformations—rotations, translations, scalings, and inversions—become linear operations. This linearization transforms the additive nature of translation into multiplicative form, unifying all transformations under the versor mechanism.

#### The Conformal Embedding

The conformal embedding maps a 3D Euclidean point $\mathbf{p} = x\mathbf{e}_1 + y\mathbf{e}_2 + z\mathbf{e}_3$ to a 5D conformal point:

$$P = \mathbf{p} + \frac{1}{2}\mathbf{p}^2\mathbf{n}_\infty + \mathbf{n}_0$$

where:
- $\mathbf{n}_0$ represents the origin with $\mathbf{n}_0^2 = 0$
- $\mathbf{n}_\infty$ represents infinity with $\mathbf{n}_\infty^2 = 0$
- $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ defines the conformal structure

This embedding requires approximately 10 FLOPs: computing $\mathbf{p}^2$ (6 operations), scaling by $\frac{1}{2}$ (1 operation), and two vector additions (3 operations each in sparse representation).

The geometric meaning becomes clear when we recognize that points trace out a paraboloid in the $\mathbf{n}_\infty$ direction. The "height" $\frac{1}{2}\mathbf{p}^2$ encodes the squared distance from the origin—transforming the nonlinear distance relationships of Euclidean space into linear relationships in conformal space.

#### Null Cone Geometry

Every conformal point lies on the null cone, satisfying $P^2 = 0$. This property enables the linearization of conformal transformations. Let's verify:

$$P^2 = \left(\mathbf{p} + \frac{1}{2}\mathbf{p}^2\mathbf{n}_\infty + \mathbf{n}_0\right)^2$$

Expanding using $\mathbf{p} \cdot \mathbf{n}_0 = 0$, $\mathbf{p} \cdot \mathbf{n}_\infty = 0$, and $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$:

$$P^2 = \mathbf{p}^2 + 2\left(\frac{1}{2}\mathbf{p}^2\mathbf{n}_\infty\right) \cdot \mathbf{n}_0 = \mathbf{p}^2 + \mathbf{p}^2(-1) = 0$$

The null property holds for all Euclidean points. This isn't a coincidence—it's the defining characteristic that makes the conformal model work.

#### Distance Through Inner Products

The conformal inner product encodes squared Euclidean distance directly:

$$P_1 \cdot P_2 = \left(\mathbf{p}_1 + \frac{1}{2}\mathbf{p}_1^2\mathbf{n}_\infty + \mathbf{n}_0\right) \cdot \left(\mathbf{p}_2 + \frac{1}{2}\mathbf{p}_2^2\mathbf{n}_\infty + \mathbf{n}_0\right)$$

Working through the algebra:

$$P_1 \cdot P_2 = \mathbf{p}_1 \cdot \mathbf{p}_2 + \frac{1}{2}\mathbf{p}_1^2(-1) + \frac{1}{2}\mathbf{p}_2^2(-1) + 0$$

$$P_1 \cdot P_2 = -\frac{1}{2}(\mathbf{p}_1^2 - 2\mathbf{p}_1 \cdot \mathbf{p}_2 + \mathbf{p}_2^2) = -\frac{1}{2}\|\mathbf{p}_1 - \mathbf{p}_2\|^2$$

The squared distance emerges from a simple inner product—no explicit subtraction or norm calculation required. This transformation of nonlinear operations into linear ones exemplifies the power of the conformal embedding.

#### Unified Object Representation

The conformal model's most striking feature is how it unifies previously distinct geometric entities:

| Object | IPNS Representation | Grade | Key Properties |
|--------|---------------------|-------|----------------|
| Point | $P = \mathbf{p} + \frac{1}{2}\mathbf{p}^2\mathbf{n}_\infty + \mathbf{n}_0$ | 1 | $P^2 = 0$ |
| Sphere | $S = P_c - \frac{1}{2}r^2\mathbf{n}_\infty$ | 1 | $S^2 = r^2$ |
| Plane | $\pi = \mathbf{n} + d\mathbf{n}_\infty$ | 1 | $\pi^2 = 1$ (unit normal) |

Spheres and planes—geometrically distinct in Euclidean space—both become grade-1 objects (vectors) in conformal space. A plane is simply a sphere with infinite radius. This unification means the same operations work on both without special cases.

#### Worked Example: Sphere Construction

Construct a sphere centered at $(2, 1, 3)$ with radius $4$:

1. **Euclidean center**: $\mathbf{c} = 2\mathbf{e}_1 + \mathbf{e}_2 + 3\mathbf{e}_3$

2. **Compute center norm**: $\mathbf{c}^2 = 4 + 1 + 9 = 14$

3. **Embed center**: $P_c = \mathbf{c} + 7\mathbf{n}_\infty + \mathbf{n}_0$

4. **Create sphere**: $S = P_c - \frac{1}{2}(16)\mathbf{n}_\infty = \mathbf{c} - \mathbf{n}_\infty + \mathbf{n}_0$

5. **Verify**: $S^2 = (\mathbf{c} - \mathbf{n}_\infty + \mathbf{n}_0)^2 = \mathbf{c}^2 + 2(-1)(-1) = 14 + 2 = 16 = r^2$ ✓

To extract the center: $\mathbf{c} = \frac{S\mathbf{n}_\infty S}{S \cdot \mathbf{n}_\infty}$

To extract the radius: $r = \sqrt{S^2}$ when $S^2 > 0$

#### Geometric Relationships

The inner product encodes all geometric relationships uniformly:

$$\begin{align}
P_1 \cdot P_2 &= -\frac{1}{2}d^2 && \text{(squared distance)} \\
P \cdot S &= \frac{1}{2}(\|\mathbf{p} - \mathbf{c}\|^2 - r^2) && \text{(power of point)} \\
P \cdot \pi &= \text{signed distance} && \text{(which side of plane)} \\
P \cdot L &= 0 \iff P \text{ lies on } L && \text{(incidence test)}
\end{align}$$

These relationships extend to all object pairs. The meet operation $A \vee B = (A^* \wedge B^*)^*$ computes intersections uniformly—sphere with plane yields circle, plane with plane yields line, sphere with sphere yields circle (or point pair if tangent).

#### Storage and Implementation

A conformal point requires 5 floats in dense representation but exhibits natural sparsity:
- Components 1-3: Euclidean position (always present)
- Component 4: Coefficient of $\mathbf{n}_0$ (typically 1)
- Component 5: Coefficient of $\mathbf{n}_\infty$ (varies with $\|\mathbf{p}\|^2$)

For normalized points where $P \cdot \mathbf{n}_\infty = -1$, only 4 independent components remain. Binary indexing represents basis blades efficiently:
- $\mathbf{e}_1 = 00001_2 = 1$
- $\mathbf{e}_2 = 00010_2 = 2$
- $\mathbf{e}_3 = 00100_2 = 4$
- $\mathbf{n}_0 = 01000_2 = 8$
- $\mathbf{n}_\infty = 10000_2 = 16$

#### Extraction and Normalization

To recover Euclidean coordinates from a conformal point:

$$\mathbf{p} = \frac{P - (P \cdot \mathbf{n}_\infty)\mathbf{n}_0}{-P \cdot \mathbf{n}_\infty}$$

For normalized points where $P \cdot \mathbf{n}_\infty = -1$:

$$\mathbf{p} = P + \mathbf{n}_0$$

Normalization maintains numerical stability: $P_{normalized} = \frac{P}{-P \cdot \mathbf{n}_\infty}$

#### Numerical Considerations

The quadratic term $\frac{1}{2}\mathbf{p}^2\mathbf{n}_\infty$ grows with distance from origin. For a point at distance $d$ from origin, this coefficient scales as $O(d^2)$. In practice:

- Work in local coordinate systems to bound $\|\mathbf{p}\|$
- Normalize periodically to maintain $P \cdot \mathbf{n}_\infty = -1$
- For domains with radius $R$, expect coefficients up to $\frac{1}{2}R^2$

The null cone projection $P_{corrected} = P - \frac{P^2}{2(P \cdot \mathbf{n}_\infty)}\mathbf{n}_\infty$ restores the null property after numerical drift.

#### The Power of Unification

The conformal model transforms our geometric toolkit. Traditional approaches require:
- Separate formulas for point-plane and point-sphere distance
- Different intersection algorithms for each object pair
- Special cases for parallel lines, tangent spheres, points at infinity

In conformal space, these distinctions vanish. One meet operation handles all intersections. One inner product tests all incidences. One sandwich product implements all transformations. The computational cost—5 floats per point versus 3, approximately 3× more operations—purchases this architectural unification.

The next chapter shows how this unification extends to transformations, where translations join rotations as multiplicative operations through the versor mechanism.

---

### TODO: Deferred Tables for Appendix

The following reference material should be incorporated into the appropriate appendices:

**For Appendix B (Formula Reference)**:
- Complete inner product formulas for all object pairs
- Detailed extraction formulas for all geometric properties
- Normalization formulas for each object type
- Null cone projection formula

**For Appendix C (Reference Tables)**:
- Binary representation of all 32 basis blades in 5D CGA
- Complete multiplication table for conformal basis vectors
- Grade structure and dimensions for conformal space
- Sparse storage patterns for common geometric objects
