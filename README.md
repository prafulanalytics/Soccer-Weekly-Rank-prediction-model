# ELO Rating System Analysis — Bundesliga 2015/2016

This repository contains all code, data, and notebooks required to reproduce an end‑to‑end analysis of the German Bundesliga 2015/16 season with a custom ELO rating system.

---

## Introduction

The study tackles four tasks:

1. **Develop an ELO implementation** that supports arbitrary **K** and **s** parameters.  
2. **Apply** that model to every fixture of the 2015/16 Bundesliga using the starting values  
   `R₀ = 100`, `s = 15`, `K = 15`.  
3. **Optimise** the hyper‑parameters `(K, s)` on that season and display the final ranking table.  
4. **Quantify surprise** — identify the single most unexpected win of the season.

---

## Methodology

### Dataset & Pre‑processing

| Data‑set | Source | Key fields | Role |
|----------|--------|------------|------|
| **df** | Official match reports (2015/16) | Match‑day, date, home/away teams, score | Base table for every computation |
| **previous_year_rank_df** | Final table 2014/15 | Final ranks | Proxy for starting strength |
| **match_df** | Merge of the above | Adds previous‑season rank per fixture | Input to ELO |
| **weekly_rankings** | Derived from `match_df` | Rolling standings after each week | Validates weekly ELO ranks |
| **final_actual_rank** | Last week of `match_df` | Ground‑truth final table | Validates end‑of‑season ELO |

Full preparation code lives in **`notebooks/01_preprocessing.ipynb`**.

### ELO Algorithm

The core update rule is implemented in `src/elo.py`:

```
new_R = old_R + K ⋅ (S_actual − S_expected)
```

where *S* is scaled by the score‑spread factor **s**.

### Hyper‑parameter Search

A grid search scans a user‑defined range of **K** & **s** values and ranks each pair by
Brier score, accuracy, and log‑loss on one‑step‑ahead forecasts.

### Surprise Metric

We define
```
Surprise = −log₂(P_outcome)
```
with `P_outcome` the pre‑match ELO probability of the result that actually occurred.
The match with the highest Surprise is flagged as the *“Most Unexpected Win.”*

---

## Repository Layout

```
.
├── data/
│   ├── raw/
│   └── processed/
├── notebooks/
│   ├── 01_preprocessing.ipynb
│   ├── 02_elo_implementation.ipynb
│   ├── 03_hyperparameter_search.ipynb
│   └── 04_surprise_metric.ipynb
├── src/
│   ├── elo.py
│   └── utils.py
├── tests/
├── requirements.txt
└── README.md
```

---

## Quick‑Start

```bash
git clone https://github.com/your‑name/bundesliga‑elo‑1516.git
cd bundesliga‑elo‑1516
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

Run the notebooks in numerical order to reproduce every figure and table.

---

## License

This project is licensed under the MIT License. See `LICENSE` for details.

---

## Citation

> Your Name (2025). “ELO‑Based Rating and Surprise Quantification for the Bundesliga 2015/16 Season.” *GitHub repository*.

Happy analysing ⚽📊!
