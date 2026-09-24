# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The official CVPR 2026 LaTeX author kit (imported from Overleaf), used as the starting point for a paper. There is no application code; the "build" is compiling LaTeX to PDF. `pixi.toml` is a bare pixi workspace with no tasks or dependencies yet; the system TeX Live (`pdflatex`, `latexmk`, `bibtex`) is what actually builds the paper.

## Build commands

```bash
# Paper (runs pdflatex + bibtex the right number of times)
latexmk -pdf -interaction=nonstopmode main.tex

# Rebuttal (separate, standalone document)
latexmk -pdf -interaction=nonstopmode rebuttal.tex

# Keep build artifacts out of the repo root
latexmk -pdf -interaction=nonstopmode -outdir=build main.tex

# Clean auxiliary files
latexmk -C
```

If hyperref is toggled on/off between runs, delete `*.aux` first (stale aux files break the build). Check `main.log` for `Overfull \hbox` and undefined-reference warnings after edits.

## Document structure

- `main.tex` is the root: document class, `cvpr` package option, paper ID / conference macros, title and authors, then `\input`s each file in `sec/` in order and the bibliography. Sections live in `sec/` as numbered files (`0_abstract`, `1_intro`, ...); add new sections there and `\input` them from `main.tex`.
- `preamble.tex` is the place for extra packages and macros. It is loaded **before** hyperref, which must stay last in `main.tex`. It defines `\red`, `\todo`, `\TODO` annotation commands and has commented-out opt-ins (`microtype`, `\paragraph` tweak, `\paper` project-name macro, math operators).
- `sec/X_suppl.tex` is the supplementary material. It is commented out in `main.tex`; enabling it appends the supplementary after the references with its own page counter (`\maketitlesupplementary`) so main paper and supplement can cross-reference each other. Delete those pages from the PDF before submission.
- `rebuttal.tex` is a self-contained document (its own preamble, its own `\begin{document}`) using `\usepackage[rebuttal]{cvpr}`. It has commented-out counter offsets so figure/table/reference numbers can continue from the main paper.
- `cvpr.sty` is the conference style; do not edit it. `ieeenat_fullname.bst` is the required bibliography style; `main.bib` holds the references.

## Paper modes (cvpr.sty option in main.tex)

Exactly one of these should be active:

| Option | Effect |
|---|---|
| `\usepackage[review]{cvpr}` | Anonymous submission: line numbers (`lineno`), page numbers, paper ID in header instead of authors. Current default. |
| `\usepackage{cvpr}` | Camera-ready: authors shown, no line/page numbers. |
| `\usepackage[pagenumbers]{cvpr}` | Camera-ready with page numbers (e.g. arXiv). |
| `\usepackage[rebuttal]{cvpr}` | Used only by `rebuttal.tex`. |

Before switching to camera-ready also set the real `\paperID`, fill in `\author`, and uncomment the two `\renewcommand`s in `preamble.tex` that silence `\TODO`/`\todo`.

## Conventions baked into the style

- `cvpr.sty` already loads `natbib` (numbers, sort&compress), `xcolor` (dvipsnames), `graphicx`, `amsmath`, `amssymb`, `booktabs`, `caption`/`subcaption`, `enumitem` (inline lists), `xspace` and `url`; don't re-add them in `preamble.tex`. `cleveref` (with `capitalize` and short names such as `Sec.`/`Fig.`/`Tab.`) is injected after hyperref via `\AtEndPreamble`, so use `\cref{}` for cross-references and `\cite{}` for citations.
- Links and citations are colored `cvprblue` (defined in `main.tex`); keep the `hyperref` options as they are, `pagebackref` is what reviewers expect.
- Page limit: 8 pages excluding references. Figure and caption fonts are already sized by the style; do not override them.
