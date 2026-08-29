# Phase 3 — Task 2: Predictive Validity (RQ2 / H4)
**Project:** *One Capability or Many? Testing the Economic Validity of Frontier AI Benchmarks*
**Louis Yiven Zhu** · Department of Statistics, London School of Economics
**Status: COMPLETE** · Consumes the n=421 Phase-1 dataset; analysis grid n=96 (complete-core).

---

## 1. Question and design
**RQ2:** does multidimensional capability structure improve out-of-sample prediction of economic-benchmark
scores over simpler representations? **H4:** k-factor predictors beat scale/date-only and single-index
baselines on held-out economic benchmarks — bootstrap 95% CI of ΔMSE excludes zero on the pooled economic
block **and** on the majority of its individual columns.

## 2. Protocol — Leave-One-Benchmark-Out (LOBO), nested CV
For each of the 12 target benchmarks, the *other 11* form the predictor pool (the held-out column is never
an input — the leakage rule). All predictors are constructed **inside each training fold only**:
per-column z-standardisation → ML-EFA (oblimin, k=3) factors estimated on training models and projected onto
test models via Thurstone regression weights (Σ⁻¹Λ). Outer **5-fold** cross-validation gives out-of-fold
predictions; inner **5-fold** `GridSearchCV` tunes each learner. Targets and predictors are standardised by
training-fold statistics so RMSE is reported in z-units.

**Predictor ladder (five rungs):** (i) release timing + scale; (ii) mean capability index; (iii) first
factor only; (iv) k factors; (v) k factors + covariates (reasoning flag, log-parameters with a
missingness indicator, open-weights). **Learners:** ridge, elastic-net, random forest, gradient boosting
(240 model configurations; ~13 min full run).

*Covariate note (transparency).* The design's rung-(v) "cost" covariates (per-task token usage, turns)
are populated only on a small flagship subset of the AA data and are absent from the parsed public-page
snapshot, so rung (v) uses the broadly-covered model covariates above as the cost/scale proxy. This is a
faithful substitution of the same construct (deployment cost/scale), disclosed rather than silent.

## 3. Results
**Predictor ladder (economic block, best learner per rung, LOBO test RMSE, z-units):**
| Rung | Predictors | Best learner | Test RMSE | Test R² |
|---|---|---|---|---|
| (i) | timing/scale | random forest | 0.741 | 0.459 |
| (ii) | mean index | ridge | 0.474 | 0.771 |
| (iii) | first factor | elastic-net | 0.950 | 0.110 |
| **(iv)** | **k factors** | **ridge** | **0.433** | **0.808** |
| (v) | k factors + cost | ridge | 0.438 | 0.800 |

Train and test RMSE track closely at the informative linear rungs (e.g. rung iv: train 0.410 vs test 0.433,
a 6% relative gap) — no material overfitting there. The exception is rung (i), whose random-forest baseline
shows a larger train–test gap (0.521 vs 0.741, ~42%), as expected for a flexible learner on a weak
timing/scale signal; it is the least predictive rung regardless. The single-factor rung (iii) predicts
*poorly*: one dimension is not enough, which is the first sign the economic benchmarks need more than the
dominant general axis.

**H4 — bootstrap ΔMSE (baseline − k-factor; positive ⇒ k-factor predicts better), 95% CI, B=2000:**
| Contrast (pooled economic) | ΔMSE | 95% CI | Excludes 0 |
|---|---|---|---|
| k-factor vs date/scale (i) | +0.373 | [+0.280, +0.472] | **yes** |
| k-factor vs mean-index (ii) | +0.037 | [+0.019, +0.055] | **yes** |
| k-factor vs first-factor (iii) | +0.714 | [+0.599, +0.839] | **yes** |

**Per-column (k-factor vs the demanding single mean-index baseline):**
| Economic benchmark | ΔMSE | 95% CI | Favours k-factor |
|---|---|---|---|
| GDPval (Elo) | +0.026 | [+0.010, +0.044] | **yes** |
| Terminal-Bench v2.1 | +0.028 | [+0.001, +0.056] | **yes** |
| τ³-Banking | +0.056 | [+0.008, +0.103] | **yes** |
| τ²-Bench | +0.038 | [−0.011, +0.086] | no (CI includes 0) |

