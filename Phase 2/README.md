
# Phase 2 — Anomaly Detection (Industrial Water Quality Monitoring)

> **Short take:** this phase builds the system that notices when water or sensors act weird — not later, not after someone sees a chart, but **now**. It uses feature engineering, anomaly models, and correlation-aware rules to surface real problems and avoid alarm fatigue.

---

## Roadmap 

1. **Correlation-first checks** — enforce physical/chemical relationships between pH, TDS, Turbidity, and Temperature.
2. **Unsupervised baseline** — Isolation Forest + lightweight statistical guards.
3. **Operator-in-the-loop** — labels from ops improve thresholds and reduce false positives.
4. **Edge-ready rollouts** — ONNX/Joblib artifacts with logging and safe fallbacks.

---

## Goal

Detect anomalous behaviour in the four core water signals — **Temperature, TDS, Turbidity, and pH** — and provide clear, explainable alerts that operators can act on quickly.

---



**Real-world uses:** early leak detection, contamination alerts, sensor-fault detection, maintenance scheduling, regulatory compliance evidence.



## 🧾 Current Implementation (Notebook Summary)  

The notebook **`water-anomaly.ipynb`** implements a **baseline anomaly detection pipeline**:

- **Libraries used:** `pandas`, `scikit-learn (IsolationForest)`, `joblib`  
- **Data loading:** CSV → timestamp parsing → chronological sorting  
- **Feature engineering (implemented):**  
  - `ph_dev` → deviation from neutral pH (7)  
  - `tds_temp_ratio` → ratio of TDS to temperature  
  - `turbidity_x_ph` → interaction of turbidity and pH  
  - `temp_sqr` → squared temperature for nonlinear effects  
- **Model:**  
  - Isolation Forest (`n_estimators=100`, `contamination=0.05`, `random_state=42`)  
  - Trained on engineered features  
- **Outputs:**  
  - `anomaly_score` → continuous decision function  
  - `anomaly_raw` → raw prediction (`-1` anomaly, `1` normal)  
  - `anomaly_status` → mapped string label (`Anomaly`/`Normal`)  
---

## Correlation & Interdependency 

Sensors interact — we treat the network as a small chemical system and test whether the readings fit expected relationships.

**Short reference matrix:**

| Trigger              |                     pH |             Temperature |                                  TDS |                                 Turbidity |
| -------------------- | ---------------------: | ----------------------: | -----------------------------------: | ----------------------------------------: |
| pH ↑ (more alkaline) |                      — |     slight ↑ (indirect) |                     depends on salts |          fewer microbes → lower turbidity |
| pH ↓ (more acidic)   |                      — |      slight ↓/no change |          can dissolve solids (TDS ↑) |         organics break down → turbidity ↑ |
| Temp ↑               |          small pH drop |                       — |         solids dissolve more (TDS ↑) |        microbial activity ↑ → turbidity ↑ |
| Temp ↓               |          small pH rise |                       — |     possible crystallization (TDS ↓) |            less bioactivity → turbidity ↓ |
| TDS ↑                |        buffered change | often with Temp pattern |                                    — | more suspended/mineral load → turbidity ↑ |
| Turbidity ↑          | may lower pH via decay | may slightly raise Temp | suspended solids affect TDS readings |                                         — |

**Practical rule example:** *If pH ≈ 9 then Temp ≈ 30°C is expected; seeing Temp ≈ 50°C at pH 9 should be flagged as an abnormal correlation.*

---

## Signals & Roles (what each tells us)

* **Temperature:** thermal regime — affects solubility, reaction rates, and microbial growth.
* **TDS (Total Dissolved Solids):** salt/mineral concentration — linked to scaling, conductivity, and chemical load.
* **Turbidity:** suspended particles / biomass — proxy for clarity, pollution, and blooms.
* **pH:** acid/base balance — controls chemistry: solubility, corrosion, and biological viability.

Each raw signal is necessary but not sufficient — we score them and then check pairwise/triadic consistency.

---

## Engineered Features (why they matter)

* **Rolling means/stddev (`*_rollmean_k`, `*_rollstd_k`)** — capture local baseline and volatility.
* **Deltas (`*_diff1`, `*_rate`)** — detect jumps and sudden drifts.
* **Interactions (`tds_temp_ratio`, `turbidity_x_ph`)** — encode expected chemistry relationships.
* **Z-scores / IQR flags** — robust outlier probes.

Freeze this exact list in a config (versioned) so inference matches training.

---

## Modeling approach (practical)

**Baseline (fast, reliable):** Isolation Forest.

* Why: works well with tabular features, low latency, easy to serialize.


---

## Thresholding & Decision Logic

* Model outputs a score. Convert score → decision using: percentile cutoffs + domain rules + correlation checks.
* **Safety posture:** bias thresholds toward recall (don’t miss dangerous events). Use debounce/hysteresis to reduce alarm noise.
* **Correlation gate:** even if score is low, inconsistent parameter pairings (example above) can force an alert.

---

## Step-by-step Pipeline

1. **Ingest & parse** timestamps; keep chronological integrity.
2. **Clean** gaps & duplicates; forward/backfill only within safe windows.
3. **Resample** to a fixed cadence (1–5 minutes) for stable features.
4. **Feature engineer** rolling windows, deltas, interactions.
5. **Split chronologically**: train = older data, validation = recent.
6. **Fit scaler** on train; persist the scaler.
7. **Train Isolation Forest** and baseline statistical thresholds.
8. **Calibrate** thresholds with safety bias; add correlation rules.
9. **Validate** using labeled incidents or synthetic injections (spikes, drifts, stuck sensors).
10. **Export** model + config + baseline correlation matrix for deployment.

---


## Evaluation & Metrics

* **If labeled:** Precision, Recall, F1, PR-AUC, detection latency.
* **If unlabeled:** operator review, backtest against injected anomalies, correlation breach counts.

Visuals to keep: score timeline, anomaly bands, and per-event feature z-scores.

---

## Operations & Monitoring

* **Drift monitors:** per-feature mean/variance alerts vs baseline.
* **Daily health checks:** anomaly volume trends, model runtime latency.
* **Logging:** store raw payload + score + correlation-check results for each event.
* **Retraining trigger:** set thresholds on drift or a steady increase in false positives.

---

## Known limitations & mitigations

* **Unsupervised noise:** flags legitimate rare states — mitigate with operator feedback and conservative de‑bouncing.
* **Site-specific chemistry:** correlation baselines must be tuned per site.
* **Sensor failure modes:** use sensor self-checks and cross-sensor consistency rather than single-sensor alarms alone.

---

---

📌 Authors  

- **Pushkar Arora**  
  - [GitHub](https://github.com/pushkar1887)  
  - [LinkedIn](https://www.linkedin.com/in/pushkar-arora-0b3599356/)  
  - [Kaggle](https://www.kaggle.com/pushkararora)  

- **Satyam Choudhary**  
  - [GitHub](https://github.com/SatyamChoudhary1909)  
  - [LinkedIn](https://www.linkedin.com/in/satyam-choudhary-114b89325/)  

---

