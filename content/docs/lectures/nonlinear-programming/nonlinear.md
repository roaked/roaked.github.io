---
title: "Nonlinear Programming"
weight: 5
bookFlatSection: true
bookHidden: false
---

# **Notes on Nonlinear Programming**

{{< hint tip >}}
- **from classes of OD**
{{< /hint >}}

### **Problem Setup**

- nonlinear programming (NLP) optimizes a nonlinear objective, with optional nonlinear constraints:

{{< katex display >}}
\min_{x \in \mathbb{R}^n} f(x)
{{< /katex >}}

{{< katex display >}}
\text{s.t. } g_i(x) \le 0,\; i=1,\dots,m, \qquad h_j(x)=0,\; j=1,\dots,p
{{< /katex >}}

- core challenge:
  - objective geometry can be curved and nonconvex
  - constraints can define complex feasible regions
  - local minima can differ strongly from global minima

### **Lagrangian and KKT Conditions**

- define Lagrangian:

{{< katex display >}}
\mathcal{L}(x,\lambda,\nu)=f(x)+\sum_{i=1}^{m}\lambda_i g_i(x)+\sum_{j=1}^{p}\nu_j h_j(x)
{{< /katex >}}

- first-order necessary conditions (under regularity assumptions):
  1. stationarity:
{{< katex display >}}
\nabla_x \mathcal{L}(x^\star,\lambda^\star,\nu^\star)=0
{{< /katex >}}
  2. primal feasibility:
{{< katex display >}}
g_i(x^\star)\le0,\quad h_j(x^\star)=0
{{< /katex >}}
  3. dual feasibility:
{{< katex display >}}
\lambda_i^\star \ge 0
{{< /katex >}}
  4. complementary slackness:
{{< katex display >}}
\lambda_i^\star g_i(x^\star)=0
{{< /katex >}}

{{< hint important >}}
- KKT gives candidate solutions; for nonconvex NLP it does not guarantee global optimality.
{{< /hint >}}

### **Convex vs Nonconvex Programs**

- **convex program**:
  - convex objective + convex inequality constraints + affine equalities
  - every local optimum is global
  - duality is often strong and numerically stable

- **nonconvex program**:
  - may have multiple local minima/saddles
  - initialization strongly impacts final solution
  - global methods usually trade optimality guarantees for high compute cost

### **Major Algorithm Families**

- **gradient-based unconstrained methods**
  - steepest descent: low per-step cost, slower convergence
  - Newton / quasi-Newton (BFGS, L-BFGS): faster near optimum using curvature

- **constrained methods**
  - projected gradient: simple constraints (box, simplex)
  - penalty / augmented Lagrangian: move constraints into objective
  - interior-point (barrier): stay inside feasible region
  - SQP (Sequential Quadratic Programming): solve QP subproblems iteratively

### **Line Search and Trust Region**

- line search update:

{{< katex display >}}
x_{k+1}=x_k+\alpha_k d_k
{{< /katex >}}

- choose step length {{< katex >}}\alpha_k{{< /katex >}} via Wolfe/Armijo conditions to ensure sufficient decrease

- trust-region update solves:

{{< katex display >}}
\min_{d} \; m_k(d) \quad \text{s.t. } \|d\| \le \Delta_k
{{< /katex >}}

- trust-region methods are robust when Hessians are indefinite or badly conditioned

### **Practical Workflow**

1. scale decision variables and constraints before solving
2. provide gradients/Jacobians/Hessians when possible
3. pick solver based on structure:
   - unconstrained smooth: L-BFGS / Newton-CG
   - box constraints: L-BFGS-B
   - general constraints: SLSQP / trust-constr / IPOPT
4. run multiple starts for nonconvex objectives
5. verify KKT residuals and sensitivity to initialization

```python
import numpy as np
from scipy.optimize import minimize, NonlinearConstraint

def f(x):
    return (x[0] - 1.5)**2 + (x[1] + 0.5)**2 + np.sin(3.0 * x[0]) * 0.1

def g(x):  # inequality g(x) <= 0
    return x[0]**2 + x[1]**2 - 1.5

x0 = np.array([0.2, 0.2])
nlc = NonlinearConstraint(g, -np.inf, 0.0)
bounds = [(-2.0, 2.0), (-2.0, 2.0)]

res = minimize(f, x0, method="SLSQP", constraints=[nlc], bounds=bounds)
print(res.x, res.fun, res.success)
```