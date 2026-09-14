# General Protocol for Automated Sequential Batch-System Molecular Docking Using AutoDock-GPU

This repository provides a standardized, high-throughput, and high-precision computational workflow designed to evaluate multi-ligand and macromolecule interactions, binding poses, and thermodynamic affinities using OpenCL/CUDA GPU acceleration. The pipeline leverages the Lamarckian Genetic Algorithm (LGA) using **AutoDock-GPU** to streamline sequential batch-system molecular docking from binary setup to publication-grade structural restoration.

---

## Workflow Overview and Procedures

The underlying workflow executes a rigorous series of processing steps across structured procedures:

* **Binary Environment Setup and Verification Process (Procedures 1–4):** Downloads, simplifies, grants execution permissions, and verifies the official pre-compiled **AutoDock-GPU v1.6** binary optimized for high-performance GPU execution.

* **Workspace and Directory Initialization (Procedure 5):** Establishes dedicated input directory (**`MolecularDockingInput`**), working directory (**`Workstation`**), and output directory (**`MolecularDockingOutput`**) to isolate calculation workflows and output files.

* **Ligand Analysis Folder Preparation and File Relocation (Procedure 6):** Dynamically initializes dedicated ligand analysis folder (**`LigandName_Analysis`**) for each prepared ligand, relocates ligand structures (**.pdbqt**) and grid parameter files (**.gpf**), and replicates the macromolecule target (**.pdbqt**) across all ligand analysis folders before removing it from the input directory.

* **AutoGrid4 Program Installation and Batch Grid Map Calculation (Procedure 7):** Installs **AutoGrid4** into the system and executes automated grid map files generation (**.fld** and **.map**) for each ligand grid space within its respective ligand analysis folders.

* **Macromolecule File Cleanup and Directory Relocation (Procedure 8):** Deletes duplicate macromolecule structure from all ligand analysis folders to prevent molecular docking interference and then relocates all prepared ligand analysis folder into **`Workstation`** directory for simulation readiness.

* **Execute Sequential Production Molecular Docking Simulation and Working Directory Purging Process (Procedure 9):** Sets up **Lamarckian Genetic Algorithm** (**LGA**) search parameters using an interactive **ipywidgets** interface (**Cell 1**) and executes GPU-accelerated docking sequentially per ligand (**Cell 2**), saving simulation log files (**.dlg** and **.xml**) and top-ranked poses (**LigandName-best.pdbqt**) to **`MolecularDockingOutput`** directory while automatically purging processed ligand analysis folder to optimize runtime storage.

* **Post-Docking Thermodynamic Analysis and Metrics Extraction (Procedure 10):** Parses simulation log file (**.dlg**) across the output directory, extracts top-ranked centroid poses (**Rank 1, Sub-Rank 1**), binding free energy ($\Delta G$), Reference RMSD ($\text{Å}$), and calculates estimated inhibition constants ($K_i$) with dynamic scientific unit scaling (**femtomolar (fM)** to **molar (M)**).

* **Reconstruct Covalent Topology and Convert Ligand PDBQT File to PDB File Format (Procedure 11)**: Using **Open Babel** (**obabel**) to reconstruct explicit covalent bond topology (**CONECT records**) from raw lowest-energy poses (**LigandName-best.pdbqt**), yielding publication-grade ligand PDB files(**LigandName_BestPose.pdb**), while automatically applying a standardized **"_Result"** suffix to raw **LigandName.dlg** files for structured archiving.

* **Archiving and Exporting Batch Molecular Docking Results (Procedure 12):** Compresses primary publication-ready output files (**.pdb** and **.dlg**) into a lightweight archive (**MolecularDockingOutput.zip**) for local downloading.

* **Output Directory and ZIP Archive Purging Process (Procedure 13):** Performs optional environment maintenance by purging temporary output files and ZIP archives from the virtual machine runtime after local download.

---

## Detailed Cell Breakdown

The following is a detailed breakdown of the title and function of each code cell within every procedure in the notebook:

* **Procedure 1: Download AutoDock-GPU Executable Binary**

  * **Cell 1 Procedure 1:** Downloads the official pre-compiled Linux x64 **AutoDock-GPU** (**v1.6**) binary directly from the Center for Computational Structural Biology (CCSB) Scripps Research GitHub repository into the execution environment.
 
* **Procedure 2: Simplify Executable Binary Filename**

  * **Cell 1 Procedure 2:** Renames the long downloaded binary file (**adgpu-v1.6_linux_x64_ocl_128wi**) into a simplified execution alias (**adgpu**).

* **Procedure 3: Grant Binary Execution Permissions**

  * **Cell 1 Procedure 3:** Grants execution permissions (**755**) to the **adgpu** binary to ensure the operating system permits it to run as an active process.

