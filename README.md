# Analysis code: The human core of research mentorship: a mixed-methods study of how physicists view graduate education in the age of generative AI

This repository contains the analysis code for the results reported in the Results section of our manuscript. The single notebook, `Python_Code.ipynb`, produces every table (Tables 1–8) and figure (Figures 1–3) in that section from a survey export.

## ⚠️ Synthetic data notice

**The dataset in this repository is synthetic. It is not the survey data we collected.**

Participants' consent and the study's ethics approval do not allow the individual responses to be released, so this repository uses a synthetic dataset instead:

- `data/AI4S_survey_synthetic_n200_11_23_2026.csv`: 200 synthetic records in the same format and column structure as the original Qualtrics export.

**All outputs produced by running this notebook on the repository (every table, CSV file and figure in `outputs/`) come from this synthetic data.** They show how the analysis works, but they are **not** the results reported in the manuscript, and they should not be read or cited as findings about real respondents. The results for the real respondents are reported in aggregate in the article and its additional files.

### Data statement

> The individual-level survey data analysed in this study cannot be shared, publicly or on request. Participants consented to the use of their responses by the research team only, and the ethics approval for the study (University of Tokyo, Nos. E2026ALS094 and E2026ALS328) does not permit release of individual responses. In addition, the combination of background characteristics collected could allow individual respondents to be identified within a small research community. All results are reported in aggregate in the article and its additional files. The survey instrument is provided in the additional files. However, a synthetic dataset and analytical code needed to interpret, replicate, and extend this work is available at https://github.com/ynaung24/AI4S_public. This code, written in Python (version 3.11.14) can be used together with the synthetic dataset to understand and extend the approach reported here.

## Repository contents

```
Python_Code.ipynb   analysis notebook (all results)
data/               synthetic survey export (input)
outputs/            tables (.csv) and figures (.png/.pdf), created when the notebook runs
```

## Setup

The code was written in Python 3.11.14 and needs Python 3.11 with numpy, pandas (< 3.0), scipy, statsmodels and matplotlib. Under pandas 3 the notebook stops in section 3 with an `IntCastingNaNError`.

With conda:

```bash
conda create -n ai4s -c conda-forge python=3.11.14 "pandas<3" numpy scipy statsmodels matplotlib ipykernel
conda activate ai4s
python -m ipykernel install --user --name ai4s --display-name "Python (ai4s)"
```

## Running

Open `Python_Code.ipynb` with the "Python (ai4s)" kernel and run all cells from top to bottom, or from the repository root run:

```bash
jupyter nbconvert --to notebook --execute Python_Code.ipynb --output Python_Code.ipynb
```

Later cells use objects defined in earlier ones, so run the notebook in order. Outputs are written to `outputs/`.

To run on a different file with the same export format, edit the `DATA` line in the first code cell:

```python
DATA = HERE / "data" / "AI4S_survey_synthetic_n200_11_23_2026.csv"
```

## What the notebook does

| Section | Analysis | Outputs |
|---|---|---|
| 1 | Data overview: removes the research team's preview records. The analytic sample is everyone who answered at least one background item. | `sample_flow.csv` |
| 2 | Background characteristics (Tables 2–4) | `table2_demographics.csv`, `table3_research_status.csv`, `table4_ai_use.csv` |
| 3 | The fifteen background features used in the models | `derived_features.csv`, `feature_table.csv` |
| 4 | Survey items by research question, with full wording (Table 1) | `table1_items.csv` |
| 5 | Response distributions (Tables 5 and 7; Figures 1 and 2) | `table5_rq1_distributions.csv`, `table7_rq2_distributions.csv`, `Figure1`, `Figure2` |
| 6 | RQ1 items: univariate OLS regressions (Table 6) | `table6_rq1_ols_significant.csv`, `rq1_ols_all_*.csv` |
| 7 | Split RQ2 items: univariate logistic regressions (Table 8; Figure 3) | `table8_rq2_logit_significant.csv`, `rq2_logit_all_*.csv`, `figure3_values.csv`, `Figure3` |

### Methods in brief

- **Likert items** are rated 1–7. Responses are grouped as disagree (1–3), neutral (4) and agree (5–7).
- **Recoding:** respondents who chose "Other" as their position and described themselves as doctoral or master's students in the free-text field are counted as such. Countries with fewer than 11 respondents are grouped as "Other" in Table 2. Multi-select first-language answers are mapped to language groups.
- **Derived features:**
  - *Mentor status*: faculty and staff scientists are mentors; postdocs and graduate students are mentees.
  - *Region of employment*: Asia, Europe, North America or Other.
  - *AI usage status*: respondents who have not used generative AI for work are non-users. For users, standardised frequency of use, time since first use and self-rated proficiency are averaged into a composite score, which is split into thirds (Low, Medium, High).
- **Sparse levels:** response levels with 10 or fewer respondents are merged into "Other". An "Other" level that still has 10 or fewer respondents is left out of the models.
- **RQ1 (Table 6):** for each item and feature, an OLS regression of the 1–7 score on the dummy-coded feature, with the first level as the reference. A feature is reported when its F test has p ≤ 0.05.
- **RQ2 split items (Table 8):** neutral responses are dropped, and agree is modelled against disagree with logistic regression for each feature. Levels with no variation in the outcome are excluded from the fit. A feature is reported when its likelihood-ratio test has p ≤ 0.05.

The thresholds (`ALPHA`, `MIN_N`, `COUNTRY_MIN_N`) are set in the first code cell.
