# E-commerce Customer Analytics: RFM Segmentation & Repeat Purchase Prediction

## Project Overview

This project analyzes e-commerce transaction data to understand customer purchasing behavior, identify valuable customer segments, and predict whether customers will make another purchase within a future 90-day period.

The project combines exploratory data analysis (EDA), customer segmentation using RFM analysis and K-Means clustering, and supervised machine learning for repeat-purchase prediction.

## Business Objectives

- Analyze sales and customer purchasing patterns.
- Identify high-value, developing, established, and inactive customer segments.
- Understand the behavioral characteristics associated with repeat purchases.
- Build and evaluate machine learning models to predict future purchasing activity.
- Translate analytical findings into actionable business recommendations.

## Dataset

**Dataset:** UCI Online Retail Dataset

**Source:** [UCI Machine Learning Repository — Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail)

The dataset contains transactions from a UK-based online retailer between December 2010 and December 2011.

Original dataset:
- 541,909 transaction records
- 8 original columns
- Customer, invoice, product, quantity, price, date, and country information

## Tools and Technologies

- Python
- Jupyter Notebook
- Pandas and NumPy
- Matplotlib and Seaborn
- Scikit-learn
- Joblib
- Git and GitHub

## Project Workflow

### 1. Data Cleaning and Preparation

The transaction dataset was cleaned before analysis.

Key steps:
- Removed duplicate transaction records.
- Excluded records without a customer ID.
- Excluded cancellation invoices identified by invoice numbers beginning with `C`.
- Removed non-positive quantities and unit prices.
- Converted transaction values into a revenue feature using quantity multiplied by unit price.
- Checked missing values, duplicates, and invalid transaction values.

**Cleaning results:**

| Metric | Result |
|---|---:|
| Original records | 541,909 |
| Cleaned purchase records | 392,692 |
| Records removed | 149,217 |
| Missing values after cleaning | 0 |
| Unique customers | 4,338 |
| Unique invoices | 18,532 |
| Unique products | 3,665 |
| Purchase-line revenue | £8,887,208.89 |

The calculated revenue represents revenue from retained positive purchase lines. It is not accounting net revenue because returns, cancellations, and other excluded transactions are not included.

### 2. Exploratory Data Analysis

Explored revenue trends, customer purchasing frequency, transaction values, and product purchasing behavior.

Key observations:
- November 2011 recorded the highest monthly revenue in the cleaned dataset.
- Revenue increased by approximately 79.5% between August and November 2011.
- December 2011 covers only the period through December 9, so it is not directly comparable with complete months.
- Customers displayed different purchasing patterns, with substantial variation in order frequency and spending.

### 3. RFM Customer Segmentation

Customers were analyzed using three RFM metrics:

- **Recency:** Days since the customer's most recent purchase.
- **Frequency:** Number of distinct invoices associated with the customer.
- **Monetary:** Total retained purchase-line revenue associated with the customer.

The RFM features were log-transformed and standardized before applying K-Means clustering.

Four customer groups were selected for business interpretability.

| Segment | Customers | Percentage | Average Recency | Average Orders | Average Monetary Value |
|---|---:|---:|---:|---:|---:|
| Recent High-Value Customers | 713 | 16.44% | 12.17 days | 13.75 | £8,088.02 |
| At-Risk / Inactive Customers | 1,622 | 37.39% | 181.51 days | 1.32 | £341.00 |
| Recent Developing Customers | 837 | 19.29% | 17.70 days | 2.19 | £557.32 |
| Established Customers | 1,166 | 26.88% | 71.64 days | 4.08 | £1,801.78 |

These segment names are interpretations of the observed cluster profiles, not independently verified customer labels.

**Potential business applications:**
- Reward recent high-value customers with loyalty benefits.
- Encourage developing customers to make another purchase.
- Run carefully targeted re-engagement campaigns for inactive customers.
- Maintain engagement with established customers through relevant product recommendations.

### 4. Repeat-Purchase Prediction

The prediction target was defined as whether a customer made at least one purchase during the 90-day period following September 1, 2011.

**Target definition:**
- `1`: At least one purchase during the future prediction window.
- `0`: No recorded purchase during that window.

Customer features were constructed using transaction history available before the cutoff.

