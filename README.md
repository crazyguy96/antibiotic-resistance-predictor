<div align="center">

# 🧬 ResistAI

### Antibiotic Resistance Decision Support System

[![Live Demo](https://img.shields.io/badge/🚀%20Live%20Demo-Visit%20App-00ff9d?style=for-the-badge&labelColor=060b12)](https://bhattyuvraj22.github.io/antibiotic-resistance-predictor)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github)](https://github.com/bhattyuvraj22/antibiotic-resistance-predictor)
[![Python](https://img.shields.io/badge/Python-3.13-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-3.0-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com)
[![License](https://img.shields.io/badge/License-Research%20Only-ff4545?style=for-the-badge)](./LICENSE)

<br/>

> **An AI-powered clinical + environmental antibiotic resistance prediction dashboard**
> powered by two ML models and a Flask REST API — built to combat antimicrobial resistance (AMR).

<br/>

![Overview Dashboard](docs/Screenshots/01-overview-dashboard.png)

</div>

---

## ✨ What is ResistAI?

**ResistAI** is a full-stack decision support system that predicts antibiotic resistance from two data sources:

- 🏥 **Clinical data** — patient demographics, comorbidities, bacterial species → predicts resistance for **15 antibiotics**
- 🌿 **Environmental data** — Nigerian surface/soil sampling locations → predicts resistance for **5 antibiotics**

It gives clinicians and researchers an instant, ranked recommendation of which antibiotics are most likely to be effective — before culture results are available.

---

## 🖥️ UI Preview

| Page | Preview |
|------|---------|
| 📊 **Overview Dashboard** | ![Overview](docs/Screenshots/01-overview-dashboard.png) |
| 🔬 **Clinical Prediction** | ![Clinical](docs/Screenshots/03-clinical-prediction.png) |
| 🌿 **Environmental Prediction** | ![Environmental](docs/Screenshots/04-environmental-prediction.png) |
| 🧬 **Gene & Features** | ![Genes](docs/Screenshots/05-gene-features.png) |
| 📈 **Model Performance** | ![Performance](docs/Screenshots/06-model-performance-metrics.png) |
| 💊 **Treatment Strategy** | ![Strategy](docs/Screenshots/08-treatment-strategy.png) |

---

## 🚀 Quick Start

```bash
# 1. Clone the repo
git clone https://github.com/bhattyuvraj22/antibiotic-resistance-predictor.git
cd antibiotic-resistance-predictor

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate        # macOS/Linux
# venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the server
python src/app.py
```

Open **http://localhost:5500** in your browser. 🎉

> ⚠️ If model `.pkl` files are absent, the app runs in **demo mode** with randomised fallback predictions.

---

## 📁 Project Structure

```
antibiotic-resistance-predictor/
│
├── 📂 Interface/               ← Frontend (HTML, CSS, JS)
│   ├── index.html
│   ├── main.css
│   └── main.js
│
├── 📂 dataset/
│   └── raw/
│       ├── Bacteria_dataset_Multiresic...
│       └── Dataset.xlsx         ← Environmental sampling data (Nigeria)
│
├── 📂 docs/
│   ├── methodology.md
│   └── Screenshots/             ← App preview images
│
├── 📂 models/                   ← Trained ML model artifacts
│   ├── primarymodel.pkl         ← Environmental model
│   ├── secondarymodel.pkl       ← Clinical model (ensemble)
│   └── feature_cols.pkl
│
├── 📂 src/                      ← Flask backend
│   ├── app.py                   ← Main API server ⭐
│   ├── primarymodel.py          ← Environmental model training
│   └── secondarymodel.py        ← Clinical model training
│
├── requirements.txt
└── README.md
```

---

## 🤖 ML Models

### 🟢 Primary Model — Environmental
| Property | Details |
|----------|---------|
| **File** | `models/primarymodel.pkl` |
| **Data** | Nigerian surface swab data (soil, concrete, butcher tables) |
| **Input** | City (Ede, Ife, Iwo, Osu) + Surface Type |
| **Output** | Sensitive / Intermediate / Resistant for 5 antibiotics |
| **Algorithm** | Random Forest MultiOutputClassifier |
| **F1 Score** | 0.62 – 0.73 across antibiotics |

### 🔵 Secondary Model — Clinical
| Property | Details |
|----------|---------|
| **File** | `models/secondarymodel.pkl` |
| **Data** | Synthetic clinical dataset (~10,000 isolates) |
| **Input** | Species, Age, Gender, Diabetes, Hypertension, Hospital history, Infection frequency |
| **Output** | Resistance probability for **15 antibiotics** |
| **Algorithm** | VotingClassifier (HistGradientBoosting + Random Forest) |
| **F1 Score** | 0.62 – 0.75 across antibiotics |
| **ROC AUC** | 0.91 (Gentamicin model) |

---

## 🔌 API Reference

### `GET /health`
```json
{
  "status": "ok",
  "models": { "clinical": true, "environmental": true }
}
```

### `POST /predict/clinical`
```json
// Request
{
  "species":      "Escherichia coli",
  "age":          45,
  "gender":       "M",
  "diabetes":     "No",
  "hypertension": "No",
  "hospital":     "Yes",
  "inf_freq":     "Often",
  "antibiotic":   "CIP"
}

// Response
{
  "antibiotic":      "CIP",
  "resistance_prob": 0.6200,
  "susceptibility":  0.3800,
  "classification":  "Resistant",
  "all_probs":       { "AMX/AMP": 0.45, "CIP": 0.62, "GEN": 0.18, "..." : "..." },
  "recommendations": [{ "name": "GEN", "resistance_prob": 0.18 }],
  "alternatives":    [{ "name": "IPM", "resistance_prob": 0.22 }]
}
```

### `POST /predict/environmental`
```json
// Request
{
  "city": "Ife", "surface": "T",
  "antibiotic": "ciprofloxacin", "sample_source": "Community"
}

// Response
{
  "prediction":    "Sensitive",
  "confidence":    0.45,
  "probabilities": { "Sensitive": 0.45, "Intermediate": 0.25, "Resistant": 0.30 }
}
```

---

## 📊 Antibiotics Covered

| Clinical (15) | Environmental (5) |
|--------------|------------------|
| AMX/AMP, AMC, CZ, FOX, CTX/CRO | Imipenem |
| IPM, GEN, AN, ofx, CIP | Ceftazidime |
| Acide nalidixique, C | Gentamicin |
| Co-trimoxazole, Furanes, colistine | Augmentin, Ciprofloxacin |

---

## 📦 Tech Stack

| Layer | Technology |
|-------|-----------|
| **Backend** | Python 3.13, Flask 3.0, Flask-CORS |
| **ML** | scikit-learn, XGBoost, joblib |
| **Data** | pandas, NumPy |
| **Frontend** | Vanilla JS, Chart.js, CSS3 |
| **Server** | Gunicorn (production) |

---

## 🌐 Production Deployment

```bash
# Using Gunicorn
gunicorn -w 2 -b 0.0.0.0:5500 src.app:app
```

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `5500` | Server port |
| `FLASK_DEBUG` | `false` | Enable debug mode |

---

## 📈 Model Performance Summary

```
Clinical Model (15 antibiotics)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Avg. Accuracy     87%
Best ROC AUC      0.91  (Gentamicin)
F1 Range          0.62 – 0.75
Resistance Rate   38% across isolates

Environmental Model (5 antibiotics)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
F1 Range          0.62 – 0.73
Classes           Sensitive / Intermediate / Resistant
```

---

## ⚠️ Disclaimer

> This tool is for **academic and research purposes only**.
> Predictions should not be used for direct clinical decision-making without appropriate laboratory validation and clinical oversight.
> Models were trained on Nigerian clinical and environmental datasets and may not generalise to other populations.

---

## 🙏 Acknowledgements

- Dataset sourced from Nigerian environmental surface sampling studies
- Built with scikit-learn, Flask, and Chart.js
- UI inspired by modern clinical decision support dashboards

---

<div align="center">

Made with 🧬 by [Yuvraj Bhatt](https://github.com/bhattyuvraj22)

[![GitHub stars](https://img.shields.io/github/stars/bhattyuvraj22/antibiotic-resistance-predictor?style=social)](https://github.com/bhattyuvraj22/antibiotic-resistance-predictor)

</div>
