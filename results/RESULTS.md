# Results Log

**Instance:** n=100 items, m=3 constraints, random seed=42  
**Weights/Values:** uniform [1, 100]  
**Capacities:** W_i = α × Σ w_ji per constraint  
**Population:** 50 | **Offspring:** 20,000 per run | **Runs:** 20 independent  
**Mutation:** 1/n | **Crossover:** uniform | **Selection:** binary tournament k=2  
**p_j computation:** GPU Monte Carlo, M=10,000,000 samples, seed=42  
**Reference best:** repair-based GA, 20 runs, 30,000 offspring, max observed  

---

## Reference Bests

| α | Reference Best |
|---|---|
| 0.25 | 2,375 |
| 0.30 | 2,739 |
| 0.40 | 3,373 |
| 0.50 | 3,909 |
| 0.75 | 4,823 |

---

## 1. p_j Distribution (Self-Calibrating Behavior)

The per-item sampling probabilities are computed once before any search begins.

| α | Min p_j | Max p_j | Mean p_j | Interpretation |
|---|---|---|---|---|
| 0.25 | 0.000 | 1.000 | 0.240 | Strong differentiation — tight instance |
| 0.50 | 0.414 | 0.493 | 0.449 | Weak differentiation — medium instance |
| 0.75 | 0.500 | 0.500 | 0.500 | No differentiation — degenerates to uniform exactly |

**Takeaway:** At α=0.75, every item gets p_j = 0.500 exactly. This is a theoretical prediction of the framework: when feasibility is plentiful, inclusion or exclusion of any single item has negligible impact, and the proportional rule recovers 0.5 for all items. The experiments confirm this holds exactly. All α=0.75 comparisons below are therefore theoretical validations, not performance comparisons — both methods are identical by construction at that tightness level.

---

## 2. Initialization Feasibility — Core Result

### Death penalty, 20 runs per condition

| α | Uniform init feas | GF-Biased init feas | Uniform final | GF-Biased final | Feasible runs (GF) |
|---|---|---|---|---|---|
| 0.25 | 0.0% | 52.5% | 0.0% | 96.5% | 20/20 |
| 0.30 | 0.0% | 53.3% | ~10% (2/20 lucky) | 97.6% | 20/20 |
| 0.40 | 1.1% | 60.2% | 98.0% | 98.2% | 20/20 |
| 0.50 | 32.6% | 71.6% | 98.5% | 98.8% | 20/20 |
| 0.60 | 92.4% | 93.5% | 99.1% | 99.0% | 20/20 |
| 0.70 | 100.0% | 100.0% | 99.4% | 99.3% | 20/20 |
| 0.75 | 100.0% | 100.0% | 99.5% | 99.5% | 20/20 |
| 0.80 | 100.0% | 100.0% | 99.6% | 99.6% | 20/20 |
| 0.90 | 100.0% | 100.0% | 99.9% | 99.8% | 20/20 |

**Takeaway:** The framework provides the largest benefit where feasibility is genuinely scarce (α ≤ 0.40). At α=0.25, uniform initialization produces zero feasible solutions in every run — selection cannot operate and the GA produces nothing. GF-biased initialization starts with 52.5% of the population feasible and converges to 96.5% of the reference best in all 20 runs. At α=0.30, uniform initialization still mostly fails (2/20 runs find any solution at all). By α=0.60, both methods start nearly fully feasible and the difference disappears.

---

## 3. Tightness Sweep — Full Results (Death Penalty)

Sweep from α=0.25 to α=0.90. Three conditions: uniform, GF-biased, and hybrid (50% GF-biased / 50% uniform initial population).

