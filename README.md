An unsupervised machine-learning project for detecting unusual KPI observations using an Isolation Forest model.

The repository contains the model-development portion of an anomaly-monitoring workflow: exploratory analysis, training, decision-boundary visualization, test-set prediction, model serialization, sample inference data, and a Dockerized JupyterLab environment.

## Table of Contents

- [Project Overview](#project-overview)
- [Business Use Case](#business-use-case)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Model Configuration](#model-configuration)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Running with Docker](#running-with-docker)
- [Using the Trained Model](#using-the-trained-model)
- [Makefile Commands](#makefile-commands)
- [Model Output](#model-output)
- [Important Compatibility Notes](#important-compatibility-notes)
- [Limitations](#limitations)
- [Recommended Improvements](#recommended-improvements)
- [Skills Demonstrated](#skills-demonstrated)

---

## Project Overview

Operational systems generate KPIs continuously. Most observations follow regular patterns, while a small percentage may represent failures, unusual traffic, degraded performance, data-quality problems, or other events requiring investigation.

This project uses **Isolation Forest**, an unsupervised anomaly-detection algorithm, to identify observations that differ substantially from the dominant data distribution.

The model analyzes two numerical KPI features:

- `mean`
- `sd`

Because labeled anomalies are not required, this approach can be useful when historical incident labels are unavailable or incomplete.

## Business Use Case

The project can serve as a starting point for monitoring:

- Application latency
- Request volume
- CPU or memory usage
- Transaction values
- Error-rate summaries
- Sensor measurements
- Service-level indicators
- Batch-processing metrics
- Financial or operational KPI aggregates

The model provides an anomaly signal that can later be connected to dashboards, APIs, incident-management systems, or alerting platforms.

> An anomaly is an investigation signal, not automatic proof of a production incident.

## Dataset

The repository contains three CSV files:

| File | Rows | Columns | Purpose |
|---|---:|---:|---|
| `train.csv` | 64,227 | 2 | Model training |
| `test.csv` | 16,057 | 2 | Holdout inference and visualization |
| `demo.csv` | 5 | 2 | Small sample for demonstration |

### Schema

| Column | Type | Description |
|---|---|---|
| `mean` | Float | Mean value of the KPI observation or aggregation window |
| `sd` | Float | Standard deviation of the KPI observation or aggregation window |

The training and test files contain no missing values in these two columns.

### Data summary

| Statistic | Training `mean` | Training `sd` | Test `mean` | Test `sd` |
|---|---:|---:|---:|---:|
| Mean | -0.0021 | 0.0008 | 0.0084 | -0.0031 |
| Standard deviation | 0.9540 | 1.0202 | 1.1661 | 0.9148 |
| Minimum | -0.2849 | -0.1067 | -0.2849 | -0.1067 |
| Maximum | 66.3248 | 57.6770 | 66.3248 | 51.7858 |

The distributions are strongly concentrated around a small set of values and include a long positive tail. The files also contain many repeated observations. Confirm that these repetitions represent valid recurring measurements before using the data in a production setting.

## Methodology

### 1. Exploratory Data Analysis
- Dataset previews
- Shape and descriptive statistics
- Scatter plots of `mean` versus `sd`
- Histograms for both features
- Box plots for identifying extreme values

The notebook uses:

- Pandas for data manipulation
- Plotly Express for interactive visualization

### 2. Model Training

1. Loads `train.csv` and `test.csv`.
2. Trains an initial Isolation Forest model.
3. Visualizes the learned decision surface.
4. Retrains the model with a defined contamination level.
5. Generates predictions for the test set.
6. Separates predicted inliers and outliers in the visualization.
7. Serializes the final model with Joblib.

### 3. Decision-Boundary Visualization

The visualization distinguishes:

- Normal regions
- Anomalous regions
- Predicted test inliers
- Predicted test outliers

### 4. Model Persistence

```text
anomaly-model.joblib
