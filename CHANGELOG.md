# Changelog

All notable changes to `recovery_order_verification.py` and its documentation.
Numerical results are listed as unchanged only where the printed log and the seven
figures were compared byte for byte with the previous release.

## v1.0.4 (2026-10-21)

### Added
- Completeness check (Theorem 2(b)): for every decay ratio, the realized target
  extension is now also verified to be strictly separated. The script counts, over the
  30 random graphs, (i) cases in which some milestone was never reached, (ii) cases in
  which two milestone times coincide on the integration grid (such ties are otherwise
  resolved in node-index order by the stable sort), and (iii) cases in which the
  realized order changes when the step is halved. Result: 0, 0 and 30/30 unchanged at
  every ratio. Printed in the `[Theorem 2(b)]` block, written to
  `derivation_verification_report.json` under `theorem2b_completeness`
  (`grid_ties`, `unreached`, `order_unchanged_at_half_step`, `separation_ok`), and
  included in the overall pass/fail conjunction.
- `_sim_dag_gate(..., return_times=True)` returns the milestone times alongside the order.

### Unchanged
- All previously printed numbers and all seven figures (byte-identical to v1.0.3).

## v1.0.3 (2026-10-15)

### Added
- Sharpness of nestedness (G4): `verify_non_nested_gate_counterexample()` integrates the
  chain 1 -> 2 -> 3 with equal rates, common closure (1 - r), common onset 0.3 and the
  non-nested gates phi_2 = r1^8, phi_3 = sqrt(r1 r2) (RK4, dt = 1e-4). Milestone times
  at q = 0.9: T1 = 1.946 (closed form ln(0.7/0.1)), T2 = 4.227, T3 = 3.266; capacity 3
  crosses before its prerequisite 2. Printed, written to the JSON report
  (`propositionA3_nestedness_sharpness`) and included in the overall conjunction.
- The 6,000-observation accumulation run plotted in Fig. 2a (seed 3) is now recomputed
  and printed in the `[Theorem B1]` block (realized 1,791.5 vs expected 1,801.0, 0.53%)
  and written to the JSON report (`theoremB1_plotted_run`); previously these values
  were computed only inside the plotting function.

### Changed
- Figure text: fonts set to DejaVu Sans; panel titles and axis labels revised
  (Fig. 1b "Support of I_k = ancestor relation of the graph"; Fig. 2a legend
  "realized information accumulation" / "expected flux dP_k/dt = I_k", y-label
  "accumulated information P_k"; Fig. 2b, S1b, S3, S4 titles; Fig. 3c title "(q = 0.9)",
  closed-form overlay c_k/c_j = L/(L - (q - r0)) added, label "c_k <= c_j"); 300 dpi.
- Printed label "exact conjugate P_final" renamed "realized accumulated information
  P_final"; comments and docstrings describing the accumulation check reworded to match
  the manuscript (the quantity is the accumulated information, which equals the
  posterior precision exactly only for a Gaussian-mean channel).
- Reproducibility statement reworded: results are reproducible to the printed precision
  in the documented environment (Python 3.11, numpy 2.4, matplotlib 3.10) and to numerical
  tolerance across machines, independently of `PYTHONHASHSEED`.
- README and CITATION.cff now cite the GitHub-linked Zenodo concept DOI
  10.5281/zenodo.21829215 (previously 10.5281/zenodo.21351889, a separate manually
  uploaded record).

### Unchanged
- All numerical results of v1.0.2 (log identical apart from the added lines).

## v1.0.2 (2026-07-17)

- Release accompanying the submitted manuscript: Theorems A, B, 1-3 and Proposition A.3
  verified; Figs. 1-3 and Supplementary Figs. S1-S4 generated; JSON report written.

## Earlier releases

- GitHub releases tagged v1 to v4 (v4 published to Zenodo on 2026-10-01) preceded the
  semantic version numbers used in CITATION.cff; their contents are those of v1.0.2 and
  its predecessors.
