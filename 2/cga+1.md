# **Notation at a Glance**

## **Vectors and Multivectors**

| Symbol | Meaning | Grade | Example/Notes |
|--------|---------|-------|---------------|
| $a, b, c$ | Scalars | 0 | Real numbers, field elements |
| $\mathbf{a}, \mathbf{b}, \mathbf{v}$ | Euclidean vectors | 1 | Bold lowercase, $\mathbf{v} \in \mathbb{R}^3$ |
| $\mathbf{e}_i$ | Orthonormal basis vectors | 1 | $\mathbf{e}_1^2 = \mathbf{e}_2^2 = \mathbf{e}_3^2 = 1$ |
| $\mathbf{e}_{ij...k}$ | Basis blade | varies | $\mathbf{e}_{123} = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ |
| $A, B, M$ | General multivectors | mixed | $A = \sum_I a_I \mathbf{e}_I$ |
| $\langle A \rangle_k$ | Grade-$k$ projection | k | Extract grade-$k$ part of $A$ |

## **Conformal Basis Elements**

| Symbol | Meaning | Properties | Geometric Role |
|--------|---------|------------|----------------|
| $\mathbf{n}_0$ | Origin null vector | $\mathbf{n}_0^2 = 0$ | Represents point at origin |
| $\mathbf{n}_\infty$ | Infinity null vector | $\mathbf{n}_\infty^2 = 0$ | Represents point at infinity |
| $\mathbf{n}_0 \cdot \mathbf{n}_\infty$ | Null vector inner product | $= -1$ | Defines conformal metric |
| $\mathbf{e}_+, \mathbf{e}_-$ | Null vector construction | $\mathbf{e}_+^2 = 1, \mathbf{e}_-^2 = -1$ | $\mathbf{n}_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+)$ |

## **Fundamental Operations**

| Operation | Symbol | Definition | Grade Effect |
|-----------|--------|------------|--------------|
| Geometric product | $AB$ or $A \cdot B + A \wedge B$ | Fundamental product | Mixed |
| Inner product | $A \cdot B$ | Symmetric contraction | Lowers grade: $\|g(A) - g(B)\|$ |
| Outer product | $A \wedge B$ | Antisymmetric extension | Raises grade: $g(A) + g(B)$ |
| Scalar product | $\langle AB \rangle$ or $\langle AB \rangle_0$ | Scalar part of $AB$ | Grade 0 |
| Left contraction | $A \lrcorner B$ | Generalized dot product | $g(B) - g(A)$ |
| Fat dot product | $A \bullet B$ | $\langle AB \rangle_{\|g(A)-g(B)\|}$ | Symmetric grade |

## **Geometric Objects in CGA**

| Object | Symbol | IPNS Representation | OPNS Representation | Key Property |
|--------|--------|---------------------|---------------------|--------------|
| Point | $P$ | $\mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | Same (grade 1) | $P^2 = 0$ |
| Sphere | $S$ | $P - \frac{1}{2}r^2\mathbf{n}_\infty$ (grade 1) | $(P_1 \wedge P_2 \wedge P_3 \wedge P_4)^*$ (grade 4) | Center + radius |
| Plane | $\pi$ | $\mathbf{n} + d\mathbf{n}_\infty$ (grade 1) | $(P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty)^*$ (grade 4) | Normal + distance |
| Line | $L$ | $(\pi_1 \wedge \pi_2)^*$ (grade 3) | $P_1 \wedge P_2 \wedge \mathbf{n}_\infty$ (grade 2) | Through 2 points |
| Circle | $C$ | $(S \wedge \pi)^*$ (grade 3) | $P_1 \wedge P_2 \wedge P_3$ (grade 2) | 3 points or sphere-plane |
| Point pair | $PP$ | $(S_1 \wedge S_2)^*$ (grade 3) | $P_1 \wedge P_2$ (grade 2) | 2-point set |

## **Transformations (Versors)**