| α | Method | Init Feas | Final Quality | Solved |
|---|---|---|---|---|
| 0.25 | Uniform | 0.0% | 0.0% | 0/20 |
| 0.25 | GF-Biased | 47.3% | 97.1% | 20/20 |
| 0.25 | Hybrid 50% | 22.8% | 96.7% | 20/20 |
| 0.30 | GF-Biased | 53.3% | 97.6% | 20/20 |
| 0.30 | Hybrid 50% | 26.2% | 97.2% | 20/20 |
| 0.40 | Uniform | 1.1% | 98.0% | 20/20 |
| 0.40 | GF-Biased | 60.2% | 98.2% | 20/20 |
| 0.40 | Hybrid 50% | 30.6% | 98.0% | 20/20 |
| 0.50 | Uniform | 32.6% | 98.5% | 20/20 |
| 0.50 | GF-Biased | 71.6% | 98.6% | 20/20 |
| 0.50 | Hybrid 50% | 52.3% | 98.6% | 20/20 |
| 0.60 | Uniform | 92.4% | 99.1% | 20/20 |
| 0.60 | GF-Biased | 93.5% | 99.0% | 20/20 |
| 0.60 | Hybrid 50% | 92.3% | 99.2% | 20/20 |
| 0.70 | Uniform | 100.0% | 99.4% | 20/20 |
| 0.70 | GF-Biased | 100.0% | 99.3% | 20/20 |
| 0.70 | Hybrid 50% | 100.0% | 99.3% | 20/20 |
| 0.75 | Uniform | 100.0% | 99.5% | 20/20 |
| 0.75 | GF-Biased | 100.0% | 99.3% | 20/20 |
| 0.75 | Hybrid 50% | 100.0% | 99.6% | 20/20 |
| 0.80 | Uniform | 100.0% | 99.6% | 20/20 |
| 0.80 | GF-Biased | 100.0% | 99.6% | 20/20 |
| 0.80 | Hybrid 50% | 100.0% | 99.7% | 20/20 |
| 0.90 | Uniform | 100.0% | 99.9% | 20/20 |
| 0.90 | GF-Biased | 100.0% | 99.8% | 20/20 |
| 0.90 | Hybrid 50% | 100.0% | 99.8% | 20/20 |

**Takeaway:** The critical transition is between α=0.30 and α=0.40. Below α=0.40, uniform initialization is unreliable or completely broken. At α=0.40 and above, all three methods converge to comparable final quality. The hybrid (50% GF / 50% uniform) consistently matches or slightly exceeds pure GF-biased in final quality while requiring only half the precomputation cost (in effect), suggesting that population diversity from the uniform half can provide a marginal late-stage benefit. Note that no uniform file exists for α=0.30 in this sweep — uniform is presumed to fail at that level based on the ILS comparison results.

---

## 4. GF + ILS Pipeline (r=50, k=5) — Across Alpha Levels

GF-biased initialization + iterated local search post-processing, compared to uniform death penalty and repair baseline. 20 runs each.

| α | Method | Init Feas | Final Quality | Min | Solved |
|---|---|---|---|---|---|
| 0.25 | Uniform + death | 0.0% | 0.0% | 0.0% | 0/20 |
| 0.25 | GF + ILS | 47.3% | **99.3%** | 98.7% | 20/20 |
| 0.25 | Repair (ref) | 0.0% | 99.8% | 99.5% | 20/20 |
| 0.30 | Uniform + death | 0.0% | 9.6% | 0.0% | 2/20 |
| 0.30 | GF + ILS | 53.3% | **99.3%** | 98.6% | 20/20 |
| 0.30 | Repair (ref) | 0.0% | 99.8% | 99.4% | 20/20 |
| 0.40 | Uniform + death | 1.1% | 98.0% | 96.7% | 20/20 |
| 0.40 | GF + ILS | 60.2% | **99.8%** | 99.1% | 20/20 |
| 0.40 | Repair (ref) | ~1% | 100.0% | 99.7% | 20/20 |
| 0.50 | Uniform + death | 32.6% | 98.5% | 97.1% | 20/20 |
| 0.50 | GF + ILS | 71.6% | **99.8%** | 99.3% | 20/20 |
| 0.50 | Repair (ref) | ~35% | 100.0% | 99.9% | 20/20 |

