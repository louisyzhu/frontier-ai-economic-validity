# Examiner-revision change summary

This revision responds to the two examiner reports on the round-3 PDF (`aa_bench_val.pdf`).
Every change below is reflected in the source `.tex` files and the re-executed notebook
(`notebook/analysis.ipynb`). Compile `paper/paper_singlecol.tex` on Overleaf.

## A. Statistics: registration-honest re-adjudication

| Hypothesis | Round-3 verdict | Revised verdict | Basis |
|---|---|---|---|
| H1 (one dominant factor) | SUPPORTED | **SUPPORTED** | first-factor communal share 74.5%, PC1 79.4%, parallel analysis retains k=1 |
| H2(i) (dominant factor predicts economic) | SUPPORTED | **SUPPORTED** | logistic-fit R²=0.505 ≥ 0.30, strongest predictor |
| H2(ii) (date drops share ≥15pp) | NOT MET (marginal) | **INDISTINGUISHABLE FROM THRESHOLD** | point drop 14.9pp; bootstrap 95% CI [-5.45, +32.73] straddles 15; P(≥15)=0.35 (B=2000, seed 42) |
| H3 (economic benchmarks discriminant under k=3) | SUPPORTED | **NOT SUPPORTED AS REGISTERED** | k=3 is selected by no registered rule; reframed as pre-declared exploratory over-extraction, with predictive rescue via H4 |
| H4 (k-factor beats single index) | SUPPORTED | **SUPPORTED** | pooled ΔMSE +0.037 [0.019, 0.055]; target-clustered [0.026, 0.041] excludes zero |

New uncertainty statistics added (all in `data/processed/` and reproduced in the notebook):
- **H2(ii) bootstrap CI** on the residualisation drop (`bootstrap_h2_drop.csv`).
- **R1 deduplication** to one row per base model (`dedup_r1_comparison.csv`): config-level
  n=96 drop 14.9pp vs base-model n=89 drop 24.1pp. The config level is retained for the
  primary analysis as the more conservative choice; R1 is reported as robustness.
- **Target-clustered ΔMSE bootstrap** treating the four economic targets as clusters
  (`deltamse_clustered_bootstrap.csv`, B=5000, seed 42).
- **Oblique factor correlations and uniquenesses** (`efa_factor_correlations.csv`,
  `efa_uniquenesses.csv`), documenting the mild Heywood case on Terminal-Bench v2.1.
- **k-selection evidence table** (`k_selection_evidence.csv`): parallel analysis, Kaiser,
  and scree all retain k=1; BIC minimises at k=4; the analysis uses k=3 as a declared
  exploratory over-extraction. The k rule is stated explicitly in the methods.

## B. The k rule, stated explicitly

The paper now states that k=3 is not selected by any registered rule (parallel analysis,
the primary rule, retains k=1), and is used deliberately as a pre-declared exploratory
over-extraction to expose sub-structure that a one-factor solution collapses. The
discriminant reading of the economic block under k=3 is therefore labelled exploratory,
and the confirmatory weight is carried by the out-of-sample H4 predictive test.

## C. Honesty and transparency

- **Deviations-from-design paragraph** added, listing every departure from the locked
  research design (rung (v) covariate proxy in place of absent per-task token/turn data;
  the k=3 exploratory extraction; the config-level primary unit).
- **Robustness-suite mapping** made honest: R1 (dedup) is run and reported; R3–R6 are
  named as not run, with reasons, in the appendix.
- **Foil citation removed.** The related-work foil (arXiv:2603.02540, from the registered R4
  plan) was found to resolve to a real but unrelated paper on the neuropsychological evaluation
  of LLMs, not a single-factor benchmark analysis. Rather than misattribute it, the citation was
  removed entirely; §1.3 now motivates the date-and-scale correction directly. The reference list
  went from 22 to 21 entries, all cited.

## D. Presentation and defects fixed

- Cross-loading caption stated as a value ("largest economic cross-loading of 0.38", the 2-dp figure used in the text and Table 1; computed 0.383), consistent across caption, text, and table.
- Rung (v) relabelled "k factors + covariates" (was "cost") throughout.
- Software attribution split correctly: learners + cross-validation use scikit-learn, the
  factor analysis uses `factor_analyzer`, SHAP uses its own reference implementation.
- Algorithm box retitled a captioned Procedure figure; label/reference consistent.
- Abstract 421-vs-96 gloss clarified; keywords line added under the abstract.
- Main text cut toward the 10-page limit by moving the clustering figure, the diagnostics
  figure, the algorithm box, and the coverage/battery table to the appendix.

## E. Reproducibility bundle

`aa_benchmark_validity.zip` contains: `paper/` (all `.tex`, both single- and two-column
mains), `notebook/analysis.ipynb` (re-executed, all outputs and figures inline),
`figures/`, `data/raw/` (snapshot manifests), `data/processed/` (all result CSVs including
the new uncertainty statistics), `requirements.txt` (pinned), and the phase reports.

