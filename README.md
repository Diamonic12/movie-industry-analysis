# 🎬 Movie Industry Analysis (1980–2020)

A data analysis portfolio project exploring box office performance, financial intelligence, and audience trends across 7,600+ films spanning four decades.

---

## 📋 Project Overview

This project performs an end-to-end analysis of a movies dataset sourced from Kaggle, covering:

- **Correlation Analysis** — Pearson and Spearman methods to uncover relationships between budget, gross, score, votes, and runtime
- **Exploratory Data Analysis (EDA)** — Distribution analysis and outlier detection using IQR and Z-score methods
- **Box Office Financial Intelligence** — Genre ROI, budget tier performance, and the score–profit relationship

---

## 📁 Project Structure

```
movie-analysis/
│
├── movies_analysis.ipynb        # Main analysis notebook
├── movies.csv                   # Dataset (source: Kaggle)
└── README.md                    # Project documentation
```

---

## 📊 Dataset

| Property | Detail |
|---|---|
| Source | [Kaggle — Movies Dataset](https://www.kaggle.com/datasets/danielgrijalvas/movies) |
| Records | 7,668 films |
| Time period | 1980 – 2020 |
| Columns | 15 (name, rating, genre, year, released, score, votes, director, writer, star, country, budget, gross, company, runtime) |

---

## 🔍 Analysis Sections

### 1. Data Cleaning
- Column-specific null handling (avoids unnecessary row loss from global `dropna`)
- Duplicate detection
- Type casting (budget, gross, votes → Int64)
- Feature engineering: profit, ROI, budget tier, country of release

### 2. Correlation Analysis
| Method | Use Case |
|---|---|
| **Pearson** | Linear relationships between continuous variables |
| **Spearman** | Rank-based; preferred here due to heavy right-skew in budget/gross |

**Top correlations (Spearman):**
- budget ↔ gross: **r = 0.69** (strong)
- votes ↔ gross: **r = 0.75** (strong)
- score ↔ budget: **r = −0.01** (near-zero — money ≠ quality)

### 3. Exploratory Data Analysis
- Distribution analysis: skewness and kurtosis for score, budget, gross, runtime
- Outlier detection: 771 films flagged via IQR; 253 via Z-score
- Key outliers: Avatar ($2.85B), Avengers: Endgame ($2.80B), Titanic ($2.20B)

### 4. Box Office Financial Intelligence
- **Genre ROI:** Horror leads with ~205% median ROI at avg budget of just $13M
- **Budget tiers:** Low-budget films are the most ROI-efficient; High-budget films earn the most in absolute terms
- **Score vs Profit:** r = 0.18 — IMDb score explains only ~3% of profit variance

---

## 📌 Key Findings

1. **Budget is the strongest revenue driver** — Spearman r = 0.69 (budget ↔ gross)
2. **Popularity beats quality as a profit predictor** — votes↔gross r = 0.75 vs score↔gross r = 0.18
3. **Horror generates the best ROI** — ~205% median return at low investment
4. **Only ~51% of films are profitable** — the industry is high-risk
5. **Blockbusters heavily skew averages** — median is the appropriate central tendency measure for this data
6. **Spearman outperforms Pearson here** — skewed distributions make rank-based correlation more reliable

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3 | Core analysis language |
| pandas | Data manipulation and aggregation |
| NumPy | Numerical operations |
| SciPy | Statistical tests (Pearson, Spearman, linregress) |
| Plotly | Interactive visualizations *(in progress)* |
| Jupyter Notebook | Analysis environment |

---

## ▶️ How to Run

**Option A — Google Colab**
1. Upload `movies.csv` to your Colab session
2. Update `DATA_PATH = '/content/movies.csv'` in Cell 1
3. Run all cells

**Option B — Local / VS Code**
```bash
git clone <repo-url>
cd movie-analysis
pip install pandas numpy scipy plotly notebook
jupyter notebook movies_analysis.ipynb
```

---

## 🧑 Author

**Sujan**  
IBM Professional Data Analyst Certificate  
[GitHub](https://github.com/Diamonic12) · [Tableau Public](https://public.tableau.com/app/profile/sujan.pathak/vizzes) · [LinkedIn](www.linkedin.com/in/sujan-pathak-bb917b197)

---
