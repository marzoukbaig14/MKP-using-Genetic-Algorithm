## Analytical Proof of Concept

**Source:** LaTeX document (worked examples)  
**Method:** Exact generating function expansion

| Instance | Uniform feasibility | Biased feasibility |
|----------|--------------------|--------------------|
| Single constraint (n=3, W=5) | 62.5% | 87.2% |
| Two constraint (n=4, W1=7, W2=8) | 56.25% | 74.05% |

**Takeaway:** Generating function approach provably increases expected
feasibility rate analytically before any simulation.

## p_j Summary

| Alpha | p_j min | p_j max | p_j mean | Interpretation |
|---|---|---|---|---|
| 0.25 | 0.000 | 1.000 | 0.240 | Strong differentiation -- tight instance |
| 0.50 | 0.414 | 0.493 | 0.449 | Weak differentiation -- medium instance |
| 0.75 | 0.500 | 0.500 | 0.500 | No differentiation -- degenerates to uniform |

## GPU Monte Carlo Estimation

**Instance:** n=100, m=3, α=0.25 | **Samples:** 10M | **Runtime:** 5.8s on T4

- 41/100 items with nonzero rho_in
- p_j range: [0.05, 0.50], mean=0.175

**Takeaway:** Monte Carlo estimation tractable even for tight instances with GPU.

# Baseline Experiment Results

Generated: 2026-04-29

## Experiment Parameters

| Parameter | Value |
|---|---|
| n (items) | 100 |
| m (constraints) | 3 |
| Instance seed | 42 |
| Population size | 50 |
| Runs per condition | 20 |
| Offspring per run | 20000 |
| Mutation rate | 1/n = 0.0100 |
| Tournament | binary k=2 |
| Crossover | uniform |
| p_j samples | 10,000,000 |
| Convergence thresholds | [50, 60, 70, 80, 90, 95, 98, 99] |

## Reference Bests

Established via repair-based GA, 20 runs, 30,000 offspring, seeds 0-19. Not proven optimal -- best known values used as convergence targets.

| Alpha | Reference Best |
|---|---|
| 0.25 | 2375 |
| 0.5 | 3909 |
| 0.75 | 4823 |

## Results by Condition

### Death penalty a=0.25

Reference best: 2375  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 0.0% | 52.5% |
| Fitness at gen 0 (% of ref) | 0.0% | 50.5% |
| Final fitness (% of ref) | 0.0% | 96.5% |
| Offspring to 50% | never | 10 (20/20) |
| Offspring to 60% | never | 194 (20/20) |
| Offspring to 70% | never | 481 (20/20) |
| Offspring to 80% | never | 1023 (20/20) |
| Offspring to 90% | never | 2309 (20/20) |
| Offspring to 95% | never | 7529 (20/20) |
| Offspring to 98% | never | 16845 (1/20) |
| Offspring to 99% | never | never |

### Death penalty a=0.50

Reference best: 3909  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 32.6% | 71.6% |
| Fitness at gen 0 (% of ref) | 67.3% | 67.8% |
| Final fitness (% of ref) | 98.5% | 98.8% |
| Offspring to 50% | 1 (20/20) | 1 (20/20) |
| Offspring to 60% | 1 (20/20) | 1 (20/20) |
| Offspring to 70% | 82 (20/20) | 54 (20/20) |
| Offspring to 80% | 316 (20/20) | 255 (20/20) |
| Offspring to 90% | 893 (20/20) | 788 (20/20) |
| Offspring to 95% | 1740 (20/20) | 1577 (20/20) |
| Offspring to 98% | 8327 (16/20) | 6724 (19/20) |
| Offspring to 99% | 14718 (5/20) | 9172 (7/20) |

### Death penalty a=0.75

Reference best: 4823  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 100.0% | 100.0% |
| Fitness at gen 0 (% of ref) | 65.1% | 65.2% |
| Final fitness (% of ref) | 99.5% | 99.5% |
| Offspring to 50% | 1 (20/20) | 1 (20/20) |
| Offspring to 60% | 2 (20/20) | 1 (20/20) |
| Offspring to 70% | 43 (20/20) | 42 (20/20) |
| Offspring to 80% | 153 (20/20) | 140 (20/20) |
| Offspring to 90% | 459 (20/20) | 410 (20/20) |
| Offspring to 95% | 897 (20/20) | 829 (20/20) |
| Offspring to 98% | 1838 (20/20) | 2250 (20/20) |
| Offspring to 99% | 4873 (18/20) | 4648 (17/20) |

### Penalty x5.0 a=0.25

Reference best: 2375  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 0.0% | 52.5% |
| Fitness at gen 0 (% of ref) | 0.0% | 51.6% |
| Final fitness (% of ref) | 73.4% | 68.5% |
| Offspring to 50% | 196 (20/20) | 17 (20/20) |
| Offspring to 60% | 253 (20/20) | 158 (20/20) |
| Offspring to 70% | 383 (20/20) | 429 (20/20) |
| Offspring to 80% | 646 (20/20) | 825 (20/20) |
| Offspring to 90% | 1410 (20/20) | 1917 (20/20) |
| Offspring to 95% | 2831 (20/20) | 5333 (19/20) |
| Offspring to 98% | 10465 (10/20) | 11165 (8/20) |
| Offspring to 99% | 10500 (2/20) | 12633 (2/20) |

### Penalty x5.0 a=0.50

Reference best: 3909  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 32.6% | 71.6% |
| Fitness at gen 0 (% of ref) | 68.6% | 68.7% |
| Final fitness (% of ref) | 89.0% | 69.4% |
| Offspring to 50% | 16 (20/20) | 18 (20/20) |
| Offspring to 60% | 16 (20/20) | 18 (20/20) |
| Offspring to 70% | 50 (20/20) | 61 (20/20) |
| Offspring to 80% | 265 (20/20) | 244 (20/20) |
| Offspring to 90% | 721 (20/20) | 672 (20/20) |
| Offspring to 95% | 1373 (20/20) | 1239 (20/20) |
| Offspring to 98% | 4683 (18/20) | 3570 (19/20) |
| Offspring to 99% | 9772 (11/20) | 10188 (11/20) |

### Penalty x5.0 a=0.75

Reference best: 4823  |  Runs: 20  |  Offspring: 20000

| Metric | Uniform | Biased |
|---|---|---|
| Feasibility at gen 0 | 100.0% | 100.0% |
| Fitness at gen 0 (% of ref) | 65.1% | 65.2% |
| Final fitness (% of ref) | 99.5% | 94.6% |
| Offspring to 50% | 1 (20/20) | 1 (20/20) |
| Offspring to 60% | 2 (20/20) | 1 (20/20) |
| Offspring to 70% | 43 (20/20) | 44 (20/20) |
| Offspring to 80% | 153 (20/20) | 137 (20/20) |
| Offspring to 90% | 404 (20/20) | 404 (20/20) |
| Offspring to 95% | 786 (20/20) | 729 (20/20) |
| Offspring to 98% | 1633 (20/20) | 1556 (20/20) |
| Offspring to 99% | 5304 (20/20) | 4256 (20/20) |
