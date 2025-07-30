**Authorial Constitution Prompt**

Write a comprehensive technical book on geometric algebra for practicing engineers. Present GA as a mathematical framework that trades computational performance for architectural unity and geometric insight. Build understanding progressively from concrete problems to abstract structures while maintaining rigorous engineering standards throughout.

**Fundamental Perspective**

Geometric algebra offers a unified mathematical language for geometric computation at significant computational cost. Engineers must understand both the profound geometric insights GA provides and the real performance penalties to make informed implementation decisions. Present both without apology or evangelism.

**Core Mathematical Development**

Begin with the engineering problem: coordinating multiple geometric representations creates architectural friction through conversion overhead, special-case proliferation, and synchronization complexity. Show how requiring an associative operation that preserves complete geometric information naturally leads to the geometric product $\mathbf{ab} = \mathbf{a} \cdot \mathbf{b} + \mathbf{a} \wedge \mathbf{b}$.

Develop the mathematical framework through discovery:
- Start with reflection as the fundamental operation (Cartan-Dieudonné)
- Derive the geometric product from the requirement for algebraic reflection
- Show how complex numbers emerge as 2D GA's even subalgebra
- Demonstrate quaternions emerging from 3D GA's even subalgebra
- Build the conformal model by embedding on the null cone to linearize transformations

Each mathematical development must include:
- Concrete motivation from engineering problems
- Step-by-step derivation with worked numerical examples
- Explicit computational cost (operation counts, memory usage)
- Clear guidance on when the tradeoff favors GA

**Critical Limitations (State Once)**

In the introduction, clearly establish:
- GA provides no native mechanism for probabilistic representation
- GA operations are inherently dense, preventing sparse optimization
- These limitations fundamentally exclude GA from modern SLAM, probabilistic robotics, and large-scale optimization

Do not repeatedly apologize for these limitations throughout the text.

**Computational Requirements**

Every algorithm must include:
- Explicit operation counts derived from fundamental operations
- Memory usage analysis with layout considerations
- Numerical conditioning analysis where relevant
- Comparison to specialized alternatives using algorithmic complexity

Example format:
"The meet operation requires ~160 FLOPs: 32 for first dual, 64 for wedge product, 32 for second dual. Specialized line-plane intersection requires ~10 FLOPs. The 16× overhead provides algorithmic unity."

**Application Coverage**

For each domain, present:
1. Where GA genuinely helps (with quantified benefits)
2. Where GA cannot compete (with technical reasons)
3. Hybrid architectures as the mature engineering solution

Include ALL coherent applications, even speculative ones (Clifford neural networks, quantum computing connections) with appropriate technical context and performance analysis.

**Voice and Style Requirements**

Write with direct technical clarity:
- No personal anecdotes or implied experience
- No rhetorical questions or narrative devices
- No defensive comparisons or apologetic hedging
- No philosophical digressions about space or dimension

Use active, declarative sentences:
- "The geometric product preserves complete geometric information"
- NOT: "One might consider how the geometric product could potentially preserve..."

Mathematical enthusiasm is appropriate for genuine insights:
- "Quaternions emerge naturally as 3D GA's even subalgebra—the same structure that seems arbitrary in traditional presentations follows necessarily from the geometric product."

**Pedagogical Structure**

Build each concept on prior foundations:
1. Concrete engineering problem
2. Why traditional approaches create friction
3. How GA addresses the specific issue
4. Computational cost of the GA solution
5. When the tradeoff makes engineering sense

Include worked examples showing actual calculations:
- Step-by-step geometric products
- Numerical meet computations
- Rotor interpolation with specific values
- Memory layout diagrams for sparse storage

**Technical Precision**

- ALL mathematics in LaTeX: `$\mathbf{a} \wedge \mathbf{b}$` not `a ∧ b`
- Consistent notation throughout (see comprehensive symbol glossary)
- Algorithmic bounds not specific benchmarks
- Storage complexity not specific memory measurements

**What to Exclude**

- All code implementations (pseudocode for algorithms only)
- Personal experiences or anecdotes
- External citations beyond historical context
- Specific performance benchmarks not derivable from complexity
- Repeated mentions of limitations after introduction
- Defensive hedging about GA's value

**What to Preserve**

All technical insights including:
- Reflection generating all transformations
- Information preservation as geometric product's purpose
- Null cone geometry and paraboloid embedding
- O(1/sin θ) conditioning improvements
- First-order versor stability
- Binary blade indexing optimization
- Bivector illumination models
- Motor singularity classification
- All frontier applications with sound mathematics

**Final Tone**

Present GA as a powerful but computationally expensive framework. Let engineers evaluate whether architectural unity and geometric insight justify 3-10× performance penalties for their specific applications. The book succeeds when readers understand both what GA offers and what it costs, enabling informed engineering decisions.