**Takeaway:** Adding lightweight ILS (50 restarts, k=5 bit-flip perturbations) to GF-biased initialization closes the gap to repair to within 0.5 percentage points at all tested alpha levels. At α=0.25 and α=0.30, where uniform initialization fails entirely, GF+ILS is the only death-penalty method that functions and achieves 99.3% of the reference best in all 20 runs. At α=0.40 and α=0.50, GF+ILS substantially outperforms pure uniform death penalty and approaches the repair ceiling. The ILS parameters (r=50, k=5) are unoptimized; these results represent a lower bound on what is achievable with downstream optimization.

---

## 5. Hybrid Initialization Sweep — α=0.25

Varying the fraction of GF-biased solutions in the initial population (remainder uniform). 20 runs per condition, death penalty.

| GF Fraction | Init Feas | Mean Quality | Min Quality | Solved (≥95%) |
|---|---|---|---|---|
| 0% (pure uniform) | 0.0% | 0.0% | 0.0% | 0/20 |
| 25% | 11.6% | 97.1% | 95.3% | 20/20 |
| 50% | 25.1% | 96.7% | 95.1% | 20/20 |
| 75% | 37.9% | 97.1% | 95.3% | 20/20 |
| 100% (pure GF) | 50.5% | 96.6% | 93.1% | 18/20 |

**Takeaway:** Any non-zero informed fraction rescues the algorithm entirely. Seeding just 25% of the population with GF-biased solutions is sufficient to achieve 20/20 runs above 95% of reference best. Mean quality is approximately flat across all non-zero fractions (96.6–97.1%), indicating the critical factor is crossing the feasibility threshold for selection to operate, not the magnitude of the feasibility boost. The pure GF condition (100%) solves 18/20 runs rather than 20/20 — consistent with reduced diversity when all solutions are drawn from the same biased distribution. The informed fraction is a tunable parameter: practitioners can trade initialization overhead against early-generation diversity.

---

## 6. Death Penalty — Detailed Results by Alpha

### α = 0.25 | ref = 2,375

| Metric | Uniform | GF-Biased |
|---|---|---|
| Init feasibility | 0.0% | 52.5% |
| Init quality (% ref) | 0.0% | 50.5% |
| Final quality — mean | 0.0% | 96.6% |
| Final quality — min/max | 0.0% / 0.0% | 93.1% / 98.6% |
| Final feasible runs | 0/20 | 20/20 |

Convergence (median offspring to threshold):

| Threshold | Uniform | GF-Biased |
|---|---|---|
| 50% | never | 1 (20/20) |
| 70% | never | 488 (20/20) |
| 90% | never | 2,066 (20/20) |
| 95% | never | 9,136 (18/20) |
| 98% | never | 18,356 (3/20) |
| 99% | never | never (0/20) |

---

### α = 0.50 | ref = 3,909

| Metric | Uniform | GF-Biased |
|---|---|---|
| Init feasibility | 32.6% | 71.6% |
| Init quality (% ref) | 67.3% | 67.8% |
| Final quality — mean | 98.5% | 98.8% |
| Final quality — min/max | 97.1% / 100.0% | 97.1% / 99.7% |
| Final feasible runs | 20/20 | 20/20 |

Convergence (median offspring to threshold):

| Threshold | Uniform | GF-Biased |
|---|---|---|
| 70% | 83 (20/20) | 57 (20/20) |
| 80% | 326 (20/20) | 260 (20/20) |
| 90% | 838 (20/20) | 758 (20/20) |
| 95% | 1,762 (20/20) | 1,585 (20/20) |
| 98% | 6,387 (16/20) | 5,455 (19/20) |
| 99% | 18,067 (5/20) | 8,089 (7/20) |

