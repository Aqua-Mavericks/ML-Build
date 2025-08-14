# Industrial Water Safety Classifier 💧🔬

A **machine learning** solution that classifies water quality as **Safe**, **Warning**, or **Danger** based on physical and chemical sensor readings.  
Designed for **industrial monitoring** where **real-time safety checks** are critical.

---

## 📌 Overview
This repository includes:
- **Trained Random Forest Classifier** for water safety prediction
- **Preprocessed dataset** from industrial sensor readings
- **Jupyter Notebook** for training, analysis, and visualization
- Model artifacts (`.pkl`) for direct deployment:
  - `water_quality_classifier.pkl` — trained classification model
  - `label_encoder.pkl` — encoder mapping class labels to numeric form

---

## 🗂 Workflow
### 1️⃣ Dataset
- **File:** `shu_cor_water_Q.csv`
- **Rows:** 3,298 | **Cols:** 7
- **Features:** `TDS`, `turbidity`, `temperature`, `pH`
- **Target:** `status` → *Safe*, *Warning*, *Danger*
- **Note:** `cause` column is intentionally populated **only** for unsafe water samples

### 2️⃣ Preprocessing
- Encoded `status` into numeric labels using **LabelEncoder**
  - Safe → 0  
  - Warning → 1  
  - Danger → 2
- Train/test split: **80% / 20%**
- No synthetic balancing — model handles class imbalance naturally

### 3️⃣ Model
- **Algorithm:** RandomForestClassifier  
- **Parameters:** `n_estimators=100`, `random_state=42`
- **Why:** High accuracy, interpretable feature importance, robust to noise

### 4️⃣ Evaluation
| Class   | Precision | Recall | F1-score |
|---------|-----------|--------|----------|
| Safe    | 1.00      | 1.00   | 1.00     |
| Warning | 0.99      | 0.98   | 0.99     |
| Danger  | 0.98      | 0.98   | 0.98     |

✅ **Test Accuracy:** ~99.88%  

---

## 📊 Dataset Features
- **TDS** — Total Dissolved Solids *(ppm)*
- **Turbidity** — Water cloudiness *(NTU)*
- **Temperature** — °C
- **pH** — Acidity/alkalinity

---



## 🛠️ Installation & Usage
```bash
# Clone repository
git clone https://github.com/yourusername/industrial-water-safety-classifier.git
cd industrial-water-safety-classifier

# Create virtual environment
python -m venv waterenv
source waterenv/bin/activate    # On Windows: waterenv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run Jupyter Notebook
jupyter notebook water-quality-classifier.ipynb
```

🔗 Connect
📬 Author: Pushkar Arora, Satyam Choudhary
- [Pushkar's Github](https://github.com/pushkar1887)
- [Satyam's Github](https://github.com/SatyamChoudhary1909)
- [LinkedIn](https://www.linkedin.com/in/pushkar-arora-0b3599356/)
- [Kaggle](https://www.kaggle.com/code/pushkararora/water-quality-classifier)
