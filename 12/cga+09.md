### Chapter 9: Debugging Without Tools

You've written your first conformal geometric algebra algorithm. The math checked out on paper. Your implementation compiles. But the robot arm is moving to $(NaN, NaN, NaN)$, your ray-sphere intersection returns mysterious grade-4 objects, and that simple reflection is somehow scaling your geometry by a factor of 7.

Welcome to debugging in geometric algebra—where your trusty debugger shows you 32 floating-point numbers labeled `mv[0]` through `mv[31]`, visualization tools are scarce, and that elegant mathematical unity you were promised feels like a curse.

This chapter provides survival strategies for debugging GA code in the current ecosystem. Unlike mature linear algebra libraries with decades of tooling, GA debugging often reduces to first principles: understanding grade structure, recognizing sparsity patterns, and building your own verification routines.

#### The Multivector Maze

Traditional 3D graphics debugging is straightforward. A vector has three components—you can visualize it immediately. A $4 \times 4$ matrix has clear spatial meaning in each row and column. But a conformal point in CGA?

$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2 n_\infty + n_0$$

In memory, this becomes an array where most entries are zero, and the non-zero values appear at seemingly random indices. Quick: what's wrong with this multivector?

```
mv[0] = 0.0   mv[8] = 0.0   mv[16] = 1.0   mv[24] = 0.0
mv[1] = 3.0   mv[9] = 0.0   mv[17] = 0.0   mv[25] = 0.0
mv[2] = 4.0   mv[10] = 0.0  mv[18] = 0.0   mv[26] = 0.0
mv[3] = 0.0   mv[11] = 0.0  mv[19] = 0.0   mv[27] = 0.0
mv[4] = 5.0   mv[12] = 0.0  mv[20] = 0.0   mv[28] = 0.0
mv[5] = 0.0   mv[13] = 0.0  mv[21] = 0.0   mv[29] = 0.0
mv[6] = 0.0   mv[14] = 0.0  mv[22] = 0.0   mv[30] = 0.0
mv[7] = 0.0   mv[15] = 0.0  mv[23] = 13.5  mv[31] = 0.0
```

Without understanding the basis blade ordering, this is meaningless. The first debugging skill is learning to read multivector dumps.

#### Grade-Based Debugging

The grade structure provides your first diagnostic tool. Every geometric object has an expected grade signature:

| Object | Expected Grades | Red Flag Grades |
|--------|----------------|-----------------|
| Point | 1 only | Any bivector components |
| Line | 3 only (CGA IPNS) | Scalar or 4-vector parts |
| Plane | 1 only | Same as sphere—check magnitude |
| Motor | 0, 2, 4 | Odd grades indicate error |
| Rotor | 0, 2 | Any grade 1 or 3 |

Building a grade extraction function should be your first debugging tool:

$$\text{grades}(A) = \{k : \langle A \rangle_k \neq \epsilon\}$$

When a motor suddenly has grade-1 components, you know immediately something is wrong—likely a failed normalization or incorrect multiplication order.

#### The Null Check Hierarchy

Conformal points must satisfy $P^2 = 0$. But numerical error means you need tolerances:

$$|P^2| < \epsilon \|P\|^2$$

This creates a hierarchy of checks:

1. **Exact null**: $P^2 = 0$ (impossible with floating point)
2. **Relative null**: $|P^2| < 10^{-14}\|P\|^2$ (freshly constructed)
3. **Approximately null**: $|P^2| < 10^{-10}\|P\|^2$ (after some operations)
4. **Drifted**: $|P^2| < 10^{-6}\|P\|^2$ (needs projection)
5. **Broken**: $|P^2| \geq 10^{-6}\|P\|^2$ (algorithm has failed)

The key insight: track this drift to identify where your algorithm loses precision.

#### Sparsity Patterns as Fingerprints

Each geometric object type has a characteristic sparsity pattern. A conformal point uses indices {1, 2, 4, 8, 16}. A line might use {3, 5, 6, 9, 10, 12, 17, 18, 20, 24}. These patterns are fingerprints:

```
Point pattern:    [- X X - X - - - X - - - - - - - X - - - - - - - - - - - - - - -]
Line pattern:     [- - - X - X X - - X X - X - - - - X X - X - - - X - - - - - - -]
```

When debugging, first check: does the sparsity pattern match the expected geometry? A "point" with 15 non-zero components isn't a point—it's either numerical debris or a misunderstood algorithm.

#### Versor Verification

Motors and rotors are versors—they satisfy $VV^{\dagger} = 1$. But like the null check, this needs tolerance:

$$|VV^{\dagger} - 1| < \sqrt{\epsilon}$$

The square root appears because versors are quadratic in the underlying parameters. A motor with $|MM^{\dagger} - 1| = 10^{-8}$ is still acceptable after thousands of operations.

More importantly, check the grade structure:
- Motors: even grades only (0, 2, 4)
- Pure rotors: grades 0 and 2 only
- Pure translators: grades 0 and 2, with $n_\infty$ terms only in grade 2

#### The Binary Blade Detective

Every basis blade has a binary representation encoding which basis vectors it contains:

$$e_1 \wedge e_3 \wedge n_\infty \rightarrow 10101_2 = 21$$

This enables rapid debugging of products. If blade 5 ($e_1 \wedge e_3$) multiplied by blade 16 ($n_\infty$) gives blade 21, you can verify:

$$5 \text{ XOR } 16 = 21 \quad \checkmark$$

But the sign? Count the swaps needed to reorder:
$$e_1 e_3 \cdot n_\infty = e_1 e_3 n_\infty \quad \text{(0 swaps)} \quad \rightarrow \quad +$$

Building a small tool to decompose multivectors into blade index and coefficient pairs transforms debugging from guesswork to systematic analysis.

