# Thermal-Aware Power Budgeting on a Chip

**From Fields to Circuits: PDE → Laplace → Sparse FDM → LP**  
*Capstone Problem A | S3 EC (AY 2025-26) | Odd Group 3*[span_0](start_span)[span_0](end_span)  

---

## Team Members

* **Amal Krishna S**[span_1](start_span)[span_1](end_span)
* **Hrishikesh S Nair**[span_2](start_span)[span_2](end_span)
* **Amir Mohammed N**
* **Robin Kirian Abraham**[span_3](start_span)[span_3](end_span)
* **Harith C V**[span_4](start_span)[span_4](end_span)

---

## Project Overview

This project models heat diffusion and thermal management on an integrated circuit die[span_5](start_span)[span_5](end_span). A 10 mm by 10 mm silicon die with four heat-generating blocks and boundaries held at 300 K ambient temperature is modeled using the 2D steady-state heat equation[span_6](start_span)[span_6](end_span). 

The continuous problem is discretized using a standard 5-point finite difference stencil into a sparse linear system[span_7](start_span)[span_7](end_span)[span_8](start_span)[span_8](end_span). Using an epigraph formulation, a linear program optimizes the power allocation across the four blocks to minimize the peak die temperature under a fixed total power budget[span_9](start_span)[span_9](end_span)[span_10](start_span)[span_10](end_span).

---

## Mathematical Pipeline & Stages

### 1. Field Model (Vector Calculus)
Energy conservation combined with Fourier's Law of conduction relates the temperature gradient to the volumetric heat source[span_11](start_span)[span_11](end_span). The silicon thermal conductivity is taken as 150 W/(m·K) and die thickness as 150 micrometers[span_12](start_span)[span_12](end_span).

### 2. PDE Formulation (Partial Differential Equations)
The steady-state boundary value problem is classified as an elliptic Poisson equation[span_13](start_span)[span_13](end_span). Dirichlet boundary conditions represent a heat sink holding all four die edges at a fixed ambient temperature of 300 K[span_14](start_span)[span_14](end_span).

### 3. Transform Solution (Transforms)
Applying the Laplace transform in time converts the transient diffusion problem into a frequency-dependent thermal impedance network composed of parallel RC stages[span_15](start_span)[span_15](end_span). The zero-frequency limit corresponds directly to the steady-state thermal resistance matrix[span_16](start_span)[span_16](end_span).

### 4. Discretization & Sparse Linear Algebra (Linear Algebra)
On a 40 by 40 grid with grid spacing of 0.2564 mm, the 5-point stencil yields 1,444 interior node equations[span_17](start_span)[span_17](end_span). The resulting sparse coefficient matrix is symmetric negative definite and is solved using sparse LU decomposition[span_18](start_span)[span_18](end_span).

### 5. Optimization (Linear Program)
Minimizing the peak temperature over all grid nodes is convex and piecewise linear[span_19](start_span)[span_19](end_span). Recasting this into epigraph form yields a linear program solved with the HiGHS solver[span_20](start_span)[span_20](end_span).

---

## Key Results

* **Scenario 1 (Symmetric Layout):** Blocks are placed symmetrically across the die[span_21](start_span)[span_21](end_span). Due to four-fold symmetry, a uniform power split of 1.25 W per block is already optimal, yielding a peak temperature of 341.83 K with no reduction possible[span_22](start_span)[span_22](end_span)[span_23](start_span)[span_23](end_span).
* **Scenario 2 (Asymmetric Layout):** One block is located near the cold boundary while three blocks form an interior cluster[span_24](start_span)[span_24](end_span)[span_25](start_span)[span_25](end_span). The linear program shifts power away from the clustered blocks toward the well-cooled edge block, lowering the peak temperature from 359.04 K down to 347.40 K (a reduction of 11.64 K)[span_26](start_span)[span_26](end_span)[span_27](start_span)[span_27](end_span).

---

## Pipeline Responsibility Table

| Stage | Math Concept | Course | Primary Task | Owner |
| :---: | :--- | :--- | :--- | :---: |
| **1**[span_28](start_span)[span_28](end_span) | Vector Calculus[span_29](start_span)[span_29](end_span) | Vector Calculus[span_30](start_span)[span_30](end_span) | Gradient, divergence, and heat-flux formulation[span_31](start_span)[span_31](end_span) | AKS[span_32](start_span)[span_32](end_span) |
| **2**[span_33](start_span)[span_33](end_span) | PDE Classification[span_34](start_span)[span_34](end_span) | PDE[span_35](start_span)[span_35](end_span) | Elliptic classification and Dirichlet boundary conditions[span_36](start_span)[span_36](end_span) | AKS[span_37](start_span)[span_37](end_span) |
| **3**[span_38](start_span)[span_38](end_span) | Laplace Transforms[span_39](start_span)[span_39](end_span) | Transforms[span_40](start_span)[span_40](end_span) | Time-domain transforms, RC modes, and steady-state limit[span_41](start_span)[span_41](end_span) | HSN[span_42](start_span)[span_42](end_span) |
| **4**[span_43](start_span)[span_43](end_span) | Sparse Systems[span_44](start_span)[span_44](end_span) | Linear Algebra[span_45](start_span)[span_45](end_span) | 5-point stencil assembly and sparse LU solve[span_46](start_span)[span_46](end_span) | AMN |
| **5**[span_47](start_span)[span_47](end_span) | Epigraph Optimization[span_48](start_span)[span_48](end_span) | Optimization[span_49](start_span)[span_49](end_span) | Linear programming setup and HiGHS solver[span_50](start_span)[span_50](end_span) | RKA[span_51](start_span)[span_51](end_span) |
| **6**[span_52](start_span)[span_52](end_span) | Verification[span_53](start_span)[span_53](end_span) | Python Lab[span_54](start_span)[span_54](end_span) | Convergence checks, transient step, and temperature maps[span_55](start_span)[span_55](end_span) | HCV[span_56](start_span)[span_56](end_span) |

---

## How to Run

1. Clone the repository:
   ```bash
   git clone <REPO_URL>
   cd <REPO_FOLDER>