### Chapter 11: When Structure Doesn't Align

The geometric product preserves all information. This feature—celebrated throughout this book—becomes a fatal flaw when your problem's efficiency depends on information *destruction*. This chapter maps the boundary between problems GA elegantly solves and those it catastrophically complicates.

#### The Sparsity Catastrophe

Modern visual SLAM tracks 100,000 landmarks across 1,000 camera keyframes—a 300,000-dimensional optimization problem. Yet only 0.01% of the state matrix contains non-zero entries. Why? Physics: cameras can't see through walls or beyond horizons. Each camera observes perhaps 100 nearby landmarks, creating a beautifully sparse Hessian matrix with block-diagonal structure.

Now watch GA destroy this sparsity.

Start with sparse vectors in $\mathbb{R}^{32}$:

$$\mathbf{a} = a_3\mathbf{e}_3 + a_7\mathbf{e}_7 + a_{19}\mathbf{e}_{19} \quad \text{(3 non-zero components)}$$

$$\mathbf{b} = b_2\mathbf{e}_2 + b_{11}\mathbf{e}_{11} + b_{23}\mathbf{e}_{23} \quad \text{(3 non-zero components)}$$

Their geometric product:

$$\mathbf{ab} = \underbrace{(a_3b_2\mathbf{e}_3\mathbf{e}_2 + a_3b_{11}\mathbf{e}_3\mathbf{e}_{11} + \ldots)}_{\text{9 bivector terms}} + \underbrace{(\text{scalar terms where indices match})}_{\text{up to 3 terms}}$$

Six non-zero inputs produce up to twelve non-zero outputs across multiple grades. Continue:

$$(\mathbf{ab})\mathbf{c} = \text{grades } 1, 3 + \text{scalar contamination}$$

$$((\mathbf{ab})\mathbf{c})\mathbf{d} = \text{fully dense across grades } 0, 2, 4$$

The geometric product mixes grades promiscuously. Information that started in isolated components spreads everywhere. This isn't a bug—it's the entire point. The product preserves all geometric relationships.

But SLAM's efficiency depends on statistical independence: distant landmarks don't interact. This independence manifests as matrix sparsity, enabling:
- Sparse Cholesky factorization
- Linear-time information filters
- Efficient marginalization

GA offers no sparse geometric product. There's no "conditional independence wedge." The algebra is fundamentally dense because geometry is fundamentally interconnected.

**You cannot have both complete geometric information and exploitable conditional independence.**

#### Graph Problems in Geometric Clothing

Consider path planning from San Francisco to New York. The algorithm needs:

```
while (!queue.empty()) {
    current = queue.extract_min();
    if (current == target) return path;
    for (neighbor : graph.neighbors(current)) {
        new_cost = cost[current] + edge_weight(current, neighbor);
        if (new_cost < cost[neighbor]) {
            cost[neighbor] = new_cost;
            parent[neighbor] = current;
            queue.update(neighbor, new_cost);
        }
    }
}
```

Where do motors help? Where does the meet operation apply? Nowhere.

You might embed cities as position vectors, but this creates lies:
- Denver to Chicago: 1000 miles (geometric distance)
- Denver to Chicago: $180 + 4 hours (actual cost via United Airlines)
- The "distance" changes with time of day, season, and airline pricing algorithms

Road networks compound the deception:
- One-way streets: $d(A \to B) \neq d(B \to A)$
- Turn restrictions: can't compose arbitrary paths
- Traffic dynamics: edge costs vary hourly

The graph structure is primary. Geometric embedding is incidental and misleading.

This mismatch afflicts entire problem classes:

**Integer Programming**: Find $\mathbf{x} \in \mathbb{Z}^n$ maximizing $\mathbf{c}^T\mathbf{x}$ subject to $A\mathbf{x} \leq \mathbf{b}$. The feasible region is discrete lattice points, not a continuous manifold. No rotation or reflection maps one feasible point to another.

**Combinatorial Optimization**: The traveling salesman visits each city exactly once. Permutations aren't rotations—there's no "halfway between" visiting Chicago first or second.

**Constraint Satisfaction**: Assign colors to graph vertices so no edge connects same-colored vertices. The constraints are logical (NOT(red AND red)), not geometric.

GA adds overhead without insight because the problems aren't geometric.

#### Matrix Factorizations and Numerical Linear Algebra

Solving $A\mathbf{x} = \mathbf{b}$ drives scientific computing. Decades of research produced algorithms that deliberately destroy matrix structure to enable fast solving:

**LU Decomposition**:
$$PA = LU \text{ where } L = \begin{pmatrix}
1 & 0 & \cdots & 0 \\
l_{21} & 1 & \cdots & 0 \\
\vdots & \ddots & \ddots & \vdots \\
l_{n1} & \cdots & l_{n,n-1} & 1
\end{pmatrix}, \quad U = \begin{pmatrix}
u_{11} & u_{12} & \cdots & u_{1n} \\
0 & u_{22} & \cdots & u_{2n} \\
\vdots & \ddots & \ddots & \vdots \\
0 & \cdots & 0 & u_{nn}
\end{pmatrix}$$

