# LaTeX structural lint report

No LaTeX engine is usable in the build sandbox (tectonic panics with
`Operation not permitted (os error 1)` because it cannot fetch its package
bundle, and there is no pdflatex/xelatex/pandoc/LibreOffice). The first PDF
compile is therefore done by the user on Overleaf. To catch the errors a
compiler would catch, both wrapper files were expanded (all `\input` resolved)
and checked programmatically. Both pass.

## Checks (both paper_singlecol.tex and paper_twocol.tex)

| Check | Result |
|---|---|
| `\begin`/`\end` environments balanced | PASS |
| Braces balanced `{`/`}` | PASS |
| Every `\ref` has a matching `\label` (31 refs, 49 labels) | PASS |
| Every `\cite` key exists in `\thebibliography` (21 keys, all cited) | PASS |
| Every `\includegraphics` name resolves under `\graphicspath` (7 figures) | PASS |
| No `\input` target missing | PASS |
| No raw non-ASCII outside math (τ²/τ³/ρ all as `\tau^2`/`\tau^3`/`\rho`) | PASS |

## Compile notes for Overleaf
- Engine: **pdfLaTeX** (default). `newtxtext`/`newtxmath` give Times New Roman
  in text and math; both are on CTAN and bundled with Overleaf.
- Bibliography is embedded as `\thebibliography`, so **no bibtex/biber pass is
  needed**; the document compiles in a single pdfLaTeX run (run twice to
  resolve the ToC, `\ref`, and hyperref page anchors).
- `references.bib` is shipped for convenience if the user prefers to switch to
  `\bibliography{references}` with a `natbib`/`biblatex` numeric style; the
  in-text `\cite` keys already match the `.bib`.
- Clickable citations: `hyperref` with `colorlinks=true, citecolor=blue`;
  clicking `[n]` jumps to the reference entry, and the ToC entries are live.
- Two-column build uses `\twocolumn[...]` for a full-width title and abstract,
  and every figure uses `figure*` so it spans both columns.
- Figures are included by bare name (`\includegraphics{fig1_eda}`) and resolved
  through `\graphicspath{{figures/}{../figures/}{./}}`, so the build works
  whether the main `.tex` sits at the repo root or inside `paper/`. This also
  avoids any path token being mistaken for an artifact reference on save.

## Files
- `paper_singlecol.tex` — single-column (11pt, article + geometry), NeurIPS-like.
- `paper_twocol.tex` — two-column (10pt), ICML/IEEE-like.
- Shared: `frontmatter.tex`, `body.tex`, `appendix.tex`, `thebibliography.tex`,
  the float inputs (`alg_lobo.tex`, `tab_*.tex`), `references.bib`.