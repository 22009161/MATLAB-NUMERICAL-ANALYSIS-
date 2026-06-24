# Sparse Matrix Solvers & Numerical Analysis Benchmarks

This repository evaluates direct and iterative linear system solvers ($Ax = b$) using a custom-generated sparse pentadiagonal matrix in MATLAB. The project benchmarks execution runtime, tracks matrix sparsity modifications, maps eigenvalue spectral properties, and illustrates hardware limitations at large scales.

## Matrix Structure Design
The system matrix $A \in \mathbb{R}^{n \times n}$ is programmatically initialized as a sparse layout containing up to 5 non-zero diagonals:
* **Main Diagonal ($A_{i,i}$):** Linear scaling coefficient scaling directly with index $i$ ($A_{i,i} = i$).
* **Sub-diagonals ($A_{i, i\pm1}$, $A_{i, i\pm2}$):** Populated with a uniform constant weight of $0.5$.

The target solution vector $b$ is instantiated as a unit matrix ($b \in \mathbb{R}^{n \times 1}$).

---

## Technical Experiments & Key Findings

### 1. Direct Solvers: LU vs. Cholesky Decomposition
* **Observation:** Cholesky factorization finishes significantly faster than standard General LU factorization across all valid matrix configurations ($n \le 10,000$).
* **Mathematical Insight:** Because our constructed matrix $A$ is both symmetric and strictly diagonally dominant, it is guaranteed to be Symmetric Positive-Definite (SPD). Cholesky factorization takes direct advantage of this physical trait, reducing algorithmic operation counts roughly in half ($O(\frac{1}{3}n^3)$ operations vs $O(\frac{2}{3}n^3)$ for traditional LU), while avoiding pivoting requirements.

### 2. Iterative Solvers: Jacobi vs. Gauss-Seidel 
* **Observation:** Both structural iterative variants achieve identical convergence criteria utilizing the exact same computation iteration budget (e.g., 11 iterations for large sizes of $n$).
* **Mathematical Insight:** The linear scaling applied to the main diagonal elements ($A_{i,i} = i$) forces extreme structural diagonal dominance. This dominates the matrix profile so completely that the historical acceleration advantage of Gauss-Seidel's look-ahead updates disappears, matching Jacobi's stepping path step-for-step.

### 3. The 74.5 GB Memory Wall ($n = 100,000$)
When configuring the program scale to handle $n = 100,000$, attempts to compute dense conversions via `full(A)` or compute direct tracking via `inv()` immediately break down with a hardware violation message:
> `Requested 100000x100000 (74.5GB) array exceeds maximum array size preference.`

* **The Takeaway:** Storing a matrix of size $100,000 \times 100,000$ uniformly requires **74.5 Gigabytes of continuous, unfragmented physical RAM**. This experiment confirms why keeping calculations in a compressed sparse data schema is critical for industrial or high-performance scientific simulations.

---

# MATLAB-NUMERICAL-ANALYSIS-
MATLAB numerical analysis project benchmarking direct (LU, Cholesky) and iterative (Jacobi, Gauss-Seidel) linear system solvers on large sparse pentadiagonal matrices.
