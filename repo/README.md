# Evaluating Causal Explanations in Large Language Models: A Student Perspective

**Master's Dissertation** — Sirine BEN MANSOUR  
Tunis Business School / Università degli Studi di Cassino e del Lazio Meridionale  
Supervisors: Sonia REBAI (TBS), Giovanni C. PORZIO (UNICAS)

---

## Overview

This repository contains all code, data processing scripts, and LaTeX source for the
dissertation. The work introduces a three-stage human-centred framework for evaluating
causal explanation quality in open-source LLMs, applied to the MMLU Economics
benchmark with graduate student reviewers.

**Framework stages:**
1. **Stage A** — Criterion weighting via the Analytic Hierarchy Process (AHP)
2. **Stage B** — Discriminative question selection using semantic and structural
   dispersion (MAD-style)
3. **Stage C** — Interval-based aggregation of Likert ratings with AHP weights

**Models evaluated:** `qwen2.5:14b`, `qwen2.5:7b`, `llama3.1:8b`,
`mistral-nemo:12b`, `mixtral:8x7b`, `mistral:7b`, `phi3:medium`, `phi3:3.8b`,
`codellama:7b`

---

## Repository structure

```
.
├── notebooks/
│   ├── 01_LLM_Querying_and_Question_Selection.ipynb
│   ├── 02_AHP_Priorities_Calculations.ipynb
│   ├── 03_Aggregation_and_Graphs.ipynb
│   └── 04_Semantic_Dispersion.ipynb
├── data/
│   ├── README.md                   # Data file descriptions and availability
│   └── [CSV files — see data/README.md]
├── figures/                        # All generated plots (PNG)
├── latex/                          # Full LaTeX dissertation source
│   ├── main.tex
│   ├── appendix.tex
│   ├── references.bib
│   ├── chapters/
│   └── style/
├── requirements.txt
└── README.md
```

---

## Notebooks

### `01_LLM_Querying_and_Question_Selection.ipynb`
Loads the MMLU Economics subset via Hugging Face `datasets`, constructs a
structured JSON prompt for each question, and queries all nine models via the
Ollama API. Implements dispersion scoring and MAD-style question selection to
identify the 12 most discriminative questions.

**Outputs:** `ollama_mmlu_econometrics_results.csv`

### `02_AHP_Priorities_Calculations.ipynb`
Reads pairwise comparison matrices from all ten judges. Computes eigenvalue-based
AHP priority vectors, Consistency Index (CI), and Consistency Ratio (CR) for each
judge. Applies CR-weighted geometric mean aggregation over consistent judges
(CR < 0.10) to produce final criterion and sub-criterion weights.

**Outputs:** Criterion weight tables (printed; used as inputs to notebook 03)

### `03_Aggregation_and_Graphs.ipynb`
Reads `LikertScale.csv`. Applies an outlier-adjustment procedure to each
(model, question) triplet of ratings, computes IQR-based quality intervals per
model and criterion, applies AHP weights to produce final ranked scores, and
generates all radar charts. Also computes ICC(2,k), Krippendorff's alpha,
Cohen's d, Hedges' g, and bootstrapped confidence intervals.

**Outputs:** `adjusted_evaluator_scores.csv`, radar chart PNGs

### `04_Semantic_Dispersion.ipynb`
Standalone pipeline for computing semantic dispersion per question using
step-level embeddings and optimal assignment (Hungarian algorithm). Supports
both a local `sentence-transformers` backend and an OpenAI embeddings backend.

**Outputs:** `dispersion_by_question.csv`

---

## Setup

### 1. Install Python dependencies

```bash
pip install -r requirements.txt
```

Python 3.10 or later is recommended.

### 2. Install Ollama and pull models

Install Ollama from [https://ollama.com](https://ollama.com), then pull all nine
models:

```bash
ollama pull qwen2.5:14b
ollama pull qwen2.5:7b
ollama pull llama3.1:8b
ollama pull mistral-nemo:12b
ollama pull mixtral:8x7b
ollama pull mistral:7b
ollama pull phi3:medium
ollama pull phi3:3.8b
ollama pull codellama:7b
```

> **Hardware note:** Running all nine models sequentially requires at least 16 GB
> RAM. `mixtral:8x7b` requires approximately 26 GB. A GPU is not required but
> will reduce runtime significantly.

### 3. Run notebooks in order

```
01 → 02 → 03 → 04
```

Notebook 03 depends on `LikertScale.csv` (human evaluation data — see
`data/README.md`) and on the AHP weights from notebook 02.

---

## Reproducing the LaTeX dissertation

```bash
cd latex
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

Requires a standard TeX distribution (TeX Live 2022+ or MiKTeX).

---

## Citation

If you use this code or framework, please cite:

```
BEN MANSOUR, S. (2026). Evaluating Causal Explanations in Large Language Models:
A Student Perspective. Master's Dissertation, Tunis Business School /
Università degli Studi di Cassino e del Lazio Meridionale.
```

---

## License

Code in `notebooks/` is released under the MIT License.  
LaTeX source in `latex/` is copyright Sirine BEN MANSOUR, all rights reserved.  
See `LICENSE` for details.
