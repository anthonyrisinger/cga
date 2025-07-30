### Appendix B: Formula Reference

#### Conformal Embeddings

**Point Embedding** (Euclidean → Conformal)
$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$$

Properties: $P^2 = 0$, often normalized to $P \cdot \mathbf{n}_\infty = -1$

**Point Extraction** (Conformal → Euclidean)

Direct extraction:
$$\mathbf{p} = \frac{P - (P \cdot \mathbf{n}_\infty)\mathbf{n}_0}{-P \cdot \mathbf{n}_\infty}$$

Normalized points ($P \cdot \mathbf{n}_\infty = -1$):
$$\mathbf{p} = P + \mathbf{n}_0$$

Wedge formulation:
$$\mathbf{p} = \frac{P \wedge \mathbf{n}_0 \wedge I_n}{-(P \cdot \mathbf{n}_\infty)I_n\mathbf{n}_\infty}$$

**Sphere Representation** (IPNS)
$$S = C - \frac{1}{2}r^2\mathbf{n}_\infty$$

where $C = \mathbf{c} + \frac{1}{2}\|\mathbf{c}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ (conformal center)

- Radius: $r = \sqrt{S^2}$ when $S^2 > 0$
- Center: $\mathbf{c} = \frac{S\mathbf{n}_\infty S}{S \cdot \mathbf{n}_\infty}$
- Point sphere: $S^2 = 0$

**Plane Representation** (IPNS)
$$\pi = \mathbf{n} + d\mathbf{n}_\infty$$

Unit normal $\mathbf{n}$, signed distance $d$. Property: $\pi^2 = 1$

#### Distance and Incidence Relations

**Point-Point Distance**
$$d^2 = -2P_1 \cdot P_2$$

Direct squared distance—no square root needed for comparisons.

**Point-Plane Distance**
$$d = \frac{P \cdot \pi}{-P \cdot \mathbf{n}_\infty}$$

Normalized points: $d = P \cdot \pi$

**Point-Sphere Power**
$$P \cdot S = \frac{1}{2}(d_{PC}^2 - r^2)$$

Classification: negative (inside), zero (on), positive (outside)

**Point-Line Distance**
$$d^2 = \frac{-2(P \wedge L)^2}{L^2}$$

#### Angle Formulas

**Vector Angle**
$$\cos\theta = \frac{\mathbf{a} \cdot \mathbf{b}}{\|\mathbf{a}\|\|\mathbf{b}\|}$$

**Bivector Angle**
$$\cos\theta = \frac{\langle B_1\tilde{B}_2 \rangle_0}{\|B_1\|\|B_2\|}$$

**Plane Angle** (unit normals)
$$\cos\theta = \pi_1 \cdot \pi_2$$

**Line Angle**
$$\cos\theta = \frac{|L_1 \cdot L_2|}{\|L_1\|\|L_2\|}$$

#### Transformations

**Reflection**
$$X' = -\pi X \pi$$

Unit hyperplane $\pi$. Operation count: ~30 FLOPs for vectors.

**Rotation**
$$R = \exp\left(-\frac{\theta}{2}B\right) = \cos\frac{\theta}{2} - \sin\frac{\theta}{2}B$$

Unit bivector $B$ ($B^2 = -1$), angle $\theta$. Apply: $X' = RX\tilde{R}$

**Translation**
$$T = 1 - \frac{\mathbf{t}\mathbf{n}_\infty}{2}$$

Key: $(\mathbf{t}\mathbf{n}_\infty)^2 = 0$ truncates exponential series.

**Uniform Scaling**
$$D = \exp\left(\frac{\lambda}{2}\mathbf{n}_0\mathbf{n}_\infty\right) = \cosh\frac{\lambda}{2} + \sinh\frac{\lambda}{2}\mathbf{n}_0\mathbf{n}_\infty$$

Scale factor $s = e^\lambda$

**Motor** (Screw Motion)
$$M = \exp\left(-\frac{\theta}{2}L^*I_c - \frac{d}{2}\mathbf{n}_\infty\right)$$

Line $L$, rotation $\theta$, translation $d$ along axis. Common form: $M = TR$

**Sphere Inversion**
$$X' = SXS^{-1}$$

Sphere $S$ with $S^2 = r^2$

#### Intersection Operations

**Universal Meet**
$$A \vee B = (A^* \wedge B^*)^*$$

Operation count: ~160 FLOPs (32 dual + 64 wedge + 32 dual + 32 extraction)