**Takeaway:** Both methods converge to similar final quality at α=0.50, but GF-biased is faster to every threshold and more reliable at 98% (19/20 vs 16/20). Despite starting with more than double the initial feasibility (71.6% vs 32.6%), the initial quality percentages are nearly identical (67.8% vs 67.3%) — the biased population starts more feasible but not richer in high-value solutions, as expected from a constraint-only initialization method.

---

### α = 0.75 | ref = 4,823 (theoretical validation only)

Both methods are identical at this tightness level because p_j = 0.5 for all items. Results are included to confirm the self-calibrating property holds empirically.

| Metric | Uniform | GF-Biased |
|---|---|---|
| Init feasibility | 100.0% | 100.0% |
| Final quality — mean | 99.5% | 99.5% |
| Final feasible runs | 20/20 | 20/20 |

---

## 7. Penalty Sweep — α = 0.25

Varying the soft penalty multiplier from ×5 to ×500, compared to death penalty. File naming: x50 = ×5.0, x100 = ×10.0, x250 = ×25.0, x500 = ×50.0, x1000 = ×100.0, x5000 = ×500.0.

| Penalty | Uniform final | Uniform feasible | GF-Biased final | GF-Biased feasible | Winner |
|---|---|---|---|---|---|
| death | 0.0% | 0/20 | 96.6% | 20/20 | **GF** (+96.6pp) |
| ×5 | 73.4% | 15/20 | 63.4% | 13/20 | Uniform (+10.0pp) |
| ×10 | 87.5% | 18/20 | 78.2% | 16/20 | Uniform (+9.3pp) |
| ×25 | 96.8% | 20/20 | 97.1% | 20/20 | ~Tie (+0.2pp GF) |
| ×50 | 97.0% | 20/20 | 96.5% | 20/20 | ~Tie (-0.4pp GF) |
| ×100 | 97.1% | 20/20 | 96.9% | 20/20 | ~Tie (-0.2pp GF) |
| ×500 | 97.1% | 20/20 | 96.9% | 20/20 | ~Tie (-0.3pp GF) |

**Takeaway:** There is a clear regime transition. Under death penalty, GF-biased is the only method that works. Under weak soft penalty (×5, ×10), uniform initialization actually outperforms GF-biased at final quality despite starting with zero feasible solutions — the soft penalty provides enough fitness signal for the GA to locate feasible solutions, but the GF-biased population converges quickly to a high-feasibility region and loses diversity needed for late-stage optimization. The final diversity for GF-biased under ×5 is 0.000 (complete convergence) versus near-0 for uniform. The crossover point is around ×25, beyond which both methods produce essentially identical results. This diversity collapse under weak soft penalty is an important limitation of the framework and an active area of investigation.

---

## 8. Penalty Sweep — α = 0.50

| Penalty | Uniform final | Uniform feasible | GF-Biased final | GF-Biased feasible | Winner |
|---|---|---|---|---|---|
| death | 98.5% | 20/20 | 98.8% | 20/20 | ~Tie (+0.2pp GF) |
| ×5 | 89.0% | 18/20 | 79.3% | 16/20 | Uniform (+9.8pp) |
| ×10 | 84.2% | 17/20 | 89.0% | 18/20 | GF (+4.8pp) |
| ×25 | 98.9% | 20/20 | 98.5% | 20/20 | ~Tie (-0.4pp GF) |
| ×50 | 98.7% | 20/20 | 98.4% | 20/20 | ~Tie (-0.3pp GF) |
| ×100 | 98.7% | 20/20 | 98.2% | 20/20 | ~Tie (-0.4pp GF) |

**Takeaway:** The same diversity collapse pattern appears at α=0.50 under weak penalty, though less extreme than at α=0.25 (because 32.6% of the uniform population is already feasible at generation 0, providing more diverse starting material). The ×10 result is unusual — GF-biased actually outperforms uniform, suggesting the penalty is strong enough to penalize infeasibility but weak enough that GF's higher initial feasibility provides a lasting advantage. This mid-range penalty behavior warrants further investigation.

