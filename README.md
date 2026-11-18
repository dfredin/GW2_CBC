# GW2_CBC — PHYS 575 Final Project

**Team:** Daniel Fredin and Jay Hsu  
**Research question:** How does STFT versus Q-transform input representation affect 2D CNN classification of four gravitational-wave transient categories?

## Repository structure

```text
GW2_CBC/
├── 01_Dataset_Exploration.ipynb    # Dataset understanding + initial final-project plan
├── requirements.txt               # Reproducible Python dependencies for this notebook
├── README.md
├── .gitignore
└── data/                          # Put GW2_Andy.h5 here (not included in Git)
    └── .gitkeep
```

## Dataset

Obtain **GW2_Andy.h5** from the course-provided distribution. It is about **256 MiB** (268 MB) and is deliberately excluded from Git history. Save the file at `GW2_CBC/data/GW2_Andy.h5` without renaming it. The four HDF5 datasets are `background`, `binaryblackhole`, `ccsn`, and `glitch`, each with shape `(2048, 2, 4096)` and dtype `float32`. The file does not provide detector IDs, absolute timestamps, or whitening metadata.

If your local file is elsewhere, specify an absolute environment path before starting VS Code or the kernel:

```bash
export GW2_DATA_PATH=/your/location/GW2_Andy.h5
```

## Setup: existing WSL / Conda `phys575` environment

Open an Ubuntu/WSL terminal and run (adjust the path):

```bash
conda activate phys575
cd ~/path/to/GW2_CBC
python -m pip install -r requirements.txt
python -m ipykernel install --user --name phys575 --display-name 'Python (phys575)'
code .
```

In VS Code, install the **Python** and **Jupyter** extensions (if not already installed), open `01_Dataset_Exploration.ipynb`, and choose **Python (phys575)** as the notebook kernel. Select **Run All**.

## Alternative: fresh environment (Jay / TA / instructor)

Python **3.10–3.13** is supported by the specified dependency rules. Python 3.11 is a straightforward starting point. With Conda:

```bash
conda create -n gw2-cbc python=3.11 -y
conda activate gw2-cbc
python -m pip install -r requirements.txt
python -m ipykernel install --user --name gw2-cbc --display-name 'Python (gw2-cbc)'
```

Or use a standard Python virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate         # Linux / macOS / WSL
python -m pip install -r requirements.txt
```

On Windows PowerShell, activation is `.venv\Scripts\Activate.ps1`. Select the matching Python interpreter/kernel in VS Code.

**Troubleshooting:** If the notebook reports `ModuleNotFoundError`, check that `python -m pip` and the chosen VS Code notebook kernel use the **same interpreter**. If the HDF5 file is not found, check the `data/` folder or set `GW2_DATA_PATH` before starting the notebook kernel. GWpy is only used in the optional Q-transform preview.

## What the exploration notebook does

1. Inspects the class shapes, types and counts directly from the file.
2. Checks missing/nonfinite values, amplitude outliers, and exact duplicates.
3. Plots both detector channels for the same example of each class.
4. Compares RMS-envelope correlations and median frequency spectra.
5. Plots STFT spectrograms for all classes and channels.
6. Optionally previews GWpy Q-transforms of four one-second examples with `whiten=False`.
7. Builds reproducible, stratified train/validation/test indices and a majority-class baseline.
8. Documents the planned STFT-versus-Q-transform CNN comparison.

No model training, GPU, external downloads, internet access, or modification of the original HDF5 file is required. Output figures are embedded when the notebook is run. Set `SAVE_FIGURES=True` to save figures to an ignored `figures/` directory.

## GitHub notes

This starter project contains **no copies of the raw dataset**. A regular GitHub repository does not allow files larger than 100 MiB, so do **not** commit `GW2_Andy.h5` directly. The `.gitignore` prevents this in normal usage. Share the course dataset through your existing authorized course channel or other approved location; everyone can then reproduce the notebook by placing their copy in `data/`. Check whether the course permits redistributing the source data publicly.

From the project root, after reviewing files and creating a remote GitHub repository:

```bash
git init
git add 01_Dataset_Exploration.ipynb requirements.txt README.md .gitignore data/.gitkeep
git status
git commit -m "Add PHYS575 dataset exploration notebook"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/GW2_CBC.git
git push -u origin main
```

Alternatively create the remote repository through VS Code's Source Control UI. Keep the remote **private** until you verify that coursework and any included materials may be shared publicly. Add Jay as a collaborator if appropriate.

## Final-project notebook (future)

The final notebook will train two 2D CNNs on identical splits: one uses STFT spectrograms and the other Q-transform spectrograms. PyTorch will be installed in a GPU-appropriate way later; this avoids altering an existing CUDA setup just to run the exploration notebook.
