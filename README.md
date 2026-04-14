
# Selective Mesh Splitting for Exactly Divergence-Free Scott-Vogelius Elements

This repository contains the complete Python/FEniCS codebase, simulation data, and data analysis notebooks for the master's thesis investigating localized mesh splitting strategies for the $P_4 \times P_3^{disc}$ Scott-Vogelius finite element pair. 

The primary focus of this work is resolving the inf-sup (LBB) instability and numerical locking that occurs on standard meshes when solving the incompressible Stokes equations. By implementing a novel "Skip Switch" algorithm, we evaluate the computational and topological trade-offs of partial barycentric refinement (1-cell, 2-cell, 3-cell, and 4-cell splits) coupled with an Iterated Penalty Method (IPM) solver.

##  Key Contributions & Discoveries
* **Partial Splitting Sufficiency:** Proved that full macro-element refinement is mathematically unnecessary. Splitting just a single cell adjacent to an interior singularity successfully shatters the straight-line configuration, restoring machine-precision mass conservation ($\nabla \cdot \mathbf{u}_h \approx 10^{-13}$).
* **The Topology Paradox:** Discovered a highly nuanced computational trade-off where the symmetric 4-cell split actually solves *faster* than the asymmetric 2-cell and 3-cell intermediate splits due to superior global stiffness matrix conditioning.

---

##  Repository Structure

```text
├── sv_codes_1_cell_split.ipynb          # FEniCS solver implementing the 1-cell selective split
├── sv_codes_2_cell_split.ipynb          # FEniCS solver implementing the 2-cell selective split
├── sv_codes_3_cell_split.ipynb          # FEniCS solver implementing the 3-cell selective split
├── sv_codes_4_cell_split.ipynb          # FEniCS solver implementing the standard full barycentric split
├── Data_Analysis.ipynb         # Jupyter Notebook for processing CSVs and generating plots
├── data/                       # Directory containing all logged simulation data
│   ├── rho_100/                # CSV logs (iterations, errors, time) for penalty parameter 100
│   └── rho_10000/              # CSV logs (iterations, errors, time) for penalty parameter 10000
|   ├── errors_with_no_corner_split.csv  # CSV logs (only corners are split)
|   ├── errors_with_no_split.csv         # CSV logs (no split is peroformed)
└── plots/                      # Output directory for high-resolution matplotlib figures
    ├── baseline_divergence_convergence.png
    ├── baseline_pressure_convergence.png
    ├── spatial_error_convergence.png
    ├── split_type_comparison.png
    ├── rho_impact_time.png
    └── topology_paradox.png
```

##  Software Dependencies

To run the simulations and analysis locally, you will need the following dependencies installed in your environment:

  * **Legacy FEniCS (`dolfin`)**: version 2019.1.0 or later (used for FEM assembly and solving)
  * **Python**: 3.7+
  * **NumPy**: For matrix and array manipulations
  * **Pandas**: For structured data logging and CSV handling
  * **Matplotlib**: For generating the academic plots in the analysis notebook
  * **Jupyter Notebook**: To run the `.ipynb` files interactively

*Note: It is highly recommended to run FEniCS via its official Docker container or via an Anaconda environment (`conda-forge`).*

-----

##  Usage Guide

### 1\. Running the FEM Simulations

Each splitting strategy is isolated in its own Jupyter Notebook. To run a specific simulation:

1.  Open the desired notebook (e.g., `1_cell_split.ipynb`).
2.  Adjust the desired mesh resolution (`N`) or penalty parameter (`rho`) if needed within the solver configuration block.
3.  Run all cells. The script will automatically assemble the matrices, apply the specified mesh modification, solve the Stokes system using the IPM, and export the resulting errors and performance metrics to the `data/` directory.

### 2\. Generating the Plots and Analysis

Once the data has been generated (or using the pre-existing data provided in this repository):

1.  Open the `Data_Analysis.ipynb` notebook.
2.  Run all cells. This notebook reads the CSV files from the `data/` folder, processes the iteration-by-iteration and global convergence data, and utilizes Matplotlib to generate log-log error plots, grouped bar charts, and computational time comparisons.
3.  The generated figures will automatically be saved to the `plots/` directory.

-----

##  Summary of Results

  * **Baseline Failure:** Standard rectangular/unstructured meshes stagnate at an $L^2$ divergence of $\approx 10^{-4}$ due to interior singular vertices locking the discrete pressure space.
  * **Optimal Convergence:** All partial and full splitting strategies (1 through 4 cells) restored optimal $\mathcal{O}(h^k)$ spatial convergence rates for velocity and pressure.
  * **Performance Scaling:** Scaling the IPM penalty parameter ($\rho$) from $100$ to $10,000$ reduced the required Uzawa iterations from 10 down to 4, effectively halving the total CPU compute time on fine grids ($N=64$).

-----

##  License & Academic Citation

This code is open-source and released under the [MIT License](https://www.google.com/search?q=LICENSE).

If you utilize this codebase, selective splitting algorithms, or the data analysis framework in your own research, please consider referencing this repository.