**Intersection Results**

| First Object | Second Object | Result Type | Grade |
|--------------|---------------|-------------|-------|
| Line | Plane | Point | 1 |
| Plane | Plane | Line | 2 |
| Sphere | Plane | Circle | 2 |
| Sphere | Sphere | Circle | 2 |
| Line | Sphere | Point pair | 2 |
| Three planes | — | Point | 1 |

#### Object Construction

**Line** (two points): $L = P_1 \wedge P_2 \wedge \mathbf{n}_\infty$

**Plane** (three points): $\pi = (P_1 \wedge P_2 \wedge P_3 \wedge \mathbf{n}_\infty)^*$

**Circle** (three points): $C = P_1 \wedge P_2 \wedge P_3$

**Sphere** (four points): $S = (P_1 \wedge P_2 \wedge P_3 \wedge P_4)^*$

#### Projections

**Point onto Line**

Direct: $P_{\text{proj}} = \frac{(P \lrcorner L)L}{L \cdot \tilde{L}}$

Via meet: $P_{\text{proj}} = L \vee (P \wedge \mathbf{n}_\infty)$

**Point onto Plane**
$$P_{\text{proj}} = P - 2(P \cdot \pi)\pi$$

For non-unit plane: $P_{\text{proj}} = P - 2\frac{P \cdot \pi}{\pi^2}\pi$

**Line onto Plane**
$$L_{\text{proj}} = (L \lrcorner \pi^*) \vee \pi$$

#### Interpolation

**Linear Point Interpolation**
$$P(t) = \frac{(1-t)P_0 + tP_1}{\|(1-t)P_0 + tP_1\|}$$

Maintains null constraint through normalization.

**Rotor Interpolation** (SLERP)
$$R(t) = R_0\exp(t\log(R_0^{-1}R_1))$$

where $\log(R) = \frac{\theta}{2}B$ for $R = \cos\frac{\theta}{2} + \sin\frac{\theta}{2}B$

**Motor Interpolation**
$$M(t) = M_0\exp(t\log(M_0^{-1}M_1))$$

Produces screw motion path. Decomposition:
- Rotation part: $R = \frac{M}{\|M\|}$
- Translation part: $T = M\tilde{R}$

#### Numerical Stability

**Null Cone Projection**
$$P = P' - \frac{P'^2}{2(P' \cdot \mathbf{n}_\infty)}\mathbf{n}_\infty$$

Projects approximately null vector onto exact null cone.

**Versor Normalization**
$$V = \frac{V'}{\sqrt{|\langle V'\tilde{V'} \rangle_0|}}$$

Maintains versor constraint $V\tilde{V} = \pm 1$

**Robust Line Distance** (skew lines)
$$d = \frac{\|(L_1 \vee L_2) \cdot \mathbf{n}_\infty\|}{\|L_1 \wedge L_2\|}$$

**Blade Normalization**
$$B_{\text{unit}} = \frac{B}{\sqrt{|B \cdot \tilde{B}|}}$$

#### Special Computational Forms

**Bivector Exponential**

For $B^2 = -\theta^2 < 0$:
$$\exp(B) = \begin{cases}
1 + B + \frac{B^2}{2} + \frac{B^3}{6} & \text{if } \theta < \epsilon \\
\cos\theta + \frac{\sin\theta}{\theta}B & \text{otherwise}
\end{cases}$$

For $B^2 = \phi^2 > 0$:
$$\exp(B) = \cosh\phi + \frac{\sinh\phi}{\phi}B$$

For $B^2 = 0$ (null):
$$\exp(B) = 1 + B$$

**Rotor from Two Vectors**
$$R = \frac{1 + \mathbf{b}\mathbf{a}}{\|1 + \mathbf{b}\mathbf{a}\|}$$

Rotates unit vector $\mathbf{a}$ to unit vector $\mathbf{b}$. Handles $\mathbf{a} = -\mathbf{b}$ by choosing arbitrary perpendicular axis.

**Motor Logarithm**

For motor $M = T(\mathbf{t})R(\theta, B)$:
$$\log(M) = \frac{\theta}{2}B^*I_c + \frac{\mathbf{t} \cdot \mathbf{u}}{2}\mathbf{n}_\infty + \frac{\mathbf{t} \wedge \mathbf{u}}{2\sin(\theta/2)}$$

where $\mathbf{u}$ is the rotation axis direction.
