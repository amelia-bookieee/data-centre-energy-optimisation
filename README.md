# Data Centre Electricity Cost and Carbon Optimisation

**An independent research project using Python, machine learning and linear programming to investigate electricity-aware workload scheduling in Great Britain.**

## 1. Project Overview

Electricity prices vary throughout the day. Some data centre workloads, such as batch processing tasks, can be scheduled at different times as long as they finish before a deadline.

This project investigates whether forecasts of electricity prices can help schedule flexible workloads at lower cost.

Using half-hourly electricity market data from Great Britain for 2024–2025, I developed a forecasting and optimisation pipeline to:

- Forecast electricity prices using historical observations and calendar patterns.
- Compare forecasting models based on prediction accuracy and scheduling performance.
- Optimise workload schedules under energy, power and deadline constraints.
- Evaluate electricity costs and associated carbon emissions using observed historical data.
- Examine the sensitivity of results to workload flexibility and forecasting uncertainty.

The analysis considers a hypothetical flexible workload. The results are based on retrospective simulations, not operational savings from an existing data centre.

## 2. Key Results

On a corrected retrospective evaluation covering **183 days from July to December 2025**, the Gradient Boosting scheduling approach achieved:

| Metric | Result |
|---|---:|
| Electricity cost reduction vs uniform scheduling | **20.46%** |
| Approximate 95% bootstrap interval for cost reduction | 16.66%–24.91% |
| Reduction in attributed carbon emissions | **18.02%** |
| Cost improvement over Ridge-based scheduling | 2.74% |
| Cost improvement over seven-day seasonal scheduling | 5.48% |
| Evaluated half-hourly observations | 8,784 |

All policies were evaluated using the same daily energy requirement of 6 MWh and maximum power of 1 MW.

### Forecasting Performance

| Model | MAE (£/MWh) | RMSE (£/MWh) | Mean Daily Spearman |
|---|---:|---:|---:|
| Seven-day naive | 24.149 | 35.274 | 0.553 |
| Ridge Regression | **17.548** | **26.168** | 0.626 |
| Gradient Boosting | 19.386 | 26.941 | **0.677** |

Ridge Regression achieved lower forecasting errors, while Gradient Boosting ranked prices within each day more accurately and produced lower scheduling costs.

This was the main finding of the project: **the model with the lowest prediction error did not produce the lowest downstream scheduling cost.**

## 3. Data

The study uses 35,088 half-hourly timestamps covering 1 January 2024 to 31 December 2025.

| Dataset | Source | Purpose |
|---|---|---|
| Electricity market prices | Elexon, APX Market Index | Forecasting and cost evaluation |
| Carbon intensity | Great Britain Carbon Intensity API | Estimated emissions evaluation |
| Electricity demand | Public GB electricity system data | Exploratory analysis |
| Electricity generation | Public GB generation data | Exploratory analysis |

Electricity prices are measured in £/MWh, and carbon intensity is measured in gCO₂/kWh.

Data were aligned to a continuous UTC timeline. Missing observations were checked and retained as missing where the actual values could not be verified.

Demand and generation were explored during data analysis but were not included as explanatory variables in the final forecasting models.

## 4. Methodology

### Step 1 — Electricity Price Forecasting

Two forecasting models were developed:

- **Ridge Regression:** A regularised linear model using lagged prices and calendar features.
- **Histogram Gradient Boosting:** A tree-based model designed to capture nonlinear relationships.

A seven-day seasonal naive forecast was used as a benchmark.

The models use historical price observations from three, four, seven and eight days earlier, together with time-of-day, day-of-week and annual calendar features.

Chronological training and validation periods were used to select the forecasting approach.

### Step 2 — Workload Optimisation

The predicted prices were passed to a linear programming model implemented using SciPy's HiGHS solver.

The model minimises predicted electricity cost while satisfying:

- Daily workload energy requirement: 6 MWh.
- Maximum processing power: 1 MW.
- Scheduling window: 24 hours.
- Workload completion within the scheduling window.

Each model-generated schedule was evaluated using observed electricity prices.

Uniform scheduling, fixed overnight scheduling and a perfect-information oracle were included as comparison policies.

### Step 3 — Backtesting and Robustness

Forecasting performance was evaluated using MAE, RMSE and within-day Spearman rank correlation.

Scheduling performance was assessed using realised historical costs and attributed emissions.

Additional experiments examined:

- Processing capacity constraints.
- Workload completion deadlines.
- Carbon-aware optimisation.
- Missing-data fallback strategies.
- Uncertainty in cost savings using a seven-day moving-block bootstrap.

Model selection used April–June 2025 validation data. The final reported evaluation covers July–December 2025 and includes documented retrospective corrections to the missing-data handling and training cutoff.

## 5. Repository Structure

```text
data/
├── raw/
└── processed/
    ├── electricity_price_2024_2025.csv
    ├── electricity_demand_2024_2025.csv
    ├── electricity_generation_2024_2025.csv
    └── carbon_intensity_2024_2025.csv

notebooks/
├── 01_data_collection.ipynb
├── 02_data_analysis.ipynb
└── 03_final_backtest.ipynb

report/
README.md
requirements.txt
...

The three notebooks serve different purposes:

01 — Data Collection: Documents API requests, data cleaning and validation procedures.

02 — Data Analysis: Contains exploratory analysis, forecasting experiments, model comparisons and sensitivity analyses.

03 — Final Backtest: Provides an independent pipeline for reproducing the corrected retrospective forecasting and scheduling results.

The `report/` directory contains the detailed research report with mathematical formulations, results, discussion and references.

## 6. Reproducing the Results

Clone the repository:

```bash
git clone https://github.com/amelia-bookieee/data-centre-energy-optimisation.git
cd data-centre-energy-optimisation


## 7. Limitations

Several assumptions affect the interpretation of the results:

Electricity prices: The study uses APX wholesale market index prices, not actual data centre retail tariffs. Network charges, supplier fees and taxes are excluded.
Workload flexibility: The model assumes that the full 6 MWh workload can be divided and shifted within the day. Individual job arrivals, task dependencies and operating costs are not modelled.
Data availability: A 24-hour historical observation delay is assumed. Actual publication timestamps were not independently verified.
Retrospective evaluation: The missing-data fallback and training cutoff were corrected after the initial test inspection. The results should not be described as a completely untouched real-time evaluation.
Carbon emissions: Emissions are calculated using national average grid carbon intensity. The reported reduction does not establish the amount of grid emissions causally avoided.

These limitations define the scope of the findings and identify areas for further development.

## 8. Research Report

The full report provides detailed explanations of the data, forecasting methods, linear programming formulation, evaluation results and sensitivity analyses.


## 9. Tools and Libraries

Language: Python

Data analysis: pandas, NumPy, Matplotlib

Machine learning: scikit-learn

Optimisation: SciPy, Linear Programming (HiGHS)

Statistical evaluation: MAE, RMSE, Spearman correlation, moving-block bootstrap

## 10. Data Sources and Documentation

[Elexon — Balancing Mechanism Reporting Service](https://bmrs.elexon.co.uk/)
[Great Britain Carbon Intensity API](https://carbonintensity.org.uk/)
[scikit-learn documentation](https://scikit-learn.org/stable/)
[SciPy optimisation documentation](https://docs.scipy.org/doc/scipy/reference/optimize.html)

*Independent research project | November 2026*
