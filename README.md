# GW2_CBC — PHYS 575 Gravitational-Wave Final Project

**Team:** Daniel Fredin, Glen "Bubba" Chung, and Tyler Grabarczyk  
**Course:** University of Washington, PHYS 575 (Autumn 2026)  
**Status:** Dataset exploration available; final modeling work is being developed and is not yet published.

## Project overview

We are exploring a labeled gravitational-wave dataset containing two detector channels and four types of one-second observations:

- **Background** (`background`)
- **Binary black hole / BBH** (`binaryblackhole`)
- **Core-collapse supernova / CCSN** (`ccsn`)
- **Instrumental glitch** (`glitch`)

**Proposed experimental direction:** Compare two time-frequency representations—**short-time Fourier transform (STFT)** and **Q-transform**—as inputs to an otherwise comparable two-channel, four-class convolutional neural network (CNN). The question is whether the representation changes the model's ability to distinguish the four labeled categories. This is a proposal for the group to discuss, not a claim that modeling has already been completed or that a particular method is better.

A separate question the group has raised is whether a model trained only on background can identify other classes as anomalies. That is a valid alternative or possible extension; the present exploration notebook does not train or evaluate an anomaly detector.

## What is in the repository now?

```text
GW2_CBC/
├── 01_Dataset_Exploration.ipynb   # Exploratory analysis; no model training
├── requirements.txt              # Dependencies for notebook 01
├── README.md
├── .gitignore
└── data/                         # Place course-provided GW2_Andy.h5 here
    └── .gitkeep                   # If present; keeps the empty directory in Git
```

**Notebook `02` is not yet published.** Results, scripts, checkpoints, generated figures, and the raw dataset are intentionally excluded from the current GitHub release. The content of the repository will evolve as the team works on the final project.

## Dataset and access

Get `GW2_Andy.h5` **from the authorized course distribution**, then place it at:

```text
GW2_CBC/data/GW2_Andy.h5
```

The file is about 256 MiB and is **not hosted on GitHub**. It contains four HDF5 datasets, each shaped `(2048, 2, 4096)` with `float32` entries: 2,048 observations per class, two channels, and 4,096 samples per channel. The course specifies a sampling rate of 4,096 Hz, corresponding to one second per observation.

The HDF5 file itself does not document absolute timestamps, detector identities, injection parameters, or preprocessing/whitening history. The notebook therefore labels channels **Detector 0** and **Detector 1**, rather than assuming verified H1/L1 identities or synchronization.

Alternatively, tell the notebook where to find the file by setting the `GW2_DATA_PATH` environment variable to its absolute path **before running the setup cell**. Example (macOS/Linux/WSL):

```bash
export GW2_DATA_PATH="/absolute/path/to/GW2_Andy.h5"
```

On Windows PowerShell:

```powershell
$env:GW2_DATA_PATH = "C:\path\to\GW2_Andy.h5"
```

## Quick start — works with VS Code, JupyterLab, or another notebook editor

These instructions do **not** require WSL. You only need Python, the course dataset, and an editor that can run Jupyter notebooks. Python 3.11 is a reasonable starting choice; use a version compatible with the pinned/ranged packages in `requirements.txt`.

**Option A: Conda / Miniconda** (Windows, macOS, Linux)

From a terminal in the repository root:

```bash
conda create -n gw2-cbc python=3.11 -y
conda activate gw2-cbc
python -m pip install -r requirements.txt
python -m pip install ipykernel
python -m ipykernel install --user --name gw2-cbc --display-name "Python (gw2-cbc)"
```

Open `01_Dataset_Exploration.ipynb` in VS Code or JupyterLab, select **Python (gw2-cbc)**, and run the cells from top to bottom.

**Option B: Python virtual environment** (no Conda)

```bash
python -m venv .venv
```

Activate it using the command for your system:

```bash
# macOS / Linux / WSL
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Then:

```bash
python -m pip install -r requirements.txt
python -m pip install ipykernel
python -m ipykernel install --user --name gw2-cbc-venv --display-name "Python (gw2-cbc-venv)"
```

Open the notebook and choose that kernel/interpreter. `ipykernel` must be installed in the chosen environment to register that kernel.

### Google Colab

Colab is also an option; **WSL and VS Code are not required**.

1. Upload `01_Dataset_Exploration.ipynb` to Colab and make `requirements.txt` available in its working directory (normally `/content`). A private GitHub repository requires appropriate access; downloading the notebook and uploading it to Colab is the simplest approach.
2. In an initial Colab code cell, install dependencies with `!python -m pip install -r requirements.txt` (or install the equivalent packages). Colab may request a runtime restart if a package upgrades an in-use library.
3. Upload the course-provided `GW2_Andy.h5` to `/content/data/GW2_Andy.h5`, **or** mount Google Drive and set `GW2_DATA_PATH` to its location before running the notebook setup cell. For example:

   ```python
   import os
   os.environ["GW2_DATA_PATH"] = "/content/drive/MyDrive/path/to/GW2_Andy.h5"
   ```

4. Run the notebook cells in order. Note that Colab runtime storage is temporary, so uploading the dataset may need to be repeated in a future session.

## What notebook 01 actually does

- Inspects HDF5 keys, data shapes, data types, and class balance.
- Checks nonfinite values, amplitude distributions, near-zero clips, and exact duplicate records.
- Visualizes example waveforms for both channels in each class.
- Examines **descriptive** inter-channel RMS-envelope correlations; these do not establish physical detector coincidence.
- Estimates median Welch power spectral densities from a reproducible sample of clips.
- Plots example STFT spectrograms and optional GWpy Q-transform previews.
- Defines a reproducible, stratified 70% / 15% / 15% train/validation/test split and a majority-class dummy baseline.
- Outlines a **proposed** comparison using matched model architectures, data splits, and evaluation metrics.

The exported exploration ran successfully on the supplied file: **8,192 total clips** (2,048 per class), no detected nonfinite values or exact duplicate records, and successful previews of all eight Q-transform examples. These are descriptive findings, **not model accuracy results**. A 25% dummy baseline is expected for balanced four-class data. Any final classifier needs independent validation and a held-out test evaluation.

## Reproducibility notes and limitations

- Set the dataset path before running the first code cell; notebook cells should be run from top to bottom.
- The original HDF5 file is read only; exploratory code does not overwrite it.
- Generated figures are shown in notebook output. To save PNG copies locally, set `SAVE_FIGURES = True`; the `figures/` directory is ignored by Git.
- No GPU is necessary to run notebook 01. The optional Q-transform plot requires **GWpy** in the active kernel.
- Visual choices in the exploration notebook are **not** frozen model hyperparameters. STFT and Q-transform frequency coverage, resolution, normalization, and CNN preprocessing must be controlled and documented in the final experiment.
- Because there are no event/template IDs or processing records, random clip-level splitting may not exclude shared-source leakage; it is not evidence of generalization to independent observing runs.

## Collaboration

The project is currently maintained in a **private GitHub repository**. Team members should provide their **GitHub usernames** to receive collaborator invitations. Once invited, they can clone or download the repository, run the notebook with their own authorized course data copy, and propose changes through commits or pull requests.

For now, this repository intentionally presents the **data exploration only**. We will update it as the group agrees on the final experimental scope and the analysis is ready to share.