Features used:
- HistoricalOrders
- HistoricalRevenue
- HistoricalQuantity
- AverageOrderValue
- Recency

The final modeling dataset contained 3,317 customers with historical records before the cutoff.

A stratified 80/20 customer-level train-test split was used:
- Training customers: 2,653
- Test customers: 664

Two supervised learning models were trained:
1. Logistic Regression with log transformation and standardization.
2. Random Forest Classifier.

A majority-class baseline was also evaluated.

### 5. Model Evaluation

| Model | Accuracy | Precision | Recall | F1-score | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Majority-Class Baseline | 56.17% | 56.17% | 100.00% | 71.94% | 0.5000 |
| Logistic Regression | **67.92%** | 72.10% | **69.97%** | **71.02%** | **0.7322** |
| Random Forest | 65.51% | **73.68%** | 60.05% | 66.17% | 0.7172 |

**Best overall candidate:** Logistic Regression.

It achieved the highest accuracy and ROC-AUC among the evaluated models. Random Forest achieved slightly higher precision but lower recall and ROC-AUC.

The baseline predicts every customer as a repeat purchaser. Its 100% positive-class recall is therefore not evidence of useful discrimination. Its ROC-AUC of 0.5000 reflects chance-level ranking performance.

The Logistic Regression model's ROC-AUC of 0.7322 indicates useful, but imperfect, discrimination between repeat and non-repeat purchasers on the test set.

### 6. Feature Importance

Random Forest feature importance produced the following ranking:

| Feature | Importance |
|---|---:|
| HistoricalRevenue | 23.54% |
| HistoricalQuantity | 23.14% |
| Recency | 19.58% |
| HistoricalOrders | 18.88% |
| AverageOrderValue | 14.86% |

Historical revenue and quantity were the most influential features in the fitted Random Forest model, followed by recency.

These are relative tree-based importance scores, not causal effects. Correlated features may share importance.

## Project Structure

```text
ecommerce-customer-analytics/
├── data/
│   ├── raw/
│   │   └── Online Retail.xlsx
│   └── processed/
│       ├── customer_rfm_segments.csv
│       └── customer_segment_summary.csv
├── notebooks/
│   └── 01_data_understanding.ipynb
├── models/
│   ├── repeat_purchase_logistic.pkl
│   └── repeat_purchase_random_forest.pkl
├── reports/
│   ├── repeat_purchase_model_comparison.csv
│   ├── repeat_purchase_feature_importance.csv
│   └── final_model_evaluation.csv
└── README.md
```

This structure reflects the current project artifacts. Update it if you add more notebooks, charts, or reports.

## How to Run the Project

1. Clone or download this repository.
2. Create and activate a Python virtual environment.
3. Install the required packages.
4. Place the dataset in `data/raw/Online Retail.xlsx`.
5. Open the notebook in Jupyter and run the cells in order.

Install dependencies:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn openpyxl joblib jupyter
```

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open `notebooks/01_data_understanding.ipynb`.

## Limitations and Future Improvements

- The prediction model was evaluated using one historical cutoff and a random customer-level test split. It has not yet been validated across multiple independent time periods.
- The dataset covers approximately one year, limiting the number of independent 90-day evaluation windows.
- Customers with no historical transactions before the cutoff were excluded from model training.
- The prediction target represents a purchase within a defined future window, not necessarily a customer's second-ever purchase.
- K-Means segments are descriptive groups and should be validated against business outcomes before operational use.
- Random Forest feature importance does not establish causality.
- The models have not been deployed or tested in a live business environment.

Future improvements:
- Perform additional time-based validation where sufficient follow-up data is available.
- Compare additional models and tune hyperparameters using training data only.
- Evaluate probability calibration and business-specific decision thresholds.
- Test segmentation stability and evaluate customer retention campaign outcomes.
- Build an interactive dashboard to communicate customer segments and purchasing trends.

## Conclusion

This project demonstrates an end-to-end customer analytics workflow, from transaction cleaning and exploratory analysis to unsupervised customer segmentation and supervised repeat-purchase prediction.

The analysis identified four customer segments with distinct purchasing patterns. Logistic Regression achieved 67.92% accuracy and 0.7322 ROC-AUC on the test set, outperforming the majority-class baseline on accuracy and discrimination.

The results provide a foundation for further experimentation in customer retention analytics and predictive modeling.
