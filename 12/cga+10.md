### Chapter 10: Probabilistic GA Is Impossible

The null constraint defines conformal points:

$$P^2 = 0$$

This algebraic equation admits no uncertainty. A point either satisfies it exactly or ceases to be a valid conformal point. No probability distribution, confidence interval, or covariance ellipsoid can soften this binary truth.

#### The Incompatibility is Algebraic

Consider a Euclidean point $\mathbf{p} = (x, y, z)$ with positional uncertainty. Standard robotics represents this as:

$$\mathbf{p} \sim \mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\Sigma})$$

where $\boldsymbol{\mu}$ is the mean position and $\boldsymbol{\Sigma}$ is the 3×3 covariance matrix. The conformal embedding:

$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$$

maps each possible $\mathbf{p}$ to a different null vector. But what represents the *distribution* of conformal points? The naive attempt:

$$P \sim \mathcal{N}(P_\mu, ?)$$

immediately fails. The covariance "?" cannot exist in the 5D conformal space while preserving $P^2 = 0$ for all samples. Any Gaussian distribution in $\mathbb{R}^5$ assigns non-zero probability to points with $P^2 \neq 0$—invalid conformal points.

The constraint manifold of null vectors forms a 4D surface in 5D space. Probability distributions on manifolds require specialized treatment (Riemannian statistics), but even these cannot help. The null cone's geometric structure—specifically its intersection with the hyperplane $P \cdot \mathbf{n}_\infty = -1$—creates a non-compact manifold where standard probability measures behave pathologically.

#### What Actually Breaks

**Kalman Filtering**: The workhorse of robotic state estimation assumes:
- Linear (or linearized) dynamics: $\mathbf{x}_{k+1} = F\mathbf{x}_k + \mathbf{w}_k$
- Gaussian noise: $\mathbf{w}_k \sim \mathcal{N}(0, Q)$
- Linear measurements: $\mathbf{z}_k = H\mathbf{x}_k + \mathbf{v}_k$

Attempting this in conformal space:
- Dynamics must preserve null constraint: $P_{k+1}^2 = 0$ always
- But $P_{k+1} = f(P_k) + W_k$ with any additive noise $W_k$ violates this
- Measurements $P \cdot S = d$ (distance to sphere) are nonlinear in conformal coordinates

**Bundle Adjustment**: Vision systems minimize reprojection error:

$$E = \sum_{i,j} \|p_{ij} - \pi(P_j, C_i)\|^2$$

where $p_{ij}$ is the observed 2D point, $P_j$ is the 3D point, and $C_i$ is camera $i$. In GA, both $P_j$ and $C_i$ are null vectors. The optimization must maintain:
- $P_j^2 = 0$ for all 3D points
- $C_i^2 = 0$ for all camera centers
- Updates must move along the null manifold

Standard Gauss-Newton or Levenberg-Marquardt updates:

$$\Delta = -(J^TJ + \lambda I)^{-1}J^T\mathbf{r}$$

will push points off the null cone. Reparameterization (like $P = f(\mathbf{p})$ where $f$ is the conformal embedding) works but sacrifices GA's elegance—you're optimizing in Euclidean space and converting afterward.

**SLAM Factor Graphs**: Modern SLAM exploits conditional independence:

$$p(X, L | Z) \propto \prod_i p(z_i | x_i, l_i)p(x_i | x_{i-1})p(l_j)$$

where $X$ are poses, $L$ are landmarks, and $Z$ are observations. The factorization enables sparse optimization. In GA:
- Each factor involves null vectors
- Marginalization would integrate over the null manifold
- No closed-form solutions exist for null-constrained Gaussians

#### Why This Is Fundamental

The incompatibility isn't a missing feature—it's a category error. Geometric algebra encodes *deterministic* geometric relationships. The null constraint $P^2 = 0$ defines a precise geometric property: having no spatial extent. Probability distributions model *uncertainty* about unknown values.

These represent orthogonal concerns:
- GA: What geometric relationships exist?
- Probability: How certain are we about values?

Attempting to merge them violates both frameworks' assumptions. It's like asking for the probability that parallel lines meet—the question misunderstands what "parallel" means.

#### Hybrid Architectures in Practice

Real systems requiring both geometric elegance and uncertainty quantification use explicit separation:

**State Representation**:
```
Deterministic GA layer:
- Motors M for rigid poses
- Null vectors P for positions
- Meet/join for constraints

Probabilistic overlay:
- Covariance Σ in tangent space
- Particles sampling the manifold
- Information matrices for factor graphs
```

**GTSAM-GA Bridge** (hypothetical but illustrative):
1. Maintain poses as GA motors: $M \in \text{Motor}(\mathbb{R}^{3,1})$
2. Parameterize uncertainty in Lie algebra: $\delta \in \mathfrak{se}(3)$
3. Update via: $M' = M \exp(\delta)$
4. Factor graph operates on $\delta$, not $M$

**Particle Filters on Motors**:
Instead of Gaussians, maintain particles:
- Each particle $M_i$ is a valid motor
- Weights $w_i$ sum to 1
- Resampling preserves geometric constraints
- Mean computation requires care (Fréchet mean on manifold)

#### The Boundary's Value

This limitation isn't a flaw—it's clarity. By acknowledging what GA cannot do, we avoid misapplication and frustration. The frameworks serve different masters:

- **Use GA when**: Geometric relationships are known precisely. Complex transformations must compose. Degeneracies need robust handling.

- **Use probabilistic methods when**: Measurements are noisy. State is partially observable. Decisions require confidence bounds.

- **Use both when**: Deterministic geometric operations (GA) feed probabilistic estimators (traditional). High-level structure (GA) guides low-level uncertainty (particles/Gaussians).

The motor algebra provides elegant rigid motion representation. The meet operation robustly computes intersections. But when the robot asks "Where am I, probably?"—reach for traditional tools. GA ensures your geometric operations are correct. Probability theory quantifies how sure you can be.