| Transform | Symbol | Construction | Application | Type |
|-----------|--------|--------------|-------------|------|
| Reflection | $\sigma$ | Unit vector (hyperplane) | $X' = -\sigma X \sigma$ | Odd versor |
| Rotation | $R$ | $\exp(-\frac{\theta}{2}B)$ | $X' = RXR^{-1}$ | Even versor (rotor) |
| Translation | $T$ | $1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$ | $X' = TXT^{-1}$ | Even versor |
| Scaling | $D$ | $\exp(\frac{\lambda}{2}\mathbf{n}_0\mathbf{n}_\infty)$ | $X' = DXD^{-1}$ | Even versor |
| Inversion | $I$ | Sphere $S$ | $X' = SXS^{-1}$ | Odd versor |
| Motor | $M$ | $TR$ (screw motion) | $X' = MXM^{-1}$ | Even versor |

## **Special Operations and Relations**

| Operation | Symbol | Meaning | Computation |
|-----------|--------|---------|-------------|
| Reverse | $\tilde{A}$ | Reverse blade factors | $\widetilde{\mathbf{e}_i\mathbf{e}_j} = \mathbf{e}_j\mathbf{e}_i$ |
| Grade involution | $\hat{A}$ | Alternating sign by grade | $\hat{A} = \sum_k (-1)^k \langle A \rangle_k$ |
| Clifford conjugation | $\bar{A}$ | $\bar{A} = \tilde{\hat{A}}$ | Combined reverse and grade involution |
| Dual | $A^*$ | Hodge dual | $A^* = AI^{-1}$ where $I$ is pseudoscalar |
| Meet | $\vee$ | Intersection | $(A^* \wedge B^*)^*$ |
| Join | $\wedge$ | Span/Union | Direct for disjoint objects |
| Commutator | $[A,B]$ | $\frac{1}{2}(AB - BA)$ | Lie bracket, bivector-valued |

## **Computational Notation**

| Notation | Meaning | Usage |
|----------|---------|-------|
| $\mathcal{O}(n)$ | Complexity class | Algorithm analysis |
| $\epsilon$ | Machine epsilon | Numerical tolerance |
| $\text{grade}(A)$ | Grade extraction | Returns set of non-zero grades |
| $\|A\|$ | Magnitude | $\sqrt{\langle A\tilde{A}\rangle_0}$ for positive definite |
| $A^{-1}$ | Inverse | $\tilde{A}/\langle A\tilde{A}\rangle_0$ when it exists |
| $I_k$ | Grade-$k$ pseudoscalar | Highest grade element in $k$-D space |

## **Index Notation for Implementation**

| Concept | Binary Representation | Example |
|---------|---------------------|---------|
| Blade index | Bits indicate basis vectors | $\mathbf{e}_{13} \rightarrow 101_2 = 5$ |
| Grade computation | Population count of index | $\text{grade}(5) = \text{popcount}(101_2) = 2$ |
| Blade product sign | Swap count parity | Count transpositions for reordering |

## **Common Formulas Quick Reference**

| Purpose | Formula | Notes |
|---------|---------|-------|
| Euclidean to conformal | $P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | Standard embedding |
| Distance squared | $d^2 = -2P_1 \cdot P_2$ | Between conformal points |
| Sphere through 4 points | $S = (P_1 \wedge P_2 \wedge P_3 \wedge P_4)^*$ | Dualize 4-blade |
| Line-plane intersection | $P = L \vee \pi$ | Universal meet operation |
| Rotor from bivector | $R = \cos\frac{\theta}{2} - B\sin\frac{\theta}{2}$ | Unit bivector $B$ |
| Motor interpolation | $M(t) = M_1(M_1^{-1}M_2)^t$ | Smooth screw motion |

## **Algebraic Properties Reference**

| Property | Statement | Significance |
|----------|-----------|--------------|
| Associativity | $(AB)C = A(BC)$ | Allows unambiguous products |
| Non-commutativity | $AB \neq BA$ in general | Encodes geometric relationships |
| Distribution | $A(B + C) = AB + AC$ | Linear structure preserved |
| Reverse property | $\widetilde{AB} = \tilde{B}\tilde{A}$ | Like transpose for matrices |
| Outermorphism | $f(A \wedge B) = f(A) \wedge f(B)$ | Linear maps extend naturally |

---

*This notation guide provides immediate access to the symbols and operations used throughout the text. Each entry includes enough context for quick comprehension while maintaining mathematical precision. For detailed explanations and derivations, consult the corresponding chapters.*
