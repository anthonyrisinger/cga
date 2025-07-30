### Appendix A: Symbol and Notation Reference

#### Core Types and Memory Layout

##### Scalars and Vectors
| Symbol | Type | Memory | Usage | Cost |
|--------|------|--------|-------|------|
| $a, b, \alpha, \beta$ | Scalar | 1 float | Coefficients, angles | O(1) ops |
| $\mathbf{v}, \mathbf{u}, \mathbf{p}$ | Vector | 3 floats | Points, directions | 3N storage |
| $\mathbf{e}_1, \mathbf{e}_2, \mathbf{e}_3$ | Basis vectors | — | Euclidean basis | $\mathbf{e}_i \cdot \mathbf{e}_j = \delta_{ij}$ |
| $\|\mathbf{v}\|$ | Magnitude | 1 float | $\sqrt{\mathbf{v} \cdot \mathbf{v}}$ | 5 FLOPs |

**Implementation note**: Vectors in GA implementations typically stored as array indices 1,2,4 in binary blade representation.

##### Multivector Types
| Symbol | Grade | Components | Memory | Primary Use |
|--------|-------|------------|--------|-------------|
| $A, B, M$ | Mixed | Varies | 2^n floats worst case | General elements |
| $R$ | Even | ~n²/2 | 4 floats (3D rotor) | Rotations |
| $B$ | 2 | $\binom{n}{2}$ | 3 floats (3D) | Oriented areas, rotation planes |
| $T$ | Mixed | Sparse | 4 floats typical | Translations |
| $M$ | Even | Sparse | 8 floats | Motors (rotation + translation) |

**Sparsity reality**: Conformal point uses 5 of 32 possible components (84% sparse). Motors use 8 of 32 (75% sparse).

#### Fundamental Operations

##### Products and Projections
| Operation | Symbol | Computation | FLOPs | Result Grade |
|-----------|--------|-------------|-------|--------------|
| Geometric product | $AB$ | Full multiplication | O(4^n) naive | Mixed |
| Inner product | $A \cdot B$ | Grade lowering | ~3-10 | $\|g_A - g_B\|$ |
| Outer product | $A \wedge B$ | Grade raising | ~6-15 | $g_A + g_B$ if independent |
| Scalar extraction | $\langle A \rangle_0$ | Pick component | 1 | 0 |
| Grade projection | $\langle A \rangle_k$ | Filter grade k | O(1) with sparse storage | k |

**Critical**: Inner product between different-grade elements uses left/right contractions in some libraries. Check documentation.

##### Sandwich Product
The fundamental transformation operation:
```
Transform point P by versor V:
P' = V P V^{-1}        # Mathematical notation
P' = V * P * ~V        # Common implementation
Cost: 54 FLOPs for motor-point transformation
```

#### Binary Blade Representation

The key to computational efficiency:

| Blade | Binary | Decimal | Grade | Example |
|-------|--------|---------|-------|---------|
| 1 | 00000 | 0 | 0 | Scalar |
| $\mathbf{e}_1$ | 00001 | 1 | 1 | X-axis |
| $\mathbf{e}_2$ | 00010 | 2 | 1 | Y-axis |
| $\mathbf{e}_{12}$ | 00011 | 3 | 2 | XY-plane |
| $\mathbf{e}_{123}$ | 00111 | 7 | 3 | Volume |

**Computational rules**:
- Grade extraction: `grade = popcount(index)`
- Blade product: `index_c = index_a XOR index_b` (for orthogonal products)
- Sign calculation: Count basis vector swaps

#### Conformal Geometric Algebra (CGA)

##### Null Basis
| Symbol | Name | Definition | Key Property | Memory Index |
|--------|------|------------|--------------|--------------|
| $\mathbf{n}_0$ | Origin | $\frac{1}{2}(\mathbf{e}_- - \mathbf{e}_+)$ | $\mathbf{n}_0^2 = 0$ | Bit 3 (index 8) |
| $\mathbf{n}_\infty$ | Infinity | $\mathbf{e}_- + \mathbf{e}_+$ | $\mathbf{n}_\infty^2 = 0$ | Bit 4 (index 16) |