* **Procedure 4: Verify Binary Installation and Display Usage Reference**

  * **Cell 1 Procedure 4:** Executes the **adgpu** binary without arguments to verify its integrity, display software version information, and load the command-line reference menu.

* **Procedure 5: Workspace and Directory Initialization**

  * **Cell 1 Procedure 5:** Automatically creates three dedicated directories: **`MolecularDockingInput`**, **`Workstation`**, and **`MolecularDockingOutput`**.

* **Procedure 6: Ligand Analysis Folder Preparation and File Relocation**

  * **Cell 1 Procedure 6:** Scans **`MolecularDockingInput`** for small-molecule ligand files (**.pdbqt**) and automatically generates a dedicated working ligand analysis folder (**`LigandName_Analysis`**) for each identified ligand.

  * **Cell 2 Procedure 6:** Moves each ligand structure (**.pdbqt**) and its matching grid parameter file (**.gpf**) into its respective **`LigandName_Analysis`** folder.

  * **Cell 3 Procedure 6:** Identifies the target macromolecule structure (**.pdbqt**), replicates it across all **`LigandName_Analysis`** folders to ensure each workspace is fully self-contained for grid generation, and cleans up the original file from the input directory.

* **Procedure 7: AutoGrid4 Program Installation and Batch Grid Map Calculation**

  * **Cell 1 Procedure 7:** Downloads and installs the **AutoGrid4** suite into the Linux runtime environment using system package management.

  * **Cell 2 Procedure 7:** Verifies proper installation by displaying the binary version information.

  * **Cell 3 Procedure 7:** Iterates through each **`LigandName_Analysis`** folder inside **`MolecularDockingInput`** directory, locates the respective grid parameter file (**.gpf**) and target macromolecule structure (**.pdbqt**), then executes **AutoGrid4** program to compute **atomic affinity grid maps** (**.map**), **desolvation and electrostatic maps** (**.d.map** and **.e.map**), and the **grid map descriptor** (**.fld**).

* **Procedure 8: Macromolecule File Cleanup and Directory Relocation**

  * **Cell 1 Procedure 8:** Deletes macromolecule PDBQT file from each **`LigandName_Analysis`** folder, as grid calculations are complete and these macromolecule PDBQT file are no longer required for AutoDock-GPU.

  * **Cell 2 Procedure 8:** Relocates all fully prepared **`LigandName_Analysis`** folders from **`MolecularDockingInput`** directory into the central **`Workstation`** directory to prepare for sequential batch molecular docking executions.

* **Procedure 9: Execute Sequential Production Molecular Docking Simulation and Working Directory Purging Process**

  * **Cell 1 Procedure 9:** Provides an interactive widget interface to configure **Lamarckian Genetic Algorithm** **(LGA)** parameters, including number of lamarckian genetic algorithm runs (**nrun**), maximum number of evaluations (**nev**), population size (**psize**), and maximum number of generations (**ngen**), and then saves them directly into **Jupyter kernel memory**.

  * **Cell 2 Procedure 9:** Processes one ligand analysis folder (**`LigandName_Analysis`**) in one cell run/execution, runs the AutoDock-GPU engine (**`adgpu`**), automatically transfers result files (**.dlg**, **.xml**, and **LigandName-best.pdbqt**) to the **`MolecularDockingOutput`** directory, and purges the processed ligand analysis folder upon completion to maintain a clean workspace.

* **Procedure 10: Post-Docking Thermodynamic Analysis and Metrics Extraction**

  * **Cell 1 Procedure 10:** Parses simulation log file (**.dlg**) across the output directory, extracts top-ranked centroid poses (**Rank 1, Sub-Rank 1**), binding free energy ($\Delta G$), Reference RMSD ($\text{Å}$), and calculates estimated inhibition constants ($K_i$) with dynamic scientific unit scaling (**femtomolar** (**fM**) to **molar** (**M**)).

* **Procedure 11: Reconstruct Covalent Topology and Convert Ligand PDBQT File to PDB File Format**

  * **Cell 1 Procedure 11:** Installs the **Open Babel** (**obabel**) suite into the Linux runtime environment using system package management.

  * **Cell 2 Procedure 11:** Parses all raw **LigandName-best.pdbqt** files in batch, recalculates 3D interatomic bond geometries, and reconstructs fully intact, standardized PDB files (**LigandName_BestPose.pdb**) with explicit covalent bond topology (**CONECT records**).

  * **Cell 3 Procedure 11:** Applies a standardized **"_Result"** suffix system to all raw simulation log files (**.dlg**) in the **`MolecularDockingOutput`** directory, renaming them from **LigandName.dlg** to **LigandName_Result.dlg** for structured archiving and post-docking identification.

