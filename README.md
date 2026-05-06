# Generating Function-Informed Initialization for Genetic Algorithms on the MKP

**Muhammad Marzouk Baig** — MS AI, Northeastern University Roux Institute  
baig.muham@northeastern.edu

---

## What this is

Implementation and experiments for a research paper on using combinatorial generating functions to bias GA initialization for the Multidimensional Knapsack Problem. Instead of initializing each bit at probability 0.5, per-item sampling probabilities are derived from sub-generating functions that characterize feasibility structure before any search begins. Full details in the paper: `docs/paper/main.tex` (arXiv preprint in preparation).

---

## Requirements

**Python:** 3.11 (Google Colab default)

**External packages** (everything else is stdlib):

```bash
pip install torch numpy
```

The notebook was developed and tested on Google Colab with PyTorch 2.10.0+cu128 on a Tesla T4 GPU. A GPU is strongly recommended for the precomputation phase — the code uses PyTorch and falls back to CPU automatically, but runtime at $M = 10^7$ samples is significantly slower without one.

If running locally rather than on Colab, remove the `google.colab` cells (Drive mount and file download) at the top of the notebook — they are Colab-specific and not needed for the core experiments.

---

## Running

Open `notebooks/mkp_gf_initialization.ipynb` in Google Colab or Jupyter. The notebook is self-contained. Set instance parameters at the top:

```python
N_ITEMS     = 100
N_CONSTR    = 3
ALPHA       = 0.25    # 0.25 / 0.50 / 0.75
SEED        = 42
MC_SAMPLES  = 10_000_000
N_RUNS      = 20
N_OFFSPRING = 20_000
POP_SIZE    = 50
FITNESS     = "death" # "death" / "penalty"
```

Results are saved to `results/` as JSON files.

---

## Structure

```
MKP-using-Genetic-Algorithm/
│
├── notebooks/
│   └── mkp_gf_initialization.ipynb
│
├── results/
│   ├── results_log.md
│   └── results_*.json
│
├── data/
│   └── orlib/
│
├── docs/
│   └── paper/
│       └── main.tex
│
└── requirements.txt
```
