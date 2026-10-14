# NBA Game Prediction Model

Predicting NBA game winners using only information available before tip-off. Home teams win 54.7% of games in this data, so that is the baseline the model has to beat (project goal: 60%+ accuracy).

**Status: in progress.** Data wrangling, exploratory analysis, and pre-game feature engineering are done. Modeling is next.

## Key findings so far

- **Home court advantage is real.** Home teams win 54.7% of games versus 45.3% for away teams. A permutation test (10,000 shuffles) gave p < 0.0001, so the gap is very unlikely to be chance.
- **Most game stats can't be used to predict.** Plus-minus (r = 0.80 with the result) is just the final margin, and stats like points and field goal percentage are only known after the game ends. Using them would be data leakage, so they are excluded from the model.
- **Pre-game features carry a modest signal.** Built from past games only, a team's season-to-date win percentage is the strongest (r = 0.27). Teams with the better record win more often, but the groups overlap heavily, which is expected for pre-game information.

## Background

The project is framed as a business problem for a fictional sports analytics startup, **BetEdge Analytics**. Its analysts pick winners with roughly 52% accuracy, and the company wants a model that does better and can process new games automatically. The full problem statement, success criteria, and constraints are in `BetEdge Proposal.pdf`.

## Data

- **Source:** the NBA Stats API, pulled with the `nba_api` package
- **Coverage:** three regular seasons (2023-24, 2024-25, 2025-26)
- **Size:** 7,380 team-game records, two rows per game (one per team)

## What's in this repo

```
Notebooks/              Data Wrangling and Exploratory Data Analysis notebooks
Data/                   Cleaned dataset used by the EDA notebook
Profile Reports/        Automated data profiling reports from the wrangling step
BetEdge Proposal.pdf    Problem statement and project scope
```

**Wrangling:** combined three seasons of game logs, created home/away and opponent columns from the matchup text, profiled the data, and handled one missing value.

**EDA:** compared every game stat with winning, found redundant features (for example REB and DREB, FTM and FTA), tested the home advantage, and identified leakage.

**Feature engineering:** for each team and its opponent, calculated season-to-date win percentage and 10-game rolling averages (points, FG%, 3P%, defensive rebounds, assists, turnovers), shifted by one game so a game never sees its own stats.

## Next steps

- Preprocess: keep only pre-game features and drop early-season rows with no history.
- Split the data by date (train on earlier games, test on later ones).
- Train and compare logistic regression and random forest against the 54.7% baseline, and report accuracy and feature importance.

## Tools

Python, pandas, NumPy, Matplotlib, Seaborn, nba_api, Jupyter (scikit-learn for modeling next)

## Running the notebooks

```bash
pip install pandas numpy matplotlib seaborn nba-api jupyter
jupyter lab
```

Run the Data Wrangling notebook first. It saves the cleaned data, which the Exploratory Data Analysis notebook loads from the `Data/` folder.

## Author

Terrance Woolfork

[GitHub](https://github.com/terrqnce) | [LinkedIn](https://linkedin.com/in/terrqnce)
