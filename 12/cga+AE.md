### Appendix E: Debugging Multivector Code

Multivector computations fail in ways that matrix algebra cannot. A rotation matrix either works or visibly shears. A multivector can silently violate grade structure, null constraints, or versor properties while appearing numerically reasonable. This appendix provides practical techniques for catching these failures.

#### Core Inspection Functions

Every GA debugging session starts with basic inspection:

```python
def inspect_multivector(M, name="M", tolerance=1e-10):
    """Complete diagnostic dump of multivector M."""
    print(f"\n{name}:")

    # Grade structure
    grades = {}
    for blade, coeff in M.items():
        grade = popcount(blade)
        if abs(coeff) > tolerance:
            if grade not in grades:
                grades[grade] = []
            grades[grade].append((blade, coeff))

    # Display by grade
    for g in sorted(grades.keys()):
        print(f"  Grade {g}:")
        for blade, coeff in grades[g]:
            basis_name = blade_to_string(blade)  # Convert binary to e₁e₂ notation
            print(f"    {basis_name}: {coeff:+.6f}")

    # Key properties
    print(f"  Magnitude: {magnitude(M):.6f}")
    print(f"  Grades present: {sorted(grades.keys())}")
    print(f"  Sparsity: {len(M)}/{2**dim} components")

    return grades
```

#### Constraint Violations

##### Null Constraint Checking

Conformal points must satisfy $P^2 = 0$ exactly:

```python
def check_null_constraint(P, name="P"):
    """Verify point lies on null cone."""
    P_squared = geometric_product(P, P)
    scalar_part = P_squared.get(0, 0)  # Grade 0 component

    if abs(scalar_part) > 1e-10:
        print(f"WARNING: {name}² = {scalar_part:.2e} (should be 0)")
        print(f"  Point has drifted {abs(scalar_part):.2e} from null cone")

        # Diagnostic: Check conformal structure
        p_inf_coeff = P.get(blade_index(n_infinity), 0)
        if abs(p_inf_coeff) < 1e-10:
            print(f"  ERROR: No n_∞ component - not a valid conformal point")
        else:
            # Compute drift in Euclidean space
            euclidean_drift = sqrt(abs(scalar_part) / (2 * p_inf_coeff))
            print(f"  Approximate Euclidean drift: {euclidean_drift:.6f} units")

    return abs(scalar_part)
```

##### Versor Normalization

Versors (rotors, motors) must satisfy $V\tilde{V} = \pm1$:

```python
def check_versor_constraint(V, name="V", expected_sign=1):
    """Verify versor normalization constraint."""
    V_rev = reverse(V)
    V_V_rev = geometric_product(V, V_rev)

    # Should be purely scalar
    scalar = V_V_rev.get(0, 0)
    other_grades = {g: v for g, v in V_V_rev.items() if g != 0}

    if other_grades:
        print(f"ERROR: {name}*reverse({name}) has non-scalar components:")
        for grade, value in other_grades.items():
            print(f"  Grade {popcount(grade)}: {value:.2e}")

    norm_error = abs(scalar - expected_sign)
    if norm_error > 1e-10:
        print(f"WARNING: |{name}*~{name}| = {scalar:.6f} (expected {expected_sign})")
        print(f"  Normalization error: {norm_error:.2e}")
        print(f"  Operations until renormalization recommended: {int(-log10(norm_error) * 100)}")

    return norm_error
```

#### Grade Anomaly Detection

Geometric products should produce predictable grade patterns:

```python
def analyze_grade_pattern(M, operation_type, *operands):
    """Detect unexpected grades from operation."""
    grades_present = {popcount(b) for b in M.keys() if abs(M[b]) > 1e-10}

    if operation_type == "vector_vector":
        expected = {0, 2}  # Scalar and bivector only
    elif operation_type == "rotor_vector":
        expected = {1, 3}  # Vector and trivector
    elif operation_type == "meet_lines_3d":
        expected = {0, 1}  # Scalar (distance) or vector (intersection point)
    elif operation_type == "conformal_point":
        expected = {1}     # Points are grade-1
    else:
        return  # Unknown operation type

    unexpected = grades_present - expected
    missing = expected - grades_present

    if unexpected:
        print(f"ANOMALY: Unexpected grades {unexpected} in {operation_type}")
        print(f"  This suggests:")
        if 4 in unexpected or 5 in unexpected:
            print(f"    - Possible conformal algebra mixed with Euclidean")
        if len(unexpected) > 2:
            print(f"    - Likely programming error in geometric product")

    if missing and 0 not in missing:  # Grade 0 can legitimately be zero
        print(f"WARNING: Missing expected grades {missing} in {operation_type}")
```

