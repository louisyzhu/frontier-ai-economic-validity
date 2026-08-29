# Phase 1 — Data Extraction, Access & Density Audit
**Project:** *One Capability or Many? Testing the Economic Validity of Frontier AI Benchmarks*
**Louis Yiven Zhu** · Department of Statistics, London School of Economics
**Snapshot date (UTC):** 2026-07-06 · **Status: GATE PASSED**

---

## 1. Purpose of this phase
Phase 1 is the pre-registered feasibility gate defined in the research design §7: confirm the two
external data sources are reachable and licensable, extract and version-pin a timestamped snapshot,
and audit whether benchmark/model density is sufficient to run the two locked ML tasks. No modelling
is performed here; the deliverable is the **submitted dataset** plus this audit.

## 2. Access & licensing outcome
| Source | Role | Access route | Result |
|---|---|---|---|
| **Artificial Analysis** — Models Leaderboard | Dense core | Data API requires a key (HTTP 401). Fell back to the design's named **public-page extraction** route: the server-rendered React (Next.js RSC) flight payload embeds the full model array. | **548 model configs** extracted |
| **Epoch AI** — Notable AI Models | Periphery / compute+date metadata (sensitivity only) | Public CSV, **CC-BY 4.0** | **1,033 models**, 528 with training-compute FLOP |

Both domains were outside the default network allowlist and were explicitly granted before extraction.
Raw bytes of both sources are stored with SHA-256 manifests under `data/raw/` — these fixed snapshots
**are** the submitted dataset (the live pages will drift).

## 3. What was extracted (dense core)
One row per model configuration at its default setting. Per-benchmark scores plus metadata:
release date (547/548), open-weights flag (548/548), creator + country (548/548),
total/active parameters (338/548), AA Intelligence Index (535/548), and GDPval Elo **with 95% CIs**
(117 configs in the raw 548-model set; 112 within the n=421 analysis set) — the CI field feeds the
error-variance weighting in Task 2.

## 4. Density audit (the binding result)
Coverage is strongly **two-tier**. After applying the pre-registered inclusion floors
(benchmark kept if ≥60 models score it; model kept if it carries ≥8 of the kept benchmarks):

- **13 of 14 benchmarks pass.** Only **APEX-Agents (26 models)** drops — exactly the benchmark the
  design flagged as "included only if retrievable."
- **421 of 548 models pass** the ≥8-benchmark rule → **n = 421**, comfortably above the design's
  n≈180–220 target.

**The economic block is the binding constraint,** and this is the analytically important finding:

| Block | Benchmarks (n models) |
|---|---|
| Economic | τ²-Bench (413), **Terminal-Bench v2.1 (121)**, **GDPval Elo (112)**, **τ³-Banking (112)** |
| Academic | GPQA Diamond (421), HLE (421), AA-Omniscience (418), *MMMU-Pro (197, multimodal)* |
| Scientific-coding | SciCode (421), CritPt (419), Terminal-Bench Hard (413) |
| Long-context / IF | AA-LCR (420), IFBench (417) |

The general battery sits at ~413–421 models; the work-realistic economic benchmarks
(GDPval, Terminal-Bench v2.1, τ³-Banking) sit at ~112–121. This yields two analysis grids:
1. **Broad grid (n≈421):** all 11 dense benchmarks — economic block represented by τ²-Bench.
2. **Economic-dense grid (n≈103–112):** models additionally carrying GDPval + Terminal-Bench v2.1 +
   τ³-Banking (**103 with all three**) — the subset where H3 (economic distinctiveness) and H4
   (predictive validity on the economic block) are actually testable.

**MMMU-Pro** (multimodal, 197) is demoted to **sensitivity-only**: it is half-covered and multimodal,
so including it in the primary factor model would confound coverage with a modality axis.

## 5. Compute / release-timing covariates (for H2)
- **Release date: 420/421** (one kept model has a null date) — the primary scale/timing axis H2 hinges
  on is essentially complete; the single missing row is dropped from any date-conditioned analysis.
- **Total parameters: 272/421** in the analysis set (AA-native) — the primary scale proxy.
- **Epoch join** for training-compute FLOP is low by name-match (61/421 via short name) because
  frontier proprietary models (GPT-5, Gemini 3.5, Claude family) are largely absent from Epoch's
  notable-models list. This is the pre-listed limitation ("missing compute for closed models;
  release date conflates scale with algorithmic progress") and is why Epoch stays **sensitivity-only**
  and release date + AA parameters carry the primary H2 test.

## 6. Gate decision
**PASS.** Both tasks are feasible on the extracted snapshot:
- **Task 1 (unsupervised, ≥3 methods):** PCA + ML-EFA + clustering on the broad grid (n≈421, 11
  benchmarks) with residualisation on release-date/parameters; economic distinctiveness tested on the
  economic-dense grid.
- **Task 2 (regression, ≥3 learners):** Leave-One-Benchmark-Out prediction of economic-benchmark
  scores, feasible on the ≥100-model economic-dense subset with a scale/date-only baseline.

The one scope adjustment forced by the data — MMMU-Pro to sensitivity, APEX-Agents dropped — is minor
and was anticipated in the design. No pre-registered hypothesis is affected.

## 7. Files produced this phase
- `data/raw/aa_leaderboards_models_snapshot.html` (+ `aa_snapshot_manifest.json`) — AA raw snapshot, SHA-256 pinned
- `data/raw/epoch_notable_ai_models_snapshot.csv` (+ `epoch_snapshot_manifest.json`) — Epoch raw snapshot, CC-BY
- `data/raw/aa_models_raw_parsed.csv` — all 548 configs parsed
- `data/processed/aa_analysis_models.csv` — **n=421 analysis-ready dataset**
- `data/processed/density_audit.csv`, `overlap_matrix.csv`, `benchmark_taxonomy.csv`
- `figures/fig_phase1_density.png` — Figure P1 (coverage + overlap)
- `notebook/01_phase1_extraction.ipynb` — reproducible extraction + audit notebook
