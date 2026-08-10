# Cepheid, Galactic, Hubble Analysis

`Cepheid, galactic, Hubble Analysis_ckw.ipynb` — a Jupyter notebook analysing a galaxy cluster using Cepheid variable stars, culminating in an estimate of the Hubble Constant and the cluster's dark matter fraction.

## Overview

The notebook works through five sections:

1. **Redshift of Galaxies** — Computes the redshift and radial velocity of each galaxy in the cluster from its observed Hα wavelength, and visualises the velocities across the sky (RA/Dec).
2. **Calibrating Cepheids** — Uses parallax and apparent magnitude data for Milky Way Cepheids (Gaia) to compute their absolute magnitudes, then fits the Period-Luminosity (Leavitt) Law `M = α·log₁₀(P) + β`.
3. **Distances to Cluster Galaxies** — Applies the calibrated Leavitt Law to Cepheids observed in the cluster galaxies to estimate each galaxy's distance.
4. **Hubble's Constant** — Combines radial velocity and distance estimates, fits Gaussians to each, and derives the Hubble Constant `H0` with an error estimate.
5. **Dark Matter** — Uses the Virial Theorem and a Mass-Luminosity relation to estimate the cluster's total and visible mass, and the resulting dark matter fraction.

## Data

Reads from the `data/` folder:

| File | Contents |
|---|---|
| `galaxy.csv` | Galaxy name, observed Hα wavelength, apparent magnitude, RA/Dec |
| `gaia.csv` | Milky Way Cepheids: parallax, mean apparent magnitude, pulsation period |
| `cepheid.csv` | Cepheids in cluster galaxies: host galaxy, apparent magnitude, pulsation period |

## Requirements

- Python 3
- `numpy`, `pandas`, `matplotlib`, `scipy`

The first cell installs these via pip if not already present.

## Usage

Open the notebook in Jupyter and run all cells in order (`Restart & Run All`), since later sections depend on variables (e.g. `alpha`, `beta`, `galaxy_data`, `cepheid_data`) computed in earlier ones.

## Outputs

Each section includes scatter/histogram plots and printed intermediate results, ending with a final estimate of `H0` (km/s/Mpc) and the dark matter fraction of the cluster, along with short written answers to the assignment's discussion questions.
