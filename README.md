# Chess-Rating-Systems: Building and comparing Elo & Glicko-2.
A project that implements Elo and Glock-2 rating systems from scratch, evaluates them on real chess data, and tests statistical hypothesis about factors that influence game outcomes.

## Data Sources
| Source | Format | Size | Description |
|--------|--------|------|-------------|
| [Lichess (Kaggle)](https://www.kaggle.com/datasets/datasnaek/chess) | CSV | 20,058 games | Online games with player ratings, results, openings, time controls |
| [FIDE Rating Lists](https://ratings.fide.com) | Fixed-width TXT | 1.8M players | Official ratings (Standard/Rapid/Blitz), titles, demographics |