#### Common Failure Patterns

##### Pattern 1: Euclidean-Conformal Mixing

```python
# WRONG: Mixing Euclidean vector with conformal point
v = e1 + e2  # Euclidean vector
P = make_conformal_point(1, 2, 3)
result = geometric_product(v, P)  # Produces nonsense

# Diagnostic output:
# Grade 0: -2.500000  # Should not have scalar from vector*point
# Grade 2: ...        # Mixture of Euclidean and conformal bivectors
```

**Fix**: Ensure consistent representation. Lift Euclidean vectors to conformal space or extract Euclidean components before operations.

##### Pattern 2: Wrong Pseudoscalar for Duality

```python
# WRONG: Using Euclidean pseudoscalar in conformal space
I_euclidean = e1 * e2 * e3
line_dual = geometric_product(line, I_euclidean)  # Wrong dimensionality

# Diagnostic: Result has wrong grade
# Expected: grade 3 (dual line in CGA)
# Actual: grade 2 (nonsense)
```

**Fix**: Use correct pseudoscalar for the algebra: $I_c = e_1 \wedge e_2 \wedge e_3 \wedge n_0 \wedge n_\infty$ for 3D CGA.

##### Pattern 3: Sign Errors in Reflection

```python
# WRONG: Forgot negative in reflection formula
def reflect_wrong(v, n):
    return geometric_product(n, geometric_product(v, n))  # Missing negation

# Diagnostic: Reflected vector has wrong orientation
# reflect_wrong(e1, e2) returns e1 instead of -e1
```

**Fix**: Reflection formula is $v' = -nvn$ for vector $v$ and unit vector $n$.

#### Visualization Helpers

When numerical inspection fails, visualization reveals structure:

```python
def visualize_rotor_action(R, test_vectors=None):
    """Show how rotor R transforms test vectors."""
    if test_vectors is None:
        test_vectors = [e1, e2, e3, e1+e2, e1+e2+e3]

    print("Rotor action visualization:")
    for v in test_vectors:
        v_rot = apply_rotor(R, v)
        print(f"  {vector_to_string(v)} -> {vector_to_string(v_rot)}")

    # Check if it's a pure rotation
    angle, axis = rotor_to_angle_axis(R)
    print(f"Rotation: {degrees(angle):.1f}° around {vector_to_string(axis)}")
```

#### Performance Profiling

GA bottlenecks hide in grade projections and sparse operations:

```python
def profile_ga_operation(func, *args, iterations=1000):
    """Profile GA function with breakdown by operation type."""
    import cProfile
    import pstats

    profiler = cProfile.Profile()

    # Run function multiple times
    profiler.enable()
    for _ in range(iterations):
        result = func(*args)
    profiler.disable()

    # Analyze GA-specific patterns
    stats = pstats.Stats(profiler)

    print(f"\nProfile for {func.__name__} ({iterations} iterations):")
    print("Top GA operations:")

    # Extract GA-specific functions
    ga_functions = []
    for func_name, (cc, nc, tt, ct, callers) in stats.stats.items():
        if any(ga_op in func_name[2] for ga_op in
               ['geometric_product', 'wedge', 'grade_project', 'reverse', 'dual']):
            ga_functions.append((func_name[2], ct, nc))

    # Sort by cumulative time
    ga_functions.sort(key=lambda x: x[1], reverse=True)

    for fname, cum_time, num_calls in ga_functions[:10]:
        avg_time = cum_time / num_calls * 1e6  # Convert to microseconds
        print(f"  {fname}: {num_calls} calls, {avg_time:.1f} μs/call")

    return result
```

