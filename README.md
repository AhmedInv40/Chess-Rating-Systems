# Chess-Rating-Systems: Building and comparing Elo & Glicko-2.
A project that implements Elo and Glicko-2 rating systems from scratch, evaluates them on real chess data, and tests statistical hypothesis about factors that influence game outcomes.

## Data Sources
| Source | Format | Size | Description |
|--------|--------|------|-------------|
| [Lichess (Kaggle)](https://www.kaggle.com/datasets/datasnaek/chess) | CSV | 20,058 games | Online games with player ratings, results, openings, time controls |
| [FIDE Rating Lists](https://ratings.fide.com) | Fixed-width TXT | 1.8M players | Official ratings (Standard/Rapid/Blitz), titles, demographics |

## Key Results

| Metric | Baseline | Elo (from scratch) | Glicko-2 (from scratch) | Logistic Regression |
|--------|----------|---------------------|--------------------------|----------------------|
| **Brier Score** | 0.2380 | 0.2261 | 0.2280 | 0.2100 |
| **Log-Loss** | 0.6931 | 0.6688 | 0.6761 | 0.6360 |
| **Accuracy** | — | 59.9% | 59.6% | — |

*Post burn-in (2,000 games). Logistic regression evaluated on last 30% of games with pre-existing Lichess ratings.*

**Main findings:**

- **Elo is remarkably well-calibrated** — predicted probabilities closely match observed win rates across the full range (Brier score 0.2077 using pre-existing ratings).
- **Elo slightly outperforms Glicko-2** on this dataset, driven by dense game data that limits Glicko-2's uncertainty-tracking advantage.
- **White has a consistent +2.4 pp advantage** (p ≈ 7×10⁻¹¹), roughly constant across all skill levels.
- **Opening choice matters at the margins** 6 of 39 tested openings deviate significantly (±9 to ±17 pp) after Bonferroni correction, but rating difference remains the dominant predictor.
- **Lichess and FIDE are structurally different populations** (KS test D=0.247, Cohen's d=0.62) their rating scales are not directly comparable.
- **Rating difference captures nearly all predictive signal** adding opening and time control features to a logistic regression provides no net improvement over well-calibrated Elo.

## Notebook Sections

| # | Section | Description |
|---|---------|-------------|
| 1 | **Introduction** | Motivation, problem statement, data sources, prior work |
| 2 | **Data Loading & Inspection** | Lichess CSV (20,058 rows × 16 cols) + FIDE fixed-width TXT (1.8M rows × 19 cols) |
| 3 | **Cleaning & Preprocessing** | Deduplication (945 rows dropped), feature engineering (rating_diff, white_score, avg_rating, rating_band), FIDE ghost filtering (1.08M entries removed), column renaming, analysis-ready subsets |
| 4 | **Exploratory Data Analysis** | Rating distributions, white advantage by skill level, Elo calibration curve, opening effects, FIDE demographics (federation, age, gender), Lichess vs FIDE comparison |
| 5 | **Mathematical Framework** | Full Elo derivation (expected score, update rule, connection to SGD on log-likelihood), Glicko-2 derivation (g(φ), E function, volatility via MAP estimation, Illinois algorithm) |
| 6 | **Implementation** | From-scratch `EloSystem` and `Glicko2System` classes, chronological simulation on 19,113 games, evaluation with burn-in, rolling Brier score convergence, calibration curves, logistic regression comparison |
| 7 | **Hypothesis Testing** | 5 formal tests with appropriate methods and effect sizes |
| 8 | **Conclusions** | Key findings, implementation results, limitations |
| 9 | **Self-Assessment** | Evaluation against course grading criteria (87/100) |

## Hypothesis Tests

| # | Question | Test | Result | Effect Size |
|---|----------|------|--------|-------------|
| H1 | White advantage significant? | One-proportion z-test | **Reject H₀** (p ≈ 7×10⁻¹¹) | +2.4 pp above 50% |
| H2 | Varies by rating band? | Chi-square independence | Fail to reject H₀ (p = 0.45) | χ² = 7.82, df = 8 |
| H3 | Openings affect win rate? | Binomial + Bonferroni | Mixed — 6/39 significant | ±9 to ±17 pp deviation |
| H4 | Lichess ≠ FIDE distribution? | Kolmogorov-Smirnov | **Reject H₀** (p ≈ 0) | Cohen's d = 0.62 (medium) |
| H5 | Gender gap in FIDE? | Welch's t-test | **Reject H₀** (p ≈ 0) | Cohen's d = 0.47 (small), ~99 pts |

## Tools

- **Python 3** — pandas, NumPy, Matplotlib, seaborn, SciPy, scikit-learn
- **Jupyter Notebook** — single notebook, fully reproducible
- **No external rating libraries** — both Elo and Glicko-2 implemented from scratch

**Author:** Atilla Ahmed
