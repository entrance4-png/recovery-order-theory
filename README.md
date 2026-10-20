# recovery-order-theory

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21829215.svg)](https://doi.org/10.5281/zenodo.21829215)

Numerical verification code for

> **Recovery order in adaptive systems is set by dependency structure**
> Hiroki Saito (2026)

This repository contains the single, self-contained script that **validates** (it does not
prove) the analytical results of the paper, and regenerates every figure in it.

The proofs live in the paper (Methods, Theorems A, B and 1-3, Proposition A.3). The numbers
here confirm that concrete minimal models behave exactly as those theorems require.

---

## Quick start

```
git clone https://github.com/entrance4-png/recovery-order-theory.git
cd recovery-order-theory
pip install -r requirements.txt
python recovery_order_verification.py figures/
```

Runtime: roughly 30-180 s depending on the machine. The script prints a progress marker
(`[1/8]` to `[8/8]`) for each stage, reports the result of each check as it runs, writes the
outcomes to `derivation_verification_report.json`, and ends with a single overall pass/fail
line that is the conjunction of every check:

```
OVERALL: all analytical claims of Theorems A and B reproduced = True
```

Only `numpy` and `matplotlib` are required. No network access, no data files, no seeds to set:
every result is deterministic.

---

## What is checked

| Claim in the paper | Check |
| --- | --- |
| **Theorem A**. Fisher information factorizes through the dependency graph, `I_k = c_k · Π_{j ∈ anc(k)} r_j` | closed form vs Monte-Carlo (variance of the score) vs the product, over a random grid of availability levels; and the zero-pattern of the Fisher field vs the graph |
| **Theorem B1**. accumulated Fisher information follows its expected flux, `dP_k/dt = I_k` (the learning model adopted in the paper; exact posterior precision for a Gaussian-mean channel) | per-observation information summed over a realized gated stream vs the accumulation ODE, for the 20,000-observation check and for the 6,000-observation run plotted in Fig. 2a |
| **Theorem B2/B3**. the recovery law `dr_k/dt = c'_k · A_k · (1 - r_k)` is derived, not assumed | integrating the Fisher-first pipeline through the derived link vs the companion law |
| **Theorem 1(a)**. onset precedence holds for any rate constants (weak form; onset times coincide in the continuous model) | threshold-crossing order at `1e-6` above a near-zero baseline, 120 random rate vectors including strongly increasing ones; a reading of the early growth under the product gate, not a measurement of onset times |
| **Theorem 1(b)**. milestone precedence holds when rates do not increase along edges (`c_k ≤ c_j`) | 108 samples with non-increasing rates vs 108 with increasing rates |
| **Sharpness**. the rate condition cannot be dropped | the counterexample (`r0 = 0.5`, `c_j = 1`, `c_k = 100`) and the reversal boundary |
| **Sharpness**. the common closure cannot be dropped either | `c_j = 1`, `c_k = 0.5` (rate condition holds), `h_j = (1-r)^3`, `h_k = (1-r)^0.2`, `r0 = 0.3`: the child crosses first (`T_k = 2.83` vs `T_j = 48.98`). `T_k` is obtained twice, by RK4 and by separation of variables |
| **Proposition A.3**. the gate enters only through its **support** | the same graph under the product gate and the min gate (a different t-norm with the same support) |
| **Sharpness of nestedness (G4)**. a non-nested gate family reverses an edge with equal rates and a common closure | chain `1 -> 2 -> 3`, `phi_2 = r1^8`, `phi_3 = sqrt(r1*r2)`, `r0 = 0.3`, `q = 0.9`: `T1 = 1.946` (closed form `ln(0.7/0.1)`), `T2 = 4.227`, `T3 = 3.266`; capacity 3 crosses before its prerequisite 2 |
| **Theorem 2(a)**. soundness: every realized order is a linear extension | chain (243-point pre-declared grid) and branch |
| **Theorem 2(b)**. completeness: every linear extension is realized | 30 random DAGs; rates decaying along a target extension σ |

## Figures

`recovery_order_verification.py <outdir>` writes the paper's figures to `<outdir>`:

| File | In the paper |
| --- | --- |
| `Figure1_Factorization.png` | Fig. 1 |
| `Figure2_RecoveryLaw.png` | Fig. 2 |
| `Figure3_Ordering.png` | Fig. 3 |
| `FigureS1_Saturation.png` | Supplementary Fig. S1 |
| `FigureS2_Counterexample.png` | Supplementary Fig. S2 |
| `FigureS3_Motor.png` | Supplementary Fig. S3 |
| `FigureS4_Curriculum.png` | Supplementary Fig. S4 |
| `derivation_verification_report.json` | machine-readable summary: the outcome of each check as a boolean, with its principal numbers |

## Reproducibility

Every random draw goes through `numpy.random.default_rng` with a fixed seed, and the
ancestor sets are sorted, so results are **reproducible to the printed precision in the documented
environment (Python 3.11, numpy 2.4, matplotlib 3.10) and to numerical tolerance across machines**,
independently of `PYTHONHASHSEED`. The expected output is:

```
Monte-Carlo == closed-form        : max rel err 2.49%
support(I_k) == prerequisite graph: True
distinct orderings                : 1 of 120
non-increasing rates              : 0/108 violations
increasing rates                  : 102/108 violations
onset precedence                  : 0/120 violations
reversal boundary                 : r0=0.0:1.64 ... r0=0.7:1.22
common closure dropped            : T_parent = 48.98, T_child = 2.83; child first
RK4 vs separation of variables    : agree (2.8295 vs 2.8294)
paired closure sweep (48 edges)    : differing closure 48/48 reverse, common closure 0/48
product gate / min gate           : 0/200, 0/200 violations; same admissible set
non-nested gate (G4 dropped)      : T1 = 1.946, T2 = 4.227, T3 = 3.266; capacity 3 first
completeness                      : 30/30 targets realized (ratio 0.5 and 0.3; 14/30 at 0.9);
                                    grid ties 0, unreached 0, order unchanged at half step 30/30 at every ratio
accumulation, 20,000 obs (seed 1) : 6021 vs 6001 (0.33%)
accumulation, 6,000 obs (seed 3)  : 1791.5 vs 1801.0 (0.53%; the run plotted in Fig. 2a)
```

## Note on what the code does *not* do

The dependency graph is an **input**, not an output. The ancestor sets are supplied as the
premise of Theorem A; the checks confirm that the Fisher information has the *derived*
support and magnitude. They do **not** discover the edges by search. Identifying the graph
from a system's geometry is the paper's central open problem.

## Citation

If you use this code, please cite the paper and the archived release:

> Saito, H. recovery-order-theory: numerical verification for "Recovery order in adaptive
> systems is set by dependency structure". *Zenodo* <https://doi.org/10.5281/zenodo.21829215> (2026).

The concept DOI `10.5281/zenodo.21829215` always resolves to the latest version. The current
release is v1.0.4.

## Changelog

- **v1.0.4** (2026-10-21). The completeness check (Theorem 2(b)) now also verifies that every realized
  target extension is strictly separated: all milestones reached, no two milestone times coinciding on
  the integration grid, and the order unchanged when the step is halved; the result is printed, written
  to the JSON report and included in the overall pass/fail conjunction. No numerical result changed.
- **v1.0.3** (2026-10-15). Added the non-nested-gate counterexample (sharpness of G4) and the
  6,000-observation accumulation run plotted in Fig. 2a to the printed output, the JSON report
  and the overall pass/fail conjunction, so that every simulated quantity quoted in the paper is
  regenerated by the script. Figure axis labels and titles revised. No numerical result changed.
- **v1.0.2** (2026-07-17). Release accompanying the submitted manuscript.

## License

MIT. See `LICENSE`.
