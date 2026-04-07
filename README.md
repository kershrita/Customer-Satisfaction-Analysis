# Customer Satisfaction Analysis

> End-to-end NLP sentiment analysis system that transforms raw restaurant reviews into customer satisfaction signals for operational decision-making.

## Overview

This project is a production-oriented machine learning pipeline for customer sentiment classification in the restaurant domain.

It is designed as a complete system, not just a training notebook:

- Ingests labeled historical reviews and unlabeled incoming reviews
- Applies a consistent NLP preprocessing contract across training and inference
- Trains and benchmarks multiple models, then persists deployable artifacts
- Produces batch prediction outputs ready for analytics and business workflows

Real-world use cases:

- Support and operations triage using negative-review detection
- Weekly customer experience trend monitoring
- Campaign and menu impact analysis via sentiment shifts
- SLA-oriented feedback pipelines where output files feed BI/reporting jobs

## Architecture

### End-to-End Flow

`Historical Reviews (TSV)` -> `NLP Preprocessing` -> `Bag-of-Words Vectorization` -> `Model Training & Evaluation` -> `Model Artifact Export` -> `Fresh Review Inference` -> `Predicted Output (TSV)`

### Architecture Diagram

![Customer Satisfaction Analysis Architecture](assets/Customer%20Satisfaction%20Analysis%20Architecture.png)

### Components

1. Input Layer
	- `Data/Historical Restaurant Reviews.tsv` for supervised training
	- `Data/Fresh Restaurant Reviews.tsv` for batch scoring
2. Processing Layer
	- Text normalization, token filtering, and stemming
	- Shared `CountVectorizer` vocabulary serialized to `BoW.pkl`
3. Model Layer
	- Logistic Regression and Gaussian Naive Bayes as candidate classifiers
	- Deterministic train/test split (`test_size=0.15`, `random_state=0`)
4. Output Layer
	- Persisted model artifacts under `Models/`
	- Batch inference export to `Fresh Reviews Predicted.tsv`

## Features

- End-to-end sentiment analysis workflow from raw text to scored output files
- Reusable preprocessing and feature extraction contract across train/infer paths
- Multi-model benchmarking with explicit model artifact management
- Batch inference pipeline for operational review scoring
- Export-ready tabular outputs for downstream BI and reporting workflows

## Technical Highlights

- System-first design: separates training and inference into independent notebook workflows while sharing artifacts for consistency.
- Reproducibility guardrails: deterministic split and serialized vectorizer reduce train/inference drift.
- Deployment-minded model selection: benchmarked Logistic Regression vs GaussianNB and persisted both artifacts for controlled comparison.
- Data contract clarity: standardized TSV input/output format enables easy integration with dashboards or orchestration tools.
- Engineering trade-off: Bag-of-Words + linear classifier prioritizes interpretability and operational simplicity over heavy model complexity.

## Model Details

- Problem type: binary sentiment classification (`liked` vs `not liked`)
- Primary model: Logistic Regression (selected for stronger balanced performance)
- Baseline model: Gaussian Naive Bayes (fast baseline for comparative evaluation)
- Evaluation method: holdout split and classification metrics (accuracy, precision, recall, F1, confusion matrix)

## Tech Stack

- Python 3.x
- pandas, numpy
- scikit-learn
- NLTK (token processing, stopwords, stemming)
- joblib / pickle (artifact persistence)
- Jupyter Notebook (experimentation + batch workflow)

## Getting Started

### 1. Clone Repository

```bash
git clone https://github.com/kershrita/Customer-Satisfaction-Analysis.git
cd Customer-Satisfaction-Analysis
```

### 2. Install Dependencies

```bash
pip install numpy pandas scikit-learn nltk matplotlib joblib jupyter
```

### 3. Train and Evaluate Models

Open and run:

- `Customer Satisfaction Model.ipynb`

This notebook performs preprocessing, vectorization, model training, evaluation, and artifact export.

### 4. Run Batch Prediction

Open and run:

- `Customer Satisfaction Predictor.ipynb`

This notebook loads serialized artifacts and generates predictions for fresh reviews.

### 5. Review Output

- `Fresh Reviews Predicted.tsv` contains the original review text and predicted sentiment labels.

## Results

Evaluation snapshot (reproduced from current pipeline with `test_size=0.15`, `random_state=0`):

| Model | Accuracy | Precision | Recall | F1 Score | Confusion Matrix |
|---|---:|---:|---:|---:|---|
| Logistic Regression | 0.8000 | 0.8312 | 0.8205 | 0.8258 | [[44, 13], [14, 64]] |
| GaussianNB | 0.6815 | 0.8571 | 0.5385 | 0.6614 | [[50, 7], [36, 42]] |

Batch inference output summary:

- Fresh reviews scored: 100
- Predicted negative sentiment (`0`): 74
- Predicted positive sentiment (`1`): 26
- Output artifact: `Fresh Reviews Predicted.tsv`

## License

This project is released under the [MIT License](LICENSE).