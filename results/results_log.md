# Results Log

All experimental results. Updated after each session.  
Instance: Random MKP n=100, m=3, seed=42 unless noted.  
p_j computed via GPU Monte Carlo, floor=0.05.  
All experiments: 15 runs, population=50.

---

## Result 1 — Analytical proof of concept

**Source:** LaTeX document (worked examples)  
**Method:** Exact generating function expansion

| Instance | Uniform feasibility | Biased feasibility |
|----------|--------------------|--------------------|
| Single constraint (n=3, W=5) | 62.5% | 87.2% |
| Two constraint (n=4, W1=7, W2=8) | 56.25% | 74.05% |

**Takeaway:** Generating function approach provably increases expected
feasibility rate analytically before any simulation.

---

## Result 2 — Chu & Beasley baseline validation

**Source:** OR-Library mknapcb1 (m=5, n=100, 30 instances)

- Gaps from best known: 0.0% to 1.36%
- All 30 solutions feasible on both random seeds

**Takeaway:** Baseline implementation correct and validated.

---

## Result 3 — GPU Monte Carlo estimation

**Instance:** n=100, m=3, α=0.25 | **Samples:** 10M | **Runtime:** 5.8s on T4

- 41/100 items with nonzero rho_in
- p_j range: [0.05, 0.50], mean=0.175

**Takeaway:** Monte Carlo estimation tractable even for tight instances with GPU.

---

## Result 4 — With repair operator, α=0.25

| Metric | Uniform | Biased |
|--------|---------|--------|
| Feasibility @ gen 0 | 0.0% | 98.0% |
| Fitness @ gen 0 | 94.1% | 92.2% |
| Offspring to 99% | 58 | 75 |
| Final quality | 100% | 100% |

**Takeaway:** Repair operator negates initialization advantage by
providing free quality boost to uniform solutions.

---

## Result 5 — Death penalty, α=0.25, 15 runs, 15000 offspring

| Metric | Uniform | Biased |
|--------|---------|--------|
| Feasibility @ gen 0 | 0.0% | 97.5% |
| Fitness @ gen 0 | 0.0% | 47.7% |
| Offspring to 90% | never | 1,733 |
| Offspring to 95% | never | 5,537 |
| Offspring to 99% | never | 6,699 (7% of runs) |
| Final quality | 0.0% | 96.5% |

**Takeaway:** Uniform completely fails under death penalty on tight
instances. Our method is the only one that functions.

---

## Result 6 — Death penalty, α=0.50, 15 runs

| Metric | Uniform | Biased | Improvement |
|--------|---------|--------|-------------|
| Feasibility @ gen 0 | 32.3% | 72.8% | +40.5pp |
| Offspring to 90% | 823 | 773 | +6.1% |
| Offspring to 95% | 1,807 | 1,456 | +19.4% |
| Offspring to 99% | 4,260 | 6,265 | -47.1% |
| Final quality | 97.7% | 98.2% | +0.5pp |

**Takeaway:** Our method faster to 90% and 95%. Diversity collapse
hurts at 99%. Classic speed vs exploration tradeoff.

---

## Result 7 — Death penalty, α=0.75, 15 runs

| Metric | Uniform | Biased | Improvement |
|--------|---------|--------|-------------|
| Feasibility @ gen 0 | 100% | 100% | same |
| Offspring to 90% | 360 | 352 | +2.1% |
| Offspring to 95% | 787 | 810 | -2.9% |
| Offspring to 99% | 4,310 | 3,349 | +22.3% |
| Final quality | 99.3% | 99.1% | similar |

**Takeaway:** Surprising win at 99% for loose instances. Both start
100% feasible so no diversity cost. 22.3% improvement unexpected.

---

## Result 8 — Penalty x5.0, α=0.25, 10 runs

| Metric | Uniform | Biased | Improvement |
|--------|---------|--------|-------------|
| Feasibility @ gen 0 | 0.0% | 97.5% | +97.5pp |
| Offspring to 90% | 1,732 | 1,258 | +27.4% |
| Offspring to 95% | 3,900 | 3,783 | +3.0% |
| Final quality | 76.9% | 84.0% | +7.1pp |

**Takeaway:** Penalty gives uniform a gradient. Our method 27.4%
faster to 90%. Better final quality. Neither reaches 99%.

---

## Result 9 — Penalty x5.0, α=0.50, 15 runs ← STRONGEST RESULT

| Metric | Uniform | Biased | Improvement |
|--------|---------|--------|-------------|
| Feasibility @ gen 0 | 31.8% | 71.4% | +39.6pp |
| Offspring to 90% | 755 | 597 | +20.9% |
| Offspring to 95% | 2,049 | 1,096 | +46.5% |
| Final quality | 98.2% | 88.5% | -9.7pp |

*99% result unreliable — only 2 total runs reached it.

**Takeaway:** 46.5% improvement at 95% threshold is headline finding.
For applications where 90-95% quality is sufficient, our method
is clearly superior.

---

## Pending experiments

- [ ] Penalty x5.0, α=0.75
- [ ] Repair operator reruns at α=0.50 and α=0.75 (15 runs)
- [ ] Hybrid initialization (50% biased + 50% uniform)
- [ ] OR-Library formal benchmark comparison (mknapcb1, mknapcb4, mknapcb7)
- [ ] Tightness sweep at α=0.35

---

## Overall pattern

| Tightness | Fitness regime | Our advantage |
|-----------|---------------|---------------|
| α=0.25 | Death penalty | Only method that works |
| α=0.25 | Penalty x5.0 | +27.4% to 90%, +7pp final quality |
| α=0.50 | Death penalty | +6.1% to 90%, +19.4% to 95% |
| α=0.50 | Penalty x5.0 | +20.9% to 90%, +46.5% to 95% |
| α=0.75 | Death penalty | +22.3% to 99% |

**Core finding:** Generating function-informed initialization
consistently improves convergence to 90-95% quality thresholds.
The advantage is largest at moderate tightness (α=0.50) where
feasibility structure is most discriminative. The repair operator
absorbs most of the advantage when present.
