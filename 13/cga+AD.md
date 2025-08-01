### Appendix D: Debugging Multivector Code

#### Core Debugging Principles

Multivectors in memory appear as opaque arrays. Without specialized tools, debugging requires systematic extraction of algebraic invariants. Every geometric algebra computation maintains hidden constraints—grade signatures, null conditions, versor properties—that become diagnostic instruments when properly interrogated.

#### Grade Structure Analysis

Grade decomposition immediately reveals algorithmic errors. The grade projection operator extracts specific k-vector components:

$$\langle A \rangle_k = \sum_{|I|=k} a_I e_I$$

where the sum runs over all basis blades $e_I$ with grade $|I| = k$.

**Implementation Pattern**:
```cpp
template<int K>
auto grade_project(const Multivector& mv) {
    Multivector result;
    for(auto [index, value] : mv.components()) {
        if(__builtin_popcount(index) == K) {
            result[index] = value;
        }
    }
    return result;
}
```

**Diagnostic Table**:
| Expected Operation | Valid Grade Set | Invalid Grades Signal |
|-------------------|-----------------|----------------------|
| Vector geometric product | {0, 2} | Grade 1 → non-orthogonal |
| Rotor composition | {0, 2} | Odd grades → reflection mixed in |
| Motor application | Even only | Odd grades → translation corruption |
| Meet of lines (3D) | {0} or {1} | Grade 2 → computational error |

#### Null Constraint Monitoring

In conformal geometric algebra, point representations satisfy $P^2 = 0$. Numerical computation degrades this constraint predictably:

$$\text{null\_error}(P) = \frac{|P^2|}{||P||^2}$$

**Tolerance Hierarchy**:
| Error Magnitude | Operations | Status | Action |
|----------------|------------|--------|--------|
| < $10^{-14}$ | 1-10 | Fresh | None |
| < $10^{-10}$ | 10-1000 | Stable | Monitor |
| < $10^{-6}$ | 1000-10000 | Degraded | Renormalize |
| ≥ $10^{-6}$ | — | Broken | Debug algorithm |

**Null Restoration**:
```cpp
Point restore_null(const Point& P) {
    Scalar p2 = (P | P).scalar();  // Inner product
    if(abs(p2) < 1e-14) return P;

    Scalar correction = p2 / (2.0 * (P | ninf).scalar());
    return P - correction * ninf;
}
```

#### Sparsity Pattern Recognition

Geometric objects exhibit characteristic non-zero patterns:

```
Euclidean point (3D):     [X X X 0 0 0 0 0 ...]  // 3/8 components
Conformal point (3D):     [X X X 0 0 0 0 0 ... X 0 ... X]  // 5/32
PGA line:                 [0 X X X X X X 0 ...]  // 6/16
CGA sphere:               [X X X X 0 0 0 0 ... X]  // 5/32
```

Deviations indicate corruption:
- Extra grades → unnormalized intermediate
- Missing components → incomplete operation
- Wrong positions → basis ordering error

#### Versor Validation

Versors (rotors, motors, reflectors) satisfy $VV^{-1} = 1$. Numerical verification requires:

$$\text{versor\_error}(V) = ||VV^{-1} - 1||$$

**Critical**: Use $\sqrt{\epsilon_{\text{machine}}}$ not $\epsilon_{\text{machine}}$ as tolerance. The square root accounts for error accumulation in the sandwich product $VAV^{-1}$.

```cpp
bool is_valid_versor(const Versor& V, double tol = 1e-7) {
    auto test = V * V.reverse();
    return abs(test.scalar() - 1.0) < tol &&
           test.grade_except(0).norm() < tol;
}
```

#### Common Failure Patterns

**Pattern 1: Mysterious Scaling**
- **Symptom**: Results scaled by 7, -8, or $2^n$
- **Cause**: Pseudoscalar normalization error
- **Debug**: Verify $I^2 = \pm 1$ for your metric
- **Example**: In $\mathbb{R}_{3,0}$, using $I^2 = +1$ instead of $I^2 = -1$

