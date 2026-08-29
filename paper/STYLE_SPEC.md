# Style specification — grounded in sources (feedback point 8)

Decisions below are learned from three reputable sources, not chosen in a vacuum:
- **A** = the assignment instruction + guidance docs (the authoritative brief).
- **B** = the four target conference papers, esp. Bean et al. *Measuring what Matters*
  (NeurIPS 2025 D&B) and Kearns *Quantifying construct validity* (Oxford MSc), inspected directly.
- **C** = standard top-ML-conference LaTeX practice (NeurIPS/ICML style files).

## 1. Format & toolchain
- **LaTeX**, two variants: `paper_singlecol.tex` (NeurIPS-like, single column) and
  `paper_twocol.tex` (ICML/IEEE-like, two column). User picks the better presentation. [A1.4→B]
- Editable source is the deliverable; PDF is compiled by the user on Overleaf. No working
  TeX engine in-sandbox, so we ship **compile-robust** source and lint structurally here. [feedback 4]
- References embedded as `\thebibliography` (compiles first-pass, no bibtex round-trip needed)
  **and** shipped as `references.bib` for convenience. [C]

## 2. Typography
- **Times New Roman throughout** via `newtxtext` + `newtxmath` (text and math both Times).
  This is the single fix for the font inconsistency in the HTML draft. [feedback 4]
- Body 11pt single-col / 10pt two-col; standard conference margins via `geometry` (single)
  or class default (two-col). [C]

## 3. Citations (feedback 1 — the clickable style)
- Numeric `[n]` in-text via `\cite`, rendered by `hyperref` with `colorlinks=true` and a
  `citecolor` so **clicking the bracket jumps to the reference entry**. This is exactly the
  "top-conference clickable citation" the user described, and matches B (Bean/Kearns PDFs
  have clickable numeric refs). `hyperref` also builds PDF bookmarks for the ToC. [B,C]

## 4. Layout / float placement (feedback 1)
- Page order per A1.3: **p1** title + abstract, **p2** ToC + List of Figures + List of Tables,
  **body from p3**, references after the body, appendix last.
- Every figure/table is a float placed with `[tbp]` and `\label`/`\ref`, positioned so it
  **appears near its first in-text mention** (verify each). Tables use **booktabs**
  (`\toprule/\midrule/\bottomrule`, no vertical rules) — the conference table convention in B,
  replacing the HTML draft's grid tables. [B,C, feedback 4]
- Figure captions lead with a **bold sentence** then description, as in B.

## 5. Equations (feedback 5)
- Every displayed equation on its **own centred line** via `\[...\]` or `align`, with
  `\medskip` breathing room around derivation steps. No inline cramming of model equations.
- Unicode math (τ², ρ, Λ, Σ) rewritten as proper LaTeX math (`\tau^2`, `\rho`, `\Lambda`,
  `\Sigma`) so it renders correctly under newtxmath. [feedback 5, error-safety]

## 6. Prose (feedback 2, 3, 10)
- **Em-dashes → near zero** (draft has 61): replace with commas, parentheses, or full stops.
- **Colons minimised**, especially mid-sentence "label:" openers.
- **No slogan openers**: delete the short 3–6 word sentences that open many paragraphs.
- **Vary paragraph length**: mix developed multi-sentence paragraphs with shorter ones so
  evidence and argument integrate; not a uniform wall of short paragraphs.
- **Deepen methodological justification**: explicit comparative reasoning (why EFA not PCA-only,
  why oblimin, why LOBO+nested CV, why each learner, why residualise on date) to hit the
  marking criteria's "critical thinking / justification of modelling decisions". [A1.2, A1.4]
- Every numeric value copied **verbatim** from the canonical CSVs / current paper. No new numbers.

## 7. Appendix (feedback 5, 6)
- Keep A (derivations, respaced), B (full benchmark table), C (hyperparameter grid),
  D (robustness), E (provenance + GitHub), F (supplementary figures).
- Add a one-line **justification** to each appendix subsection: what it supports and where the
  main text references it, so the reader sees why it belongs. [feedback 6]
- All values defensible against artifacts; parallel-analysis eigenvalues cited from
  `parallel_analysis.csv`. [feedback 6, 10]