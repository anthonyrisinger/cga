### Chapter 9: Debugging Without Tools

You've implemented your first geometric algebra algorithm. The mathematics checked out on paper. The code compiles cleanly. But your robot arm moves to coordinates that overflow IEEE 754, your ray-sphere intersection returns mysterious grade-4 components in 3D space, and a simple reflection scales your entire scene by -8.

Welcome to debugging geometric algebra—where standard debuggers show 32 meaningless floats, no visualization tools exist, and that promised mathematical elegance hides behind opaque multivector coefficients.

#### Reading Multivector Dumps

Traditional 3D debugging offers immediate intuition. A vector `(3, 4, 5)` clearly points up and right with magnitude 7.07. A conformal GA point presents this:

```cpp
// What your debugger shows:
double mv[32] = {
    0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,  // indices 0-7
    0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,  // indices 8-15
    1.0, 3.0, 4.0, 5.0, 0.0, 0.0, 0.0, 25.5, // indices 16-23
    0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0   // indices 24-31
};
```

Without knowing your library's blade ordering, these numbers mean nothing. Build a decoder:

```cpp
void decodeBlade(int index, const std::string& library) {
    if (library == "ganja") {
        const char* blades[] = {"1", "e0", "e1", "e2", "e3", "e01", "e02", /*...*/};
        std::cout << "mv[" << index << "] = " << blades[index] << std::endl;
    }
    // Each library uses different conventions
}
```

#### Grade-Based Structure Validation

Every geometric object exhibits a characteristic grade signature:

$$\text{Grade}_k(A) = \langle A \rangle_k = \frac{1}{2^n} \sum_{B \in \text{blades}} \epsilon_B \, B \, A \, B$$

Build grade inspection into your workflow:

```cpp
template<typename Multivector>
void inspectGrades(const Multivector& mv, const std::string& name) {
    std::cout << name << " grades: {";
    for (int k = 0; k <= mv.dimension(); ++k) {
        if (mv.grade(k).norm() > 1e-10) {
            std::cout << k << " ";
        }
    }
    std::cout << "}" << std::endl;
}
```

Expected signatures catch structural errors immediately:

| Object Type | Expected Grades | Red Flag |
|------------|-----------------|-----------|
| CGA Point | {1} | Any even grade |
| CGA Line | {3} | Grades 1, 2, or 4 |
| Motor | {0, 2, 4} | Any odd grade |
| Rotor | {0, 2} | Grades 1, 3, 4 |

#### The Null Condition Spectrum

Conformal points must satisfy:

$$P^2 = P \cdot P = 0$$

Floating-point reality transforms this into a spectrum:

```cpp
enum class NullStatus {
    EXACT,      // |P²|/|P|² < 1e-14
    NUMERICAL,  // |P²|/|P|² < 1e-10
    DEGRADED,   // |P²|/|P|² < 1e-6
    BROKEN      // |P²|/|P|² ≥ 1e-6
};

NullStatus checkNull(const CGA::Point& P) {
    double null_error = std::abs(P.dot(P)) / P.norm_squared();

    if (null_error < 1e-14) return NullStatus::EXACT;
    if (null_error < 1e-10) return NullStatus::NUMERICAL;
    if (null_error < 1e-6)  return NullStatus::DEGRADED;
    return NullStatus::BROKEN;
}
```

Track degradation through your algorithm to identify problematic operations.

#### Sparsity Patterns as Fingerprints

Each geometric primitive occupies specific coefficient slots:

```cpp
void printSparsityPattern(const double* mv, int size = 32) {
    for (int i = 0; i < size; ++i) {
        std::cout << (std::abs(mv[i]) > 1e-10 ? "X" : "·");
        if ((i + 1) % 8 == 0) std::cout << " ";
    }
    std::cout << std::endl;
}

// Expected patterns:
// Point:  ········ ········ XXXX···X ········
// Line:   ········ XXX···XX ········ X·······
// Motor:  X···XXX· ········ XXXX···X ········
```

A "point" with 15 non-zero components signals numerical debris or algorithmic confusion.

#### Versor Constraint Verification

Versors must satisfy:

$$V\tilde{V} = 1$$

The tolerance reflects first-order stability:

```cpp
template<typename Versor>
bool verifyVersor(const Versor& V) {
    double error = std::abs((V * V.reverse()).scalar() - 1.0);

    // First-order stability → sqrt(machine_epsilon) tolerance
    if (error > 1e-8) {
        std::cerr << "Versor constraint violated: " << error << std::endl;
        return false;
    }

    // Check grade structure for rotors
    if (V.grade(1).norm() + V.grade(3).norm() > 1e-8) {
        std::cerr << "Rotor contains odd grades!" << std::endl;
        return false;
    }

    return true;
}
```

#### Common Failure Modes

**Conformal Coordinate Explosion**

