
# Selective Mesh Splitting for Exactly Divergence-Free Scott-Vogelius Elements

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1kLx0PFDq8W36y1DJmc9XhtNv-oXeaJzT?authuser=3#scrollTo=L0ghsAiDYMeN)

This repository contains the complete Python/FEniCS codebase, generated meshes, and output datasets for the master's thesis investigating localized mesh splitting strategies for the $P_4 \times P_3^{\text{disc}}$ Scott-Vogelius finite element pair. 

The primary focus of this work is resolving the inf-sup (LBB) instability and numerical locking that occurs on standard meshes when solving the incompressible Stokes equations. By implementing a novel "Skip Switch" algorithm, we evaluate the computational and topological trade-offs of partial barycentric refinement coupled with an Iterated Penalty Method (IPM) solver.

## Key Contributions & Discoveries
* **Partial Splitting Sufficiency:** Proved that full macro-element refinement is mathematically unnecessary. Splitting just a single cell adjacent to an interior singularity successfully shatters the straight-line configuration, restoring machine-precision mass conservation.
* **The Topology Paradox:** Discovered a highly nuanced computational trade-off where the symmetric 4-cell split actually solves *faster* than the asymmetric 2-cell and 3-cell intermediate splits due to superior global stiffness matrix conditioning.

---

## Execution Guide: Google Colab Workflow

Unlike local containerized workflows, this project was designed and executed entirely within **Google Colab**. This cloud-based approach completely eliminates the need for local Linux environments, Docker containers, or complex C++ backend compilations.

### 1. Environment Setup
Google Colab instances do not have finite element libraries pre-installed. To automate the installation of legacy FEniCS (`dolfin`) via the `fem-on-colab` script, run the following command in the very first cell of the notebook:

```python
try:
    import dolfin
except ImportError:
    !wget "[https://fem-on-colab.github.io/releases/fenics-install.sh](https://fem-on-colab.github.io/releases/fenics-install.sh)" -O "/tmp/fenics-install.sh" && bash "/tmp/fenics-install.sh"
    import dolfin

!pip install pandas matplotlib numpy
```

### 2. Selecting the Mesh Splitting Strategy
The simulation can be executed sequentially by running the notebook cells. To reproduce the "Topology Paradox" and compare the different selective splitting approaches, modify the `opt_meshmod` variable and the `skip_index` inside the `mesh_allsplit` function block:

```python
# Options: "no_split", "corner_split", "all_split"
opt_meshmod = "all_split"

# If "all_split" is selected, configure the Skip Switch inside the function:
# skip_index = 0 (splits 3 cells), etc.
```

### 3. Exporting Results and Plots
**Important:** Google Colab environments are ephemeral (data is lost when the runtime disconnects). The code automatically saves DataFrames as CSV files and divergence maps as high-resolution PDFs. Download them to your local machine using the Colab file explorer, or execute the following block at the end of your run:

```python
from google.colab import files
files.download('errors__with_new_eqn_rho_10000.0_4.csv')
files.download('MeshPlot_all_split.pdf')
```

---

## Repository Structure

```text
├── sv_codes_1_cell_split.ipynb          # FEniCS solver implementing the 1-cell selective split
├── sv_codes_2_cell_split.ipynb          # FEniCS solver implementing the 2-cell selective split
├── sv_codes_3_cell_split.ipynb          # FEniCS solver implementing the 3-cell selective split
├── sv_codes_4_cell_split.ipynb          # FEniCS solver implementing the standard full barycentric split
├── Data_Analysis.ipynb                  # Jupyter Notebook for processing CSVs and generating plots
├── data/                                # Directory containing all logged simulation data
│   ├── rho_100/                         # CSV logs for penalty parameter 100
│   └── rho_10000/                       # CSV logs for penalty parameter 10000
│   ├── errors_with_no_corner_split.csv  # CSV logs (only corners are split)
│   ├── errors_with_no_split.csv         # CSV logs (no split is performed)
└── plots/                               # Output directory for high-resolution matplotlib figures
```

---

## Summary of Results

* **Baseline Failure:** Standard unstructured meshes stagnate at an $L^2$ divergence of $\approx 10^{-4}$ due to interior singular vertices locking the discrete pressure space.
* **Optimal Convergence:** All partial and full splitting strategies (1 through 4 cells) restored optimal $\mathcal{O}(h^k)$ spatial convergence rates for velocity and pressure.
* **Performance Scaling:** Scaling the IPM penalty parameter ($\rho$) from $100$ to $10,000$ reduced the required Uzawa iterations from 10 down to 4, effectively halving the total CPU compute time on fine grids ($N=64$).

---

## License & Academic Citation

This code is open-source and released under the [MIT License](https://opensource.org/licenses/MIT).

```
