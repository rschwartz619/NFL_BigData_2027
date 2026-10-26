# NFL_BigData_2027
NFL Big Data 2027 Project

## Wide receiver rookie redraft

Run the forward-in-time prediction backtest from the repository root:

```powershell
.\.venv\Scripts\python.exe .\redraft_wide_receivers.py
```

The script reads the competition CSVs from `data\nfl-big-data-bowl-2027` and
writes:

- `outputs\wr_predictive_redraft.csv`: out-of-time forecasts for each later
  class, with predicted rookie yards and a predicted ranking for each model.
- `outputs\wr_predictive_backtest.csv`: model accuracy by held-out draft class.
- `outputs\wr_redraft_scorecard.csv`: outcomes and Combine/tracking inputs.
- `outputs\wr_redraft_coverage.csv`: cohort coverage and route eligibility.

The prediction target is **rookie-season regular-season receiving yards**,
transformed with `log1p` while fitting to reduce the influence of extreme
outliers. Receiving yards measure realized rookie contribution, not pure
receiver skill: they also depend on team, quarterback, health, and playing
opportunity. Results should be interpreted as early-career contribution, not
career value or salary-adjusted value.

The backtest is chronological: 2024 is predicted using only the 2023 Combine
class; 2025 is predicted using only 2023 and 2024. It compares a prior-class
mean, a draft-pick-only baseline, a ridge model using pre-NFL Combine
measurements, and the same model with additional route-drill tracking
summaries. **Draft position is not a feature in either Combine model**, so the
Combine redraft can differ from the original order. Missing predictor values
are imputed from the training classes only; standardization and model fitting
also use training data only.

The model uses a fixed ridge penalty and a small, hand-selected feature set.
The backtest reports log-scale MAE, receiving-yard MAE, rank correlation, and
top-five overlap. Because there are only three draft classes, this is a small
historical test—not strong evidence of general predictive performance. The
2025 class is the newest class with rookie outcomes in these files, so the
script cannot produce a forecast for an unevaluated upcoming class yet.

The route-drill summaries (mean within-attempt speed range, peak speed, and
peak acceleration) are descriptive features, not validated biomechanics
metrics. The separate scorecard's `rookie_value_index` is still hindsight-only;
use `wr_predictive_redraft.csv` for the actual held-out predictions.
The `--min-routes` and `--min-separation-observations` options only affect
eligibility in that retrospective scorecard; the predictive forecasts include
all Combine WRs, including players with zero recorded rookie receiving yards.