**Verdict: H4 SUPPORTED** — pooled CI excludes zero and **3 of 4** economic columns favour the k-factor
representation. Only τ²-Bench (the most general-loading economic benchmark) is inconclusive.

### Selected hyperparameters (inner-CV grid search)
The inner 5-fold grid search selects hyperparameters per fold and per target. We report the modal choice
for the winning learner at each rung, taken across all 12 LOBO targets; the final column gives that modal
setting's agreement count (of 12). The setting is a plurality, not a consensus — agreement is 5–8 of 12,
and individual targets often differ (full per-target/fold selections in `lobo_hyperparameters_full.csv`,
240 rows).

| Rung | Learner | Modal hyperparameters | Modal agreement (of 12 targets) |
|---|---|---|---|
| (i) timing/scale | random forest | max_depth=4, min_samples_leaf=3, n_estimators=300 | 8/12 |
| (ii) mean index | ridge | alpha=0.03 | 6/12 |
| (iii) first factor | elastic-net | alpha=0.03, l1_ratio=0.2 | 5/12 |
| (iv) k factors | ridge | alpha=0.03 | 6/12 |
| (v) k factors + cost | ridge | alpha=1 | 6/12 |

The winning summary is `lobo_hyperparameters.csv`. The ridge factor rungs (ii, iv) most often select light
regularisation (alpha=0.03); the covariate rung (v) most often selects alpha=1. Selections vary across
targets — e.g. ridge alpha=1 and alpha=3 also win for some individual targets — as expected on the modest
n=96 grid; the H4 verdict is unaffected because it rests on the fold-averaged out-of-fold errors, not on a
single hyperparameter setting.

## 4. Explainability
Permutation importance and ridge coefficients agree that **F1 (the economic/agentic factor) is the leading
predictor** of every economic benchmark (permutation importance 6.4–9.0 across targets), but F2 (academic)
and F3 (reasoning) each contribute materially — this multi-factor dependence is precisely why rung (iv)
beats the scalar rung (ii). SHAP on a gradient-boosting model for GDPval ranks F1 and the reasoning flag
as the top two drivers, with F3 and log-parameters secondary. Out-of-fold diagnostics for GDPval:
R² = 0.88, residuals show no systematic trend. **Error analysis (named):** the largest residuals are
interpretable — e.g. *Grok 4.3 (Non-reasoning)* scores higher on GDPval than its factor profile predicts,
while several small open-weight models (Gemma 3, Mistral Small) under-perform their profile.

## 5. Interpretation (positionality & directionality)
All factors are oriented so higher = better, and ΔMSE is signed so positive favours the richer
representation. The economic signal that Phase 2 located in *loading* space — small, and not endorsed by
parallel analysis — nonetheless carries **reliable out-of-sample predictive value**: knowing a model's full
factor profile predicts its economic-benchmark scores better than knowing a single capability index. The
effect is modest in magnitude (a few percent of MSE against the mean-index) but statistically robust. This
is the study's consistent, qualified conclusion across both tasks: **economic benchmarks are not merely a
relabelling of general capability — they add a distinct, predictively useful component — but that component
is small relative to the shared frontier-progress trend, so current economic benchmarks provide limited
*discriminant* leverage.**

## 6. Files produced
- `notebook/03_task2_prediction.ipynb` — reproducible LOBO pipeline (verified; fast ridge path reproduces the verdict, `FULL=True` runs all learners)
- `figures/fig5_prediction.png` — Figure 4: predictor ladder, H4 forest plot, SHAP importances
- `figures/fig6_diagnostics.png` — Figure 5: predicted-vs-observed, residuals, named error analysis
- `data/processed/`: `lobo_metrics_full.csv`, `lobo_rung_summary.csv`, `h4_bootstrap_dmse.csv`,
  `task2_prediction_results.json`, `task2_explainability.csv`, `task2_shap_gdpval.csv`,
  `task2_error_analysis_gdpval.csv`
