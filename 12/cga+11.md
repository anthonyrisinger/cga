### Chapter 11: When Structure Doesn't Align

You've built motors that elegantly encode screw motion. You've seen how the conformal model linearizes translation through five-dimensional null vectors. The meet operation promises to replace dozens of specialized intersection algorithms with one universal formula. But now your SLAM system needs to process 100,000 landmarks with conditional independence structure, or your neural network requires specific sparse matrix factorizations, or your path planning algorithm fundamentally operates on graphs.

The algebra fights back. What was elegant becomes cumbersome. What was unified becomes forced. This chapter examines the boundary where geometric algebra's structure fundamentally mismatches your problem's structure—not a performance issue that clever optimization might fix, but an architectural mismatch that no amount of engineering can reconcile.

#### The Sparsity Catastrophe

Consider a basic fact: geometric products mix grades. Multiply two vectors in 3D:

$$\mathbf{a}\mathbf{b} = \mathbf{a} \cdot \mathbf{b} + \mathbf{a} \wedge \mathbf{b}$$

Even if $\mathbf{a}$ and $\mathbf{b}$ each have only three non-zero components, their product fills both grade-0 and grade-2 slots. Now multiply by a third vector:

$$(\mathbf{ab})\mathbf{c} = (\mathbf{a} \cdot \mathbf{b})\mathbf{c} + (\mathbf{a} \wedge \mathbf{b}) \cdot \mathbf{c} + (\mathbf{a} \wedge \mathbf{b}) \wedge \mathbf{c}$$

Grades 1 and 3 activate. Continue this process and the initially sparse geometric objects densify into full multivectors.

Modern visual SLAM exploits a different kind of sparsity. With 10,000 poses and 100,000 landmarks, the full state has 360,000 degrees of freedom. The information matrix would contain 130 billion entries if dense. But each camera only observes nearby landmarks, and consecutive poses connect through motion constraints. This creates a sparse graph structure where only about 0.01% of entries are non-zero.

Factor graph optimization leverages this structure:
- Cholesky factorization respects sparsity patterns
- Variable elimination follows the graph topology
- Marginalization operations remain sparse

The geometric product cannot preserve this structure. Consider a simple motor update in bundle adjustment:

$$M_{\text{new}} = M_{\text{update}} M_{\text{old}}$$

Even if $M_{\text{update}}$ encodes a small perturbation with few non-zero components, the motor product fills all eight components. Chain these through thousands of poses and the sparsity is obliterated.

This isn't a flaw in current implementations—it's algebraically fundamental. The geometric product's information-preserving nature, its very strength, prevents the factorization that sparse solvers require. You cannot have both complete geometric information and exploitable conditional independence.

#### Graph Problems in Geometric Clothing

Path planning on a road network involves discrete nodes connected by edges with costs. The A* algorithm maintains a priority queue of nodes to explore, using a heuristic to guide search toward the goal. The fundamental operations are:
- Insert node into priority queue
- Extract minimum-cost node
- Update neighbor costs
- Test goal condition

Where exactly does the geometric product help here? The nodes aren't vectors—they're abstract entities. The edges aren't geometric objects—they're adjacency relations with scalar costs.

You might embed the graph in geometric space, assigning each node a position vector. But this adds structure that wasn't there. The shortest path in the graph need not resemble the geometric shortest path. Consider one-way streets: the path from A to B differs from B to A, but geometric distance is symmetric.

The same mismatch afflicts many discrete optimization problems:
- Integer programming: Variables must take discrete values
- Combinatorial optimization: Solutions are permutations or subsets
- Constraint satisfaction: Variables must satisfy logical relations

These problems have algebraic structure, but it's not geometric. They live in discrete spaces where the continuous transformations of GA—rotations, translations, reflections—have no meaning.

#### Matrix Factorizations and Numerical Linear Algebra

Solving $\mathbf{Ax} = \mathbf{b}$ remains the computational workhorse of scientific computing. Decades of research have produced exquisitely tuned algorithms:
- LU decomposition with partial pivoting
- QR factorization via Householder reflections
- Singular value decomposition for rank-deficient systems
- Iterative methods exploiting problem-specific structure

These algorithms don't just use matrices—they decompose them into structured factors. QR factorization finds an orthogonal matrix $\mathbf{Q}$ and upper triangular $\mathbf{R}$ such that $\mathbf{A} = \mathbf{QR}$. This decomposition enables backward substitution for solving linear systems and least-squares problems.

Can we reformulate QR factorization in GA? The orthogonal matrix $\mathbf{Q}$ corresponds to some versor, and applying versors is what GA does well. But the upper triangular structure of $\mathbf{R}$ has no natural GA representation. It's a computational convenience, not a geometric property.

More fundamentally, these factorizations achieve their efficiency by *not* preserving all information at each step. Gaussian elimination zeros out matrix entries systematically. Householder reflections annihilate entire column segments. This deliberate information destruction enables the nested loop structure that hardware can optimize.

