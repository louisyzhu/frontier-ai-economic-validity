# One Capability or Many? — Testing the Economic Validity of Frontier AI Benchmarks
**Louis Yiven Zhu** · Department of Statistics, London School of Economics

Reproducibility bundle for the submitted paper. All results trace to the SHA-256-pinned data snapshots
via a single Jupyter notebook.

## Folder layout
```
aa_benchmark_validity/
├── README.md                     # this file
├── GITHUB_SETUP.md               # how to publish this bundle + compile on Overleaf
├── requirements.txt              # pinned Python environment
├── paper/                        # LaTeX source (compile on Overleaf with pdfLaTeX)
│   ├── paper_singlecol.tex       # single-column build (NeurIPS-like)
│   ├── paper_twocol.tex          # two-column build (ICML/IEEE-like)
│   ├── frontmatter.tex body.tex appendix.tex   # shared content
│   ├── thebibliography.tex references.bib       # references (embedded + BibTeX)
│   ├── alg_lobo.tex tab_*.tex    # algorithm box and result tables
│   ├── STYLE_SPEC.md LINT_REPORT.md             # style decisions + structural lint
│   └── references.json           # 21-entry reference list (source of the .bib)
├── notebook/
│   └── analysis.ipynb     # single notebook: extraction, EDA+Task1, Task2, all figures
├── data/
│   ├── raw/                      # timestamped, SHA-256-pinned source snapshots = THE SUBMITTED DATASET
│   │   ├── aa_leaderboards_models_snapshot.html
│   │   ├── epoch_notable_ai_models_snapshot.csv
│   │   ├── aa_snapshot_manifest.json / epoch_snapshot_manifest.json
│   │   └── aa_models_raw_parsed.csv               # all 548 configs, flattened
│   └── processed/                # analysis set + every result table (see below)
│       ├── aa_analysis_models.csv                 # n=421 analysis-ready dataset
│       ├── loadings_raw.csv / loadings_residualised.csv
│       ├── task1_structure_results.json / hypothesis_adjudication.csv / parallel_analysis.csv
│       ├── cluster_validation.csv / model_cluster_profiles.csv / benchmark_hclusters.csv
│       ├── lobo_rung_summary.csv / lobo_metrics_full.csv / h4_bootstrap_dmse.csv
│       ├── lobo_hyperparameters.csv (+ _full.csv) / task2_prediction_results.json
│       ├── task2_explainability.csv / task2_shap_gdpval.csv / task2_error_analysis_gdpval.csv
│       └── density_audit.csv / overlap_matrix.csv / benchmark_taxonomy.csv
├── figures/
│   ├── fig1_eda.png fig2_loadings.png fig3_clusters.png fig4_prediction.png fig5_diagnostics.png
│   ├── fig1_eda … fig5_diagnostics.png            # five main-text figures
│   ├── suppl_scree_parallel.png suppl_benchmark_dendrogram.png suppl_eda_distributions.png appxfig_density.png  # appendix figures
│   └── figA1_scree.png figA2_dendrogram.png fig_eda_distributions.png fig_phase1_density.png  # notebook-generated source renders
└── PHASE{1,2,3}_REPORT.md        # per-phase methodology and adjudication records (working notes)
```

## Reproducibility

Open `notebook/analysis.ipynb` and run all cells. It runs the entire pipeline from the
SHA-256-pinned snapshots in `data/raw/` — extraction and the coverage gate, EDA, the Task-1
structure analysis, the Task-2 LOBO prediction ladder, the SHAP attribution behind Figure 3c,
and the registered R1 base-model deduplication with its H4 re-run — regenerating every figure
in `figures/`. No live network call and no manual step is required, every random seed is set,
and no path leaves the repository.

**Learner sweep.** Cell 41 sets `FULL=True` by default: all four registered learners run and the
ladder produces all 240 per-target metric rows. Set `FULL=False` for a ridge-only fast path
(same numbers on the ridge subset) when iterating.

