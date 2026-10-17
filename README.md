# Thermal-Aware Power Budgeting on a Chip

**From Fields to Circuits: PDE → Laplace → Sparse FDM → LP**  
*Capstone Problem A | S3 EC (AY 2025–26) | Odd Group 3*

---

## 📌 Project Overview

This repository models heat diffusion and thermal management on an integrated circuit. A $10 \times 10\text{ mm}$ silicon die with four heat-generating blocks and boundaries held at ambient temperature ($300\text{ K}$) is governed by the 2D Poisson equation:

$$\nabla^2 T = -\frac{Q}{k}$$

The system is discretized using a standard 5-point finite difference stencil into a large, sparse linear system:

$$Au = -Bq$$

By leveraging the inherent linearity of the Poisson PDE, we formulate an epigraph-based Linear Program (LP) to budget power across active blocks such that the maximum die temperature is minimized:

$$\min_{z} t \quad \text{s.t.} \quad Au + Bq = 0, \quad \mathbf{1}^\top q = P_{\max}, \quad -t \le u_i \le t, \quad q \ge 0$$

### Key Findings
- **Symmetric Layout (Scenario 1):** Due to geometric symmetry and convexity (Jensen's inequality), uniform power allocation is mathematically provable as optimal ($341.83\text{ K}$ peak).
- **Asymmetric Layout (Scenario 2):** The linear program redistributes power toward blocks near the die edge (cooler heat-sink paths), dropping the peak temperature from $359.04\text{ K}$ to $347.40\text{ K}$ (a reduction of **$11.64\text{ K}$**).

---

## 👥 Team Members

| Name | Role / Pipeline Stage | Initials |
| :--- | :--- | :--- |
| **Amal Krishna S** | Field Model (Vector Calculus) & PDE Classification | `AKS` |
| **Hrishikesh S Nair** | Laplace Transform & Thermal Impedance ($Z_{\text{th}}$) | `HSN` |
| **Amir Mohammed N** | FDM Discretization & Sparse Linear Algebra (`scipy.sparse`) | `AMN` |
| **Robin Kirian Abraham** | Linear Program Formulation (`scipy.optimize.linprog` / HiGHS) | `RKA` |
| **Harith C V** | Verification, Numerical Convergence & Visualizations | `HCV` |

---

## 🔬 Mathematical & Engineering Pipeline

| Stage | Mathematical Concept | Course Domain | Formulation / Tool |
| :--- | :--- | :--- | :--- |
| **1. Field Model** | Gradient, Divergence, Divergence Thm | Vector Calculus | $q_h = -k\nabla T$, $\operatorname{div} q_h = Q$ |
| **2. PDE** | Elliptic classification, Dirichlet BCs | PDEs | $\nabla^2 u = -Q/k$, $u\vert_{\partial\Omega} = 0$ |
| **3. Transform** | Laplace in time ($s \to 0$ limit) | Transforms | $Z_{\text{th}}(s) = \sum \frac{R_{mn}}{1 + s\tau_{mn}}$, SymPy verification |
| **4. Discretization** | 5-point stencil, Kronecker spectrum | Linear Algebra | $Au = -Bq$, `scipy.sparse.splu` |
| **5. Optimization** | Epigraph form, Minimax linear program | Optimization | $\min t$ s.t. $-t \le u \le t$, HiGHS solver |
| **6. Verification** | Order of convergence, transient limit | Numerical Methods | $O(h^2)$ check, backward-Euler time-stepping |

---

## 🛠️ Repository Structure

```text
├── README.md                  # Project documentation and summary
├── requirements.txt           # Python dependencies
├── run.py                     # Main executable script generating all figures and tables
├── src/
│   ├── pde_solver.py          # 5-point Laplacian assembler and sparse solver
│   ├── lp_optimizer.py        # Epigraph LP formulation using scipy.optimize.linprog
│   ├── transient.py           # Backward-Euler time stepping and Laplace transform verification
│   └── plotting.py            # Heatmaps, convergence curves, and power sweep figures
└── figures/                   # Generated output plots (PDF / PNG)
```

---

## 🚀 Quick Start

### 1. Prerequisites
Ensure you have Python 3.9+ installed. Install the dependencies via:

```bash
pip install -r requirements.txt
```

#### Typical `requirements.txt`:
```text
numpy>=1.22.0
scipy>=1.10.0
matplotlib>=3.6.0
sympy>=1.11.0
```

### 2. Run Simulations and Generate Plots
To execute the complete pipeline, perform analytical verifications, and recreate all figures (~10 seconds run time):

```bash
python run.py
```

---

## 📊 Verification & Validation

1. **Spatial Convergence:** Truncation error verification on manufactured solutions ($u = \sin\frac{\pi x}{L}\sin\frac{\pi y}{L}$) shows clean $O(h^2)$ convergence (order $\approx 2.00$).
2. **Matrix Condition & Stability:** Discretized matrix $A$ has symmetric negative-definite Kronecker eigenvalues, ensuring stability.
3. **Impedance Symmetry:** The steady-state thermal resistance matrix $Z_{\text{th}}(0)$ obeys reciprocity ($Z = Z^\top$) within machine precision ($10^{-9}$).
4. **Transient to Steady State:** Backward Euler integration converges directly to the DC limit predicted by the Laplace transform final-value theorem as $t \to \infty$.

---

## 📚 References

1. Incropera, F. P., et al. *Fundamentals of Heat and Mass Transfer*, Wiley.
2. Evans, L. C. *Partial Differential Equations*, American Mathematical Society.
3. LeVeque, R. J. *Finite Difference Methods for Ordinary and Partial Differential Equations*, SIAM.
4. Bertsimas, D., & Tsitsiklis, J. *Introduction to Linear Optimization*, Athena Scientific.
5. Virtanen, P., et al. "SciPy 1.0: Fundamental Algorithms for Scientific Computing in Python." *Nature Methods* 17 (2020).
