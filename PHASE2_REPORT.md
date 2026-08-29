# Phase 2 — EDA & Task 1: Unsupervised Structure (RQ1)
**Project:** *One Capability or Many? Testing the Economic Validity of Frontier AI Benchmarks*
**Louis Yiven Zhu** · Department of Statistics, London School of Economics
**Status: COMPLETE** · Consumes the n=421 Phase-1 dataset.

---

## 1. What this phase does
Adjudicates **RQ1** — *do economically oriented benchmarks measure a latent capability distinct from
general benchmark performance, once model scale and release timing are controlled?* — using the three
pre-registered unsupervised methods (PCA, ML-EFA with oblimin rotation, clustering) and the four
pre-specified quantitative tests (H1, H2(i), H2(ii), H3). No thresholds were adjusted post hoc; the one
knife-edge result (H2(ii)) is reported exactly as it fell.

## 2. Data grids and preprocessing
Per-benchmark z-standardisation (primary). Two complete-case grids from the Phase-1 coverage structure:
- **G1 — complete-core (n = 96):** all 12 primary benchmarks scored. The only grid on which the economic
  block (GDPval, Terminal-Bench v2.1, τ³-Banking) is fully present, so all of H1–H3 are tested here.
- **G2 — high-n dense (n = 409):** the 9 benchmarks with ≥ 409 models; used for model clustering and as a
  stability complement.

Sampling adequacy on G1 is excellent (**KMO = 0.933**; Bartlett's sphericity p < 10⁻³⁰⁰), so factor
analysis is strongly warranted. Mean off-diagonal rank correlation across the 12 benchmarks is **ρ = 0.79**
— the battery is highly collinear before any modelling.

## 3. Results — hypothesis by hypothesis
| Hypothesis | Pre-registered test | Value | Verdict |
|---|---|---|---|
| **H1 — Dominance** | first-factor communal-variance share > 50% | **74.5%** | **SUPPORTED** |
| **H2(i) — date-driven** | dominant factor's release-date **logistic-fit** R² ≥ 0.30 **and** strongest of any factor | **0.505** (logistic, max of 0.505 / 0.364 / 0.286; OLS-R² 0.477) | **SUPPORTED** |
| **H2(ii) — share drop** | residualising on date/compute drops first-factor share ≥ 15 pp | **14.9 pp** (date-only); **9.3 pp** (date+compute, n=58) | **NOT MET** (marginal) |
| **H3 — Economic distinctiveness** | residualised factor loads ≥ 0.40 on economic benchmarks, exceeding their cross-loadings | **4/4 pass**; mean \|F1\| 0.72 vs 0.32 | **SUPPORTED**, but only under k=3 over-extraction (parallel analysis retains k=1) |

**PCA / factor count.** PC1 alone explains **79.4%** of total variance; Horn's parallel analysis retains
**k = 1** on both grids. BIC leans to k=4 (the standard PA-vs-BIC tension) — parallel analysis is the
design's primary criterion, BIC is reported as secondary.

**H1.** The raw first factor holds **74.5%** of common variance — remarkably close to the Kearns thesis's
72% calibration reference, an external validation of the whole design. Robust under rank-based EFA
(**94.5%**) and logit-transform (**91.8%**) — the Pearson figure is if anything conservative (R2).

**H2.** The mechanism is confirmed: the dominant factor is the *most* release-date-driven of all factors
(logistic-fit R² = 0.505, the pre-registered functional form; OLS-R² = 0.477 agrees), and residualising
on date compresses its share from 74.5% → 59.6%. But that compression is
**14.9 pp — fractionally under** the pre-registered 15 pp line, and only 9.3 pp on the compute-known
subsample. So H2 is **partially supported**: general frontier progress (indexed by release date) is the
main content of the dominant dimension, but it does not account for a *full* 15 pp of dominance.

**H3.** After residualisation, all four economic benchmarks concentrate on one factor (F1: GDPval 0.84,
Terminal-Bench v2.1 1.01, τ³-Banking 0.54, τ²-Bench 0.50) with near-zero cross-loadings — an
"agentic/work-realistic execution" factor distinct from an academic-knowledge factor (GPQA, IFBench,
AA-LCR, SciCode) and a reasoning factor (HLE, CritPt). The discriminant criterion passes cleanly. **The
honest caveat:** parallel analysis on the residualised matrix still retains only **k = 1**, so this
economic factor emerges only when more factors are extracted than the data statistically justify.

**Clustering (third method).** Models in factor space split into a stable **k = 2** solution
(k=2 silhouette: k-means 0.480, GMM 0.467, agglomerative 0.499 — all three methods agree k=2 is best) — two capability tiers separated
by release date (median 2025-09 vs 2026-03) and Intelligence Index (13.7 vs 37.1). Hierarchical clustering
of benchmarks places 10 of 12 — including 3 of 4 economic — in a single general block; only τ²-Bench and
IFBench branch off. The model-space structure *is* the scale/date axis, reinforcing H2.

## 4. Interpretation (positionality & directionality)
All factors are oriented to correlate positively with mean capability (higher = better), so loadings are
directly readable. The economic benchmarks sit **inside** the general-capability cloud in both loading
space and model-cluster space; they acquire a distinct position only after the date-driven general factor
is partialled out, and even then the separation is not endorsed by parallel analysis. The net reading is
the design's pre-written qualified conclusion, reached by the data rather than rescued after it:
**current economic benchmarks carry limited discriminant information beyond general frontier progress** —
a distinct capability signal exists but is small relative to the shared trend. Task 2 tests whether that
signal is nonetheless *predictively* useful (H4).

## 5. Files produced
- `notebook/02_eda_task1_structure.ipynb` — reproducible EDA + Task-1 analysis (verified end-to-end)
- `figures/fig2_eda.png` — Figure 1: correlation, score-vs-date, coverage
- `figures/fig3_loadings.png` — Figure 2: raw vs residualised loadings + variance-share compression
- `figures/fig4_clusters.png` — Figure 3: model tiers, cluster validation, benchmark dendrogram
- `data/processed/`: `hypothesis_adjudication.csv`, `loadings_raw.csv`, `loadings_residualised.csv`,
  `task1_structure_results.json`, `cluster_validation.csv`, `model_cluster_profiles.csv`,
  `benchmark_hclusters.csv`
