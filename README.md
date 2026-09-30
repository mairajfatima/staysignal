# StaySignal 🚦
### Predicting employee attrition before it happens

Most companies find out an employee is a flight risk the day they resign — by then, it's too late to do anything about it. **StaySignal** is an end-to-end data pipeline that flips that timeline: it scores employees on their risk of leaving *before* they hand in their notice, so HR can act while there's still time.

---

## The Problem

A company was struggling to retain employees, but had no systematic way to answer a basic question:

> **Which employees are likely to leave next — and why?**

Without an early warning system, retention efforts were reactive: something only happened *after* someone resigned. The goal of this project was to replace that guesswork with a data-driven, proactive process, tested first on a small pilot group before rolling out further.

## The Approach

1. **Structure the data** — Historical employee records (with a known outcome: stayed or quit) and a new pilot group (unknown outcome) were loaded into **Google BigQuery** as separate tables.
2. **Explore & clean** — The data was pulled into **Google Colab** using Python and BigQuery's client library, then explored to understand class balance and key patterns.
3. **Train a model** — A **Random Forest** classifier (via **PyCaret**'s AutoML workflow) was trained on the historical data to learn which factors are associated with an employee leaving.
4. **Score the pilot group** — The trained model was applied to the new-employee pilot group to predict who is at risk of quitting.
5. **Persist the results** — Both the predictions *and* the model's feature importances were written back into BigQuery as their own tables — so the results aren't stuck in a notebook, they're a live, queryable, shareable asset.
6. **Visualize** — A **Looker Studio** dashboard connects directly to those BigQuery tables, turning raw predictions into a dashboard anyone on the HR team can read in seconds.
7. **Recommend action** — The final output isn't just "who's at risk" — it's a short set of concrete retention recommendations (recognition programs, professional development, retention incentives for long-tenured staff) tied to what the model found actually drives churn.

## Why This Matters

A churn probability score on its own is not very useful — it's just a number. What makes this project valuable is that it doesn't stop at prediction: it turns a model's output into something a manager can act on *this week*. That's the real problem being solved here — not "can we predict churn," but "can we turn that prediction into a decision."

## Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Data Warehouse | **Google BigQuery** | Stores historical + pilot employee data, predictions, and feature importances |
| Modeling | **Google Colab (Python)** | Environment for data cleaning, exploration, and model training |
| ML | **PyCaret / Random Forest** | AutoML-assisted training of the churn classification model |
| Dashboard | **Looker Studio** | Live, interactive visualization connected directly to BigQuery |

## Pipeline Overview

```
Raw HR Data (BigQuery)
        │
        ▼
 Clean & Explore (Colab / Python)
        │
        ▼
 Train Random Forest (PyCaret)
        │
        ▼
 Score Pilot Group ─────► Predictions + Feature Importances (BigQuery)
                                     │
                                     ▼
                          Looker Studio Dashboard
                                     │
                                     ▼
                        Retention Recommendations
```

## Key Insights from the Dashboard

- **Risk, quantified** — A small, specific percentage of the pilot group was flagged as likely to leave, rather than a vague company-wide estimate.
- **Drivers, not guesses** — Feature importance straight from the Random Forest model shows which factors (e.g. average monthly hours, satisfaction level) most strongly predict churn.
- **Where it's concentrated** — Risk is broken down by department, so retention efforts can be targeted rather than company-wide.

## Repository Structure

```
.
├── Employee_churn_analysis.ipynb   # Data prep, model training, scoring, and export to BigQuery
├── README.md                       # You're here
└── dashboard/                      # Looker Studio export / screenshots (optional)
```

## How to Run

1. Open `Employee_churn_analysis.ipynb` in Google Colab.
2. Authenticate against your own GCP project (`google.colab.auth.authenticate_user()`).
3. Update the BigQuery project/dataset/table names to point at your own data.
4. Run the notebook top to bottom — it will train the model, score the pilot group, and push predictions + feature importances back to BigQuery.
5. Connect a Looker Studio report to the resulting BigQuery tables to reproduce the dashboard.

## Business Impact

- Turns raw HR data into a short, specific list of at-risk employees — not a vague, company-wide guess.
- Surfaces *why* employees are at risk, straight from the model's feature importances.
- Ends in a decision, not a notebook: concrete retention recommendations tied to the data.

---

*Built as an end-to-end demonstration of cloud data warehousing, Python-based ML workflows, and BI storytelling — from raw data to a decision someone can act on.*
