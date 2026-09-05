# YZV202E Optimization for Data Science (Homework 3)
**Name:** Yusuf Oğuz | **Student ID:** 150220322

---

## Question 1: Water Distribution Transportation Problem

### Parameters

From student ID 150220322, the last four digits are 0, 3, 2, 2:
- a = 4th from last = 0 → **5** (0 replaced by 5)
- b = 3rd from last = 3 → **3**
- c = 2nd from last = 2 → **2**
- d = last digit = 2 → **2**

Unit costs (×1000 TL): Ankara→Çanakkale = 5, Ankara→Denizli = 3, Bursa→Çanakkale = 2, Bursa→Denizli = 2.
Supply: Ankara = 90, Bursa = 80. Demand: Çanakkale = 55, Denizli = 65.

### 1.1 LP Formulation

Decision variables: x₁₁ (Ankara→Çanakkale), x₁₂ (Ankara→Denizli), x₂₁ (Bursa→Çanakkale), x₂₂ (Bursa→Denizli).

**Minimize:**
$$\min \; 5x_{11} + 3x_{12} + 2x_{21} + 2x_{22}$$

**Subject to:**
$$x_{11} + x_{12} \leq 90 \quad \text{(Ankara supply)}$$
$$x_{21} + x_{22} \leq 80 \quad \text{(Bursa supply)}$$
$$x_{11} + x_{21} = 55 \quad \text{(Çanakkale demand)}$$
$$x_{12} + x_{22} = 65 \quad \text{(Denizli demand)}$$
$$x_{11}, x_{12}, x_{21}, x_{22} \geq 0$$

### 1.2 Standard Form

Slack variables s₁, s₂ ≥ 0 are introduced for the supply inequalities, converting them to equalities:

$$\min \; 5x_{11} + 3x_{12} + 2x_{21} + 2x_{22} + 0s_1 + 0s_2$$

$$\mathbf{A} = \begin{bmatrix} 1&1&0&0&1&0\\ 0&0&1&1&0&1\\ 1&0&1&0&0&0\\ 0&1&0&1&0&0 \end{bmatrix}, \quad \mathbf{b} = \begin{bmatrix}90\\80\\55\\65\end{bmatrix}, \quad \mathbf{c} = \begin{bmatrix}5\\3\\2\\2\\0\\0\end{bmatrix}$$

### 1.3 Analytical Solution

Using the Minimum Cost Method, routes are allocated in ascending cost order:

| Step | Route | Flow | Remaining |
|---|---|---|---|
| 1 | Bursa→Çanakkale (cost 2) | x₂₁ = 55 | Çanakkale satisfied, Bursa = 25 |
| 2 | Bursa→Denizli (cost 2) | x₂₂ = 25 | Bursa exhausted, Denizli = 40 |
| 3 | Ankara→Denizli (cost 3) | x₁₂ = 40 | Denizli satisfied, Ankara = 50 |
| 4 | Ankara→Çanakkale (cost 5) | x₁₁ = 0 | Already satisfied |

**Optimal solution:** x₁₁=0, x₁₂=40, x₂₁=55, x₂₂=25

**Total cost:** 5(0) + 3(40) + 2(55) + 2(25) = 0 + 120 + 110 + 50 = **280 × 1000 TL = 280,000 TL**

This solution is optimal. Shifting any unit away from the Bursa routes (cost 2) toward Ankara→Çanakkale (cost 5) adds 3 units of cost per unit moved. There is no cheaper alternative.

### 1.4 CVXPY Validation

CVXPY confirms: optimal cost = 280,000 TL, with the same allocation (x₁₁=0, x₁₂=40, x₂₁=55, x₂₂=25).

### 1.5 Effect of Parameter a

As a varies from 1 to 10, two regimes emerge:

- **a ≤ 2:** Ankara→Çanakkale is as cheap or cheaper than Bursa→Çanakkale (cost 2), so some flow shifts to x₁₁. The total cost drops as a decreases below 2.
- **a = 2:** Both cities cost the same to reach Çanakkale. The solver can split flows freely without affecting the total cost.
- **a > 2:** Ankara→Çanakkale is strictly more expensive. The optimal solution drops x₁₁ to zero - Bursa handles all of Çanakkale's demand (x₂₁=55), and Ankara only covers Denizli (x₁₂=40). Since a no longer appears in the active solution, the total cost stays flat at 280 regardless of how large a gets.

So the cost function is piecewise linear in a and completely flat for a ≥ 2. Once a route is priced out of the solution, changes to its cost have no effect at all. This is a structural property of transportation problems.

---

## Question 2: LP Duality

