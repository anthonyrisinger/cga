### Chapter 2: Reflection—The Fundamental Operation

Stand before a mirror. Your reflection reverses perpendicular to the mirror's surface while preserving everything parallel to it. This simple operation generates all of geometric transformation.

Place two mirrors at 45°. An object reflected in the first mirror, then the second, rotates by 90°—exactly twice the angle between mirrors. Three mirrors can produce any spatial rotation. Four mirrors generate any rigid transformation. This observation leads to a fundamental theorem.

**Theorem (Cartan-Dieudonné)**: Every orthogonal transformation in $n$-dimensional space decomposes into at most $n$ reflections.

This isn't merely a mathematical curiosity. It reveals that reflection is the atomic operation from which all geometric transformations build. Understanding reflection algebraically unlocks a unified approach to geometric computation.

#### The Reflection Formula

Consider a point $\mathbf{p} = (3, 4, 5)$ reflecting in a plane through the origin with unit normal $\mathbf{n} = (1, 0, 0)$. The reflection formula:

$$\mathbf{p}' = \mathbf{p} - 2(\mathbf{p} \cdot \mathbf{n})\mathbf{n}$$

Step-by-step computation:
- $\mathbf{p} \cdot \mathbf{n} = 3(1) + 4(0) + 5(0) = 3$
- $2(\mathbf{p} \cdot \mathbf{n})\mathbf{n} = 2(3)(1, 0, 0) = (6, 0, 0)$
- $\mathbf{p}' = (3, 4, 5) - (6, 0, 0) = (-3, 4, 5)$

The $x$-component flips sign while $y$ and $z$ remain unchanged—correct reflection in the $yz$-plane.

**Operation count** (dimension $n$):
- Dot product: $n$ multiplications, $n-1$ additions
- Scalar-vector product: $n$ multiplications
- Vector subtraction: $n$ subtractions
- Total: $2n$ multiplications, $n-1$ additions, $n$ subtractions

For 3D: 6 multiplications, 2 additions, 3 subtractions = 11 operations.

#### The Composition Problem

Single reflections compute efficiently. Complications arise with composition. Consider two successive reflections with unit normals $\mathbf{n}_1$ and $\mathbf{n}_2$:

First reflection: $\mathbf{p}' = \mathbf{p} - 2(\mathbf{p} \cdot \mathbf{n}_1)\mathbf{n}_1$

Second reflection: $\mathbf{p}'' = \mathbf{p}' - 2(\mathbf{p}' \cdot \mathbf{n}_2)\mathbf{n}_2$

Substituting:
$$\mathbf{p}'' = \mathbf{p} - 2(\mathbf{p} \cdot \mathbf{n}_1)\mathbf{n}_1 - 2[(\mathbf{p} - 2(\mathbf{p} \cdot \mathbf{n}_1)\mathbf{n}_1) \cdot \mathbf{n}_2]\mathbf{n}_2$$

Expanding the second dot product:
$$\mathbf{p}'' = \mathbf{p} - 2(\mathbf{p} \cdot \mathbf{n}_1)\mathbf{n}_1 - 2(\mathbf{p} \cdot \mathbf{n}_2)\mathbf{n}_2 + 4(\mathbf{p} \cdot \mathbf{n}_1)(\mathbf{n}_1 \cdot \mathbf{n}_2)\mathbf{n}_2$$

The geometric meaning—this represents a rotation—is completely obscured by the algebraic expansion. For three reflections, the formula explodes into dozens of terms.

#### Matrix Representation

Linear algebra offers an alternative. The reflection matrix for unit normal $\mathbf{n}$:

$$R = I - 2\mathbf{n}\mathbf{n}^T$$

For our example with $\mathbf{n} = (1, 0, 0)$:

$$R = \begin{pmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix} - 2\begin{pmatrix} 1 \\ 0 \\ 0 \end{pmatrix}\begin{pmatrix} 1 & 0 & 0 \end{pmatrix} = \begin{pmatrix} -1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

Composing reflections becomes matrix multiplication: $R_2R_1$. This handles composition algebraically but:
- Requires $n^2$ storage per reflection
- Obscures geometric interpretation
- Extracting rotation axes from $R_2R_1$ requires eigenanalysis (~50 operations)

#### Toward Algebraic Reflection

Both approaches—vector formulas and matrices—struggle with composition. The core issue: traditional vector algebra lacks a product that preserves geometric structure through transformations.

Consider what reflection accomplishes algebraically. Given unit normal $\mathbf{n}$ and vector $\mathbf{v}$, reflection decomposes $\mathbf{v}$ into components parallel and perpendicular to $\mathbf{n}$:

$$\mathbf{v}_\parallel = (\mathbf{v} \cdot \mathbf{n})\mathbf{n}$$
$$\mathbf{v}_\perp = \mathbf{v} - (\mathbf{v} \cdot \mathbf{n})\mathbf{n}$$