The geometric product moves in the opposite direction—it preserves and mixes information. This makes it excellent for tracking geometric relationships but antithetical to the structured elimination that numerical linear algebra requires.

#### Fixed-Topology Pipeline Requirements

Real-time graphics pipelines exemplify another structural mismatch. A modern GPU processes vertices through fixed stages:

1. Vertex shader: Transform vertices by 4×4 matrices
2. Primitive assembly: Group vertices into triangles
3. Rasterization: Convert triangles to fragments
4. Fragment shader: Compute pixel colors

This pipeline is carved into silicon. The GPU has dedicated hardware for 4×4 matrix multiplication, interpolating vertex attributes across triangles, and depth testing. It expects data in specific formats: three 32-bit floats for positions, four 8-bit integers for colors.

You cannot simply swap in 32-component multivectors. The rasterizer doesn't know how to interpolate bivectors. The depth buffer compares scalar values, not null vectors on a 5D cone. The entire architecture assumes a specific mathematical structure.

Similar constraints bind other real-time systems:
- Digital signal processors optimize for multiply-accumulate operations
- Neural network accelerators expect tensor contractions
- Quantum computers manipulate unitary matrices

When performance requires specialized hardware, you must speak its mathematical language.

#### The Probabilistic Void

Chapter 10 established that probabilistic GA is impossible—the null condition $P^2 = 0$ that makes conformal points work is fundamentally incompatible with uncertainty. But many engineering domains are inherently probabilistic:

**State estimation**: A robot's pose isn't a precise motor but a probability distribution over SE(3). Sensor measurements arrive corrupted by Gaussian noise. The Kalman filter recursively updates beliefs by propagating uncertainty through measurement and motion models.

**Machine learning**: Neural network weights are increasingly treated as probability distributions. Bayesian deep learning captures model uncertainty. Dropout approximates variational inference. None of this has a GA formulation.

**Stochastic optimization**: Simulated annealing, genetic algorithms, and particle swarm optimization explore solution spaces probabilistically. They maintain populations of candidates and update them stochastically.

The mismatch runs deep. Probability theory is built on measure spaces and sigma algebras—abstract structures with no geometric content. Random variables are measurable functions, not geometric objects. Expectations are integrals, not sandwiches.

You can compute with geometric objects and separately track their uncertainties using traditional covariance matrices. But this breaks the unified representation that makes GA attractive. Once you're maintaining parallel standard representations for uncertainty, the architectural benefit evaporates.

#### Recognizing Structural Misalignment

How do you know when GA's structure fights your problem? Watch for these signs:

**Forced geometric embeddings**: You find yourself assigning arbitrary "positions" to abstract entities just to make GA applicable. Customer preferences become vectors. Database relations become multivectors. The geometry adds nothing.

**Exploding representations**: Simple operations produce increasingly complex multivectors. What started sparse becomes dense. Memory usage grows faster than problem size.

**Missing operations**: Your problem requires matrix factorizations, graph traversals, or discrete optimization. GA offers no natural formulation for these fundamental operations.

**Hardware impedance**: Target platforms—GPUs, DSPs, quantum processors—have fixed architectures optimized for specific mathematical structures that don't align with GA.

**Probabilistic requirements**: Uncertainty is central to your problem. You need Bayesian updates, confidence intervals, or stochastic sampling.

#### The Hybrid Escape Hatch

When structure doesn't align, forcing GA throughout your system is architectural malpractice. But complete abandonment might be premature. Many successful systems use GA tactically:

**High-level orchestration**: GA manages coordinate frames and geometric relationships. When computation is needed, extract coordinates and feed traditional algorithms. A robotics system might use motors for forward kinematics but convert to matrices for dynamics.

**Robust geometry**: Use GA for geometric predicates and incidence tests where its degeneracy handling excels. Implement performance-critical inner loops with specialized methods.

**Offline precomputation**: Employ GA's analytical power during development. Derive formulas symbolically, then generate optimized code for deployment.

**Domain boundaries**: Different subsystems use different mathematics. The vision system speaks GA internally but exports point clouds. The planner receives geometric constraints but runs graph search.

This isn't compromise—it's engineering. Use the right tool for each job.

#### Beyond Geometric Structure

The deepest misalignments occur when your problem's structure is fundamentally non-geometric. Information theory deals with entropy and mutual information. Category theory manipulates abstract morphisms. Type theory reasons about computational properties. None of these fit GA's geometric mold.

Even within geometry, some structures resist GA formulation. Differential geometry's connection forms and curvature tensors have GA interpretations, but the index gymnastics of general relativity often remain clearer in traditional notation. Symplectic geometry's phase spaces follow different algebraic rules.

Recognizing these boundaries isn't failure—it's mathematical maturity. GA provides powerful tools for problems with inherent geometric structure. When that structure is absent or incompatible, other mathematics serves better.

The next chapter explores one domain where structure aligns beautifully: machine learning problems with natural geometric symmetries, where GA's equivariance properties provide exactly the inductive bias modern neural networks need.