**Runtime.** About 26 minutes end to end with `FULL=True` on a laptop-class 12-core
machine; about 75 seconds with `FULL=False`. The two nested-CV sweeps (the ladder in cell 41 and
the hyperparameter table in cell 45) account for nearly all of it.

**Self-verification.** Three cells check their own output against the shipped tables: the
ladder (cell 41), the SHAP attribution, and the deduplication block. The SHAP and
deduplication tables reproduce exactly (deviation 0).

The ladder check asserts on **the quantities the paper prints**, not on intermediate cells.
On every run it recomputes, from the freshly fitted ladder, the four per-target and pooled
economic ΔMSE values and the five rows of the ladder table for the economic block (train
RMSE, test RMSE and R² at the best learner per rung), compares each against the canonical
value, and fails at 5e-4 — half a unit at the third decimal, the precision the paper prints.
It separately asserts that the best-learner ranking behind that table's learner column is
unchanged. **A run that passes cannot have altered any reported value**, and the canonical
`lobo_metrics_full.csv` is never written: the refitted table goes to
`data/processed/lobo_metrics_full_rerun.csv` alongside it.

Individual ladder cells are reported as a per-rung and per-learner deviation table, and are
deliberately not fatal. The first-factor rung, rung (iii), can differ across platforms: the
maximum-likelihood factor solution has a weakly determined leading direction for this
correlation structure, so the fit can settle on a slightly different point, and tree learners
carry that difference most visibly because a split threshold either flips or does not. This
has been observed at up to 7.2e-4 on one machine and 5.8e-6 on another, while the mean-index
and k-factor rungs that every reported quantity is built from agreed to ~1e-7 on both. A
divergence there is a platform fact, not a broken pipeline, and the assertions above are what
decide whether it matters.

**Tables that ship without notebook derivation.** Twelve result tables are read by the notebook
rather than recomputed in it: `loadings_raw.csv`, `loadings_residualised.csv`,
`task1_structure_results.json`, `cluster_validation.csv`, `lobo_rung_summary.csv`,
`h4_bootstrap_dmse.csv`, `task2_error_analysis_gdpval.csv`, `k_selection_evidence.csv`,
`ksweep_rung_iv.csv`, `efa_factor_correlations.csv`, `efa_uniquenesses.csv` and
`eda_distribution_stats.csv`. The notebook computes and prints the structural quantities behind
the first four, and prints every value it reads, so each is checkable against the notebook's own
output.

**GitHub:** `https://github.com/louisyzhu/frontier-ai-economic-validity`

## Licence and attribution

**Code** (the notebook and any scripts): MIT — see [`LICENSE`](LICENSE).

**Derived data** (everything in `data/processed/`, plus `data/raw/aa_models_raw_parsed.csv`):
CC BY 4.0 — see [`LICENSE-DATA`](LICENSE-DATA).

**Redistributed source snapshots** in `data/raw/` keep their upstream terms and are *not*
covered by the CC BY 4.0 grant:

- `epoch_notable_ai_models_snapshot.csv` — Epoch AI, *Notable AI Models*, CC BY 4.0.
  Attribution to Epoch AI is required and is preserved here and in the paper.
- `aa_leaderboards_models_snapshot.html` — Artificial Analysis, *Models Leaderboard*,
  redistributed under that site's terms as a dated verbatim snapshot so the published
  results can be verified. All capability and price figures derived from it originate with
  Artificial Analysis.

Both snapshots carry a manifest in `data/raw/` recording the capture timestamp and SHA-256.
Neither upstream source endorses this work or its conclusions.

## How to cite

