# Thermal-Aware Power Budgeting on a Chip

**Capstone Problem A · Odd Group 3 · S3 EC (AY 2025–26)**
*PDEs, Transforms & Optimization Techniques*

From Fields to Circuits: **PDE → Laplace → Sparse FDM → LP**

## Team

| Name | Stage owned |
|---|---|
| Amal Krishna S | 1. Field model, 2. PDE |
| Hrishikesh S Nair | 3. Transform |
| Amir Mohammed N | 4. Discretisation |
| Robin Kirian Abraham | 5. LP |
| Harith C V | 6. Verification |

## Overview

A 10 × 10 mm silicon die has four heat-generating blocks, with all four edges held at 300 K by a heat sink. The steady temperature field is modelled by the Poisson equation, discretised with a 5-point finite-difference stencil into a sparse linear system, and the power split across the blocks that **minimises the peak temperature** is found with a linear program (epigraph formulation).

**Headline results** (total budget P_max = 5 W):

| Scenario | Uniform peak | LP peak | Reduction | LP allocation q1..q4 (W) |
|---|---|---|---|---|
| 1. Layout from the brief (symmetric) | 341.83 K | 341.83 K | 0.00 K | 1.25, 1.25, 1.25, 1.25 |
| 2. Asymmetric layout | 359.04 K | 347.40 K | 11.64 K | 1.82, 0.94, 1.12, 1.12 |

For the symmetric layout the uniform split is already optimal (proved by a symmetry/Jensen argument in the report). For the asymmetric layout the LP moves power to the well-cooled block near the heat sink and away from the mid-die cluster.

## Pipeline

| Stage | Math concept | Course | Implementation |
|---|---|---|---|
| 1. Field model | grad, div, divergence theorem | Vector Calculus | q = −k∇T, div q = Q |
| 2. PDE | Elliptic, Dirichlet BCs, well-posedness | PDE | ∇²T = −Q/k, T = T_amb on the boundary |
| 3. Transform | Laplace transform in time, s → 0 limit | Transforms | Z_th(s) = Σ R_mn/(1 + sτ_mn), SymPy check |
| 4. Discretisation | Sparse systems, Kronecker spectrum | Linear Algebra | Au = −Bq, `scipy.sparse`, `splu` |
| 5. LP | Epigraph form, constraints | Optimisation | min t s.t. −t ≤ u_i ≤ t, `linprog` (HiGHS) |
| 6. Verification | Numerical methods | Python Lab | Heatmaps, sweep, convergence, transient |

## Model parameters

| Parameter | Value | Source |
|---|---|---|
| Die size L | 10 mm | Brief |
| Ambient / edge temperature | 300 K | Brief |
| Thermal conductivity k | 150 W m⁻¹ K⁻¹ | Brief |
| Grid | N = 40 (1444 interior unknowns) | Report |
| Die thickness d | 150 µm | **Assumption** (chosen so the uniform peak is near the brief's ≈ 343 K) |
| Volumetric heat capacity ρc | 1.63 × 10⁶ J m⁻³ K⁻¹ | **Assumption** (silicon) |

## How to run

```bash
python run.py
```

This regenerates every figure and table in the report (about 10 s).

**Requirements:** Python 3 with `numpy`, `scipy` (sparse solvers and `linprog`/HiGHS), `matplotlib` and `sympy`.

```bash
pip install numpy scipy matplotlib sympy
```

## Verification summary

- **Convergence:** manufactured solution on grids N = 11, 21, 41, 81 gives a fitted order of 2.00.
- **Reciprocity:** Z_th(0) is symmetric to 10⁻⁹, as expected from A = Aᵀ.
- **Transient to steady state:** backward-Euler peak at 300 ms is 341.78 K against a steady value of 341.83 K.
- **LP consistency:** the LP objective t equals the true PDE peak rise T_max − T_amb to 10⁻⁴ relative.
- **Linear algebra checks:** A is symmetric negative definite (λ_max = −1.97 × 10⁵, λ_min = −1.21 × 10⁸ m⁻²), condition number ≈ 616.

## Known limitations

1. **Point sources are singular.** In 2-D the Green's function diverges logarithmically, so absolute peaks depend on mesh size h and on the assumed d. The allocation and the relative reduction are much less sensitive.
2. **Simplified physics.** The model is 2-D with constant k, no heat flow through the thickness, and no package or convection. d and ρc are assumptions not given in the brief.
3. **Symmetric brief layout.** The brief's quoted LP value (≈ 327 K) cannot be obtained from a symmetric layout, so Scenario 2 was added rather than tuning the data.
4. **Budget as equality.** The brief writes Σq_i ≤ P_max, but this is implemented as Σq_i = P_max, since otherwise q = 0 gives a trivial solution.

## Contents

- `report_with_team.pdf`: the full 4-page report.
- `run.py`: regenerates all figures and results.

## References

1. F. P. Incropera et al., *Fundamentals of Heat and Mass Transfer*, Wiley.
2. L. C. Evans, *Partial Differential Equations*, AMS.
3. R. J. LeVeque, *Finite Difference Methods for ODEs and PDEs*, SIAM.
4. D. Bertsimas & J. Tsitsiklis, *Introduction to Linear Optimization*, Athena Scientific.
5. P. Virtanen et al., *SciPy 1.0*, Nature Methods 17 (2020).

Repository: *[Git URL]*