### Overleaf compile recipe
1. New Project → Upload Project → `aa_benchmark_validity.zip`.
2. Set the main document to `paper/paper_singlecol.tex` (Menu → Main document).
3. Compiler: pdfLaTeX. Run twice so `\ref`/`\cite` numbers resolve.
4. The `figures/` and `../figures/` paths both resolve via `\graphicspath`.

## F. Second examiner round (compliance, technical faults 4–9, language, page limit)

**Compliance.**
- **SHA-256 typesetting.** The full 64-character hash was overflowing the text width and
  rendering as ~41 visible characters. It is now set with `\seqsplit` (breakable monospace),
  so all 64 characters print. The §2.1 short form was corrected from `...04de` to `...04de6`
  to match the true hash ending. `seqsplit` added to both mains.
- **Page limit.** Two problems addressed by typographic tightening, not content cuts (per the
  brief that content matters more than a strict page count). The front-matter lists (ToC + list
  of figures + list of tables) that spilled onto page 3 are now compacted with `tocloft` and set
  `\small`, fitting page 2. The body was tightened from eleven toward ten pages via margins
  (0.9→0.85 in vertical, 0.95→0.88 in horizontal), `\parskip` (0.25→0.15 em), and the prose pass
  below. Exact page count is confirmed on the Overleaf compile.

**Residual technical faults (4–9).**
- **(4) Unit-dependent H2(ii) verdict now in the main text.** §5.2 states plainly that under the
  registered base-model unit the drop is 24.1 points and passes, under the deviated
  configuration-level primary it is 14.9 and fails, and on the 58-model subsample date alone gives
  16.5 and passes — two of three specifications clear the threshold. The failing configuration-level
  specification is retained as primary because it is the conservative reading, but the reversal is no
  longer confined to the appendix.
- **(5) k=3 as documented deviation.** The methods and abstract no longer describe k=3 as
  "pre-declared"; it is a documented deviation from the parallel-analysis rule, reported as such.
  A new k-sweep (appendix C7) shows the predictive advantage holds at every k in {2,3,4,5}, all
  bootstrap intervals excluding zero, with the gain growing in k, so k=3 is conservative for the
  predictive test relative to BIC's k=4.
- **(6) H4 on the deduplicated grid.** The predictive test is re-run on the 89-model base-model grid
  (`h4_bootstrap_dedup.csv`): pooled economic ΔMSE +0.038 [0.020, 0.056], matching the
  configuration-level +0.037 and excluding zero, so the gain is not an artefact of near-duplicate
  configurations. Added to §5.4 and appendix R1.
- **(7) Target-cluster bootstrap reframed as descriptive.** The four-target cluster bootstrap is
  presented as a descriptive spread over four positive estimates (a percentile interval over four
  positive means cannot include zero by construction, and 4/4 positive signs is not conventional
  significance). The inference-carrying test is the model-level bootstrap on the deduplicated grid.
- **(8) Compute-shrinks-drop anomaly explained.** §5.2 notes the date-plus-compute correction (9.3
  points) is smaller than date alone (16.5) on the 58-model subsample because compute and date are
  collinear there; the joint model is read as compute adding little beyond date, not as compute
  reversing the effect.
- **(9) Primary-grid CI acknowledged.** The main text states the primary-grid drop is indistinguishable
  from both zero and the 15-point threshold, so the temporal-confound confirmation rests on the
  subsample and deduplicated grids rather than the primary grid alone.

**Consistency sweep.**
- "removes a seventh of the general factor" corrected to "about a fifth" (14.9/74.5).
- Abstract R² corrected from 0.48 to 0.51, matching the adjudicating logistic fit (0.505).
- Appendix deduplication rule operationalised: keep the highest-intelligence-index row per base
  model after stripping reasoning, effort, and preview suffixes.

**Language (six AI-slop patterns).** A full prose pass removed stock intensifiers
("immediate and strong", "strikingly", "deliberate", "genuine", "measured reading"), the
twice-repeated "dominated by, but not reducible to", ritual justification ("is exactly what",
"which is precisely"), summarising scaffolds ("Taken together", "reusable template"), and
anaphoric openers ("This has", "These findings", "These limitations"), following the register of
the four supplied reference papers. Paragraph lengths were varied (68–167 words, median 108) to
remove the uniform slogan-like rhythm. Zero prose em-dashes remain.

**New EDA content (checklist gap).** A univariate-distribution and skewness view was added
(`fig_eda_distributions.png`, `eda_distribution_stats.csv`), showing the bounded, right-skewed
accuracies (CritPt γ=3.8, HLE γ=1.5, τ³-Banking γ=0.9) that motivate the logit robustness check.

## G. Third examiner round (compile-confirmed formatting, one-page cut, further prose)

