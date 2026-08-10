# Cosmology - Hubble Tension

Cosmology Project at Astrae

Fits of Type Ia Supernova (Pantheon) distance-modulus data to cosmological distance
models, with parameter estimation done three different ways (Metropolis-Hastings,
`emcee` affine-invariant ensemble sampling, and Adaptive Metropolis) so their
efficiency and reliability can be compared directly.

## Notebooks

| Notebook | Model | Free parameters |
|---|---|---|
| [`cosmic_distnaces.ipynb`](cosmic_distnaces.ipynb) | Flat ΛCDM | $\Omega_m$, $H_0$ |
| [`mcmc_3param_analysis.ipynb`](mcmc_3param_analysis.ipynb) | Flat ΛCDM + radiation | $\Omega_m$, $H_0$, $\Omega_r$ |
| [`wcdm_mcmc_analysis.ipynb`](wcdm_mcmc_analysis.ipynb) | Flat wCDM (dark energy equation of state) | $\Omega_m$, $H_0$, $w$ |

All three follow the same internal structure and can be read/run independently:

1. **Distance functions** — Hubble parameter $H(z)$ for that model, comoving/luminosity
   distance via numerical integration (`scipy.integrate.quad`), distance modulus.
2. **Pantheon SNe Ia data** — loaded from `../Pantheon_SNdata.csv` (built once from the
   raw `../Pantheon - Scolnic SNe 1A 1048 z-asc 27May'19 (2).xlsx` if the CSV doesn't
   exist yet). Columns: `#name`, `zcmb`, `zhel`, `dz`, `mb`, `dmb`, `dist_mod`.
3. **$\chi^2$ likelihood** comparing the model's predicted distance modulus to the data.
4. **Three parameter-estimation sections**, in order:
   - **Metropolis-Hastings (MH)** — single-chain random walk with a fixed, hand-picked
     step size per parameter.
   - **emcee** — affine-invariant ensemble sampler (many walkers, self-tuning proposal
     shape).
   - **Adaptive Metropolis (AM)** — single chain like MH, but its proposal covariance is
     seeded from the MH pilot run and then periodically re-estimated from the chain's
     own history (Haario et al. 2001), so it learns the posterior's correlation
     structure instead of assuming the parameters are independent.
5. **Diagnostics per sampler** — posterior histograms with Gaussian fits, pairwise
   scatter plots colored by $\chi^2$/log-posterior, trace plots, and a corner plot.
6. **Styled corner plots** — one extra corner plot per sampler (separate, not
   overlaid) showing only the 2σ and 3σ filled contours with no 1σ region and no raw
   scatter points, matching a likelihood-contour-figure style rather than the default
   `corner` look. These are in addition to the default corner plots, not a replacement.
7. **Three-way comparison table** — $H_0$/$\Omega_m$/(third parameter) estimates ± std,
   acceptance fraction, autocorrelation time, **properly autocorrelation-corrected**
   effective sample size for all three methods (not just emcee), effective samples per
   1000 likelihood calls, and qualitative flags (manual tuning needed, parallelizable,
   built-in convergence diagnostics).

## How results are cached

Each notebook's three sampler cells check a `mcmc_cache_*/` folder (created next to the
notebook) before running:

- **MH / Adaptive Metropolis** — chain saved to a `.csv`, plus a `.pkl` for AM's
  covariance-evolution snapshots.
- **emcee** — uses its native `emcee.backends.HDFBackend` (`.h5` file), which also
  transparently restores `get_chain()`, `get_log_prob()`, `get_autocorr_time()`, and
  `acceptance_fraction` without re-sampling.

Each cache has a small `*_meta.json` sidecar recording the settings used to generate it
(iteration count, step sizes, walker count, etc.). On the next run, current settings are
compared against the sidecar — if they match, the cache loads instantly; if you changed
`mh_n_iterations`, `n_steps`, `am_n_iterations`, or similar, it's detected automatically
and the sampler re-runs (no stale results loaded silently). Each sampler cell also has a
`force_rerun*` flag if you want to force a re-sample regardless.

**If you're running a notebook live in Jupyter**: edits made to a notebook file outside
that Jupyter session (e.g. by an assistant/editor) won't appear until you reload the
notebook there — Jupyter keeps its own in-memory copy and will silently overwrite
external changes on its next save. Reload before continuing if the file was touched
externally.

## Requirements

```
numpy
scipy
pandas
matplotlib
openpyxl        # to read the raw Pantheon .xlsx on first run
emcee
h5py            # for emcee's HDF5 chain-caching backend
corner
```

`h5py` in particular must be installed **before** `emcee` is first imported in a given
kernel session — `emcee.backends.hdf` caches whether `h5py` is available at import
time, so installing it mid-session and re-importing `h5py` alone isn't enough; restart
the kernel after installing it.

## Data files (repo root, one level up from this folder)

- `Pantheon_SNdata.csv` — pre-processed Pantheon compilation (auto-generated on first
  run if missing).
- `Pantheon - Scolnic SNe 1A 1048 z-asc 27May'19 (2).xlsx` — raw source data.

## Suggested order to run a notebook

Top to bottom, once. After that, re-running is fast (cache hits) unless you
intentionally change a sampler's settings, in which case only that sampler re-runs.
