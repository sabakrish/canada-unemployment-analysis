# Canadian Unemployment Analysis and Forecasting (2005–2025)

Analysis of 21 years of Statistics Canada labour data in Python: unemployment trends by province, age group and gender, regression models that predict the monthly unemployment rate, a 12-month forecast, and classifiers that flag high-unemployment months. The project ends with policy recommendations.

**Open the notebook:** [canada_unemployment_analysis.ipynb](canada_unemployment_analysis.ipynb)

## Question

How has unemployment in Canada changed since 2005, who is most affected, and how well can recent history predict the next month's unemployment rate?

## Data

[Statistics Canada, Table 14-10-0287-01](https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1410028701): *Labour force characteristics by province, monthly, seasonally adjusted* (Labour Force Survey). The notebook downloads the file itself.

- **Period analyzed:** January 2005 to December 2025 (252 months)
- **Breakdowns:** Canada and 10 provinces; Men+ and Women+; age groups 15–24, 25–54 and 55+
- **Measures used:** unemployment rate (%) and number of unemployed people

### Data-cleaning note

Each month of each series appears in several rows: the estimate plus its standard errors, and seasonally adjusted plus unadjusted versions. Averaging them together badly understates the unemployment rate. The notebook keeps only **Estimate** rows of **seasonally adjusted** data, so its figures match Statistics Canada's published rates. It reads the large source file in chunks to keep memory use low.

## Key findings

| Finding | Result |
|---|---|
| Highest rate | 14.2% in May 2020 (COVID-19), with 2.72 million people unemployed |
| Financial-crisis peak | 8.8% in June 2009 |
| Lowest rate | 4.8% in July 2022 |
| Highest-unemployment province | Newfoundland and Labrador, 13.1% average |
| Most stable province | Manitoba (lowest month-to-month variation) |
| Most affected age group | 15–24 years, 12.8% average, more than twice the 25–54 rate (5.8%) |
| Gender gap | Men+ 7.3% vs Women+ 6.5% on average |

Unemployment has been rising again since its 2022 low, from a 5.3% yearly average in 2022 to 6.9% in 2025.

## Models

**Features:** year, month, the rate 1–3 months earlier (lags), and the average of the previous 3 months. All features use past data only.

**Validation:** a time-based split. The models train on April 2005 to October 2021 and are tested on November 2021 to December 2025, so they never see the future during training.

### Regression: predicting the monthly rate

| Model | R² | MAE (percentage points) |
|---|---|---|
| **Linear regression** | **0.83** | **0.21** |
| Decision tree | 0.68 | 0.33 |

Linear regression is the better model: unemployment moves gradually, and a linear model extrapolates smoothly from recent months, while a tree gives step-shaped predictions. Its 12-month forecast sees the rate easing from about 7.0% in early 2026 to about 6.6% by December 2026.

### Classification: flagging high-unemployment months (rate ≥ 7.0%)

| Model | Accuracy | High months caught (test period) |
|---|---|---|
| **Logistic regression** | **86%** | **3 of 4** |
| Decision tree | 76% | 1 of 4 |

The 7.0% threshold sits just above the 2005–2025 median (6.8%). The test period contains only 4 high-unemployment months, because 2022–2024 had low unemployment, so the recall figures rest on a small sample. Missed high months (false negatives) are treated as the more serious error, since they would delay a policy response.

**Most important predictor:** the previous month's rate (correlation 0.93 with the current rate; 94% of the decision tree's feature importance).

## Policy recommendations

- **Youth employment:** paid internships, apprenticeships and co-op placements; short, job-focused training
- **Regional:** infrastructure investment and economic diversification in resource-dependent provinces
- **Participation:** affordable childcare, retraining for older workers, and faster recognition of newcomers' credentials

## How to run

1. Install the requirements: `pip install -r requirements.txt`
2. Open `canada_unemployment_analysis.ipynb` in Jupyter, VS Code or Google Colab.
3. Run all cells. The first run downloads the Statistics Canada file, which takes a few minutes.

## Tools

Python · pandas · NumPy · Matplotlib · scikit-learn (LinearRegression, DecisionTreeRegressor, LogisticRegression, DecisionTreeClassifier)

---

*Final project, Data Science & AI Specialist Advanced Diploma, Canadian Business College, 2026. Author: Krish Sabapathy.*
