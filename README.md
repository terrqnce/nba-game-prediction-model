# NBA Game Prediction Model

**Status: in progress.** Data wrangling, exploratory analysis, and pre-game feature engineering are complete. Modeling is next.

## Project overview

This project is built around a business scenario: **BetEdge Analytics**, a sports analytics startup, wants to replace manual game analysis with a machine learning model that predicts NBA game outcomes. Its analysts currently pick winners with roughly 52% accuracy. The goal is a model that reaches **60%+ accuracy** using only data that is available before a game starts.

A useful reference point is the **home-team baseline**: in this data, home teams win 54.7% of games, so simply picking the home team every time is already right 54.7% of the time. The model has to beat that to add value.

The full problem statement, success criteria, scope, and constraints are in `BetEdge Proposal.pdf`.

## Data

- **Source:** the NBA Stats API, pulled with the `nba_api` Python package (`LeagueGameLog`)
- **Coverage:** three regular seasons (2023-24, 2024-25, 2025-26)
- **Size:** 7,380 team-game records, with two rows per game (one for each team)

## Repository structure

```
notebooks/
  Data Wrangling.ipynb              Pull, check, and clean the raw data
  Exploratory Data Analysis.ipynb   Explore relationships, test home court, build features
data/
  nba_games_cleaned.csv             Cleaned dataset used by the EDA notebook
BetEdge Proposal.pdf                Problem statement and project scope
```

## What has been done

### 1. Data wrangling
- Pulled three seasons of game logs from the NBA API and combined them.
- Created a home/away flag and an opponent column from the `MATCHUP` text.
- Profiled the data and checked quality. The data was already clean, with one missing free-throw percentage (a game with zero attempts), which was filled with 0.
- Confirmed that the games with 290 minutes were double-overtime games, not errors.
- Dropped an unused column and saved the cleaned file.

### 2. Exploratory data analysis
- **Home court advantage is real.** Home teams win 54.7% of games versus 45.3% for away teams. A permutation test (10,000 shuffles) gave p < 0.0001, so the difference is very unlikely to be chance.
- **Same-game stats track winning.** Points (r = 0.50), field goal percentage (0.47), field goals made (0.43), defensive rebounds (0.38), and assists (0.33) are all positively related to winning. Free throws and shot attempts show little relationship.
- **Redundant features.** REB and DREB, FTM and FTA, PTS and FGM, and made versus attempted shots overlap heavily, so only one of each is needed.
- **Data leakage identified.** Plus-minus (r = 0.80 with the result) is just the final point margin, and every other same-game stat is also only known after the game ends. None of these can be used to predict a game.

### 3. Pre-game feature engineering
To avoid leakage, features are built only from games played before the one being predicted, using lagged calculations (`shift(1)`):

- Season-to-date win percentage for each team
- 10-game rolling averages for points, FG%, 3P%, defensive rebounds, assists, and turnovers
- The same features for the **opponent**, merged onto each game
- The difference between a team and its opponent in win percentage

Prior win percentage is the strongest pre-game feature (r = 0.27). Teams with the better record win more often, but the groups overlap heavily, which is expected for pre-game information in a