Reflection reverses the perpendicular component:
$$\mathbf{v}' = \mathbf{v}_\parallel - \mathbf{v}_\perp = (\mathbf{v} \cdot \mathbf{n})\mathbf{n} - [\mathbf{v} - (\mathbf{v} \cdot \mathbf{n})\mathbf{n}] = -\mathbf{v} + 2(\mathbf{v} \cdot \mathbf{n})\mathbf{n}$$

This can be rewritten:
$$\mathbf{v}' = -\mathbf{v} + 2(\mathbf{v} \cdot \mathbf{n})\mathbf{n} = -[\mathbf{v} - (\mathbf{v} \cdot \mathbf{n})\mathbf{n} + (\mathbf{v} \cdot \mathbf{n})\mathbf{n}] = -[\mathbf{v}_\perp - \mathbf{v}_\parallel]$$

The pattern suggests we need an algebraic operation where:
1. Products of vectors produce new geometric objects
2. These objects can be multiplied associatively
3. The operation preserves metric information

#### The Sandwich Pattern Emerges

Examining successful reflection representations across mathematics reveals a pattern. In quantum mechanics, unitary transformations take the form $U\psi U^\dagger$. In group theory, conjugation $gxg^{-1}$ changes representation while preserving structure.

For geometric reflection, we seek an algebraic product where reflection takes the form:
$$\mathbf{v}' = -\mathbf{n}\mathbf{v}\mathbf{n}$$

This "sandwich" pattern appears natural, but traditional vector algebra cannot express it—we cannot multiply three vectors to get a vector.

The resolution requires extending our algebra. We need a product of vectors that:
- Produces something that can be multiplied again
- Preserves all geometric information
- Reduces to familiar operations in special cases

#### Transformation Decompositions

The Cartan-Dieudonné theorem guarantees decomposition into reflections. The specific decompositions reveal geometric structure:

**Identity**: 0 reflections (or any even number in the same plane)

**Reflection**: 1 reflection through the given hyperplane

**Rotation**: 2 reflections in intersecting planes
- Angle between planes = half the rotation angle
- Intersection line = rotation axis
- Example: 90° rotation = 2 reflections in planes at 45°

**Translation**: 2 reflections in parallel planes
- Distance between planes = half the translation distance
- Translation direction = perpendicular to planes

**General rigid motion (screw)**: Up to 4 reflections
- Can always decompose into rotation then translation
- Screw axis = common perpendicular to reflection planes

**Scaling**: Not achievable through reflections alone (not orthogonal)

This decomposition provides both theoretical insight and practical algorithms. Any rigid transformation can be built from reflections, suggesting a computational approach based on reflection composition.

#### Computational Tradeoffs

| Operation | Vector Formula | Matrix Method | GA (Preview) | Notes |
|-----------|---------------|---------------|--------------|-------|
| Single reflection | 11 ops | 9 ops | 11 ops | Vector formula optimal |
| Two reflections | ~30 ops | 27 ops | 16 ops | GA composition shines |
| Extract rotation | Not available | ~50 ops | Direct | GA preserves structure |
| Storage (3D) | 3 floats/plane | 9 floats/matrix | 4 floats/rotor | GA compact for rotations |

The geometric algebra approach (previewed here, developed in Chapter 3) excels at composition while matching single-reflection performance.

#### Engineering Implications

Reflection-based decomposition offers:

**Numerical advantages**:
- Reflections preserve lengths exactly (orthogonal transformations)
- No trigonometric functions required
- Avoids gimbal lock and singularities

**Architectural benefits**:
- Single operation type (reflection) generates all transformations
- Composition through multiplication
- Geometric meaning preserved through operations

**Computational costs**:
- More operations than optimized special cases
- Requires understanding new algebraic structure
- Initial learning investment

#### When to Use Reflection-Based Methods

**Use when**:
- Composing many transformations programmatically
- Numerical stability near singularities matters
- Geometric insight aids algorithm development
- Building transformation libraries from scratch

**Avoid when**:
- Performance critical with known, fixed transformations
- Hardware acceleration available (GPU matrices)
- Working within established matrix pipelines
- Team lacks geometric algebra experience

#### The Path Forward

Traditional vector algebra cannot express reflection composition elegantly because it lacks a product that preserves complete geometric information. The dot product $\mathbf{a} \cdot \mathbf{b}$ yields only a scalar, losing directional information. The cross product $\mathbf{a} \times \mathbf{b}$ works only in 3D and loses magnitude information.

The next chapter develops the geometric product—an operation that preserves both metric and directional information, enabling the sandwich formula for reflection and, through it, all geometric transformations. This product emerges not from mathematical abstraction but from the concrete requirement to compose reflections algebraically.
