# Vanguard Digital UI A/B Test

Ironhack Data Analytics Bootcamp — Module 2 Project (EDA, Performance Metrics, Hypothesis Testing, Tableau)

## Team

- Ailixier Kurban
- Juan Francisco Calvo Sánchez

## Business Context

Vanguard's Customer Experience team ran an A/B test on their client-facing online process (start → step 1 → step 2 → step 3 → confirm) from **March 15, 2017 to June 20, 2017**.

- **Control group**: traditional UI
- **Test group**: redesigned, more intuitive UI with in-context prompts

**Question:** Does the new UI lead to a meaningfully better client experience, and specifically a higher process completion rate?

## Repository Structure

```
vanguard-ab-test/
├── data/
│   └── raw/                     # raw source .txt files (not committed — see .gitignore)
├── notebooks/
│   └── EDA.ipynb                # full analysis: cleaning, EDA, KPIs, hypothesis tests, evaluation
├── images/
│   └──                          # images used in Tableau dashboard
├── Project_2.twbx               # interactive Tableau dashboard
├── requirements.txt
└── README.md
```

## Data Sources

Three datasets provided by Vanguard/Ironhack (place raw files in `data/raw/`):

| File | Description |
|---|---|
| `df_final_demo.txt` | Client demographics — age, gender, tenure, balance, calls, logons |
| `df_final_experiment_clients.txt` | Which clients were assigned to Test/Control (`Variation`) |
| `df_final_web_data_pt_1.txt` + `_pt_2.txt` | Web activity trace per client/visit/step, merged in the notebook |

Source: *[Link](https://github.com/data-bootcamp-v4/lessons/tree/main/5_6_eda_inf_stats_tableau/project/files_for_project)*

## Setup

```bash
pip install -r requirements.txt
jupyter notebook notebooks/EDA.ipynb
```

## Methodology

1. **Data Cleaning** — dropped duplicates, removed ~30% of clients with no experiment assignment (`Variation` null), dropped remaining nulls in demographics, merged web data with the experiment roster.
2. **EDA** — demographic profile of clients and comparison of Test vs. Control at baseline.
3. **KPIs** — completion rate, time spent per step, error rate (backward navigation), plus additional KPIs: step-to-step drop-off, total time-to-complete, and repeat-visit rate.
4. **Hypothesis Testing** — two-proportion z-tests for rate-based KPIs, Mann-Whitney U for time (non-normal, skewed), chi-square for drop-off/repeat-visit comparisons.
5. **Experiment Evaluation** — randomization balance check, representativeness/selection-bias check, duration assessment.
6. **Tableau** — interactive dashboard for stakeholder exploration.

## Key Findings

### Who uses the process
Primary users are middle-aged (median ~47 years old), long-standing clients (median ~12 years tenure), with moderate engagement (~6 logons and ~3 support calls over 6 months). Test and Control were well balanced on these traits at baseline.

### Completion Rate
- Test: **69%** vs. Control: **66%**
- Two-proportion z-test: **+3.70 pp difference, p < 0.00001** → statistically significant
- Relative increase: **+5.64%**, which **clears Vanguard's 5% cost-effectiveness threshold**
- Holds directionally across all age groups

### Time Spent per Step
- Mann-Whitney U per step (all differences significant, p < 0.0001)
- Test users are **faster** on start, step_2 and step_3.
- Test users are **slower** on step_1 and confirm.
- Total time-to-complete: Test is **~28s faster** (median 232s vs. 260s, ~10.8% improvement)

### Error Rate (backward navigation)
- Test: **33.7%** vs. Control: **26.4%** (+7,3 pp, +27.7% relative, p < 0.00001)
- Test users navigate backward more often — worth investigating as a UX friction point even though completion still improves overall

### Other KPIs
- **Drop-off**: Test reduces drop-off at step_1 and confirm, slightly higher at step_2, no difference at step_3
- **Repeat visits**: statistically significant but practically negligible difference (17.53% Test vs. 18.62% Control needed 2+ visits, Cramér's V = 0.014)
- A Bonferroni correction across all tests leaves conclusions unchanged, except the 46-60 age-group completion-rate difference (p = 0.0151), which would no longer hold at the stricter threshold

### Experiment Evaluation
- Group sizes were unequal (Test ~53% / Control ~47%) — acceptable, since baseline balance is what matters, not equal split
- Baseline randomization check: groups are well balanced (isolated significant p-values are sample-size artifacts, not meaningful differences)
- **Selection bias**: the ~50K experiment participants are not representative of Vanguard's full client base — they log in more, call support less, are slightly older, and hold higher balances than the ~20K excluded clients. Results should be generalized to digitally-engaged clients, not the full population
- The 3-month window is a single seasonal period; results should be monitored post-rollout across other periods (e.g. year-end rebalancing)

### Recommendation
Completion rate is the primary decision metric, and the new design clears both the statistical significance bar and the 5% cost-effectiveness threshold. Recommend a **phased, monitored rollout** with device-level reporting, while investigating the higher error rate and the slower "confirm" step as UX follow-ups.

## Tableau Dashboard

`tableau/Project_2.twbx` — completion rate, time per step, and error rate by Test/Control, with filters for demographics (age group, gender).


## Tech Stack

Python (pandas, numpy, matplotlib, seaborn, scipy, statsmodels) · Jupyter · Tableau