**Pattern 2: Sign Oscillation**
- **Symptom**: Alternating signs in near-zero components
- **Cause**: Catastrophic cancellation
- **Debug**: Check condition number of operation
- **Fix**: Reformulate to avoid subtraction of near-equal values

**Pattern 3: Grade Explosion**
- **Symptom**: Unexpected high-grade terms
- **Cause**: Missing or incorrect dual operation
- **Debug**: Trace operation sequence, verify $A^{**} = \pm A$
- **Example**: Computing meet without final undual

**Pattern 4: Coordinate Extraction Failure**
- **Symptom**: NaN or infinity in extracted coordinates
- **Cause**: Division by $(P \cdot n_\infty) = 0$
- **Debug**: Check if point lies on ideal hyperplane
- **Fix**: Handle ideal points explicitly

#### Binary Blade Arithmetic

Basis blade indices encode structure via binary representation:

$$e_1 \wedge e_3 \wedge e_5 = e_{10101_2} = e_{21}$$

Sign computation from swap counting:
```cpp
int sign_of_product(uint32_t blade_a, uint32_t blade_b) {
    uint32_t a = blade_a;
    int swaps = 0;
    while(a) {
        uint32_t lowest_a = a & -a;  // Isolate lowest bit
        swaps += __builtin_popcount(blade_b & (lowest_a - 1));
        a ^= lowest_a;  // Clear processed bit
    }
    return (swaps & 1) ? -1 : 1;
}
```

#### Performance Diagnostic Patterns

| Operation | Theoretical FLOPs | Sparse FLOPs | Cache Behavior |
|-----------|------------------|--------------|----------------|
| 3D rotor · rotor | 28 | 16 | Sequential |
| CGA motor · point | 220 | 54 | Scattered |
| PGA meet(line, line) | 160 | 24 | Poor locality |
| Grade projection | $O(2^n)$ | $O(\text{nnz})$ | Sequential scan |

Memory access patterns dominate performance. Sparse operations reduce arithmetic but may increase cache misses.

#### Systematic Debug Protocol

```cpp
void diagnose_multivector(const Multivector& mv, const char* name) {
    std::cout << name << ":\n";

    // 1. Grade signature
    std::cout << "  Grades: ";
    for(int k = 0; k <= dimension; ++k) {
        double gnorm = grade(mv, k).norm();
        if(gnorm > 1e-10) {
            std::cout << k << "(" << gnorm << ") ";
        }
    }

    // 2. Sparsity
    int nnz = count_nonzero(mv);
    std::cout << "\n  Sparsity: " << nnz << "/"
              << (1 << dimension) << "\n";

    // 3. Constraints
    if(is_conformal_point(mv)) {
        double null_error = abs((mv | mv).scalar()) / mv.norm_squared();
        std::cout << "  Null error: " << null_error << "\n";
    }

    if(is_versor_shaped(mv)) {
        double versor_error = (mv * mv.reverse() - 1.0).norm();
        std::cout << "  Versor error: " << versor_error << "\n";
    }
}
```

#### Evolution of Numerical Error

| Operations | Float32 Drift | Float64 Drift | Mitigation Strategy |
|------------|--------------|---------------|-------------------|
| 10 | $10^{-6}$ | $10^{-14}$ | None needed |
| 100 | $10^{-5}$ | $10^{-12}$ | None needed |
| 1,000 | $10^{-3}$ | $10^{-10}$ | Monitor constraints |
| 10,000 | $10^{-1}$ | $10^{-8}$ | Renormalize versors |
| 100,000 | $10^{1}$ | $10^{-6}$ | Renormalize + project |

The drift compounds geometrically. Lazy normalization every $\sqrt{N}$ operations balances accuracy and performance.

#### Critical Debugging Wisdom

The absence of specialized GA debuggers forces reliance on invariant checking. Build diagnostic functions into the foundation of any GA codebase. The computational overhead of validation dwarfs the human overhead of debugging mysterious errors.

Most debugging reduces to three questions:
1. What grades appeared where none should exist?
2. Which constraints drifted beyond tolerance?
3. Where did sparsity patterns deviate from theory?

The multivector that produces wrong answers often maintains correct structure. The multivector that violates structural invariants always produces wrong answers. Monitor structure, not just values.