This round was checked against the user's actual Overleaf-compiled PDF, so the targets are exact.

**Formatting (from the compiled PDF).**
- **Table of contents bold restored.** The round-4 compaction had over-ridden `\cftsecfont` with
  `\normalsize`, which stripped the default bold from section titles. Section titles and their page
  numbers are bold again (`\cftsecfont`/`\cftsecpagefont` = `\bfseries`); subsections and the figure
  and table lists stay `\small`. Before-skips and separators were tightened so the three lists fit
  page 2 alone (the compile showed the List of Tables spilling to page 3).
- **Appendix float order fixed.** The LOBO Procedure box used `\begin{figure}[tb]`, so it floated out
  of Appendix A and rendered under Appendix C in the compiled PDF. It is now `\begin{figure}[H]` (via
  the `float` package), locked in place under Appendix A. The appendix now reads cleanly A (LOBO) → B
  (positionality) → C (mathematical derivations) → D–J.

**Factor-variance derivation corrected.** Appendix C stated the first factor's common-variance share as
"the first diagonal entry of ΛΦΛ⊤ divided by its trace", which is a per-benchmark communality, not a
factor-level share. It now reads, correctly, as the first factor's sum of squared loadings over the
total, $s_1 = (\Lambda^\top\Lambda)_{11}/\operatorname{tr}(\Lambda^\top\Lambda)$, computed on the
orthogonal unrotated solution the statistic actually uses, with the numeric SS values (7.79, 2.34, 0.32
→ 74.5%) and an explicit contrast with a communality (a diagonal entry of $\Lambda\Lambda^\top$). This
matches the notebook's `factor1_share` exactly.

**One-page cut.** The compiled body ran pages 4–14 (eleven). The two sections the examiner named were
each trimmed by about a third with no loss of results: §4.4's factor-count exposition and §5.5's
predictive-validity narrative (every H4 number retained). Combined with the round-4 typographic
compaction this targets a ten-page body; the exact count is confirmed on the next Overleaf compile.

**Further prose pass (read-aloud).** The surviving over-long sentences were split (the §1.4 scope chain,
the §1.1 research-question sentence, the §5.5 bootstrap chain). Stock phrasing was removed ("what makes
it meaningful is its direction and consistency", the Discussion's "converge on a single qualified
answer"), both abstract "not X, but Y" templates were recast, and the excess contrasting phrasing was
reduced ("rather than" from 23 to 15 occurrences, total contrast constructions from 29 to 21). More
high-level transitions were added as sentence and paragraph starters (However, Moreover, Consequently,
Finally, Having), with no correlative slop reintroduced.

**GitHub link.** Retained at the author's explicit instruction, consistent across the appendix,
`README.md`, and `GITHUB_SETUP.md`.

## H. Fourth examiner round (ToC style, deep language simplification, further page cut)

This round was driven by a compiled PDF and a screenshot of the target contents-page style.

**Table of contents restored to the publishable style.** The round-5 compaction had shrunk subsection,
figure, and table entries to `\small` and wrapped the whole block in `{\small ...}`, which made the
contents inconsistent with the List of Figures and List of Tables. All of those overrides were removed.
Section titles and page numbers stay bold (`\cftsecfont`/`\cftsecpagefont`); subsections, figures, and
tables now render at full regular size with standard dot leaders, matching the supplied screenshot. Only
a modest before-skip tightening (2pt/1pt) is kept so the three lists still fit one page.

**Language, the priority of this round.** The remaining contrasting phrasing was removed outright:
"rather than" from fifteen occurrences to zero, and every contrastive ", not X" / "and not X" construction
recast as a plain positive statement (the only surviving "not" is the literal verdict name "not
supported"). Sentence structure was simplified: semicolons from twenty-seven to one (the last inside a
parenthetical), explanatory colons from about fifteen to four (kept only where they introduce a genuine
list or question), and the connectives "since", "because", and "which" reduced by splitting causal and
relative clauses into direct sentences. Long sentences (over forty words) were cut from twenty to six,
the six remaining being genuine enumerations (a tools list, a primary-quantities list, the five-rung
predictor ladder, the scope list, a four-learners list, and a list of benchmark examples). High-level transitions were added as sentence and
paragraph starters where the logical relation is real (However, Moreover, Consequently, Finally, Having,
Crucially, Beyond), with no correlative slop.

**Further page cut.** The compiled body ran eleven pages. Beyond the language simplification, redundant
material was removed with no loss of results: the Contributions section's closing sentences that
duplicated the Discussion, verbose related-work clauses, the "two counts appear in this paper" scaffold,
and prose restatements of appendix tables in Robustness and Preprocessing. The body text dropped about
3.3% (roughly thirty lines). Every headline number is preserved. As before, the exact typeset page count
can only be confirmed by compiling on Overleaf.