* **Procedure 12: Archiving and Exporting Batch Molecular Docking Results**

  * **Cell 1 Procedure 12:** Compresses primary publication-ready output files (**.pdb** and **.dlg**) into a lightweight archive (**MolecularDockingOutput.zip**) for local downloading.

* **Procedure 13: Output Directory and ZIP Archive Purging Process**

  * **Cell 1 Procedure 13:** Empties all generated output files (**.pdb**, **.dlg**, and **.xml**) stored inside the **`MolecularDockingOutput`** directory.

  * **Cell 2 Procedure 13:** Deletes the compressed archive files (**.zip**) from the root execution environment (**`/content/`**) to free up storage space and maintain a clean workstation.

---

## Required Input Files

Before running the workflow notebook, prepare the following input files on your local workstation:

* **Prepared Macromolecule File (.pdbqt):** The prepared 3D coordinate structure of the target macromolecule containing partial atomic charges and atom types (e.g., **macromolecule.pdbqt** or **receptor.pdbqt**). The filename **must contain** the keyword **"macromolecule"** or **"receptor"** (case-insensitive) to allow the automated script to distinguish it from ligand files.

* **Prepared Ligand Files (.pdbqt):** The prepared 3D coordinate structures of single or multiple small-molecule ligands containing partial atomic charges (e.g., **Gasteiger** charges) and flexible torsional bonds in **PDBQT** format (e.g., **ligand1.pdbqt**, **ligand2.pdbqt**, etc.).

* **Grid Parameter Files (.gpf):** The grid parameter files (**.gpf**) generated from **AutoDockTools** corresponding to each ligand (e.g., **ligand1.gpf**, **ligand2.gpf**, etc.). Each grid parameter file defines the 3D grid box coordinates, dimensions, spacing ($\text{Å}$), and atom-type map specifications required for **AutoGrid4** calculation process.

---

## Version Information and Releases History

### Current Version
* **Current Release Version:** **`v1.0`** (Initial Stable Production Workflow)
* **Release Date:** September 14th, 2026
* **Core Dependency Versions:**
  * **AutoDock-GPU:** **`v1.6`** (GPU-accelerated Lamarckian Genetic Algorithm molecular docking engine)
  * **AutoDock Suite (AutoDock4, AutoGrid4, and AutoDockTools4 Program):** **`v4.2.6`** (grid map generation and atomic parameterization)
  * **Open Babel:** **`v3.1.1`** (chemical format conversion and topology reconstruction)

### Releases History
* Version 1.0 (**`v1.0`**): September 14th, 2026 ((Initial Stable Release for Google Colab Platform Only).

---

## Citations and References

If you use this automated workflow or its outputs in your published research, please cite the following foundational software packages and their corresponding primary publications:

1. **AutoDock-GPU**

   * **Primary Publication**: *Santos-Martins, D., Solis-Vasquez, L., Tillack, A. F., Sanner, M. F., Koch, A., & Forli, S. (2021). Accelerating AutoDock4 with GPUs and Gradient-Based Local Search. Journal of Chemical Theory and Computation, 17(2), 1060–1073. https://doi.org/10.1021/acs.jctc.0c01006.*

   * **Software Repository**: Forli Lab, Scripps Research. AutoDock-GPU (**Version 1.6**), https://github.com/ccsb-scripps/AutoDock-GPU.

2. **AutoDock Suite (AutoDock4, AutoGrid4, and AutoDockTools4 Program)**

   * **Primary Publication**: *Morris, G. M., Huey, R., Lindstrom, W., Sanner, M. F., Belew, R. K., Goodsell, D. S., & Olson, A. J. (2009). AutoDock4 and AutoDockTools4: Automated docking with selective receptor flexibility. Journal of Computational Chemistry, 30(16), 2785–2791. https://doi.org/10.1002/jcc.21256.*

   * **Software Package**: Forli Lab, Scripps Research. AutoDock Suite (**Version 4.2.6**), https://autodock.scripps.edu.

3. **Open Babel**

   * **Primary Publication**: *O’Boyle, N. M., Banck, M., James, C. A., Morley, C., Vandermeersch, T., & Hutchison, G. R. (2011). Open Babel: An open chemical toolbox. Journal of Cheminformatics, 3(1), 33. https://doi.org/10.1186/1758-2946-3-33.*

   * **Software Package**: Open Babel Development Team. Open Babel (**Version 3.1.1**), https://openbabel.org.

---

## Author and Contact Information

* **Author:** Farhan Hidayat (Researcher in Computational Pharmacy, Molecular Biology, and In Silico Drug Discovery from Bandung, West Java, Indonesia).
* **Email:** research.farhanhidayat@gmail.com
* **Profiles:** [LinkedIn](https://www.linkedin.com/in/hidayatfarhan/) | [GitHub](https://github.com/HHIADNA) | [ORCID](https://orcid.org/0009-0007-5304-302X)
