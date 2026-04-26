# MKP-using-Genetic-Algorithm
Research: Using combinatorial generating functions &amp; Monte Carlo sampling to bias GA initialization toward feasible regions of the Multidimensional Knapsack Problem search space

readme = """# Generating Function-Informed Initialization for Genetic Algorithms on the Multidimensional Knapsack Problem

**Author:** Muhammad Marzouk Baig  
**Program:** MS Artificial Intelligence, Northeastern University Roux Institute  
**Supervisor:** Prof. Sandy Ganzell, Khoury College of Computer Sciences  
**Status:** Active research — experiments in progress

---

## Overview

This repository contains the implementation and experimental results for my master's thesis research on using combinatorial generating functions and Monte Carlo sampling to inform genetic algorithm initialization for the Multidimensional Knapsack Problem (MKP).

The core idea: instead of initializing a GA population uniformly at random (each bit set to 1 with probability 0.5), we analytically characterize the feasibility structure of the MKP search space using generating functions, and derive per-bit sampling probabilities that bias initialization toward high-feasibility regions — before any search begins.

---

## Research Question

Can combinatorial generating functions be used to analytically characterize the feasibility structure of the multidimensional knapsack search space at the schema level, and does using this characterization to bias GA initialization improve convergence behavior relative to uniform initialization?

---

## Key Findings (preliminary)

- Our method produces **97.5% feasible initial solutions** at generation 0 on tight instances (α=0.25) where uniform initialization produces **0% feasible** solutions
- Our method reaches **95% solution quality 46.5% faster** than uniform initialization on moderate tightness instances (α=0.50)
- Our method reaches **90% solution quality 27.4% faster** on tight instances under penalty fitness
- The advantage is most pronounced at **moderate tightness (α=0.50)** where feasibility structure is most discriminative
- The repair operator (Chu & Beasley) partially absorbs the initialization advantage by providing a free quality boost to uniform solutions

---

## Method

**Phase 1 — Precomputation (done once before GA starts)**

For each item j, compute the sub-generating function by removing item j from the full product and evaluating against two capacity thresholds to get feasibility densities:
- rho_j_1 = fraction of completions feasible when item j is included  
- rho_j_0 = fraction of completions feasible when item j is excluded

For large m (constraints), GPU-accelerated Monte Carlo sampling replaces exact polynomial expansion.

**Phase 2 — Biased initialization**

Set per-bit sampling probability p_j = rho_j_1 / (rho_j_1 + rho_j_0) and generate initial population by sampling each bit independently with probability p_j instead of uniform 0.5.

---

## Repository Structure
