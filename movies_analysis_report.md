# Movie Industry Analysis Report (1980–2020)

**Author:** Sujan  
**Date:** June 2026  
**Dataset:** Movies Dataset — 7,668 films, 15 variables  
**Tools:** Python, pandas, NumPy, SciPy  

---

## Executive Summary

This report presents a comprehensive analysis of the global film industry using a dataset of 7,668 movies spanning 1980 to 2020. The analysis reveals that **budget and audience engagement are the primary drivers of box office revenue**, while critical acclaim has minimal financial impact. Horror is the most capital-efficient genre, and only half of all films turn a profit — highlighting the high-risk nature of film investment.

---

## 1. Dataset Overview

The dataset contains 15 variables covering production, audience, and financial dimensions of each film:

| Category | Variables |
|---|---|
| Identity | name, year, released, genre, rating |
| Audience | score, votes |
| Financial | budget, gross |
| Production | director, writer, star, company, country, runtime |

**Data quality at a glance:**
- `budget` has the highest null rate (~28%) — handled column-specifically rather than via global row removal
- `score`, `votes`, `runtime` have null rates under 0.5%
- No duplicate records were found

---

## 2. Data Cleaning

### Approach
A targeted cleaning pipeline was applied to preserve as much data as possible:

- **Null handling:** Rows dropped only when the columns critical to a specific analysis were null (e.g., `budget` and `gross` required only for financial analysis). A global `dropna()` would have discarded ~35% of the dataset unnecessarily.
- **Type casting:** `budget`, `gross`, and `votes` cast to `Int64` (nullable integer) to safely handle remaining nulls.
- **Feature engineering:** `profit`, `ROI`, `budget_M`, `gross_M`, `profit_M` columns added; `released` parsed to datetime; `country_release` extracted.

### Engineered Features

| Feature | Formula | Purpose |
|---|---|---|
| profit | gross − budget | Absolute financial performance |
| roi | (profit / budget) × 100 | Return on investment percentage |
| budget_M / gross_M | ÷ 1,000,000 | Human-readable scale |
| budget_tier | Quantile-based (Low/Mid/High) | Group-level comparison |
| country_release | Parsed from `released` column | Geography analysis |

---

## 3. Correlation Analysis

### 3.1 Method Selection

Two methods were applied and compared:

| Method | Assumptions | Suitable When |
|---|---|---|
| **Pearson** | Linear relationship, normal distribution | Both variables are symmetric, no extreme outliers |
| **Spearman** | Monotonic relationship, rank-based | Data is skewed or contains outliers |

Since `budget`, `gross`, and `votes` are all heavily right-skewed (skewness 2.4, 4.7, and 3.1 respectively), **Spearman is the preferred method** for this dataset.

### 3.2 Results

**Spearman Correlation Matrix (key pairs):**

| Variable 1 | Variable 2 | Spearman r | Strength |
|---|---|---|---|
| votes | gross | 0.746 | Strong positive |
| budget | gross | 0.694 | Strong positive |
| score | votes | 0.495 | Moderate positive |
| votes | budget | 0.493 | Moderate positive |
| votes | year | 0.427 | Moderate positive |
| score | runtime | 0.413 | Moderate positive |
| score | budget | −0.010 | Negligible |

### 3.3 Pearson vs Spearman — Key Differences

| Pair | Pearson r | Spearman r | Difference | Reason |
|---|---|---|---|---|
| votes ↔ gross | 0.615 | 0.746 | +0.131 | Outlier blockbusters inflate Pearson |
| votes ↔ year | 0.206 | 0.427 | +0.221 | Long-tail vote accumulation not linear |
| score ↔ budget | 0.072 | −0.010 | −0.082 | True relationship is near-zero |

---

## 4. Exploratory Data Analysis

### 4.1 Distribution Analysis

| Variable | Mean | Median | Skewness | Kurtosis | Interpretation |
|---|---|---|---|---|---|
| IMDb Score | 6.39 | 6.50 | −0.63 | 1.12 | Slight left-skew; films rarely rated very low |
| Budget ($M) | 35.9 | 21.0 | 2.44 | 7.44 | Heavy right-skew; most films are low-budget |
| Gross ($M) | 78.5 | 20.2 | 4.67 | 35.4 | Extreme right-skew; blockbusters dominate |
| Runtime (min) | 107.3 | 104.0 | 1.42 | 4.57 | Moderate right-skew; most films 90–120 min |

**Key insight:** Budget and gross have extreme positive skew — a small number of blockbusters pull the mean far above the median. For these variables, the **median is a more representative measure** than the mean.