Cite the paper, [arXiv:2608.29420](https://arxiv.org/abs/2608.29420), and cite this
repository for the code and pinned data:

```bibtex
@misc{zhu2026onecapability,
  author = {Zhu, Louis Yiven},
  title  = {One Capability or Many? Testing the Economic Validity of Frontier AI Evaluation},
  year   = {2026},
  eprint = {2608.29420},
  archivePrefix = {arXiv},
  url    = {https://arxiv.org/abs/2608.29420}
}

@misc{onecapability_code,
  author = {Zhu, Louis Yiven},
  title  = {One Capability or Many? Testing the Economic Validity of Frontier AI Evaluation
            --- code and pinned data},
  year   = {2026},
  howpublished = {\url{https://github.com/louisyzhu/frontier-ai-economic-validity}}
}
```

## Data provenance (transparency)
| Source | URL | License | Role | Snapshot (UTC) |
|---|---|---|---|---|
| Artificial Analysis — Models Leaderboard | https://artificialanalysis.ai/leaderboards/models | site terms; public page | Dense core (scores) | 2026-07-06 |
| Epoch AI — Notable AI Models | https://epoch.ai/data/notable_ai_models.csv | CC-BY 4.0 | Periphery / compute metadata | 2026-07-06 |

Every downstream number traces to the pinned raw snapshots via `notebook/analysis.ipynb`.
The AA Data API requires a key (HTTP 401); the notebook uses the public-page extraction route and stores
the exact bytes so results are stable regardless of live-page drift.

## Dataset at a glance
- **548** model configurations parsed → **421** retained (≥8 of 13 kept benchmarks).
- **12 primary benchmarks** across 5 taxonomy blocks (Economic, Academic, Scientific-coding,
  Long-context, Instruction-following); MMMU-Pro held for sensitivity; APEX-Agents dropped (<60 models).
- Economic-dense subset: **103** models pass the density gate, carrying all three sparsely scored economic benchmarks (GDPval Elo, Terminal-Bench v2.1, τ³-Banking). Task 2 runs on the **96**-model complete-case grid, which carries all twelve primary benchmarks.

## Status
- [x] **Phase 1** — extraction, access & licensing audit, density gate → **PASS**
- [x] **Phase 2** — EDA + Task-1 unsupervised structure (PCA / ML-EFA / clustering; date residualisation)
      → **RQ1 adjudicated**: H1 supported (74.5%), H2(i) supported (logistic-fit date-R²=0.505; OLS 0.477), H2(ii) not met
      (14.9pp < 15pp), H3 supported under over-extraction (PA retains k=1). See `PHASE2_REPORT.md`.
- [x] **Phase 3** — Task-2 predictive validity (LOBO regression, nested 5×5 CV, 4 learners)
      → **H4 SUPPORTED**: k-factor beats single mean-index on pooled economic block (ΔMSE +0.037,
      95% CI [+0.019,+0.055]) and 3/4 economic columns; far larger gain over date/scale. See `PHASE3_REPORT.md`.
- [x] **Phase 4** — paper written as **LaTeX** in two column layouts (`paper_singlecol.tex`,
      `paper_twocol.tex`) sharing one body, appendix, and bibliography. Times New Roman via newtx,
      clickable numeric citations via hyperref, booktabs tables, one-per-line equations. Byline
      **Louis Yiven Zhu** only. Structure: title + abstract, ToC + lists, body (intro→conclusion),
      references, appendix (math derivations, coverage audit, hyperparameter grid, robustness, provenance,
      supplementary figures). Compile on Overleaf with pdfLaTeX (see `GITHUB_SETUP.md`).

## Deliverables for submission
1. `paper/paper_singlecol.tex` and `paper/paper_twocol.tex` — the manuscript in both layouts, with
   shared `body.tex`, `appendix.tex`, `frontmatter.tex`, `thebibliography.tex`, `references.bib`, and
   float files. Compile with pdfLaTeX on Overleaf.
2. `notebook/analysis.ipynb` — the single consolidated notebook, with all outputs and figures
   rendered inline.
3. `data/raw/` — SHA-256-pinned data snapshots (the dataset); `data/processed/` — all result tables.
4. `PHASE{1,2,3}_REPORT.md` — per-phase methodology and adjudication records (supporting working notes).