**Critical relation**: $\mathbf{n}_0 \cdot \mathbf{n}_\infty = -1$ enables distance encoding.

##### Geometric Objects
| Object | IPNS Formula | Active Components | Test Operation |
|--------|--------------|-------------------|----------------|
| Point | $P = \mathbf{p} + \frac{1}{2}\|\mathbf{p}\|^2\mathbf{n}_\infty + \mathbf{n}_0$ | 5 of 32 | $P^2 = 0$ |
| Sphere | $S = P_c - \frac{1}{2}r^2\mathbf{n}_\infty$ | 5 of 32 | $P \cdot S = 0$ on surface |
| Plane | $\pi = \mathbf{n} + d\mathbf{n}_\infty$ | 4 of 32 | $P \cdot \pi = 0$ on plane |
| Line | Via dual of bivector | 6-8 of 32 | $P \wedge L = 0$ on line |

#### Transformation Versors

##### Common Versors
| Transform | Formula | Components | Application | FLOPs |
|-----------|---------|------------|-------------|-------|
| Reflection | $\sigma$ (normalized vector/plane) | 3-4 | $-\sigma X \sigma$ | 45 |
| Rotation | $R = \exp(-\frac{\theta}{2}B)$ | 4 | $RXR^{-1}$ | 54 |
| Translation | $T = 1 - \frac{1}{2}\mathbf{t}\mathbf{n}_\infty$ | 4 | $TXT^{-1}$ | 54 |
| Motor | $M = TR$ | 8 | $MXM^{-1}$ | 54 |

##### Special Operations
| Operation | Symbol | Purpose | Implementation Note |
|-----------|--------|---------|---------------------|
| Reverse | $\tilde{A}$ | Reverse blade factors | Flip sign by grade |
| Dual | $A^* = AI^{-1}$ | Hodge dual | 32 FLOPs in CGA |
| Meet | $A \vee B = (A^* \wedge B^*)^*$ | Intersection | ~128 FLOPs |
| Inverse | $A^{-1} = \tilde{A}/\langle A\tilde{A}\rangle_0$ | For versors | Normalize by scalar part |

#### Common Implementation Patterns

##### Grade Extraction (Python)
```python
def grade(multivector, k):
    """Extract grade-k part. O(1) with sparse storage."""
    return {idx: coeff
            for idx, coeff in multivector.items()
            if popcount(idx) == k}
```

##### Versor Normalization
```python
def normalize_versor(V):
    """Maintain V * ~V = 1 constraint."""
    norm_squared = scalar_part(V * reverse(V))
    return V / sqrt(abs(norm_squared))
    # Cost: 8 FLOPs for rotor
```

##### Performance Critical Operations
| Operation | Naive | Optimized | Optimization |
|-----------|-------|-----------|--------------|
| 3D rotor application | 216 FLOPs | 54 FLOPs | Exploit sparsity |
| Bivector exponential | Taylor series | 12 FLOPs | Closed form |
| Point normalization | Full product | 4 FLOPs | Track n_inf coefficient |
| Motor composition | Dense product | 28 FLOPs | Even-grade only |

#### Library-Specific Mappings

##### Common Function Names
| Mathematical | klein | gafro | kingdon |
|--------------|-------|-------|---------|
| $AB$ | `a * b` | `a * b` | `a * b` |
| $A \wedge B$ | `a ^ b` | `a.wedge(b)` | `a ^ b` |
| $A \cdot B$ | `a | b` | `a.dot(b)` | `a | b` |
| $\tilde{A}$ | `~a` | `a.reverse()` | `~a` |

#### Debugging Quick Reference

**Sign errors**: Check blade ordering and metric signature.

**Normalization drift**: Versors need renormalization after ~1000 operations.

**Grade mismatches**: Verify expected grades before operations.

**Sparse failures**: Ensure zero components truly absent, not small.

**Performance surprises**: Profile before optimizing—cache misses often dominate.