Gaussian elimination systematically zeros the subdiagonal, creating triangular structure. Forward substitution on $L\mathbf{y} = P\mathbf{b}$:

$$y_1 = (Pb)_1$$
$$y_2 = (Pb)_2 - l_{21}y_1$$
$$y_3 = (Pb)_3 - l_{31}y_1 - l_{32}y_2$$

Each step uses only previously computed values—enabled by zeros below the diagonal. Back substitution on $U\mathbf{x} = \mathbf{y}$ works similarly. Total cost: $O(n^2)$ after factorization.

Can GA reformulate this? The orthogonal matrix $Q$ in QR decomposition maps to a versor. But "upper triangular" has no GA meaning. There's no "upper triangular multivector" because components aren't ordered like matrix entries. More fundamentally, the *zeros* make the algorithm fast. GA preserves information where numerical linear algebra destroys it.

**The information destruction that GA avoids is exactly what makes these algorithms fast.**

#### Fixed-Topology Pipeline Requirements

Modern GPUs implement this pipeline in silicon:

$$\text{Vertices} \xrightarrow{\text{4×4 matrix}} \text{Clip Space} \xrightarrow{\text{Rasterize}} \text{Fragments} \xrightarrow{\text{Shade}} \text{Pixels}$$

The hardware assumes:
1. Vertices are 4-vectors (x, y, z, w)
2. Transformations are 4×4 matrices
3. Interpolation is linear in screen space
4. Depth is a scalar for comparison

You cannot feed 32-component CGA multivectors to a vertex shader—the silicon literally has 4-wide SIMD units. The rasterizer interpolates scalars and vectors, not bivectors or null vectors. The depth buffer compares single floats via dedicated circuits.

Similar constraints bind all performance-critical domains:

**Digital Signal Processors**: Implement $y[n] = \sum_{k=0}^{N-1} h[k]x[n-k]$ with fixed-point multiply-accumulate units. No geometric product in silicon.

**Neural Accelerators**: TPUs multiply int8 matrices for $\mathbf{y} = \text{ReLU}(W\mathbf{x} + \mathbf{b})$. Quantization to 8 bits destroys GA's algebraic structure.

**Quantum Processors**: Apply gates from fixed sets {H, CNOT, T}. These are unitary matrices, not multivectors. The hardware implements specific 2×2 and 4×4 complex matrices.

When performance demands specialized hardware, you must speak its language. Silicon doesn't care about mathematical elegance.

#### The Reframing Question

Before abandoning GA, ask: does hidden geometric structure exist?

**Success: Crystallography**. The 230 space groups seemed purely discrete until viewed as versor groups. Each symmetry operation—rotation, reflection, glide, screw—is a versor in GA. The discrete group multiplication table emerges from continuous geometric products. GA revealed structure that matrix representations obscured.

**Success: Area Lights**. Analytical integration over light sources seemed purely computational. But representing lights as bivector-valued (intensity + orientation) enabled closed-form solutions. The geometric structure was always there, hidden in the integrals.

**Failure: Database Joins**. No amount of reframing makes SELECT statements geometric. Relations are logical, not spatial.

The key: look for *hidden* geometric structure, not *forced* geometric analogies.

#### The Hybrid Reality

When global GA adoption fails, tactical deployment can still add value:

**CAD Robustness**: A commercial CAD system kept traditional B-rep structures but replaced all intersection tests with GA's meet operation. Result: 45% fewer edge-case failures, 2.3× computational overhead. For design software where correctness trumps speed, this trade-off works.

**Robot Architecture**: Task planner uses motors for pose representation—enabling smooth interpolation and elegant singularity analysis. Joint controllers receive converted 4×4 matrices, maintaining 1kHz control loops with optimized linear algebra. The system boundary aligns with performance requirements.

**Offline Analysis**: Crystallography software uses GA to enumerate symmetry operations, identify equivalences, and generate optimal viewing angles. Results export as rotation matrices for runtime graphics. GA provides insight during analysis; matrices provide performance during execution.

This isn't compromise—it's engineering. Use appropriate mathematics for each subdomain.

#### Recognizing Structural Misalignment

Warning signs that GA doesn't fit your problem:

**Forced Embeddings**: You're inventing geometric meaning. "Customer preference vectors"? "Database relationship multivectors"? If the geometric interpretation feels artificial, it is.

**Representation Explosion**: Operations produce increasingly complex multivectors. Cache misses dominate runtime. Memory bandwidth becomes the bottleneck.

**Missing Operations**: Your algorithms need non-geometric operations—eigendecomposition, convolution, graph traversal, linear programming. GA offers no geometric insight because none exists.

**Hardware Barriers**: Your platform has fixed mathematical assumptions. Fighting hardware is futile.

**Discrete Structure**: Your problem involves permutations, combinations, or finite state machines. Continuous geometric transformations don't meaningfully map to discrete transitions.

When you see these signs, stop. GA won't help. Use appropriate abstractions:
- Sparse matrices for statistical independence
- Graph algorithms for network structures
- Numerical linear algebra for solving equations
- Discrete optimization for combinatorial problems

Recognizing these boundaries isn't failure—it's mathematical maturity.
