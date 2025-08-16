💧 Industrial Water ML Pipeline (Multi-Phase)
🌍 Vision

The goal of this project is to build an integrated machine learning system that combines:

Classification (status detection)

Anomaly Detection (unexpected behavior)

Predictive Maintenance (prevent equipment failure)

Parameter Forecasting (future trends)

together into one pipeline for industrial water monitoring & management.

Why?
Industrial water systems generate huge amounts of sensor data. Misjudgments can lead to unsafe water usage, equipment breakdown, or wastage. A single spike in readings is common due to noise—but long-term patterns matter more. This pipeline is designed to capture both instantaneous risks and future behaviors, giving industries a decision-support system for sustainability.

This repo is not just code—it’s a foundation. With more sensor parameters and richer datasets, this system can evolve further.
✅ All models and artifacts are open for use, extension, and improvement.

📊 Datasets Used

shu_cor_water_Q.csv (core dataset, 3,298 rows) → Used in Phase 1 & Phase 2

anomaly_detection.csv (derived in Phase 2) → Used in Phase 3

maintenance_labeled_data.csv (derived in Phase 3) → Used in Phase 4

Each phase builds upon the previous one, ensuring smooth integration.

🏗️ Pipeline Overview
🔹 Phase 1 — Classification

Task: Predict water quality status (Safe / Warning / Danger).

Model: RandomForestClassifier (n_estimators=100, random_state=42).

Features: TDS, turbidity, temperature, pH.

Label Encoding: Status column converted using LabelEncoder → Safe=0, Warning=1, Danger=2.

Why Label Encoding? Makes categorical target usable for ML, ensures consistent mapping across training and deployment.

Outcome:

~99.88% accuracy

F1 Scores → Safe 1.00, Warning 0.99, Danger 0.98

Artifacts:

water_quality_classifier.pkl

label_encoder.pkl

MODEL_CARD.md (usage, dataset, risks, metrics).

🔹 Phase 2 — Anomaly Detection

Task: Detect abnormal sensor behavior.

Approach: Start with Safe-only data → train IsolationForest → detect anomalies in full dataset.

New dataset created: anomaly_detection.csv with original readings + engineered features + anomaly flags.

Features engineered:

ph_dev (distance from neutral pH=7)

tds_temp_ratio, turbidity_x_ph

rolling averages (tds_rollmean_3) and rolling std (ph_rollstd_3)

diffs (temp_diff, tds_diff, ph_diff)

Why? Industrial data often has noise spikes. Using feature engineering + anomaly detection helps highlight unusual patterns without false alarms.

🔹 Phase 3 — Predictive Maintenance

Task: Predict when maintenance is required.

Rule-based labeling: Maintenance needed if

|tds_diff| > 100 OR

|temp_diff| > 5 OR

|ph_diff| > 0.5

Lag features: Used past 3 readings (t-1, t-2, t-3) for each parameter.

Why? Avoids reacting to a single noisy spike. Instead, decisions are based on patterns over time.

Model: RandomForestClassifier (n_estimators=150, max_depth=7, random_state=42).

Outcome: ~0.99 accuracy (temporal split, no shuffle).

Artifacts:

predictive_maintenance_model.pkl

maintenance_labeled_data.csv.

🔹 Phase 4 — Parameter Forecasting

Task: Forecast future values of Temp, TDS, Turbidity, pH.

Approach: Separate regression models per parameter.

Models: RandomForestRegressor (n_estimators=100, random_state=42).

Features: Sensor readings + engineered features (ph_dev, tds_temp_ratio, diffs, rolling stats).

Lags: Included past 3 readings for each parameter to learn temporal patterns.

Artifacts:

temp_regression_forecast_model.pkl

tds_regre_forecast_model.pkl

turbidity_forecast_model.pkl

pH_forecast_model.pkl.

🧠 Design Ideology

Integration-first: Each phase feeds into the next, enabling a pipeline from raw CSV → anomalies → maintenance → forecasts.

Noise-aware: Spikes ≠ real problems. Models consider temporal consistency before flagging risks.

Explainability: Clear MODEL_CARDS and metrics → helps industries trust the pipeline.

Scalability: New sensor parameters can be added into the same pipeline.

Openness: Free to use, adapt, and build upon.

⚙️ Requirements

Python 3.10

Libraries:
numpy, pandas, scikit-learn==1.5.1, matplotlib, seaborn, joblib, flask, jupyter

Install:

pip install -r requirements.txt

🚀 Quick Start
git clone https://github.com/Aqua-Mavericks/ML-Build.git
cd ML-Build
python -m venv venv
source venv/bin/activate   # (or venv\Scripts\activate on Windows)
pip install -r requirements.txt
jupyter notebook

🔮 Roadmap

Live sensor integration with Flask / Node-RED

Online anomaly detection (streaming)

Probabilistic forecasting (uncertainty-aware models)

Scaling pipeline for larger datasets

🔗 Connect

📬 Authors: Pushkar Arora, Satyam Choudhary

Pushkar's Github

Satyam's Github

Pushkar's LinkedIn

Satyam's LinkedIn

Kaggle