The embedding contains a quadratic term:

$$P = p + \frac{1}{2}|p|^2 n_\infty + n_0$$

```cpp
// PROBLEM: At |p| = 1000, coefficient = 500,000
// SOLUTION: Work in local coordinate frames
class LocalFrame {
    Vec3 center;
public:
    CGA::Point embed(const Vec3& p) {
        Vec3 local = p - center;
        return CGA::Point(local, 0.5 * local.norm_squared());
    }
};
```

**Grade Contamination**

Project to expected grades after products:

```cpp
template<typename Multivector>
Multivector cleanGrades(const Multivector& A, std::initializer_list<int> grades) {
    Multivector result;
    for (int g : grades) {
        result += A.grade(g);
    }
    return result;
}

// Usage: auto motor_clean = cleanGrades(motor_raw, {0, 2, 4});
```

**Dual Operation Instability**

The dual becomes unstable when A contains pseudoscalar components:

$$A^* = A I^{-1}$$

Always verify: $(A^*)^* = \pm A$

#### Debugging Workflow Example

```cpp
void debugMotorComposition() {
    // Create motors
    Motor M1 = Rotor(PI/4, e1^e2) * Translator(3*e1);
    Motor M2 = Rotor(PI/3, e2^e3) * Translator(2*e2);
    Motor M_comp = M1 * M2;

    // 1. Check grades
    inspectGrades(M_comp, "Composed");  // Expect: {0, 2, 4}

    // 2. Verify constraints
    if (!verifyVersor(M_comp)) {
        std::cerr << "Motor composition failed versor test" << std::endl;
    }

    // 3. Test action
    Point P(1, 0, 0);
    Point P_seq = M2.apply(M1.apply(P));
    Point P_comp = M_comp.apply(P);

    double error = (P_seq - P_comp).norm();
    if (error > 1e-10) {
        std::cerr << "Composition error: " << error << std::endl;
        // Common fix: check order M1*M2 vs M2*M1
    }
}
```

#### Building Verification Infrastructure

Embed invariant checks throughout your code:

```cpp
struct GAInvariants {
    // Distance preservation
    static bool checkIsometry(const Motor& M, const Point& P1, const Point& P2) {
        double d_before = (P1 - P2).norm();
        double d_after = (M.apply(P1) - M.apply(P2)).norm();
        return std::abs(d_before - d_after) < 1e-10;
    }

    // Incidence preservation
    static bool checkIncidence(const Motor& M, const Point& P, const Line& L) {
        double before = (P ^ L).norm();
        double after = (M.apply(P) ^ M.apply(L)).norm();
        return std::abs(before) < 1e-10 == std::abs(after) < 1e-10;
    }
};
```

#### Performance Profiling

The 3-10× overhead concentrates in predictable hotspots:

```cpp
// Typical distribution:
// - Geometric products: 60-80%
// - Grade projections: 10-20%
// - Dual/meet operations: 5-15%

// Focus optimization here:
Multivector operator*(const Multivector& a, const Multivector& b) {
    // This function will dominate your profiles
}
```

#### Debugging Without Visualization

Compensate for missing visual tools:

```cpp
// 1. Test in 2D first (where you can plot)
// 2. Extract to Euclidean for sanity checks
Vec3 extractPoint(const CGA::Point& P) {
    double w = -P.dot(n_infinity);
    if (std::abs(w) < 1e-10) throw std::runtime_error("Point at infinity");

    return Vec3(P[e1]/w, P[e2]/w, P[e3]/w);
}

// 3. Verify symbolic special cases
void testRotation90() {
    Rotor R = exp(-PI/4 * (e1^e2));
    Vector rotated = R.apply(e1);
    assert((rotated - e2).norm() < 1e-10);
}
```

#### Pattern Recognition

Common failure signatures:

| Symptom | Cause | Fix |
|---------|-------|-----|
| Grade 4 in 3D | Forgot dual | Check $(A^*)^* = \pm A$ |
| NaN coordinates | Point at infinity | Test $P \cdot n_\infty$ first |
| Scaling by 7, -8 | Wrong normalization | Use $\sqrt{P^2}$ not $P^2$ |
| Mirror results | Sandwich order | Verify $VXV^{-1}$ order |

#### The Engineering Reality

Debugging GA requires building your own infrastructure. The mathematical elegance that simplifies algorithms also obscures errors behind abstraction layers. Success demands:

- Systematic verification at every step
- Deep understanding of library conventions
- Pattern recognition from repeated failures
- Acceptance that bugs can hide in the mathematics

The debugging situation won't improve dramatically—decades haven't produced adequate tools. But the algorithmic clarity and robustness, once achieved, often justify the investment.

Until better tools arrive, we debug in the dark, guided by mathematical constraints and hard-won pattern recognition. The elegance exists—it's just harder to see through 32 floating-point coefficients.
