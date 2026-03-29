# Stochastic collapse and first-passage dynamics in marathon pacing: a Kramers–Moyal approach

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/)
[![PRE](https://img.shields.io/badge/target-Physical%20Review%20E-red.svg)]()

This repository contains the full analysis pipeline for the paper:

> **Stochastic collapse and first-passage dynamics in marathon pacing: a Kramers–Moyal approach**  
> A. Domínguez-Monterroza  
> *Physical Review E* (under review), 2026  

---

## Overview

We construct a coarse-grained stochastic description of marathon pacing dynamics and characterize the onset of pacing collapse — the "wall" — as a first-passage event in a distance-dependent system. Using split-time records from **531,100 runners** across four World Marathon Majors (Boston, Chicago, New York, Berlin; 2021–2024), we estimate Kramers–Moyal drift and diffusion coefficients directly from the data and identify a **distance-driven bifurcation at x\* ≈ 10.25 km** beyond which steady pacing ceases to be a stable fixed point.

Key findings:
- Collapse proceeds via **attractor annihilation** rather than classical Kramers barrier crossing
- First-passage distribution concentrated in the **25–35 km** region with a non-monotone hazard structure
- Langevin-equation simulations reproduce mean pacing trajectory (R² = 0.88 in-sample; R² = 0.64 out-of-sample on Berlin)
- Systematic Wasserstein offset W₁ ≈ 2 km attributed to short-range compensatory dynamics

---

## Repository structure

```
marathon-km-collapse/
│
├── README.md
├── LICENSE
├── requirements.txt
│
├── notebooks/
│   ├── 01_data_preparation.ipynb       # Cleaning, segmentation, state variable
│   └── 02_stochastic_analysis_PRE.ipynb  # Full KM pipeline (this repo)
│
├── figures/                            # Publication-quality EPS figures
│   ├── fig1_mean_pacing.eps
│   ├── fig2_drift_diffusion_PRE_CI.eps
│   ├── fig3_first_passage_hazard_PRE.eps
│   ├── fig4_potential_bifurcation_PRE.eps
│   ├── fig5_simulation_validation_PRE.eps
│   ├── fig6_app_threshold_robustness.eps
│   ├── figA_pawula.eps
│   ├── figB_markov.eps
│   └── figC_out_of_sample.eps
│
└── results/                            # Intermediate outputs from Notebook 1
    ├── segment_paces_data.csv          # Segment-wise pace values per runner
    └── wall_labels_data.csv            # Collapse labels and onset distances
```

---

## Data

### Source

Race results were obtained from the official results pages of the four World Marathon Majors:

| Marathon | Editions | Runners | Source |
|----------|----------|---------|--------|
| Boston Marathon | 2021–2024 | 90,762 | [BAA Official Results](https://www.baa.org/races/boston-marathon/results) |
| Chicago Marathon | 2021–2024 | 166,871 | [Chicago Marathon Results](https://www.chicagomarathon.com/runners/results/) |
| TCS New York City Marathon | 2021–2024 | 139,180 | [NYRR Results](https://results.nyrr.org/) |
| Berlin Marathon | 2021–2024 | 134,287 | [Berlin Marathon Results](https://www.bmw-berlin-marathon.com/en/facts-and-figures/results-archive/) |

**Total:** N = 531,100 runners after cleaning.

### Data cleaning

Raw records were cleaned by removing:
- Runners with missing splits at any checkpoint
- Segment paces outside the physiologically plausible range [2, 20] min/km
- Finish times outside [120, 480] min

### Checkpoints

Ten standard split checkpoints per runner: 5, 10, 15, 20, 21.1 (half-marathon), 25, 30, 35, 40, and 42.195 km.

### Data availability

The raw race results are publicly available at the URLs listed above. Due to the terms of use of the individual marathon organizations, the cleaned dataset is not redistributed in this repository. The data preparation notebook (`01_data_preparation.ipynb`) documents the full cleaning and feature engineering pipeline to reproduce the analysis from the original sources.

---

## Reproducibility

### Requirements

```bash
pip install -r requirements.txt
```

Core dependencies:
```
numpy>=1.24
pandas>=2.0
scipy>=1.11
scikit-learn>=1.3
matplotlib>=3.7
```

### Running the analysis

1. Download official results from the URLs listed above for each marathon edition (2021–2024)
2. Run `notebooks/01_data_preparation.ipynb` to produce `segment_paces_data.csv` and `wall_labels_data.csv`
3. Run `notebooks/02_stochastic_analysis_PRE.ipynb` to reproduce all figures and numerical results

The main analysis notebook (`02_stochastic_analysis_PRE.ipynb`) is self-contained and includes:

| Section | Content |
|---------|---------|
| §1–3 | Imports, configuration, data loading |
| §4–5 | State variable definition and coarse-graining |
| §6 | Transition dataframe construction |
| §7 | Kramers–Moyal estimation with bootstrap CI (B = 500) |
| §8 | Pawula test (D³, D⁴ coefficients) |
| §9 | Markov diagnostics (memory test + autocorrelation) |
| §10 | First-passage formalism |
| §11 | Survival and hazard functions |
| §12 | Effective potential and bifurcation analysis |
| §13 | Langevin simulation (Euler–Maruyama, 30,000 paths) |
| §14 | Out-of-sample validation (Berlin Marathon) |
| §15 | KS and Wasserstein tests on first-passage distributions |
| §16 | Robustness checks (threshold and state variable sensitivity) |
| §17 | Publication-quality PRE figures |

### Google Colab

The notebook is compatible with Google Colab. Mount your Drive and adjust the paths in §2 before running:

```python
RESULTS_DIR = Path('/content/drive/MyDrive/marathon_results')
FIGURES_DIR = Path('/content/figures_PRE')
```

---

## Key parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `Y_THRESHOLD` | 0.10 | Primary collapse threshold yc |
| `LATE_START_IDX` | 5 | Onset index for collapse detection (≥ 25 km) |
| `TRAIN_CITIES` | Boston, Chicago, New York | Training set for KM estimation |
| `TEST_CITIES` | Berlin | Held-out set for OOS validation |
| `B` | 500 | Bootstrap resamples per state bin |
| `n_bins` | 30 | State bins for KM estimation |
| `min_occupancy` | 250 | Minimum observations per bin |
| `n_paths` | 30,000 | Langevin simulation trajectories |

---

## Results summary

| Metric | Value |
|--------|-------|
| Bifurcation distance x* | 10.25 km |
| Persistent-wall rate | 0.608 (N = 241,427 runners) |
| In-sample R² (mean trajectory) | 0.881 |
| OOS R² — Berlin | 0.641 |
| Wasserstein distance W₁ (in-sample) | 1.99 km |
| Wasserstein distance W₁ (OOS) | 1.72 km |
| Lag-1 autocorrelation r₁ | −0.195 |
| Max D⁴/(D²)² | 0.44 |
| Median excess kurtosis | 16.4 |

---

## Citation

If you use this code or data pipeline in your research, please cite:

```bibtex
@article{dominguez2025marathon,
  author  = {Dom\'inguez-Monterroza, Andy},
  title   = {Stochastic collapse and first-passage dynamics
             in marathon pacing: a {Kramers--Moyal} approach},
  journal = {Phys.\ Rev.\ E},
  year    = {2025},
  note    = {under review}
}
```

### Related work

```bibtex
@article{dominguez2026universal,
  author  = {Dom\'inguez-Monterroza, Andy},
  title   = {Universal collapse and sex-dependent scaling laws
             in human performance and aging},
  journal = {Chaos Solitons Fractals},
  volume  = {208},
  pages   = {118177},
  year    = {2026},
  doi     = {10.1016/j.chaos.2026.118177}
}
```

---

## License

This code is released under the [MIT License](LICENSE). The race result data are subject to the terms of use of the respective marathon organizations.

---

## Contact

Andy Domínguez-Monterroza  
Department of Mathematics, Pontificia Universidad Javeriana, Bogotá D.C., Colombia  
andy.dominguez@javeriana.edu.co
