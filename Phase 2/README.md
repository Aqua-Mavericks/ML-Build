# 🚰 Phase 2 — Anomaly Detection (Industrial Water Quality Monitoring)

> **Short take:** this phase builds the system that notices when water or sensors act weird — not later, not after someone sees a chart, but **now** ⚡.  
It uses **feature engineering + anomaly models + correlation-aware rules** to surface *real problems* while avoiding **alarm fatigue** 🚨.

---

## 🛣️ Roadmap 

1. 🔗 **Correlation-first checks** — enforce physical/chemical rules between pH, TDS, Turbidity, and Temperature.  
2. 🤖 **Unsupervised baseline** — Isolation Forest + lightweight statistical guards.  
3. 👩‍💻 **Operator-in-the-loop** — ops feedback → refine thresholds → cut false positives.  
4. 📦 **Edge-ready rollouts** — ONNX/Joblib artifacts with logging + safe fallbacks.  

---

## 🎯 Goal

Detect **abnormal behaviour** in the four core water signals —  
🌡️ Temperature, 🧂 TDS, 🌫️ Turbidity, ⚗️ pH —  
and provide **clear, explainable alerts** that operators can act on instantly.  

💡 **Real-world uses:** leak detection, contamination alerts, sensor-fault detection, predictive maintenance, compliance evidence.

---

## 📓 Current Implementation (Notebook: `water-anomaly.ipynb`)  

✅ **Libraries:** `pandas`, `scikit-learn (IsolationForest)`, `joblib`  
✅ **Data pipeline:** CSV → timestamp parsing → chronological sort  
✅ **Engineered features:**  
- `ph_dev` → deviation from neutral pH (7)  
- `tds_temp_ratio` → ratio of TDS to temperature  
- `turbidity_x_ph` → turbidity × pH  
- `temp_sqr` → squared temperature (nonlinear effect)  

🤖 **Model:**  
- Isolation Forest (`n_estimators=100`, `contamination=0.05`, `random_state=42`)  
- Trained on engineered features  

📊 **Outputs:**  
- `anomaly_score` → continuous decision function  
- `anomaly_raw` → raw prediction (`-1` anomaly, `1` normal)  
- `anomaly_status` → mapped (`Anomaly` / `Normal`)  

---

## 🔗 Correlation & Interdependency  

Sensors interact like a **mini chemical system** 🧪. We test if readings fit expected patterns.  

| Trigger              |        pH | Temperature |   TDS | Turbidity |
| -------------------- | --------: | ----------: | ----: | --------: |
| pH ↑ (alkaline)      |        —  | slight ↑   | salts → ↑/↓ | ↓ microbes → ↓ |
| pH ↓ (acidic)        |        —  | slight ↓   | solids dissolve → ↑ | organics decay → ↑ |
| Temp ↑               | small ↓   |        —   | dissolve solids → ↑ | microbial growth → ↑ |
| Temp ↓               | small ↑   |        —   | crystallization → ↓ | less bioactivity → ↓ |
| TDS ↑                | buffered  | often ↑    |    —   | suspended load → ↑ |
| Turbidity ↑          | decay → ↓ | slight ↑   | affects TDS readings | — |

⚠️ **Example rule:** If pH ≈ 9 & Temp ≈ 50°C → 🚩 mismatch (chemically odd).

---

## 🧩 Engineered Features (Why they matter)

- 📉 **Rolling stats** (`*_rollmean_k`, `*_rollstd_k`) → smooth baselines.  
- 🔀 **Deltas & rates** (`*_diff1`, `*_rate`) → sudden jumps/drifts.  
- 🔗 **Interactions** (e.g. `tds_temp_ratio`) → capture chemistry logic.  
- 📏 **Robust z-scores/IQR** → outlier guards.  

> 🗂️ Keep a **versioned config** so inference = training features.

---

## ⚙️ Step-by-step Pipeline  

1. ⏳ Ingest & parse timestamps → keep order.  
2. 🧹 Clean missing/dup values → safe forward/backfill.  
3. ⏱️ Resample → fixed cadence (1–5 min).  
4. 🛠️ Engineer features → rolling stats, deltas, ratios.  
5. 📊 Train-test split (chronological).  
6. 📏 Fit scaler → persist.  
7. 🤖 Train Isolation Forest + stats thresholds.  
8. ⚖️ Calibrate thresholds → add correlation rules.  
9. 🧪 Validate with synthetic anomalies (spikes, drifts, flatlines).  
10. 🚀 Export model + config + correlation matrix.  

---

## 📈 Evaluation & Metrics  

- If **labeled**: Precision, Recall, F1, PR-AUC, Latency.  
- If **unlabeled**: operator review + backtests with injected anomalies.  
- Always visualize: 📉 anomaly scores vs. timeline + 🚩 flagged events.

---

## 🛠️ Ops & Monitoring  

- 📊 Drift monitors → mean/variance per signal.  
- ⏰ Daily checks → anomaly count, latency.  
- 📝 Logging → input + score + correlation flags.  
- ♻️ Retraining triggers → drift or false positive creep.  

---

## 🚧 Known Limitations  

- ⚡ **Unsupervised noise:** rare but valid states flagged → mitigate with debounce + operator feedback.  
- 🌍 **Site-specific chemistry:** correlation baselines need tuning.  
- 🛑 **Sensor faults:** solve with cross-sensor consistency, not single-sensor alarms.  

---

## 👥 Authors  

- **Pushkar Arora**  
  - [GitHub](https://github.com/pushkar1887) | [LinkedIn](https://www.linkedin.com/in/pushkar-arora-0b3599356/) | [Kaggle](https://www.kaggle.com/pushkararora)  

- **Satyam Choudhary**  
  - [GitHub](https://github.com/SatyamChoudhary1909) | [LinkedIn](https://www.linkedin.com/in/satyam-choudhary-114b89325/)  

---
