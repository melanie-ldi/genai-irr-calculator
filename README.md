# IRR Calculator

**Inter-Rater Reliability Calculator**  
Learning Data Insights · GenAI Evidence Hub

> ⚗ **Experimental** · This tool is provided for exploratory use. Results should be verified against established IRR software for publication or formal reporting purposes.

---

## Overview

A self-contained, browser-based application for computing inter-rater reliability (IRR) on systematic literature review coding sheets. No installation, no server, no dependencies — open `irr_calculator.html` in any modern browser and it works immediately.

Built for the [GenAI Evidence Hub](https://www.learningdatainsights.com/our-projects/genai-evidence-hub), a systematic literature review project tracking generative AI use in educational assessment across three evidence domains: Automated Scoring, Item Generation, and Formative Feedback.

**Created by Melanie Kurimchak** in collaboration with Claude Sonnet 4.6 (Anthropic). Melanie conceived the tool, defined all requirements, tested functionality, and directed development through iterative feedback.

---

## Getting Started

1. Download `irr_calculator.html`
2. Open it in Chrome, Firefox, Edge, or Safari
3. Upload your combined coding sheet
4. Select the coding sheet version (auto-detected)
5. Review the pre-selected IRR columns and adjust if needed
6. Select coders and metrics
7. Click **Calculate IRR**

---

## Features

- **Pooled coincidence matrix** — Krippendorff's Alpha computed from all `(Paper ID, Research Question ID, Field)` units pooled into one matrix per scope, matching the reference `compute_irr.py` implementation
- **Coder Pair IRR** — per-pair pooled alpha with a two-level accordion: pair → paper → RQ
- **Value normalization** — numeric values normalized (`85` == `85.0`), multiselect fields compared order-independently, missing tokens excluded
- **Free-text field detection** — fields like `Research Question` and `Tested LLM Model & Version Used` are flagged as free-text and excluded from pre-selection with a warning if included
- **Auto-detection** of coding sheet version from column signatures
- **Coder filter** — restrict calculations to a subset of reviewers
- **Export** to CSV or styled Excel, mirroring the on-screen layout
- **No installation** — single HTML file, fully client-side, no data sent to any server

---

## Supported File Formats

| Format | Extensions |
|--------|------------|
| Comma-separated | `.csv` |
| Excel | `.xlsx`, `.xls` |
| Tab-separated | `.tsv` |
| Plaintext | `.txt` |

---

## Coding Sheet Requirements

### Structure
Long-format file — one row per reviewer per research question:

| Paper ID | Research Question ID | Reviewer | Domain | Metric1-type | ... |
|---|---|---|---|---|---|
| 249 | 249.1 | Alice | Education | QWK | ... |
| 249 | 249.1 | Bob | Education | Pearson | ... |

### Fixed Columns (always used automatically)

| Column | Purpose |
|---|---|
| `Research Question ID` | Primary grouping unit for IRR |
| `Reviewer` | Coder identifier |
| `Paper ID` | Groups RQs into papers for summary display |
| `Paper Title` | Display label in summary cards |
| `Research Question` | Display label in the RQ table |

### Known Artifact Row
The second data row containing `"Numerical (index identifier)"` in the Research Question ID column is automatically excluded. The reviewer value `"String"` is also excluded.

---

## Coding Sheet Versions

| Version | Status | Signature Columns |
|---|---|---|
| **Automated Scoring** | Active | `Metric1-type`, `Bias / Fairness Evaluated` |
| **Item Generation** | Active | `Metric1-analysis type`, `Bias / Equity Evaluated` |
| **Formative Feedback** | Placeholder | — |

---

## Supported Metrics

| Metric | Requirements |
|---|---|
| **Krippendorff's Alpha** | 2+ coders · nominal, ordinal, or interval |
| **Pairwise % Agreement** | 2+ coders · all data types |
| **Cohen's Kappa** | Exactly 2 coders |
| **Fleiss' Kappa** | 3+ coders |

---

## Calculation Methodology

Krippendorff's Alpha is computed using the **pooled coincidence matrix** method:

- **Unit of analysis:** one `(Paper ID, Research Question ID, Field)` cell
- **Overall alpha:** all cells pooled into one coincidence matrix
- **Per-pair alpha:** same pooled approach, restricted to the two coders
- **Per-paper alpha:** cells of that paper only
- **Per-RQ alpha:** cells of that RQ only
- **Missing values:** blank, `N/A`, `none`, `tbd`, `-`, `?` are excluded; a unit requires ≥ 2 coders to contribute
- **Numeric normalization:** `"85"`, `85`, `85.0` all resolve to `"85"`
- **Multiselect normalization:** comma-separated values sorted order-independently

This matches the reference implementation in `compute_irr.py`.

### Free-Text Fields (excluded by default)
The following fields use divergent prose and cannot be reliably auto-scored by exact string match:
- `Research Question`
- `Tested LLM Model & Version Used`
- `Tested Model & Techniques List`
- `Baseline list`

They appear in the column picker with an `⚠ free-text` badge and are not pre-selected. Including them will deflate alpha.

---

## Reference Implementation

`compute_irr.py` is the canonical Python implementation. It accepts the same coding workbook and produces the ground-truth values this tool is calibrated against. See its own README for usage.

```bash
python compute_irr.py WORKBOOK.xlsx --sheet SHEET_NAME
python compute_irr.py WORKBOOK.xlsx --sheet SHEET_NAME --per-paper --pairs
```

---

## Project Context

This tool is part of the **GenAI Evidence Hub**, a systematic literature review at Learning Data Insights examining generative AI use in educational assessment. For questions, contact the LDI project team.
