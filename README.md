# Generating Function-Informed Initialization for Genetic Algorithms on the MKP

**Muhammad Marzouk Baig** — MS AI, Northeastern University Roux Institute
baig.muham@northeastern.edu

---

## What this is

Implementation and experiments for ongoing research on using combinatorial generating functions to bias GA initialization for the Multidimensional Knapsack Problem (MKP). Instead of initializing each bit at probability 0.5, per-item sampling probabilities are derived from sub-generating functions that characterize feasibility structure before any search begins.

The current version uses importance sampling with adaptive q to scale the Monte Carlo estimation to large tight instances where uniform Bernoulli(0.5) sampling collapses. A short pilot finds a Bernoulli probability biased toward feasibility, samples are reweighted by the likelihood ratio to recover unbiased per-item estimates, and seed restarts handle the hardest cells where even adapted sampling fails on the first try.

Full mathematical derivation and experimental results in `docs/proposal/Before The Search Begins.pdf`. This is being developed as an MS thesis at Northeastern over the next few months, with a short conference paper as the secondary target.

---

## Current results

Tested on the full OR-Library Chu & Beasley benchmark: 270 instances, n ∈ {100, 250, 500}, m ∈ {5, 10, 30}, α ∈ {0.25, 0.50, 0.75}.

| Tightness | GF wins vs Hill 1999 (per 90) | Uniform feasibility |
|---|---|---|
| α = 0.25 (tight) | 86 / 90 | 0% across all instances |
| α = 0.50 (medium) | 90 / 90 | low-teens |
| α = 0.75 (loose) | method self-calibrates to uniform | 100% |

See `results/RESULTS.md` for the full breakdown and `results/OR_library_consolidated_results.csv` for the per-cell numbers.

---

## Requirements

**Python:** 3.11 (Google Colab default)

**External packages** (everything else is stdlib):

```bash
pip install torch numpy pandas
```

The notebook was developed and tested on Google Colab with PyTorch 2.10.0+cu128 on a Tesla T4 GPU. A GPU is strongly recommended for the precomputation phase, the code uses PyTorch and falls back to CPU automatically, but runtime at M = 10⁷ samples is significantly slower without one.

If running locally rather than on Colab, remove the `google.colab` cells (Drive mount and file download) at the top of the notebook, they are Colab-specific and not needed for the core experiments.

---

## Running

Open `notebooks/mkp_gf_initialization.ipynb` in Google Colab or Jupyter. The notebook is self-contained. Set instance parameters at the top:

```python
N_ITEMS       = 100
N_CONSTR      = 3
ALPHA         = 0.25       # 0.25 / 0.50 / 0.75
SEED          = 42
MC_SAMPLES    = 10_000_000
N_RUNS        = 20
N_OFFSPRING   = 20_000
POP_SIZE      = 50
FITNESS       = "death"    # "death" / "penalty"

# importance sampling controls
USE_IS        = True       # enable adaptive q + IS reweighting
PILOT_SAMPLES = 5_000      # pilot pass to find q
MAX_RESTARTS  = 5          # fall back to seed restarts on hard cells
```

For the OR-Library benchmark sweep, use `notebooks/orlib_benchmark.ipynb` instead, which iterates over all 270 instances and writes per-cell results to `results/`.

Results are saved to `results/` as JSON files.

---

## Structure

```
MKP-using-Genetic-Algorithm/
│
├── notebooks/
│   ├── mkp_gf_initialization.ipynb     # single-instance experiments
│   └── orlib_benchmark.ipynb           # full OR-Library sweep
│
├── results/
│   ├── RESULTS.md                              # human-readable summary
│   ├── OR_library_consolidated_results.csv     # 27 cells, all metrics
│   ├── paper_table.csv                         # clean table for paper
│   ├── per_instance_results.csv                # all 270 instances
│   └── results_*.json                          # raw per-experiment results
│
├── data/
│   └── orlib/                          # mknapcb1.txt through mknapcb9.txt
│
├── docs/
│   └── proposal/
│       ├── Before The Search Begins.pdf
│       
│
└── requirements.txt
```

---

## Status

Work in progress, being developed toward an MS thesis. In correspondence with Prof. Raymond Hill (AFIT, retired) for methodological feedback. Plans:

- Refactor the codebase from notebook-driven to module-driven
- Expand the literature review
- Submit a short conference paper / poster
- Complete as MS thesis at Northeastern in the paper-style format

Issues and questions welcome.