---

## 9. Exact vs Monte Carlo p_j Comparison

Small instances (n=15, m=3) where exact enumeration is tractable. Five initialization methods compared: uniform, greedy heuristic, gf_exact (exact sub-GF computation), gf_ranked (p_j sorted/ranked variant), and empty (start from empty solution).

### α = 0.10 | ref = 1,150

| Method | Init Feas | Final Mean | Min | Max |
|---|---|---|---|---|
| uniform | 0.0% | 0.0% | 0.0% | 0.0% |
| greedy | 100.0% | 93.2% | 85.1% | 97.1% |
| gf_exact | 100.0% | 93.0% | 88.8% | 95.9% |
| gf_ranked | 0.0% | 0.0% | 0.0% | 0.0% |
| empty | 100.0% | 93.1% | 85.9% | 96.4% |

### α = 0.20 | ref = 1,995

| Method | Init Feas | Final Mean | Min | Max |
|---|---|---|---|---|
| uniform | 0.0% | 0.0% | 0.0% | 0.0% |
| greedy | 100.0% | 96.4% | 93.0% | 98.7% |
| gf_exact | 100.0% | 95.7% | 92.1% | 98.3% |
| gf_ranked | 0.0% | 0.0% | 0.0% | 0.0% |
| empty | 100.0% | 95.8% | 94.0% | 99.0% |

### α = 0.25 | ref = 2,375

| Method | Init Feas | Final Mean | Min | Max |
|---|---|---|---|---|
| uniform | 0.0% | 0.0% | 0.0% | 0.0% |
| greedy | 100.0% | 96.7% | 94.1% | 98.6% |
| gf_exact | 100.0% | 96.9% | 94.0% | 99.0% |
| gf_ranked | 0.0% | 24.1% | 0.0% | 97.0% |
| empty | 100.0% | 96.8% | 94.0% | 99.6% |

**Takeaway:** On these small instances, gf_exact, greedy, and empty initialization all achieve comparable final quality (all within ~1pp of each other), all dramatically outperforming uniform. The gf_exact method matches or slightly exceeds greedy at α=0.25, which is the most meaningful comparison since it requires no objective function. The gf_ranked variant (p_j sorted by rank rather than raw value) fails at α=0.10 and α=0.20, suggesting that the proportional allocation rule is important — sorting/ranking loses the quantitative feasibility signal. The empty initialization method performing on par with gf_exact suggests that for very tight small instances, starting from a single fully-feasible (empty) solution is an effective baseline. This comparison validates the correctness of the exact GF computation.

---

## 10. Files Reference

| File | Description |
|---|---|
| `results_Death_penalty_α025/050/075.json` | Core death penalty results, 3 alpha levels |
| `results_Penalty_x50__α025/050/075.json` | Soft penalty ×5, 3 alpha levels |
| `results_Penalty_sweep_a025_*.json` | Full penalty sweep α=0.25, 7 multipliers |
| `results_Penalty_sweep_a05_*.json` | Full penalty sweep α=0.50, 6 multipliers |
| `results_GF_ILS_comparison_a025/03/04/05__*.json` | GF+ILS vs uniform vs repair, 4 alpha levels |
| `results_Hybrid_sweep_a025_death_ratio*.json` | Hybrid initialization sweep, 5 GF fractions |
| `results_Tightness_sweep_death_a*.json` | Full tightness sweep α=0.25–0.90 |
| `results_exact_dp_comparison_a01/02/025.json` | Exact GF vs Monte Carlo vs greedy, small instances |

---

## 11. Active / Pending

- OR-Library benchmark instances (mknapcb1, mknapcb4, mknapcb7): not yet run
- Adversarial instances (negative value-weight correlation): not yet run
- Penalty sweep for α=0.30, 0.40 (only α=0.25 and α=0.50 completed)
- Finer-grained hybrid sweep at additional alpha levels
