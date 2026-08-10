# Observational Cosmology

A collection of Jupyter notebooks and supporting data that demonstrate observational methods used to estimate cosmological parameters (Hubble constant, matter density, dark energy equation of state) using Cepheid variable stars and Type Ia supernovae (Pantheon compilation). This repository contains analysis notebooks, data-processing helpers, and cached results so readers can reproduce the figures and parameter estimates.

## What this repo contains

- Cepheid_Variables/
  - `Cepheid, galactic, Hubble Analysis_ckw.ipynb` — analysis using Milky Way Cepheids to calibrate the Leavitt (Period–Luminosity) law and measure distances to cluster galaxies; combines distances with radial velocities to estimate H0 and the cluster dark-matter fraction.
  - `README.md` — detailed overview of the Cepheid notebook and its input data files.
  - `.gitkeep` — placeholder so the directory is tracked when empty.

- Supernovae/
  - `cosmic_distnaces.ipynb` — Flat ΛCDM analysis using the Pantheon SNe Ia distance-modulus compilation to estimate H0 and Ωm.
  - `mcmc_3param_analysis.ipynb` — Flat ΛCDM + radiation (Ωr) model, with three samplers (Metropolis-Hastings, emcee, Adaptive Metropolis) compared.
  - `wcdm_mcmc_analysis.ipynb` — wCDM model (free w) with the same sampler comparisons and diagnostics.
  - `README.md` — detailed description of the notebooks, samplers, caching, and required data files.
  - `.gitkeep` — placeholder file.

- Pantheon data and other files (expected at repo root)
  - `Pantheon_SNdata.csv` — preprocessed Pantheon SNe Ia CSV used by the Supernovae notebooks (auto-generated on first run if missing).
  - `Pantheon - Scolnic SNe 1A 1048 z-asc 27May'19 (2).xlsx` — raw source spreadsheet referenced by the Supernovae notebooks (if present).

## Quick summary

- Primary language: Jupyter Notebook (Python 3) with standard scientific stack (numpy, scipy, pandas, matplotlib).
- Notebooks are runnable in Jupyter Lab / Notebook and include cells that cache results to speed repeated runs.
- Supernovae notebooks compare three MCMC/parameter-estimation methods and include diagnostic plots and cached backends.

## Requirements

These notebooks were written for Python 3 and require the scientific Python stack. Install with pip in a fresh virtual environment or conda environment.

Recommended packages (the notebooks also attempt to pip-install missing dependencies in the first cell where practical):

- numpy
- scipy
- pandas
- matplotlib
- emcee
- h5py
- corner
- openpyxl (only needed if you want to re-generate Pantheon_SNdata.csv from the raw .xlsx)

Install via pip:

```bash
python -m venv .venv
source .venv/bin/activate     # macOS / Linux
.venv\\Scripts\\activate      # Windows (PowerShell: .\\.venv\\Scripts\\Activate.ps1)
python -m pip install --upgrade pip
python -m pip install numpy scipy pandas matplotlib emcee h5py corner openpyxl
```

Or using conda:

```bash
conda create -n obs_cosmo python=3.10
conda activate obs_cosmo
conda install numpy scipy pandas matplotlib h5py openpyxl -c conda-forge
python -m pip install emcee corner
```

If you prefer reproducible builds, create a requirements.txt with the above packages and install `pip install -r requirements.txt`.

## How to run the notebooks (short path)

1. Clone the repository:

```bash
git clone https://github.com/chinwas/Observational_Cosmology.git
cd Observational_Cosmology
```

2. Create and activate a Python environment and install dependencies (see Requirements above).
3. Launch Jupyter Lab or Notebook:

```bash
jupyter lab
# or
jupyter notebook
```

4. Open and run the notebooks in order. Suggested order:

- For Cepheid-based results: `Cepheid_Variables/Cepheid, galactic, Hubble Analysis_ckw.ipynb` (run top-to-bottom).
- For Supernovae results: run the Supernovae notebooks (any order is valid, but each is self-contained): `Supernovae/cosmic_distnaces.ipynb`, `Supernovae/mcmc_3param_analysis.ipynb`, `Supernovae/wcdm_mcmc_analysis.ipynb`.

Important: use `Kernel -> Restart & Run All` to ensure a clean run and that notebooks pick up variables computed in earlier cells.

## Notes on caching and long runs (Supernovae)

- The Supernovae notebooks use cache folders next to each notebook (`mcmc_cache_*`) to store sampler outputs. This avoids re-sampling the MCMC every time. Each cache has a `_meta.json` sidecar to ensure cache validity; if you change sampler settings the notebook will re-run the affected sampler.
- emcee uses `HDFBackend` and stores `.h5` files; ensure `h5py` is installed before importing `emcee` in a kernel that will use its backend.
- If you see stale results in a live Jupyter session after editing notebook files externally, reload the notebook before re-running (Jupyter keeps an in-memory copy).

## Data files and where to get them

- Pantheon_SNdata.csv: the notebooks expect this CSV in the repository root (one level above the Supernovae folder). If missing, `cosmic_distnaces.ipynb` will attempt to build it from the raw Excel spreadsheet if that file is present. If you do not have the raw Excel, obtain the Pantheon compilation from the Scolnic dataset (or place the prepared CSV at the repo root).

- Cepheid notebooks read data from `Cepheid_Variables/data/` (entries described in that folder's README): `galaxy.csv`, `gaia.csv`, `cepheid.csv`. If those files are missing, please add them according to the column descriptions in `Cepheid_Variables/README.md`.

## Reproducing results non-interactively

To run a notebook non-interactively and save outputs (static HTML or executed notebook), use nbconvert:

```bash
# Execute and write output notebook
jupyter nbconvert --to notebook --execute "Supernovae/cosmic_distnaces.ipynb" --output "Supernovae/cosmic_distnaces.ran.ipynb"

# Export executed notebook to HTML
jupyter nbconvert --to html "Supernovae/cosmic_distnaces.ipynb"
```

Be mindful that MCMC samplers can be time-consuming; use cached outputs where appropriate or reduce iteration counts for quick testing.

## What changed / enhancements in this README

This README consolidates information from the per-folder READMEs and clarifies:
- Which files are required and where they should live
- How to set up an environment and run the notebooks
- Caching behaviour and pitfalls (h5py + emcee, Jupyter in-memory state)

## Suggestions for future repository improvements (optional)

- Add a top-level `requirements.txt` or `environment.yml` for reproducible installs.
- Add smaller example datasets (or scripts to download them) so newcomers can run the notebooks quickly.
- Add a `Makefile` or `justfile` with commands to run tests, build caches, or export figures.
- Add a LICENSE file to clarify reuse terms.

## Contact / Attribution

If you have questions about the analyses or reproduce the results, open an issue or contact the repository owner.