#### Memory Layout Inspection

Cache misses dominate GA performance. Inspect memory patterns:

```python
def analyze_sparsity_pattern(M):
    """Analyze multivector sparsity for optimization opportunities."""
    components = [(blade, coeff) for blade, coeff in M.items() if abs(coeff) > 1e-10]
    components.sort(key=lambda x: x[0])  # Sort by blade index

    print(f"Sparsity analysis:")
    print(f"  Non-zero components: {len(components)}/{2**dim}")
    print(f"  Memory footprint: {len(components) * 8} bytes (sparse) vs {2**dim * 8} bytes (dense)")

    # Detect patterns
    grades = [popcount(blade) for blade, _ in components]
    unique_grades = set(grades)

    if len(unique_grades) == 1:
        print(f"  Optimization: Single grade {unique_grades.pop()} - use specialized storage")
    elif len(unique_grades) == 2 and unique_grades == {0, 2}:
        print(f"  Optimization: Scalar+bivector pattern - use complex number storage")

    # Check for block patterns
    blade_indices = [b for b, _ in components]
    if all(b < 8 for b in blade_indices):
        print(f"  Optimization: All components in first 8 basis elements - use vector register")
```

#### Integration Test Patterns

Verify GA computations against known properties:

```python
def test_motor_properties(M, tolerance=1e-10):
    """Comprehensive motor validation."""
    failures = []

    # Test 1: Versor constraint
    norm_error = check_versor_constraint(M, "Motor")
    if norm_error > tolerance:
        failures.append(f"Normalization error: {norm_error:.2e}")

    # Test 2: Preserves null points
    test_point = make_conformal_point(1, 0, 0)
    transformed = apply_motor(M, test_point)
    null_error = check_null_constraint(transformed, "Transformed point")
    if null_error > tolerance:
        failures.append(f"Null preservation error: {null_error:.2e}")

    # Test 3: Preserves distances
    p1 = make_conformal_point(0, 0, 0)
    p2 = make_conformal_point(1, 0, 0)
    d_before = conformal_distance(p1, p2)

    p1_t = apply_motor(M, p1)
    p2_t = apply_motor(M, p2)
    d_after = conformal_distance(p1_t, p2_t)

    distance_error = abs(d_after - d_before)
    if distance_error > tolerance:
        failures.append(f"Distance preservation error: {distance_error:.2e}")

    # Test 4: Composition with inverse yields identity
    M_inv = motor_inverse(M)
    identity = geometric_product(M, M_inv)
    identity_error = abs(identity.get(0, 0) - 1.0)
    for blade, coeff in identity.items():
        if blade != 0 and abs(coeff) > tolerance:
            identity_error += abs(coeff)

    if identity_error > tolerance:
        failures.append(f"Inverse composition error: {identity_error:.2e}")

    # Report
    if failures:
        print(f"MOTOR VALIDATION FAILED:")
        for failure in failures:
            print(f"  - {failure}")
        return False
    else:
        print(f"Motor validation PASSED (tolerance={tolerance})")
        return True
```

#### Emergency Debugging Checklist

When GA code produces complete nonsense:

1. **Verify algebra setup**: Correct metric signature? Right dimension?
2. **Check basis consistency**: All operations using same basis ordering?
3. **Inspect pseudoscalar**: $I^2 = \pm 1$ as expected for your metric?
4. **Test on known cases**: $e_1 \wedge e_2 = e_{12}$? Reflection of $e_1$ in $e_1$ gives $e_1$?
5. **Verify grade patterns**: Vector product gives grades 0,2? Rotor has even grades only?
6. **Check conformal embedding**: Points satisfy $P^2 = 0$? Have $n_\infty$ component?
7. **Validate versors**: $R\tilde{R} = 1$? Motors preserve null vectors?
8. **Profile sparse operations**: Using dense operations on sparse data?
9. **Memory pattern analysis**: Cache-friendly blade ordering?
10. **Compare with known library**: Same result as ganja.js visualization?

Most GA bugs come from mixing incompatible representations or using wrong pseudoscalars. When in doubt, print grade structure and check against expected patterns.
