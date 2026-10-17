# Thermal-Aware Power Budgeting on a Chip

**From Fields to Circuits: PDE → Laplace → Sparse FDM → LP**  
*Capstone Problem A | S3 EC (AY 2025-26) | Odd Group 3*  

---

## 👥 Team Members

* **Amal Krishna S**
* **Hrishikesh S Nair**
* **Amir Mohammed N**
* **Robin Kirian Abraham**
* **Harith C V**

---

## 📌 Project Overview

This repository models heat diffusion and thermal management on an integrated circuit. A $10 \times 10\text{ mm}$ silicon die with four heat-generating blocks and boundaries held at ambient temperature ($300\text{ K}$) is governed by the 2D Poisson equation:

$$\nabla^2 T = -\frac{Q}{k}$$

The system is discretized using a standard 5-point finite difference stencil into a large, sparse linear system:

$$Au = -Bq$$

An epigraph-form Linear Program (LP) optimizes the power distribution among the blocks to minimize the maximum peak chip temperature.

---

## 📐 Mathematical Formulation & Stages

### 1. Field Model (Vector Calculus)
From Fourier's Law and steady energy conservation:

$$q_h = -k\nabla T$$

$$\operatorname{div} q_h = Q \implies -k \operatorname{div}(\operatorname{grad} T) = Q \implies \nabla^2 T = -\frac{Q}{k}$$

where $k = 150\text{ W m}^{-1}\text{K}^{-1}$ and die thickness $d = 150\text{ }\mu\text{m}$.

### 2. PDE Formulation (Elliptic)
The equation is classified as elliptic ($B^2 - AC < 0$) with Dirichlet boundary conditions:

$$T = T_{\text{amb}} = 300\text{ K} \quad \text{on } \partial\Omega$$

### 3. Transform Solution (Laplace Domain)
Applying Laplace transforms turns transient diffusion into a multi-mode thermal impedance network:

$$Z_{\text{th}}(x, x_s; s) = \sum_{m,n} \frac{R_{mn}}{1 + s\tau_{mn}}$$

Taking the steady-state limit ($s \to 0$) yields the DC thermal resistance matrix $Z_{\text{th}}(0)$.

### 4. Discretization & Sparse Linear Algebra
Using a grid of $N = 40$ ($h = 0.2564\text{ mm}$), the interior nodes ($n^2 = 1444$) form the system:

$$Au = -Bq$$

The matrix $A \in \mathbb{R}^{1444 \times 1444}$ is symmetric negative definite and solved using SciPy sparse LU decomposition (`splu`).

### 5. Optimization (Linear Program)
The minimax peak temperature problem is recast in epigraph form:

$$\min_{z} t$$

subject to:

$$Au + Bq = 0$$

$$\mathbf{1}^\top q = P_{\max}$$

$$u_i - t \le 0, \quad -u_i - t \le 0$$

$$q \ge 0, \quad t \ge 0$$

The LP is solved using the SciPy `linprog` HiGHS solver.

---

## 📊 Summary of Results

| Scenario | Uniform Peak | LP Optimal Peak | Peak Reduction | LP Power Split $q_1 \dots q_4$ (W) |
| :--- | :---: | :---: | :---: | :---: |
| **Scenario 1 (Symmetric)** | $341.83\text{ K}$