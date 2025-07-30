## Appendices

### Appendix A: Notation Reference

#### Core Symbols

| Symbol | Type | Grade | Definition |
|--------|------|-------|------------|
| $a, b, c, \alpha, \beta, \gamma, \lambda, \mu, \nu$ | Scalar | 0 | Parameters, angles, indices |
| $\mathbf{a}, \mathbf{b}, \mathbf{v}, \mathbf{p}, \mathbf{q}, \mathbf{r}$ | Vector | 1 | Bold: Euclidean vectors |
| $\mathbf{e}_i$ | Basis vector | 1 | $\mathbf{e}_i \cdot \mathbf{e}_j = \delta_{ij}$ |
| $\mathbf{e}_{i_1i_2...i_k}$ | Basis blade | $k$ | $\mathbf{e}_{i_1} \wedge \mathbf{e}_{i_2} \wedge \cdots \wedge \mathbf{e}_{i_k}$ |
| $A, B, C, M$ | Multivector | Mixed | General elements |
| $X, Y, Z$ | Geometric object | Varies | Points, lines, planes, spheres |
| $\langle A \rangle_k$ | Grade projection | $k$ | Grade-$k$ part of $A$ |
| $A^{(k)}$ | Pure $k$-vector | $k$ | Alternative notation |
| $\mathcal{G}(A)$ | Grade set | — | $\{k : \langle A \rangle_k \neq 0\}$ |

#### Conformal Basis

| Symbol | Name | Defining Relations |
|--------|------|-------------------|
| $\mathbf{n}_0$ | Origin | $\mathbf{n}_0^2 = 0$, $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ |
| $\mathbf{n}_\infty$ | Infinity | $\mathbf{n}_\infty^2 = 0$, $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ |
| $\mathbf{e}_+, \mathbf{e}_-$ | Null generators | $\mathbf{e}_+^2 = 1$, $\mathbf{e}_-^2 = -1$ |
| — | Construction | $\mathbf{n}_0 = \frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+)$, $\mathbf{n}_\infty = \mathbf{e}_- + \mathbf{e}_+$ |

#### Products and Operations

| Operation | Symbol | Grade Result | Definition |
|-----------|--------|--------------|------------|
| Geometric | $AB$ | Mixed | Fundamental product |
| Inner | $A \cdot B$ | $\|$grade$(A) -$ grade$(B)\|$ | Symmetric part |
| Outer | $A \wedge B$ | grade$(A) +$ grade$(B)$ | Antisymmetric part |
| Scalar | $\langle AB \rangle$ or $\langle AB \rangle_0$ | 0 | Scalar part |
| Left contraction | $A \lrcorner B$ | grade$(B) -$ grade$(A)$ | $\langle AB \rangle_{\text{grade}(B)-\text{grade}(A)}$ |
| Right contraction | $A \llcorner B$ | grade$(A) -$ grade$(B)$ | $\langle AB \rangle_{\text{grade}(A)-\text{grade}(B)}$ |
| Fat dot | $A \bullet B$ | $\|$grade$(A) -$ grade$(B)\|$ | $\langle AB \rangle_{\|$grade$(A)-$grade$(B)\|}$ |
| Commutator | $[A,B]$ | Bivector | $\frac{1}{2}(AB - BA)$ |
| Anticommutator | $\{A,B\}$ | Mixed | $\frac{1}{2}(AB + BA)$ |
| Meet | $A \vee B$ | Intersection | $(A^* \wedge B^*)^*$ |
| Join | $A \wedge B$ | Union | Direct wedge if disjoint |

#### Transformations

| Operation | Symbol | Action | Type |
|-----------|--------|--------|------|
| Reverse | $\tilde{A}$ | $\widetilde{a_1a_2...a_k} = a_k...a_2a_1$ | Involution |
| Grade involution | $\hat{A}$ | $\sum_k (-1)^k \langle A \rangle_k$ | Involution |
| Clifford conjugation | $\bar{A}$ | $\widetilde{\hat{A}} = \hat{\tilde{A}}$ | Involution |
| Dual | $A^*$ | $AI^{-1}$ | Grade: $n - \text{grade}(A)$ |
| Inverse | $A^{-1}$ | $\tilde{A}/\langle A\tilde{A} \rangle_0$ | When $\langle A\tilde{A} \rangle_0 \neq 0$ |
| Sandwich | $VXV^{-1}$ | Versor transformation | Preserves grade |

#### Versors

