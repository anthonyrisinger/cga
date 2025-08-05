### Chapter 11: Probabilistic GA Is Impossible

The null constraint defines conformal points: $P^2 = 0$. This algebraic requirement admits no uncertainty. A point either satisfies it exactly or ceases to be a valid conformal point.

#### The Mathematical Incompatibility

Consider a Euclidean point $\mathbf{p} = (x, y, z)$ with positional uncertainty:

$$\mathbf{p} \sim \mathcal{N}(\boldsymbol{\mu}, \boldsymbol{\Sigma})$$

The conformal embedding maps this to:

$$P = \mathbf{p} + \frac{1}{2}|\mathbf{p}|^2 n_\infty + n_0$$

For $\mathbf{p} = (1, 2, 3)$:

$$P = \begin{pmatrix} 1 \\ 2 \\ 3 \\ 7 \\ 1 \end{pmatrix} \quad \text{satisfying } P^2 = 0$$

Adding Gaussian noise $\mathbf{w} \sim \mathcal{N}(0, \sigma^2 I)$ breaks this constraint:

$$\begin{align}
(P + \mathbf{w})^2 &= P^2 + 2P \cdot \mathbf{w} + \mathbf{w}^2 \\
&= 2P \cdot \mathbf{w} + \mathbf{w}^2 \neq 0
\end{align}$$

Concretely, with $\mathbf{w} = (0.1, 0, 0, 0, 0)$:

$$P' = \begin{pmatrix} 1.1 \\ 2 \\ 3 \\ 7 \\ 1 \end{pmatrix}, \quad (P')^2 = 0.2$$

The point has left the null cone. This isn't numerical error—it's geometric violation.

#### Why Standard Approaches Fail

The null constraint defines a 4D submanifold of measure zero in $\mathbb{R}^5$. Any continuous probability distribution assigns probability one to non-null vectors—invalid conformal points.

**Kalman Filtering** breaks because maintaining $P_{k+1}^2 = 0$ under linear dynamics:

$$P_{k+1} = \mathbf{F}P_k + \mathbf{w}_k$$

requires $\mathbf{w}_k$ to satisfy nonlinear constraints that destroy Gaussian structure and closed-form updates.

**Bundle Adjustment** fails because projecting Gauss-Newton updates back to the null cone:

$$P_j^{proj} = \frac{P_j + \Delta P_j}{|(P_j + \Delta P_j) \cdot n_\infty|}$$

introduces nonlinearity that destroys sparse Jacobian structure.

**Factor Graphs** require closed-form Gaussian products. For null-constrained variables, no such closed form exists while maintaining $P^2 = 0$.

#### The Philosophical Divide

Geometric algebra encodes *ideal* geometric relationships—the discrete symmetries and exact constraints that physical systems approach asymptotically. The null constraint represents perfect pointness, zero extent. Probability theory models *actual* measurements—inherently noisy, uncertain, approximate.

These operate in orthogonal conceptual spaces. Attempting probabilistic ideal points is like assigning uncertainty to theorem truth—a category error.

#### Practical Hybrid Architectures

Real systems separate concerns explicitly:

```cpp
struct GeometricState {
    Motor pose;                    // Rigid transformation
    ConformalPoint position;       // Null vector P^2 = 0

    Blade meet(const Blade& other) const {
        return (dual() ^ other.dual()).dual();
    }
};

struct ProbabilisticState {
    Eigen::Vector6d pose_mean;     // se(3) coordinates
    Eigen::Matrix6d pose_cov;      // Uncertainty
    Eigen::Vector3d point_mean;    // Euclidean position
    Eigen::Matrix3d point_cov;     // Position uncertainty

    GeometricState deterministic() const {
        return {
            Motor::exp(pose_mean),
            ConformalPoint::embed(point_mean)
        };
    }
};
```

**Motor Uncertainty via Lie Algebra**

Motors form a 6D Lie group. Uncertainty lives in the algebra:

$$M = \exp\left(\boldsymbol{\xi}^\wedge\right), \quad \boldsymbol{\xi} \sim \mathcal{N}(\boldsymbol{\mu}_\xi, \boldsymbol{\Sigma}_\xi)$$

```cpp
class UncertainMotor {
    Eigen::Vector6d xi_mean;      // Lie algebra coordinates
    Eigen::Matrix6d xi_cov;       // Covariance in se(3)

public:
    Motor mean() const {
        return Motor::exp(xi_mean);
    }

    Motor sample() const {
        Eigen::Vector6d xi = xi_mean + cholesky(xi_cov) * randn(6);
        return Motor::exp(xi);
    }
};
```

**Particle Filters on Null Manifold**

```cpp
struct ConformalParticle {
    ConformalPoint P;  // Guarantees P^2 = 0
    double weight;
};

class ConformalParticleFilter {
    std::vector<ConformalParticle> particles;

    ConformalPoint mean() const {
        // Compute Fréchet mean on manifold
        Eigen::Vector3d euclidean_mean = Eigen::Vector3d::Zero();
        double total_weight = 0;

        for (const auto& p : particles) {
            euclidean_mean += p.weight * p.P.euclideanPart();
            total_weight += p.weight;
        }

        return ConformalPoint::embed(euclidean_mean / total_weight);
    }
};
```

#### Interface Boundaries

```cpp
class HybridSLAM {
    // Probabilistic backend
    gtsam::NonlinearFactorGraph graph;
    gtsam::Values estimates;

    // GA geometric operations
    ConformalPoint meetLineWithPlane(const Blade& L, const Blade& Pi) {
        Blade result = (L.dual() ^ Pi.dual()).dual();
        return extractPoint(result);
    }

    // Clean conversion at boundaries
    void addGeometricConstraint(const Line& L, const Plane& Pi) {
        ConformalPoint P = meetLineWithPlane(L, Pi);
        Eigen::Vector3d p_euclidean = P.euclideanPart();

        // Convert to factor graph constraint
        graph.add(PointOnPlaneeFactor(p_euclidean, Pi.euclideanNormal()));
    }
};
```

#### The Value of Impossibility

This incompatibility clarifies system design. Rather than forcing unnatural combinations:

**GA handles:**
- Exact constraints: $L \wedge \Pi = 0$
- Robust predicates: parallelism, coincidence
- Transformation chains: $M_n \cdots M_2 M_1$
- Singularity structure: $L_1 \vee L_2 \vee L_3$

**Probability handles:**
- Sensor fusion
- State estimation
- Belief planning
- Uncertainty propagation

Clean interfaces convert between representations only when necessary. Each framework solves problems within its natural domain. The impossibility of probabilistic GA prevents architectural confusion and guides engineers toward robust hybrid designs that leverage both frameworks appropriately.
