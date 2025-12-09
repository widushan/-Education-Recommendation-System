# Education Recommendation System — Application Description

This document explains the Education Recommendation System and provides an interview-ready description of the application's purpose, architecture, data flow, model, evaluation, deployment, limitations, and common interview Q&A.

---

## 1. Project Overview

- **Name:** Education Recommendation System
- **Goal:** Suggest likely career paths for students based on academic scores and contextual features. The app returns the top-3 career recommendations (with probabilities) to guide students and counselors.
- **Audience:** Students, career advisors, school administrators, educational platforms.
- **What it does:** Accepts student attributes (gender, part-time job flag, attendance, extracurriculars, weekly study hours, per-subject scores, and aggregate scores) via a web form, transforms inputs to the same representation used at training time, calls a pre-trained scikit-learn classifier to compute probabilities, and shows the top recommendations.

---

## 2. High-level System Architecture

- **Web framework:** Flask application in `app.py` serves routes and templates.
- **Templates:** `templates/home.html`, `templates/recommend.html`, `templates/results.html` for UI and forms.
- **Static assets:** `static/` for CSS/JS (if present).
- **Model artifacts:** `Models/scaler.pkl` and `Models/model.pkl` — a fitted scaler and a trained scikit-learn classifier (pickled).
- **Main endpoints:**
  - `GET /` → `home.html`
  - `GET /recommend` → `recommend.html` (form)
  - `POST /pred` → reads form data, calls `Recommendations(...)`, renders `results.html` with top recommendations

---

## 3. Input Features (as implemented in `app.py`)

- Categorical / Binary:
  - `gender` → encoded inline as `female` = 1, else 0
  - `part_time_job` → boolean → 1/0
  - `extracurricular_activities` → boolean → 1/0
- Numeric:
  - `absence_days` (int)
  - `weekly_self_study_hours` (int)
  - Subject scores (ints): `math_score`, `history_score`, `physics_score`, `chemistry_score`, `biology_score`, `english_score`, `geography_score`
  - Aggregates: `total_score` (float), `average_score` (float)

Order and exact feature format must match the training-time ordering used when the model and scaler were created.

---

## 4. Data Preprocessing & Feature Handling

- Inference-time preprocessing (in `Recommendations` function):
  - Manual encoding of categorical flags into 0/1 integers.
  - Assemble features into a NumPy 2D array with shape `(1, n_features)`.
  - Call `scaler.transform(feature_array)` where `scaler.pkl` is the same `StandardScaler` (or similar) fit during training.
- Rationale for scaling: prevents features with large ranges (e.g., scores vs. binary flags) from dominating, helps numeric stability and performance for many ML algorithms.
- Important interview note: Always preserve training-time transforms (scalers, encoders) and save them to disk with the model.

---

## 5. Model & Training (what to say in an interview)

- The app uses a pickled scikit-learn classifier with `predict_proba` support and a saved scaler.
- Likely classifier choices include `RandomForestClassifier`, `GradientBoostingClassifier`, `LogisticRegression` (multinomial), or `XGBClassifier`.
- Typical training pipeline to describe:
  1. Load and clean `student-scores.csv` (handle NaNs and type issues).
  2. Feature engineering (create totals/averages, interactions, bin scores if needed).
  3. Encode categorical variables consistently (LabelEncoder or OneHotEncoder saved for inference).
  4. Split data: train/validation/test or use cross-validation.
  5. Fit a `StandardScaler` (or `MinMaxScaler`) on training features and transform features.
  6. Train different classifiers and tune hyperparameters with `GridSearchCV` or `RandomizedSearchCV`.
  7. Evaluate with proper metrics (Top-K accuracy, confusion matrix, per-class F1), select best model.
  8. Save the final `scaler` and `model` with `pickle.dump`.

- How to inspect the model locally (quick snippet):

```powershell
python - <<'PY'
import pickle
m = pickle.load(open('Models/model.pkl','rb'))
print(type(m))
print('Has predict_proba:', hasattr(m, 'predict_proba'))
try:
    print(m.get_params())
except Exception as e:
    print('get_params() not available:', e)
PY
```

---

## 6. Prediction Flow (code-level sequence)

1. User fills and submits the form from `recommend.html`.
2. Browser sends `POST /pred` with form fields.
3. `app.py` reads and converts form values (strings → ints/floats/booleans).
4. `Recommendations(...)` encodes categorical values, builds feature array, calls `scaler.transform()`.
5. `model.predict_proba(scaled_features)` returns class probabilities array.
6. Top-3 classes are selected (highest probabilities) and returned to the view.
7. `results.html` displays career names and their probabilities.