| Versor | Form | Parameter | Transformation |
|--------|------|-----------|----------------|
| Reflection | $\sigma$ | Unit vector | $X' = -\sigma X \sigma$ |
| Rotor | $R = e^{-\theta B/2}$ | Bivector $B$, angle $\theta$ | $X' = RX\tilde{R}$ |
| Translator | $T = 1 - \frac{\mathbf{t}\mathbf{n}_\infty}{2}$ | Vector $\mathbf{t}$ | $X' = TX\tilde{T}$ |
| Dilator | $D = e^{\lambda\mathbf{n}_0\mathbf{n}_\infty/2}$ | Scale $\ln(s) = \lambda$ | $X' = DX\tilde{D}$ |
| Motor | $M = TR$ | Screw parameters | $X' = MX\tilde{M}$ |
| Transversor | $K = 1 + \mathbf{k}\mathbf{n}_0$ | Vector $\mathbf{k}$ | Special conformal |

#### Pseudoscalars and Metrics

| Space | Dimension | Signature | Pseudoscalar | $I^2$ |
|-------|-----------|-----------|--------------|-------|
| $\mathbb{R}^2$ | 2D | $(2,0,0)$ | $I_2 = \mathbf{e}_1\mathbf{e}_2$ | $-1$ |
| $\mathbb{R}^3$ | 3D | $(3,0,0)$ | $I_3 = \mathbf{e}_1\mathbf{e}_2\mathbf{e}_3$ | $-1$ |
| $\mathbb{R}^{3,1}$ | Conformal 2D | $(3,1,0)$ | $I_c = I_2\mathbf{n}_\infty\mathbf{n}_0$ | $-1$ |
| $\mathbb{R}^{4,1}$ | Conformal 3D | $(4,1,0)$ | $I_c = I_3\mathbf{n}_\infty\mathbf{n}_0$ | $-1$ |
| $\mathbb{R}^{1,3}$ | Spacetime | $(1,3,0)$ | $I_4 = \gamma_0\gamma_1\gamma_2\gamma_3$ | $+1$ |

#### Computational Elements

| Concept | Symbol | Definition | Implementation |
|---------|--------|------------|----------------|
| Complexity | $O(f(n))$ | Asymptotic bound | Upper bound on operations |
| Machine epsilon | $\epsilon$ | Smallest $1 + \epsilon \neq 1$ | $\approx 2.22 \times 10^{-16}$ |
| Condition number | $\kappa$ | $\|A\|\|A^{-1}\|$ | Numerical sensitivity |
| Grade dimension | $\binom{n}{k}$ | Components in grade $k$ | $n$-dimensional space |
| Vector derivative | $\nabla$ | $\sum_i \mathbf{e}^i\partial_i$ | Geometric calculus |

#### Binary Blade Encoding

| Operation | Formula | Example | Result |
|-----------|---------|---------|--------|
| Blade index | Binary representation | $\mathbf{e}_1\mathbf{e}_3 \rightarrow 101_2 = 5$ | Decimal index |
| Grade extraction | `popcount(index)` | `popcount(5) = 2` | Blade grade |
| Blade product | `index_a XOR index_b` | `3 XOR 6 = 5` | Product index |
| Sign computation | Count swaps to canonical | $\mathbf{e}_3\mathbf{e}_1 \rightarrow -\mathbf{e}_1\mathbf{e}_3$ | $(-1)^{\text{swaps}}$ |

#### Geometric Objects (CGA)

| Object | IPNS | OPNS | Grade | Test |
|--------|------|------|-------|------|
| Point $P$ | $\mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | Same | 1 | $P^2 = 0$ |
| Sphere $S$ | $C - \frac{1}{2}r^2\mathbf{n}_\infty$ | $(P_1 \wedge P_2 \wedge P_3 \wedge P_4)^*$ | 1 | $S^2 = r^2$ |
| Plane $\pi$ | $\mathbf{n} + d\mathbf{n}_\infty$ | $(P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty)^*$ | 1 | $\pi^2 = 1$ |
| Line $L$ | $(\pi_1 \wedge \pi_2)^*$ | $P_1 \wedge P_2 \wedge \mathbf{n}_\infty$ | 2/3 | Plücker |
| Circle $C$ | $(S_1 \wedge S_2)^*$ | $P_1 \wedge P_2 \wedge P_3$ | 2/3 | $C^2 > 0$ |

#### Specialized Notations

| Symbol | Context | Definition |
|--------|---------|------------|
| $F = \mathbf{E} + I\mathbf{B}$ | Electromagnetism | Bivector field |
| $\gamma_\mu$ | Spacetime | Dirac matrices as vectors |
| $\psi$ | Quantum | Spinor (even multivector) |
| $L = \mathbf{r} \wedge \mathbf{p}$ | Physics | Angular momentum bivector |
| $\mathcal{R}$ | Gauge theory | Curvature bivector |
| $\text{Log}(R)$ | Lie theory | $\frac{\theta}{2}B$ for rotor $R$ |
