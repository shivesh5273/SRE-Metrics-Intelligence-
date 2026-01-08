# SRE Metrics Intelligence (AWS CloudWatch) — Anomaly Detection + Forecasting

A compact, resume-grade time-series ML project that simulates a real SRE/Platform workflow:
- load + standardize monitoring metrics
- detect anomalies (baseline + unsupervised ML)
- forecast near-future metric values (lag-based regression)
- export artifacts (plots, CSV rankings, JSON metrics, serialized models)

This notebook demonstrates practical ML applied to monitoring signals — relevant for **Backend / Systems / SRE-adjacent ML / Platform Engineering** roles.

---

## Dataset
Source: **Kaggle – Numenta Anomaly Benchmark (NAB)**  
We use the AWS CloudWatch subset:
- `archive/realAWSCloudwatch/realAWSCloudwatch/ec2_cpu_utilization_53ea38.csv`

The project is built so you can easily switch the `SERIES_PATH` to any other CSV in the dataset.

---

## What this project does

### 1) Data loading & resampling
- parses `timestamp` as timezone-aware UTC datetimes
- ensures numeric `value`
- sorts and resamples to a regular cadence (**5 minutes**)
- interpolates small gaps to make the series model-ready

### 2) Anomaly detection
Two methods:
1. **Baseline z-score residual detector**  
   Flags points where rolling z-score exceeds a threshold (`|z| > 3.0`).
2. **Isolation Forest (unsupervised ML)**  
   Trained on lightweight monitoring features:
   - `value`, `diff`, `roll_mean`, `roll_std`  
   Outputs:
   - anomaly flags
   - anomaly scores (ranked list for investigation)

### 3) Forecasting (short-term)
- builds **lag features** (last 24 timesteps ≈ ~2 hours at 5-min intervals)
- trains a **Ridge regression** forecaster
- evaluates with:
  - MAE
  - RMSE

### 4) Artifact export (GitHub-friendly)
Exports:
- `images/anomaly_plot.png`
- `artifacts/top_50_anomalies.csv`
- `images/forecast_plot.png`
- `artifacts/forecast_metrics.json`
- `artifacts/isolation_forest.joblib`
- `artifacts/ridge_forecaster.joblib`

---

## Results summary (your run)
- Resample frequency: **5 minutes**
- Baseline anomalies flagged: **5**
- IsolationForest anomalies flagged: **121**
- Forecast MAE: **0.0469**
- Forecast RMSE: **0.0676**

**Interpretation**
- Baseline method is conservative (few flags).
- Isolation Forest is more sensitive and provides a ranked anomaly list for triage.
- Ridge forecaster tracks short-term dynamics closely (low MAE/RMSE).

---

