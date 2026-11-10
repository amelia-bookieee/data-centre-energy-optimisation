# Data Centre Electricity Cost and Carbon Optimisation

An independent project investigating whether electricity price forecasts can improve the scheduling of flexible data centre workloads in Great Britain.

## Overview

Electricity prices vary throughout the day. Some data centre tasks, such as batch processing, can be moved to lower-cost periods if their completion deadlines allow.

This project examines whether forecasting electricity prices can reduce the cost of scheduling these workloads, and how forecast accuracy affects the resulting decisions.

Using half-hourly electricity data from Great Britain (2024–2025), I developed a Python pipeline combining time-series forecasting and linear programming.

The analysis follows three stages:

1. **Forecasting:** Compare Ridge Regression and Gradient Boosting against a seven-day seasonal baseline using lagged prices and calendar features.
2. **Optimisation:** Use predicted prices to schedule a hypothetical 6 MWh daily workload, subject to a 1 MW power limit and a 24-hour completion deadline.
3. **Evaluation:** Compare schedules using observed electricity prices and carbon intensity, with additional tests for forecasting uncertainty and workload flexibility.

## Key Findings

On a corrected retrospective evaluation of **183 days (July–December 2025)**:

| Metric | Result |
|---|---:|
| Cost reduction vs uniform scheduling | **20.46%** |
| Estimated emissions reduction | **18.02%** |
| Cost improvement over Ridge-based scheduling | 2.74% |
| 95% bootstrap interval for cost reduction | 16.66%–24.91% |

Ridge Regression achieved lower forecasting errors (MAE 17.55 vs 19.39 £/MWh), while Gradient Boosting ranked daily electricity prices more accurately and produced lower scheduling costs.

**The main finding is that lower prediction error does not necessarily lead to better scheduling decisions.**

## Code and Report

| File | Contents |
|---|---|
| `notebooks/01_data_collection.ipynb` | Data collection and preprocessing |
| `notebooks/02_data_analysis.ipynb` | Exploratory analysis, model selection and sensitivity tests |
| `notebooks/03_final_backtest.ipynb` | Reproducible forecasting and scheduling evaluation |
| `Data_centre.pdf` | Full research report, including methodology, results and limitations |

To reproduce the results, install the dependencies in `requirements.txt` and run `03_final_backtest.ipynb` using the processed datasets. The historical API requests do not need to be repeated.

## Scope and Limitations

The study models a hypothetical fully flexible workload using wholesale market index prices. It does not estimate actual data centre electricity bills. Carbon results use national average carbon intensity and should not be interpreted as verified avoided grid emissions.

A 24-hour historical data delay is assumed, and the retrospective evaluation includes documented corrections to missing-data handling and the training cutoff. Full details are provided in the report.

**Data sources:** [Elexon](https://bmrs.elexon.co.uk/) · [GB Carbon Intensity API](https://carbonintensity.org.uk/)