#### Common Failure Modes

**The Exploding Conformal Coordinate**: Points far from the origin have large $n_\infty$ coefficients:

$$P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2 n_\infty + n_0$$

At $\|\mathbf{p}\| = 1000$, the $n_\infty$ coefficient is 500,000. Floating-point precision degrades. Solution: work in local coordinate frames or periodically recenter.

**Grade Explosion in Products**: Multiplying high-grade objects produces results that fill all grades:

$$(e_1 \wedge e_2 \wedge e_3)(e_1 \wedge e_2 \wedge n_\infty) = e_3 \wedge n_\infty - e_1 \wedge e_2 \wedge e_1 \wedge e_2 \wedge e_3 \wedge n_\infty$$

The second term should vanish (repeated indices) but numerical error creates small non-zero values across many grades. Solution: explicitly project to expected grades after products.

**The Dual Trap**: Computing $A^* = AI^{-1}$ when $A$ has components parallel to $I$ produces numerical instability. The pseudoscalar $I = e_1 e_2 e_3 n_0 n_\infty$ in CGA has grade 5. If your object has grade 5 components, the dual operation amplifies numerical noise.

#### Building Your Own Verification Suite

Without mature debugging tools, build your own invariant checks:

**Distance Preservation**: After applying motor $M$:
$$|-2P_1 \cdot P_2| = |-2(MP_1M^{\dagger}) \cdot (MP_2M^{\dagger})|$$

**Incidence Preservation**: If $P \cdot S = 0$ (point on sphere), then after transformation:
$$(MPM^{\dagger}) \cdot (MSM^{\dagger}) = 0$$

**Grade Consistency**: Define expected grades for every operation:
$$\text{grade}(A \wedge B) = \text{grade}(A) + \text{grade}(B) \quad \text{(if independent)}$$

**Meet Validation**: The meet should satisfy:
$$\text{grade}(L_1 \vee L_2) \leq \min(\text{grade}(L_1), \text{grade}(L_2))$$

#### Numerical Conditioning Indicators

Watch for these warning signs of numerical instability:

1. **Oscillating signs** in components that should be zero
2. **Grade leakage**—energy appearing in unexpected grades
3. **Magnitude drift** in normalized versors
4. **Asymmetry** in operations that should commute

The condition number of the meet operation grows as $O(1/\sin\theta)$ for angle $\theta$ between objects. When debugging intersection code, always test near-parallel configurations first—they expose numerical weaknesses immediately.

#### Print Debugging for GA

Since traditional debuggers show only raw arrays, develop formatted printing:

```
Point P: (3.0, 4.0, 5.0) + 25.0*n∞ + n₀
  Null check: |P²| = 2.3e-15 ✓
  Grade signature: {1}
  Sparsity: 5/32 components
```

Include geometric interpretation, not just numbers. A motor should print its axis, angle, and pitch—not 8 floating-point values.

#### Testing Without Ground Truth

How do you test GA algorithms without mature reference implementations? Use mathematical properties:

**Involution Tests**:
- $(A^*)^* = \pm A$ (double dual)
- $\widetilde{\widetilde{A}} = A$ (double reverse)
- Grade projection completeness: $\sum_k \langle A \rangle_k = A$

**Algebraic Relations**:
- $e_i e_j = -e_j e_i$ for $i \neq j$
- $(e_i)^2 = \pm 1$ according to metric
- $I^2 = \pm 1$ for pseudoscalar

**Geometric Consistency**:
- Rotating by $\theta$ then $-\theta$ returns identity
- Meeting a plane with itself gives the plane
- Point at origin: $P \cdot n_\infty = -1$, $P \cdot n_0 = 0$

#### Performance Debugging

The "3-10× overhead" isn't uniform. Profile to find hotspots:

- **Geometric products**: Dense $O(n^2)$ operations
- **Dual operations**: Full pseudoscalar multiplication
- **Grade projections**: Scanning all components
- **Sparse handling**: Overhead of index management

Often, 90% of time concentrates in 10% of operations. Optimize those paths while keeping the rest readable.

#### Debugging Without Visualization

Visualization remains GA's weakest tooling area. Strategies for "blind" debugging:

1. **Reduce to 2D**: Test algorithms in 2D GA first where you can manually verify
2. **Check limits**: As radius → 0, sphere → point
3. **Test symmetry**: Reflect twice across same plane = identity
4. **Verify in Euclidean**: Extract Euclidean coordinates and check there

The formula for extracting Euclidean coordinates from conformal:

$$\mathbf{p} = \frac{P - (P \cdot n_\infty)n_0}{-P \cdot n_\infty}$$

provides a bridge back to familiar territory.

#### Learning from Failure

Every GA debugging session teaches patterns. That mysterious grade-4 component? Probably forgot to dualize. NaN coordinates? Likely divided by $(P \cdot n_\infty)$ when it was zero (point at infinity). Scaling by 7? Check if you normalized by $P^2$ instead of $\sqrt{P^2}$.

Build a catalog of failure modes. Unlike mature ecosystems where error patterns are documented, GA debugging knowledge lives in practitioners' hard-won experience.

#### The Path Forward

Debugging GA without tools requires patience and systematic thinking. But it also builds deep understanding. When you finally track down why that reflection was scaling—perhaps you used a non-unit plane—you understand the algebra at a level that no amount of reading can provide.

The tools will come. Libraries like `ganja.js` already provide some visualization. IDEs will eventually have multivector inspectors. But until then, debugging GA remains an exercise in first principles: understand the grade structure, verify algebraic properties, and always, always check your null vectors.

The next chapter explores a fundamental limitation that no amount of debugging can fix: the impossibility of probabilistic representations in geometric algebra. Understanding this boundary helps set appropriate expectations for what GA can and cannot do.