**Primal:**
$$\max \; x_1 + x_2 \quad \text{s.t.} \quad x_1 + 2x_2 \leq 3, \quad x_1 \geq x_2, \quad x_1, x_2 \geq 0$$

### 2.1 Dual Problem

Rewriting x₁ ≥ x₂ as −x₁ + x₂ ≤ 0, we assign dual variable y₁ ≥ 0 to the first constraint and y₂ ≥ 0 to the second:

**Dual:**
$$\min \; 3y_1$$
$$y_1 - y_2 \geq 1 \quad \text{(column for } x_1)$$
$$2y_1 + y_2 \geq 1 \quad \text{(column for } x_2)$$
$$y_1, y_2 \geq 0$$

**Derivation steps:**
- Objective: min **b**ᵀ**y** = min 3y₁ + 0·y₂ = min 3y₁
- For x₁: A column = [1, −1]ᵀ, c₁ = 1 → y₁ − y₂ ≥ 1
- For x₂: A column = [2, 1]ᵀ, c₂ = 1 → 2y₁ + y₂ ≥ 1

### 2.2 CVXPY Solution

**Primal vertices:**
- (0, 0): f = 0
- (3, 0): f = 3, satisfies x₁ ≥ x₂ ✓
- (1, 1): f = 2, satisfies x₁ = x₂ ✓

→ **Primal optimal: x₁\* = 3, x₂\* = 0, value = 3**

**Dual check at y₁=1, y₂=0:** 3(1)=3, y₁−y₂=1≥1 ✓, 2(1)+0=2≥1 ✓.

→ **Dual optimal: y₁\* = 1, y₂\* = 0, value = 3**

Strong duality holds: primal = dual = **3**.

### 2.3 Effect of Removing x₁ ≥ x₂

Without the constraint, the feasible region gains the vertex (0, 1.5), and the new optimal is still (3, 0) with value **3** - identical to before.

The constraint was **inactive** at the original optimum (x₁ = 3 > 0 = x₂). By complementary slackness, an inactive constraint carries a dual variable of zero, which means it puts no pressure on the objective. Removing it changes nothing. This is the practical takeaway: only binding constraints actually shape the solution.

---

## Question 3: Lagrange Multipliers

$$\min_{x,y} \; xy \quad \text{s.t.} \quad x^2 + y^2 = 2$$

(a = last digit of student ID = 2)

### 3.1 Contour Plot

The contour lines of f(x,y) = xy are hyperbolas symmetric about both diagonals. The constraint is a circle of radius √2. The minimum sits where a contour is tangent to the circle from below (most negative xy); the maximum is tangent from above.

At the minimum points (1,−1) and (−1,1), the gradient of the objective ∇f = [y, x] and the gradient of the constraint ∇g = [2x, 2y] point in opposite directions exactly the Lagrange condition. See Figure q3_contour.png.

### 3.2 Analytical Solution

Lagrangian: L(x,y,λ) = xy + λ(x²+y²−2)

**Stationarity conditions:**
- ∂L/∂x = y + 2λx = 0 → y = −2λx   (1)
- ∂L/∂y = x + 2λy = 0 → x = −2λy   (2)
- ∂L/∂λ = x²+y²−2 = 0               (3)

Substituting (1) into (2): x = −2λ(−2λx) = 4λ²x. Since x ≠ 0: **4λ² = 1 → λ = ±1/2**.

**λ = −1/2:** y = x → 2x² = 2 → x = ±1 → Points (1,1), (−1,−1), f = +1 (maximum)

**λ = +1/2:** y = −x → 2x² = 2 → x = ±1 → Points (1,−1), (−1,1), f = −1 (**minimum**)

### 3.3 Effect of a

For the general problem min xy s.t. x²+y²=a:
- Optimal points: (√(a/2), −√(a/2)) and (−√(a/2), √(a/2))
- Minimum value: **f\* = −a/2**

The circle just scales with a. The optimal direction (y = −x diagonal) never changes.Only how far out on that diagonal the solution lands. So doubling a doubles the magnitude of the optimal value.

### 3.4 scipy Verification

scipy.optimize.minimize (SLSQP) confirms f\* = −1.000000 at (1,−1) and (−1,1). ✓

### 3.5 Maximization vs Minimization

Maximizing xy on the same circle gives f\* = +1 at (1,1) and (−1,−1).() the y=x diagonal instead of y=−x.)

Along the circle parametrized as (√a·cosθ, √a·sinθ), the objective is f(θ) = (a/2)·cos(2θ). It oscillates between −a/2 and +a/2. The minimum is at θ = 3π/4 (and 7π/4), the maximum at θ = π/4 (and 5π/4). Between them, the function is strictly monotone on each arc, there are no other stationary points on the constraint.