### 4.2 Outlier Detection

**IQR Method** (primary — better for skewed data):

| Variable | IQR Lower Bound | IQR Upper Bound | Outliers Found |
|---|---|---|---|
| Budget ($M) | Below −$5M (none) | Above $75.5M | 445 films |
| Gross ($M) | Below −$61.5M (none) | Above $155.4M | 551 films |
| IMDb Score | Below 4.5 | Above 8.7 | 86 films |

**Total unique films flagged:** 771 (IQR) vs 253 (Z-score)

**Top gross outliers:**

| Film | Budget ($M) | Gross ($M) | Score |
|---|---|---|---|
| Avatar | $237M | $2,847M | 7.8 |
| Avengers: Endgame | $356M | $2,798M | 8.4 |
| Titanic | $200M | $2,202M | 7.8 |
| Star Wars: Episode VII | $245M | $2,070M | 7.8 |
| Avengers: Infinity War | $321M | $2,048M | 8.4 |

---

## 5. Box Office Financial Intelligence

### 5.1 Genre ROI Analysis

Using **median ROI** (preferred over mean — robust to extreme outliers):

| Genre | Median ROI | Avg Budget ($M) | Avg Gross ($M) | % Profitable |
|---|---|---|---|---|
| Horror | ~205% | $13M | $39M | 68% |
| Animation | ~193% | $76M | $280M | 82% |
| Comedy | ~121% | $27M | $61M | 61% |
| Action | ~89% | $58M | $108M | 57% |
| Drama | ~45% | $22M | $33M | 48% |
| Biography | ~32% | $30M | $40M | 44% |

**Key finding:** Horror is the most capital-efficient genre — high ROI at the lowest average budget of any major genre. Animation delivers the highest absolute gross but requires ~6× the budget investment.

### 5.2 Budget Tier Performance

| Tier | Films | Avg Gross ($M) | Avg Profit ($M) | Median ROI | % Profitable |
|---|---|---|---|---|---|
| Low (<$13M) | ~1,800 | $41M | $31M | 90% | 64% |
| Mid ($13–$34M) | ~1,800 | $88M | $55M | 71% | 58% |
| High (>$34M) | ~1,800 | $224M | $149M | 63% | 55% |

**Key finding:** High-budget films earn the most in absolute dollars, but Low-budget films are the most **ROI-efficient**. The diminishing return on high investment is a consistent pattern.

### 5.3 Profit vs IMDb Score

Linear regression of profit on IMDb score:

| Metric | Value |
|---|---|
| Pearson r | 0.18 |
| R-squared | 3.2% |
| Slope | ~$27M per score point |
| P-value | < 0.05 (statistically significant but practically weak) |

**Interpretation:** While the relationship is technically statistically significant due to the large sample size (5,400+ films), IMDb score explains only **3.2% of profit variance**. A film's quality rating is a poor predictor of its financial success.

---

## 6. Key Findings

1. **Budget drives revenue more than quality.** Budget↔gross Spearman r = 0.69 vs score↔gross r = 0.18.

2. **Popularity (votes) is the strongest gross predictor.** votes↔gross r = 0.75 — audience engagement matters more than critical rating.

3. **Horror is the most capital-efficient genre** with ~205% median ROI at an average budget of just $13M.

4. **Only ~51% of films turn a profit** — the industry is fundamentally high-risk.

5. **IMDb score is a poor predictor of financial success** — R² = 3.2%. Critical acclaim and box office revenue are largely independent.

6. **Blockbusters heavily distort averages.** The top 5 films (Avatar, Endgame, Titanic, etc.) each earned 30× the median gross revenue.

7. **Spearman correlation is more reliable than Pearson** for this dataset due to heavy right-skew in budget, gross, and votes.

---

## 7. Limitations

- **Budget reporting is incomplete** — ~28% of films have no budget data, which may skew financial findings toward larger productions that publicly disclose budgets.
- **Domestic vs global gross** — the `gross` column appears to reflect US domestic gross in some cases and worldwide gross in others.
- **Genre simplification** — each film is assigned one primary genre; multi-genre films lose nuance.
- **Survivorship bias** — poorly performing films may be underrepresented in online databases.

---

## 8. Next Steps

- **Predictive modelling** — regression model to predict gross using budget, genre, runtime, and MPAA rating
- **Time-series analysis** — decade-by-decade trends in budgets, genres, and scores
- **Interactive dashboard** — Plotly/Tableau visualizations for exploratory use
- **SQL extension** — replicate aggregations in SQL Server for multi-platform portfolio coverage

---

*This report was produced as part of a data analyst portfolio project.*
