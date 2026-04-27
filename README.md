# Generating Function-Informed Initialization for Genetic Algorithms on the Multidimensional Knapsack Problem

**Author:** Muhammad Marzouk Baig  
**Program:** MS Artificial Intelligence, Northeastern University Roux Institute  
**Advisor:** TBD  
**Status:** Active research — experiments in progress  
**GitHub:** Active daily commits  

---

## Overview

This repository contains the implementation and experimental results for my master's thesis research on using combinatorial generating functions and GPU-accelerated Monte Carlo sampling to inform genetic algorithm initialization for the Multidimensional Knapsack Problem (MKP).

The core idea: instead of initializing a GA population uniformly at random (each bit set to 1 with probability 0.5), we analytically characterize the feasibility structure of the MKP search space using generating functions, and derive per-bit sampling probabilities that bias initialization toward high-feasibility regions — before any search begins and without solving any optimization subproblem.

---

## Research Question

Can combinatorial generating functions be used to analytically characterize the feasibility structure of the multidimensional knapsack search space at the schema level, and does using this characterization to bias GA initialization improve convergence behavior relative to uniform initialization?

---

## Key Findings (15-run experiments, n=100, m=3, seed=42)

### Feasibility at Generation 0 — primary metric

| Tightness | Fitness regime | Uniform | Biased (ours) |
|-----------|---------------|---------|---------------|
| α=0.25 | Death penalty | 0.0% | 97.5% |
| α=0.50 | Death penalty | 32.3% | 72.8% |
| α=0.75 | Death penalty | 100% | 100% |
| α=0.25 | Penalty x5.0 | 0.0% | 97.5% |
| α=0.50 | Penalty x5.0 | 31.8% | 71.4% |

### Convergence speed improvements

| Tightness | Fitness regime | Improvement at 90% | Improvement at 95% |
|-----------|---------------|--------------------|--------------------|
| α=0.25 | Death penalty | uniform never converges | uniform never converges |
| α=0.50 | Death penalty | +6.1% faster | +19.4% faster |
| α=0.75 | Death penalty | +2.1% faster | +22.3% faster to 99% |
| α=0.25 | Penalty x5.0 | +27.4% faster | +3.0% faster |
| α=0.50 | Penalty x5.0 | +20.9% faster | **+46.5% faster** |

**Headline result:** At moderate tightness (α=0.50) with penalty fitness, our method reaches 95% solution quality 46.5% faster than uniform initialization.

**Key finding:** At tight instances (α=0.25) under death penalty, uniform initialization completely fails to converge — our method is the only one that functions, reaching 96.5% of reference best.

---

## Novelty Confirmation

The closest prior work — Hill (1999) — proposed using a single global Pr(X=1) derived from a greedy heuristic for all items. Professor Hill confirmed personally (personal communication, 2025):

> "I did not do anything such as you suggested."

His method achieved 20.2% feasible solutions at tight instances. Our per-item generating function approach achieves 97.5% — a 77 percentage point improvement.

Key distinctions:
- **Hill (1999):** one global probability for all n items, derived from greedy heuristic, m=2 only, no full GA experiments
- **Ours:** individual p_j per item from generating function feasibility densities, works for any m, full GA convergence experiments, no optimization subproblem required

---

## Method

**Phase 1 — Precomputation (done once before GA starts)**

For each item j, compute the sub-generating function g_j by removing item j from the full product and evaluating against two capacity thresholds:

- ρ_j^1 = fraction of completions feasible when item j is included
- ρ_j^0 = fraction of completions feasible when item j is excluded

For small m (≤3): exact polynomial expansion. For large m: GPU-accelerated Monte Carlo sampling (10M samples in 5.8s on T4).

**Phase 2 — Biased initialization**

Per-bit sampling probability: p_j = ρ_j^1 / (ρ_j^1 + ρ_j^0)

Generate initial population by sampling each bit independently with probability p_j instead of uniform 0.5. Requires no optimization subproblem — purely arithmetic on weights and capacities.

---


## Repository Structure

```
MKP-using-Genetic-Algorithm/
├── notebooks/
│   └── mkp_gf_initialization.ipynb   main research notebook
├── results/
│   └── results_log.md                running log of all findings
├── data/
│   └── orlib/                        OR-Library benchmark instances
├── docs/
│   └── proposal/                     mathematical framework (LaTeX)
└── requirements.txt
```

---

## Experimental Setup

- Instance: Random MKP, n=100 items, m=3 constraints, seed=42
- Tightness levels: α=0.25 (tight), α=0.50 (moderate), α=0.75 (loose)
- Fitness regimes: Death penalty, Penalty x5.0, Repair operator (Chu & Beasley)
- Runs: 15 independent runs per experiment
- Population: 50 | Max offspring: 10,000–15,000
- p_j computation: GPU Monte Carlo, 10M samples, floor=0.05

---

## Related Work

- Chu & Beasley (1998) — A Genetic Algorithm for the Multidimensional Knapsack Problem
- Holland (1975) — Adaptation in Natural and Artificial Systems
- Goldberg (1989) — Genetic Algorithms in Search, Optimization and Machine Learning
- Andrews (1976) — The Theory of Partitions
- Hill (1999) — A Monte Carlo Study of GA Initial Population Generation Methods
- Hill, Moore, Hiremath, Cho (2012) — Test Problem Generation of Binary Knapsack Problem Variants

---

## Dependencies
torch
numpy
matplotlib

---

## Contact

Muhammad Marzouk Baig  
MS Artificial Intelligence, Northeastern University Roux Institute  
baig.muham@northeastern.edu

---