### 3.6 SONC, SOSC, and Local Minimizer Check

The Hessian of the Lagrangian is:
$$\nabla^2_{xx}\mathcal{L} = \begin{bmatrix} 2\lambda & 1 \\ 1 & 2\lambda \end{bmatrix}$$

At the minimum point (1,−1) with λ = +1/2:
$$\nabla^2_{xx}\mathcal{L} = \begin{bmatrix} 1 & 1 \\ 1 & 1 \end{bmatrix}$$

The tangent space to the constraint at (1,−1) is spanned by ∇g = [2,−2], so the tangent direction is d = t[1,1] for t ∈ ℝ. Evaluating:

$$d^T \nabla^2_{xx}\mathcal{L} \, d = t^2 \cdot [1,1]\begin{bmatrix}1&1\\1&1\end{bmatrix}\begin{bmatrix}1\\1\end{bmatrix} = 4t^2 > 0 \quad \forall t \neq 0$$

SOSC is satisfied (strictly positive definite on the tangent space), so (1,−1) is a strict local minimizer. By symmetry, (−1,1) is too. And since the constraint set is compact and we've found all stationary points, both are global minimizers.

SONC is also satisfied SOSC is the stronger condition and implies it.

---

## Question 4: KKT Conditions

$$\min_{x_1, x_2} \; x_1^2 + x_2^2 \quad \text{s.t.} \quad g_1: x_1 \leq 7, \quad g_2: -x_1 + \frac{x_2^2}{4} + 4 \leq 0$$

### 4.1 Contour Plot and Feasible Region

The objective contours are concentric circles centered at the origin. But g₂ ≤ 0 forces x₁ ≥ x₂²/4 + 4 ≥ 4, so the entire feasible region lies to the right of x₁ = 4. The origin is not feasible.

The smallest circle touching the feasible region hits the parabola g₂ = 0 at (4, 0)  the point on that curve closest to the origin. Setting x₂ = 0 minimizes the distance, giving x₁ = 4. See Figure q4_contour.png.

**Constraint activity at the optimum (4, 0):**
- g₁ = 4 − 7 = −3 < 0 → **inactive**
- g₂ = −4 + 0 + 4 = 0 → **active** (binding)

### 4.2 KKT Conditions

KKT Lagrangian:
$$\mathcal{L} = x_1^2 + x_2^2 + \mu_1(x_1 - 7) + \mu_2\!\left(-x_1 + \frac{x_2^2}{4} + 4\right)$$

**Stationarity:**
- K1: 2x₁ + μ₁ − μ₂ = 0
- K2: 2x₂ + μ₂·(x₂/2) = 0

**Primal feasibility:** x₁ ≤ 7, −x₁ + x₂²/4 + 4 ≤ 0

**Dual feasibility:** μ₁, μ₂ ≥ 0

**Complementary slackness:** μ₁(x₁−7) = 0, μ₂(−x₁+x₂²/4+4) = 0

### 4.3 Analytical Solution

**Regularity (LICQ):** Where g₂ is active, ∇g₁ = [1,0]ᵀ and ∇g₂ = [−1, x₂/2]ᵀ. These are linearly independent for x₂ ≠ 0. At x₂ = 0 (which is the case we find), g₁ is inactive so only one constraint is active and LICQ holds trivially.

**Case 1: g₁ inactive (μ₁=0), g₂ active**

From K2: x₂(2 + μ₂/2) = 0.

*Sub-case 1a (x₂ = 0):* g₂ active → −x₁+4 = 0 → x₁ = 4. From K1: 8 − μ₂ = 0 → **μ₂ = 8 > 0** ✓. Check g₁: 4−7 = −3 < 0 ✓.
→ **Valid KKT point: (4, 0), f = 16**

*Sub-case 1b (x₂ ≠ 0):* μ₂ = −4 < 0. Violates dual feasibility. Invalid.

**Case 2: Both g₁ and g₂ active (x₁ = 7)**

g₂: −7 + x₂²/4 + 4 = 0 → x₂² = 12 → x₂ = ±2√3.
From K2 with x₂ ≠ 0: μ₂ = −4 < 0. Invalid.

**Case 3: g₁ active (x₁ = 7), g₂ inactive (μ₂ = 0)**

From K2: x₂ = 0. From K1: 14 + μ₁ = 0 → μ₁ = −14 < 0. Invalid.

The only valid KKT point is **(x₁\*, x₂\*) = (4, 0)** with **f\* = 16**. Only g₂ is active; g₁ plays no role in the solution.

### 4.4 CVXPY Verification

CVXPY (CLARABEL solver) confirms: f\* = 16.000000, x₁ = 4.000000, x₂ ≈ 0.000000. ✓
