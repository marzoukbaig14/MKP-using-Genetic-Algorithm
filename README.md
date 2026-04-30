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

The method is mathematically guaranteed to improve initial feasibility rates and has been empirically confirmed across multiple tightness levels. On tight instances uniform initialization produces 0% feasible solutions while our method produces over 50%. On loose instances the method automatically degenerates to uniform initialization — no harm done when there is nothing to gain.

---

## Research Question

Can combinatorial generating functions be used to analytically characterize the feasibility structure of the multidimensional knapsack search space at the schema level, and does using this characterization to bias GA initialization improve convergence behavior relative to uniform initialization?

---

## Key Findings (20-run experiments, n=100, m=3, seed=42)

### Mathematical Guarantee

The improvement in initial feasibility rate is analytically proven via worked examples:

| Instance | Uniform Expected Feasibility | Biased Expected Feasibility | Gain |
|---|---|---|---|
| Single constraint (n=3, W=5) | 62.5% | 87.2% | +24.7pp |
| Two constraint (n=4, W1=7, W2=8) | 56.25% | 74.05% | +17.8pp |

This is a mathematical result guaranteed by construction — not dependent on any experimental outcome.

### Feasibility at Generation 0 — primary metric

| Tightness | Uniform | Biased (ours) | Interpretation |
|---|---|---|---|
| α=0.25 (tight) | 0.0% | 52.5% | Uniform produces zero feasible solutions |
| α=0.50 (medium) | 32.6% | 71.6% | Biased more than doubles feasibility rate |
| α=0.75 (loose) | 100% | 100% | Method degenerates to uniform automatically |

### p_j Behavior — the method characterizes instance difficulty automatically

| Alpha | p_j min | p_j max | p_j mean | Interpretation |
|---|---|---|---|---|
| 0.25 | 0.000 | 1.000 | 0.240 | Strong per-item differentiation |
| 0.50 | 0.414 | 0.493 | 0.449 | Weak differentiation |
| 0.75 | 0.500 | 0.500 | 0.500 | No differentiation — degenerates to uniform |

### Convergence Speed — death penalty, α=0.50

| Threshold | Uniform | Biased | Improvement |
|---|---|---|---|
| 70% of reference | 82 offspring | 54 offspring | +34.5% |
| 90% of reference | 893 offspring | 788 offspring | +11.7% |
| 98% of reference | 8327 offspring | 6724 offspring | +19.3% |
| 99% of reference | 14718 offspring | 9172 offspring | +37.7% |
| Final quality | 98.5% | 98.8% | essentially identical |

Biased initialization converges faster at every threshold with no quality cost. The advantage grows at higher thresholds.

### Key behavioral findings

- At tight instances with death penalty, uniform initialization is completely broken — zero fitness signal means zero evolution. Biased initialization is the only viable approach.
- At medium-tight instances with death penalty, biased converges faster at every threshold with no final quality cost. This is the strongest clean result.
- At loose instances, both methods start fully feasible and perform identically — the method correctly identifies when it has nothing to contribute.
- Under soft penalty, biased initialization shows diversity collapse at tight instances — the population converges to a feasible region quickly but loses diversity needed for late-stage optimization. Ongoing experiments are characterizing the penalty strength at which this behavior changes.
- Hybrid initialization experiments (mixing biased and uniform individuals) show that as little as 25% biased individuals is sufficient to rescue the GA from complete failure at tight instances, with 50% hybrid achieving the fastest convergence to 95% of reference quality.

---

## Novelty

The closest prior work — Hill (1999) — proposed using a single global inclusion probability derived from a greedy heuristic, applied identically to all items. Professor Hill confirmed the novelty of our per-item generating function approach in personal communication (2025). His method achieved approximately 20% feasible solutions at tight instances. Our per-item approach achieves over 50% — and the improvement is mathematically guaranteed by construction rather than empirically derived.

Key distinctions from Hill (1999):
- Hill used one global probability for all n items derived from a greedy heuristic
- We compute individual p_j per item from generating function feasibility densities
- Hill's method required m=2 constraints only; ours works for any m via GPU Monte Carlo
- We provide full GA convergence experiments across tightness levels and penalty regimes
- No optimization subproblem is required — purely arithmetic on weights and capacities

---

## Method

### Phase 1 — Precomputation (done once per problem instance)

For each item j, compute the sub-generating function g_j by removing item j from the full product and evaluating against two capacity thresholds:

- ρ_j^1 = fraction of completions feasible when item j is included (capacity threshold W_i - w_ji)
- ρ_j^0 = fraction of completions feasible when item j is excluded (capacity threshold W_i)

For large instances: GPU-accelerated Monte Carlo sampling (10M samples, ~7 seconds on T4 GPU, standard error < 0.016%). This is a one-time cost — the resulting probability vector p is reused across all GA runs on that instance.

### Phase 2 — Biased initialization

Per-bit sampling probability: p_j = ρ_j^1 / (ρ_j^1 + ρ_j^0)

Generate initial population by sampling each bit independently with probability p_j instead of uniform 0.5. Requires no optimization subproblem — purely arithmetic on weights and capacities. All other GA components (selection, crossover, mutation, penalty) remain identical to the baseline.

---

## Experimental Setup

- Instance: Random MKP, n=100 items, m=3 constraints, seed=42
- Values: uniform random [1, 99], Weights: uniform random [1, 49]
- Capacities: int(alpha × sum of constraint weights) per constraint
- Tightness levels: α=0.25 (tight), α=0.50 (moderate), α=0.75 (loose)
- Penalty regimes: Death penalty, Soft penalty (multipliers 5, 10, 25, 50, 100, 500)
- Runs: 20 independent runs per condition
- Population: 50 | Offspring per run: 20,000
- Tournament: binary (k=2) | Crossover: uniform | Mutation: 1/n
- p_j computation: GPU Monte Carlo, 10M samples, seed=42
- Reference best: established via repair-based GA, 20 runs, 30k offspring, take max
- Diversity tracking: mean pairwise Hamming distance every 50 offspring

---

## Active Experiments

- Penalty strength sweep: α=0.25 and α=0.50, penalties 5 through 500 and death — characterizing where biased initialization stops losing to uniform under soft penalty
- Hybrid initialization sweep: mixing biased and uniform individuals at varying ratios
- Tightness sweep: finer granularity from α=0.10 to α=0.90
- OR-Library benchmark validation: mknapcb1, mknapcb4, mknapcb7
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
