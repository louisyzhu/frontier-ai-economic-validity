# Uploading this project to GitHub

The repository is referenced from the appendix of the paper (Section E, Data
provenance and reproducibility). The appendix and README already point to
`https://github.com/louisyzhu/frontier-ai-economic-validity`, so create the
repository under that exact name and the link will resolve.

## 1. Create the repository
On GitHub (account **louisyzhu**), create a new **empty** repository named
`frontier-ai-economic-validity` (no README, licence, or .gitignore, since this
bundle already contains them). The repository is published under the author's own
name; it carries no student identifier, which also makes an anonymised mirror for
double-blind submission straightforward to produce.

## 2. Initialise and push
From inside the `aa_benchmark_validity/` folder:

```bash
git init
git add .
git commit -m "Economic validity of frontier AI evaluation: code and pinned data"
git branch -M main
git remote add origin https://github.com/louisyzhu/frontier-ai-economic-validity.git
git push -u origin main
```

## 3. What gets uploaded
The `.gitignore` excludes build artifacts (LaTeX `.aux`/`.log`, notebook
checkpoints, `__pycache__`, the local `.jupyter` install, `.tectcache`) and OS
files. Everything else is version-controlled, including:

- `paper/` — both LaTeX sources (`paper_singlecol.tex`, `paper_twocol.tex`),
  the shared body, appendix, and float files, `references.bib`, and the figures
  they include.
- `notebook/analysis.ipynb` — the single notebook with outputs and
  figures rendered inline.
- `data/raw/` — the SHA-256-pinned snapshots (the dataset), and
  `data/processed/` — every result table the paper and notebook consume.
- `figures/` — nine PNGs: five main-text figures, three appendix figures
  (`suppl_scree_parallel`, `suppl_benchmark_dendrogram`, `appxfig_density`), and
  `fig_phase1_density` (the notebook's own copy of the density audit, identical to
  `appxfig_density`).
- `requirements.txt`, `README.md`, `PHASE{1,2,3}_REPORT.md`.

## 4. Compiling the paper (Overleaf)
Upload the contents of `paper/` to a new Overleaf project (or push this repo and
import it). Set the main document to `paper_singlecol.tex` or `paper_twocol.tex`
and compile with **pdfLaTeX**. The bibliography is embedded, so no separate
BibTeX pass is needed; compile twice so the table of contents, cross-references,
and clickable citations resolve.

## 5. Data size note
`data/raw/aa_leaderboards_models_snapshot.html` is about 4.3 MB. It is well
under GitHub's 100 MB per-file limit, so it can be committed directly with no
need for Git LFS.