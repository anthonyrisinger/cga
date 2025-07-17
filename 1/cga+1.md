# **Notation at a Glance**

## **Fundamental Elements**

| Symbol | Name | Description | Example |
|--------|------|-------------|---------|
| $\mathbf{e}_i$ | Basis vector | Orthonormal basis vectors of Euclidean space | $\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3$ |
| $\mathbf{n}_0$ | Origin null vector | Represents the origin in conformal space | $\mathbf{n}_0^2 = 0$ |
| $\mathbf{n}_\infty$ | Infinity null vector | Represents the point at infinity | $\mathbf{n}_\infty^2 = 0$, $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ |
| $I$ | Pseudoscalar | Oriented unit volume element | $I = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ (3D), $I^2 = -1$ |
| $\langle \cdot \rangle_k$ | Grade projection | Extracts grade-$k$ part of multivector | $\langle AB \rangle_2$ extracts bivector part |

## **Geometric Objects in Conformal Space**

| Object | Symbol | Grade | Construction | Constraint |
|--------|--------|-------|--------------|------------|
| Point | $P$ | 1 | $P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | $P^2 = 0$ |
| Sphere | $S$ | 1 | $S = \mathbf{c} - \frac{1}{2}r^2\mathbf{n}_\infty$ | $S^2 = r^2$ |
| Plane | $\pi$ | 1 | $\pi = \mathbf{n} + d\mathbf{n}_\infty$ | $\pi^2 = 1$ (normalized) |
| Line | $L$ | 2 | $L = P_1 \wedge P_2 \wedge \mathbf{n}_\infty$ | $L^2 \leq 0$ |
| Circle | $C$ | 2 | $C = S_1 \wedge S_2$ or $P_1 \wedge P_2 \wedge P_3$ | $C^2 > 0$ |
| Point Pair | $PP$ | 2 | $PP = P_1 \wedge P_2$ | $PP^2 \geq 0$ |

## **Fundamental Operations**

| Operation | Symbol | Name | Definition | Properties |
|-----------|--------|------|------------|------------|
| Geometric Product | $AB$ | Full product | Fundamental operation | $AB = A \cdot B + A \wedge B$ for vectors |
| Inner Product | $A \cdot B$ | Symmetric product | $\frac{1}{2}(AB + BA)$ | Grade-lowering, metric |
| Outer Product | $A \wedge B$ | Antisymmetric product | $\frac{1}{2}(AB - BA)$ | Grade-raising, construction |
| Left Contraction | $A \lrcorner B$ | Directional projection | Generalized dot product | $\text{grade}(A \lrcorner B) = \text{grade}(B) - \text{grade}(A)$ |
| Scalar Product | $\langle AB \rangle$ | Scalar part | $\langle AB \rangle_0$ | Extracts grade-0 component |
| Commutator | $[A,B]$ | Lie bracket | $\frac{1}{2}(AB - BA)$ | Generates transformations |
| Dual | $A^*$ | Hodge dual | $AI^{-1}$ | Maps between OPNS and IPNS |
| Reverse | $\tilde{A}$ | Grade involution | Reverses order of vectors | $\widetilde{AB} = \tilde{B}\tilde{A}$ |
| Meet | $A \vee B$ | Intersection | $(A^* \wedge B^*)^*$ | Finds common elements |
| Join | $A \wedge B$ | Span | Outer product (if disjoint) | Constructs containing space |

## **Versors (Transformation Operators)**

| Transformation | Versor | Construction | Action | Parameters |
|----------------|--------|--------------|--------|------------|
| Reflection | $\pi$ | Unit vector/plane | $X' = -\pi X \pi$ | Hyperplane $\pi$ |
| Rotation | $R$ | $R = \cos\frac{\theta}{2} - B\sin\frac{\theta}{2}$ | $X' = RXR^{-1}$ | Angle $\theta$, bivector $B$ |
| Translation | $T$ | $T = 1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$ | $X' = TXT^{-1}$ | Vector $\mathbf{t}$ |
| Scaling | $D$ | $D = e^{\frac{\lambda}{2}\mathbf{n}_0\mathbf{n}_\infty}$ | $X' = DXD^{-1}$ | Scale factor $e^\lambda$ |
| Motor | $M$ | $M = TR$ (screw motion) | $X' = MXM^{-1}$ | Combines rotation + translation |
| Transversion | $K$ | $K = 1 + \mathbf{k}\mathbf{n}_0$ | $X' = KXK^{-1}$ | Special conformal |

## **Grade Structure**

| Grade | Name | Dimension (3D Conformal) | Geometric Interpretation | Example Elements |
|-------|------|-------------------------|-------------------------|------------------|
| 0 | Scalar | 1 | Magnitude | $a$ |
| 1 | Vector | 5 | Points, planes, spheres | $P$, $\pi$, $S$ |
| 2 | Bivector | 10 | Lines, circles, rotations | $L$, $C$, $\mathbf{e}_1\mathbf{e}_2$ |
| 3 | Trivector | 10 | Volumes, dual lines | $\mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ |
| 4 | 4-vector | 5 | Dual points | $P^*$ |
| 5 | Pseudoscalar | 1 | Oriented hypervolume | $I = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3\mathbf{n}_0\mathbf{n}_\infty$ |

## **Key Relations and Tests**

| Test/Relation | Formula | Interpretation |
|---------------|---------|----------------|
| Incidence (OPNS) | $X \wedge A = 0$ | Point $X$ lies on object $A$ |
| Incidence (IPNS) | $X \cdot A = 0$ | Point $X$ satisfies constraint $A$ |
| Distance | $d^2 = -2P_1 \cdot P_2$ | Squared Euclidean distance |
| Angle | $\cos\theta = \frac{\mathbf{a} \cdot \mathbf{b}}{|\mathbf{a}||\mathbf{b}|}$ | Angle between vectors |
| Perpendicular | $A \cdot B = 0$ | Objects are orthogonal |
| Parallel | $A \wedge B = 0$ | Objects are parallel/dependent |

## **Common Formulas**

| Operation | Formula | Notes |
|-----------|---------|-------|
| 3D to CGA point | $P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | Conformal embedding |
| Extract position | $\mathbf{p} = \frac{P \wedge \mathbf{n}_0 \wedge I}{(P \cdot \mathbf{n}_\infty)I\mathbf{n}_\infty}$ | From CGA point |
| Point-plane distance | $d = \frac{P \cdot \pi}{-P \cdot \mathbf{n}_\infty}$ | Signed distance |
| Sphere center | $\mathbf{c} = S\mathbf{n}_\infty S$ | Extract center |
| Sphere radius | $r = \sqrt{S^2}$ | Extract radius |
| Rotor interpolation | $R(t) = R_1(R_1^{-1}R_2)^t$ | SLERP between rotors |

## **Computational Notation**

| Concept | Notation | Description |
|---------|----------|-------------|
| Blade representation | $\mathbf{e}_{i_1...i_k}$ | Basis blade with indices |
| Binary indexing | $\mathbf{e}_J$ where $J = \sum 2^{i-1}$ | Efficient computational indexing |
| Sparse multivector | $M = \sum_i a_i\mathbf{e}_{J_i}$ | Only non-zero coefficients |
| Grade set | $\mathcal{G}(A) = \{k : \langle A \rangle_k \neq 0\}$ | Active grades in multivector |

---

*This notation guide provides essential symbols and formulas for quick reference throughout the text. For detailed explanations and derivations, see the corresponding chapters.*
