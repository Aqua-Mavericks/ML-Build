
---

## **MODEL_CARD.md**
```markdown
# Model Card — Industrial Water Safety Classifier

## Model Details
- **Name:** Industrial Water Safety Classifier
- **Algorithm:** RandomForestClassifier
- **Version:** 1.0
- **Author:** Pushkar Arora
- **Framework:** scikit-learn 1.5.1

## Intended Use
- **Primary purpose:** Classify water quality into Safe, Warning, or Danger categories based on sensor readings.
- **Intended users:** Industrial operators, environmental monitoring agencies, researchers.
- **Out-of-scope use cases:** Medical diagnosis, legal decision-making, or any high-risk safety-critical systems without human oversight.

## Training Data
- **Dataset:** `shu_cor_water_Q.csv`
- **Rows:** 3,298
- **Features used:** TDS, Turbidity, Temperature, pH
- **Target variable:** status (Safe / Warning / Danger)

## Metrics
- **Accuracy (test set):** 99.88%
- **Per-class F1 scores:**
  - Safe: 1.00
  - Warning: 0.99
  - Danger: 0.98

## Ethical Considerations
- False negatives for “Danger” class can result in serious consequences if used in production.
- Always ensure sensor calibration and data integrity before inference.
- Use as a decision-support tool, not as a sole authority.