---

## 7. Evaluation & Metrics (recommended answers)

- Core metrics:
  - **Accuracy** (baseline) — but not sufficient for multi-class with many labels.
  - **Top-K accuracy** (Top-3): critical for recommenders — measures whether the true label is among the top K predictions.
  - **Confusion Matrix**: to understand systematic confusions between classes.
  - **Precision/Recall/F1 per class**: especially for minority classes.
- Model selection strategy: use k-fold cross-validation, report mean and variance of metrics.
- If classes are imbalanced: consider class weights in model, resampling (SMOTE, undersample), or evaluate with balanced accuracy.
- Probability calibration: if you rely on predicted probabilities, check calibration curves and apply Platt scaling or isotonic regression if necessary.

---

## 8. Deployment & Running Locally

- Local run (PowerShell):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install flask numpy scikit-learn==1.3.2
python app.py
```

- Access the app at `http://127.0.0.1:5000/`.
- Production recommendations:
  - Use a production WSGI server: `gunicorn` (Linux) or `waitress` (Windows).
  - Containerize with Docker and deploy to Azure App Service, AWS Elastic Beanstalk, or a Kubernetes cluster.
  - Add logging, error handling, input validation, and rate limiting.
  - Secure model artifacts and restrict access to sensitive files.

---

## 9. Limitations & Future Improvements

- Current dataset features are limited to scores and a few flags — lacks user preferences, projects, internship history, personality/interest survey data.
- `class_names` are hard-coded in `app.py`. Dynamically load mapping from training metadata to avoid mismatches.
- No server-side input validation — malformed input may crash the app.
- No authentication, monitoring, or model versioning.

Planned improvements:
- Add input validation and user-friendly error messages.
- Introduce explainability (SHAP values) to show why each career was recommended.
- Track user feedback (accepted/rejected recommendations) to build supervised improvement pipeline.
- Expand features (interest surveys, portfolio, internships) and re-train periodically.
- Build a REST JSON endpoint for programmatic access.

---

## 10. Common Interview Questions & Suggested Answers

- Q: How does the recommendation engine work?
  - A: The app assembles student features, applies saved preprocessing (`scaler`), feeds the scaled vector to a saved scikit-learn classifier, extracts class probabilities with `predict_proba`, and returns the top-3 careers by probability.

- Q: Why use scaling?
  - A: Many models assume features are on a similar scale; scaling improves numeric stability and model performance, especially for algorithms sensitive to feature magnitude.

- Q: What metric is most important?
  - A: For this recommender, Top-3 accuracy is most meaningful because the system is expected to suggest a small set of plausible career options rather than a single deterministic label.

- Q: How do you prevent bias?
  - A: Check fairness across subgroups, compare metrics per subgroup, and if disparities exist, consider reweighting, additional data collection, or fairness-aware constraints during training.

- Q: How would you improve the model?
  - A: Add richer features (surveys, projects), collect more labeled outcomes, apply feature selection/engineering, tune hyperparameters, and use ensembles.

---

## 11. Quick Troubleshooting & Inspection Commands

- Check model type and whether `predict_proba` exists:

```powershell
python - <<'PY'
import pickle
m = pickle.load(open('Models/model.pkl','rb'))
print(type(m))
print('predict_proba:', hasattr(m, 'predict_proba'))
PY
```

- Test `Recommendations()` quickly from Python REPL (example):

```python
from app import Recommendations
# Example inputs (replace with plausible values):
res = Recommendations('female', True, 2, True, 10, 85, 70, 78, 72, 68, 80, 74, 747.0, 73.7)
print(res)
```

---

## 12. How to Present This Project in an Interview

- One-liner: "A small Flask-based recommendation app that suggests careers for students by applying a saved scikit-learn classifier to scaled academic and contextual features; it returns top-3 career probabilities for quick guidance."
- Architecture: Briefly describe `app.py`, templates, and saved `Models/` artifacts.
- Explain your modeling choices (why you scaled, evaluation metric preference, handling imbalanced classes).
- Mention one or two concrete improvements and explain the impact (e.g., adding interest surveys to reduce irrelevant recommendations).
- Be ready to show a short demo (run locally) and inspect the `model.pkl` to show the model type.

---

If you want, I can now:
- Add a short 1-page cheat-sheet derived from this doc for memorization.
- Inspect `Models/model.pkl` and `Models/scaler.pkl` to provide exact model type and scaler parameters, and append them to this file.
- Add a REST endpoint that returns JSON (instead of HTML) for programmatic access.

Tell me which of these you'd like next.
