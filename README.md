# Customer Lifetime Value (CLV) Analysis
## RFM Segmentation, Historical CLV & Predictive Modeling

A production-ready, end-to-end Customer Lifetime Value analysis combining RFM segmentation, historical CLV calculation, K-means clustering, and XGBoost predictive modeling. Identifies high-value customer segments, predicts future value, and delivers actionable business recommendations with quantified ROI impact.

---

## Project Highlights

- **3,874 customers** analyzed after rigorous preprocessing of 347,946 UK transactions (Dec 2010 – Dec 2011)
- **RFM Segmentation**: 27.6% Champions contribute 72.7% of revenue—stark value concentration
- **Historical CLV**: Average £986.05; 69.6% retention rate; top 20% drive 72.1% of total CLV
- **Predictive Model**: XGBoost with Test R² = 0.5136, MAE = £688.22; suitable for strategic planning
- **Business Impact**: Projected **+£145,991 incremental revenue** (2.6% ROI) through targeted initiatives
- **Actionable Playbooks**: Retention strategies for Champions/Loyal, reactivation campaigns for At-Risk, development programs for Potential Loyalists

---

## Table of Contents

- [Project Overview](#project-overview)
- [Dataset](#dataset)
- [Methodology](#methodology)
- [Key Results](#key-results)
- [Repository Structure](#repository-structure)
- [How to Run](#how-to-run)
- [Business Recommendations](#business-recommendations)
- [Model Performance](#model-performance)
- [Limitations & Future Work](#limitations--future-work)
- [Tech Stack](#tech-stack)
- [Documentation](#documentation)
- [Citation & License](#citation--license)
- [Contact](#contact)

---

## Project Overview

This repository provides a comprehensive framework for quantifying and predicting Customer Lifetime Value (CLV) in retail environments. The analysis combines:

1. **RFM Segmentation** – stratify customers into 10 behavioral segments based on Recency, Frequency, and Monetary value
2. **Historical CLV** – measure actual 6-month customer spending across a holdout window to establish baseline patterns
3. **Clustering Analysis** – K-means identifies 5 distinct behavioral groups beyond traditional RFM segmentation
4. **Predictive Modeling** – XGBoost forecasts future CLV with interpretable feature importance insights
5. **Business Strategy** – data-driven recommendations for retention, reactivation, and growth with quantified ROI

**Use Case**: Enable marketing and customer success teams to prioritize high-value customers, allocate budgets strategically, and measure retention ROI with confidence.

---

## Dataset

### Overview
| Property | Value |
|----------|-------|
| **Source** | Online Retail Dataset (UCI ML Repository) |
| **Time Period** | Dec 1, 2010 – Dec 9, 2011 |
| **Initial Records** | 541,909 transactions |
| **Final Records** | 347,946 transactions (after cleaning) |
| **Unique Customers** | 3,874 |
| **Total Revenue** | £5,614,837.20 |
| **Geographic Focus** | United Kingdom (91.4% of transactions) |
| **Key Features** | InvoiceNo, CustomerID, InvoiceDate, Quantity, UnitPrice, Description, Country |

### Download Dataset
The dataset is hosted on Google Drive due to file size constraints:

** [Download OnlineRetail.csv](https://drive.google.com/file/d/1N-WfPSe9rUtBIHPPOpBYVbZev_nRsV3w/view?usp=drive_link)** (Public Access)

Place the downloaded file in your working directory before running the notebook.

### Data Quality Issues Addressed
- **Missing CustomerID**: 134,697 rows (24.8%) removed
- **Cancelled Orders**: 9,288 rows (1.7%) removed
- **Invalid Prices/Quantities**: 40 rows removed
- **Outliers**: 6,378 rows (P99 threshold) removed
- **Geographic Filter**: Non-UK transactions excluded for consistency

**Result**: 35.8% reduction in data volume; improved signal quality for analysis

---

## Methodology

### Step 1: Data Preprocessing & Feature Engineering
```
Raw Data (541,909 rows)
    ↓
Remove cancelled orders, missing IDs, invalid values
    ↓
Filter to UK market, remove outliers (P99)
    ↓
Add Revenue column (Quantity × UnitPrice)
    ↓
Clean Data (347,946 rows, 3,874 customers)
```

**Key Transformations**:
- Convert InvoiceDate to datetime format
- Calculate Revenue column for all transactions
- Engineer features: Purchase_Rate, Revenue_Per_Day, Customer_Lifespan

### Step 2: RFM Segmentation
**RFM Scoring** (Percentile-based, 1-5 scale):
- **Recency (R)**: Days since last purchase (lower = better)
- **Frequency (F)**: Number of transactions (higher = better)
- **Monetary (M)**: Total spending (higher = better)

**Segmentation Logic**:
- Calculate combined RFM scores (R+F+M range: 3-15)
- Map to 10 customer segments (Champions, Loyal, At Risk, Lost, etc.)
- Each segment receives tailored retention/reactivation strategy

### Step 3: Historical CLV Calculation
```
Training Period (6 months):     Dec 2010 – May 2011 → Learn customer patterns
    ↓
Extract Training Features:
  - Purchase_Frequency, Total_Revenue, Avg_Order_Value
  - Customer_Lifespan, Purchase_Rate, Revenue_Per_Day
    ↓
Prediction Period (6 months):   Jun 2011 – Dec 2011 → Actual future spending
    ↓
Target Variable: Actual_CLV_6M = sum of revenue in prediction period
    ↓
Retention Metric: % of customers with CLV > 0 = 69.6%
```

### Step 4: K-means Clustering
- **Features**: Recency, Frequency, Monetary (standardized)
- **Optimal K**: 5 (determined via elbow method)
- **Output**: 5 behavioral clusters (High-Value Active, At-Risk, Recent Customers, etc.)

### Step 5: Predictive CLV Modeling

**Model**: XGBoost Regressor
- **Hyperparameters**: n_estimators=100, max_depth=6, learning_rate=0.1
- **Features**: Purchase_Frequency, Total_Revenue, Avg_Order_Value, Customer_Lifespan, Purchase_Rate, Revenue_Per_Day, Total_Quantity
- **Train-Test Split**: 80/20 (1,941 train, 486 test)
- **Validation**: 5-fold cross-validation

**Rationale**: XGBoost handles non-linear relationships between features and CLV, provides interpretable feature importance, and generalizes well on financial datasets.

---

## Key Results

### RFM Segmentation Results

| Segment | Customers | % of Base | Avg Revenue | Revenue Share | Strategic Action |
|---------|-----------|-----------|-------------|---------------|------------------|
| **Champions** | 1,068 | 27.6% | £3,821.86 | **72.7%** | VIP retention programs |
| **Loyal Customers** | 516 | 13.3% | £1,177.74 | 10.8% | Nurture & reward |
| **Potential Loyalists** | 582 | 15.0% | £674.61 | 7.0% | Growth campaigns |
| **About to Sleep** | 492 | 12.7% | £363.63 | 3.2% | Reactivation nudges |
| **Lost Customers** | 543 | 14.0% | £242.50 | 2.3% | Win-back offers |
| **At Risk** | 174 | 4.5% | £489.67 | 1.5% | Immediate intervention |
| Other Segments | 499 | 13.0% | £232–£1,190 | 1.5% | Situational tactics |

**Key Insight**: 27.6% of customers (Champions) generate 72.7% of revenue—extreme concentration requiring priority retention strategies.

### Historical CLV Analysis

| Metric | Value |
|--------|-------|
| Total Customers (6-month training) | 2,427 |
| Customers with Positive CLV | 1,690 |
| **6-Month Retention Rate** | **69.6%** |
| Average 6-Month CLV | £986.05 |
| Median 6-Month CLV | £370.45 |
| Top 20% Customers Contribution | 72.1% of total CLV |
| At-Risk Customers (31.2% base) | 1,209 |
| Potential Revenue at Risk | £395,787 (7.0% of total) |

**Correlations**:
- Frequency ↔ CLV: **0.607** (strong positive)
- Customer Lifespan ↔ CLV: **0.350** (moderate positive)

### Clustering Results (K=5)

| Cluster | Size | Recency (avg) | Frequency (avg) | Avg Value | Profile |
|---------|------|---------------|-----------------|-----------|---------|
| Recent Customers | 2,537 (65.5%) | 45 days | 3.15x | £961 | Core customer base |
| High-Value Active | 31 (0.8%) | 6 days | 38x | £25,399 | VIP/whales segment |
| At-Risk Customers | 957 (24.7%) | 247 days | 1.55x | £401 | Churned, inactive |
| Emerging High-Value | 345 (8.9%) | 17 days | 14.4x | £5,293 | Growth potential |
| Ultra High-Value | 4 (0.1%) | 2 days | 128x | £44,736 | Extreme outliers |

---

## Model Performance

### Prediction Accuracy

| Metric | Train | Test | Interpretation |
|--------|-------|------|-----------------|
| **R²** | 0.9604 | **0.5136** | Model explains 51% of CLV variance |
| **RMSE** | £464.79 | £1,421.74 | Typical prediction error ±£1,422 |
| **MAE** | – | **£688.22** | Median error magnitude |
| **Within ±£100** | – | 14.4% | Precise predictions (rare) |
| **Within ±£500** | – | 62.3% | Reliable directional predictions |

### Feature Importance

| Feature | Importance | % of Model |
|---------|------------|-----------|
| Total_Revenue | 0.6228 | **62.3%** |
| Revenue_Per_Day | 0.2006 | 20.1% |
| Customer_Lifespan | 0.0448 | 4.5% |
| Total_Quantity | 0.0423 | 4.2% |
| Purchase_Frequency | 0.0328 | 3.3% |
| Purchase_Rate | 0.0317 | 3.2% |
| Avg_Order_Value | 0.0250 | 2.5% |

**Key Insight**: Historical revenue dominates predictions (62%), indicating past spending is the strongest CLV predictor. Recent revenue velocity (Revenue_Per_Day) adds 20% more signal.

### Cross-Validation
- **Mean CV R²**: 0.5295 (±0.2706)
- **Interpretation**: Consistent 51% variance explained across folds; model generalizes reasonably well

---

## Repository Structure

```
customer-lifetime-value-analysis/
│
├── README.md                                    # This file
├── LICENSE                                      # MIT License
├── Customer_Lifetime_Value_Analysis.ipynb       # Main analysis notebook
├── RFM analysis.pdf                             # Detailed project report
└── requirements.txt                             # Python dependencies (create from list below)
```

**Note**: Dataset is hosted externally on Google Drive due to size constraints. Download link provided in [Dataset](#dataset) section.

---

## How to Run

### Prerequisites
- Python 3.10 or higher
- Jupyter Notebook
- 8GB+ RAM recommended

### Step 1: Clone the Repository
```bash
git clone https://github.com/yourusername/customer-lifetime-value-analysis.git
cd customer-lifetime-value-analysis
```

### Step 2: Set Up Environment
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Step 3: Download Dataset
1. Download `OnlineRetail.csv` from [Google Drive link](https://drive.google.com/file/d/1N-WfPSe9rUtBIHPPOpBYVbZev_nRsV3w/view?usp=drive_link)
2. Place the file in your working directory (same location as the notebook)

### Step 4: Run the Analysis
```bash
# Launch Jupyter Notebook
jupyter notebook Customer_Lifetime_Value_Analysis.ipynb
```

### Step 5: Execute Notebook
Run all cells sequentially (Cell → Run All). The notebook is organized in clear sections:
1. Environment setup & imports
2. Data loading & preprocessing
3. Exploratory Data Analysis
4. RFM analysis & segmentation
5. Historical CLV calculation
6. Clustering analysis
7. Predictive modeling (XGBoost)
8. Business insights & recommendations

**Expected Runtime**: 2-3 minutes on standard hardware

---

## Business Recommendations

### 1. Champion & Loyal Customer Retention (1,584 customers)
**Objective**: Reduce churn by 5% to retain £86,333 in CLV

**Tactics**:
- Implement VIP loyalty program with exclusive benefits
- Personalized product recommendations based on purchase history
- Early access to new products and seasonal sales
- Dedicated customer service tier with faster response times
- Exclusive rewards/points multiplier (2-3x on purchases)

---

### 2. Customer Reactivation Campaign (666 at-risk/lost customers)
**Objective**: Achieve 10% reactivation rate to capture £26,411 new CLV

**Tactics**:
- Staged win-back email campaigns segmented by inactivity level
- Special one-time discount offers (10-15% off or free shipping)
- Survey to understand churn drivers
- Targeted product recommendations based on prior purchase patterns
- Limited-time "We Miss You" incentives

---

### 3. Growth Development Program (823 potential loyalists)
**Objective**: Increase average order value by 15% to grow £33,247 in CLV

**Tactics**:
- Cross-sell and upsell campaigns with intelligent bundling
- Loyalty point system incentivizing repeat purchases
- Frequency-based escalating discounts (5% after 3 orders, 10% after 5)
- Personalized category recommendations
- Gamification elements (badges, milestones)

---

### 4. Predictive CLV Integration
**Objective**: Optimize resource allocation using model predictions

**Tactics**:
- Set dynamic **Customer Acquisition Cost (CAC) limits** by predicted CLV tier
  - Champions: up to £500 CAC
  - Loyal: up to £150 CAC
  - Potential Loyalists: up to £50 CAC
- Allocate support resources proportional to predicted CLV
- Implement early churn warning system (low predicted CLV + high recency)
- A/B test offers on high-predicted-CLV segments first
- Prioritize merchandising and inventory based on CLV predictions

---

### Financial Impact Summary

| Initiative | Revenue Impact | Mechanism |
|-----------|-----------------|-----------|
| Retention Improvement | £86,333 | 5% churn reduction on Champions/Loyal |
| Customer Reactivation | £26,411 | 10% success rate on 666 at-risk targets |
| Upselling/Growth | £33,247 | 15% AOV increase on Potential Loyalists |
| **Total Projected Uplift** | **£145,991** | **2.6% ROI vs. current revenue** |

---

## Limitations & Future Work

### Current Limitations
1. **Moderate Model R²** (0.5136): Explains ~51% of CLV variance; ~49% driven by external factors not captured in features
2. **Single Market/Year**: UK-only analysis over 12 months; patterns may differ in other geographies or time periods
3. **Feature Scope**: Lacks product category, campaign exposure, customer demographics, and seasonality indicators
4. **Temporal Assumptions**: Contiguous training/prediction periods; no gap for true holdout testing

### Recommended Enhancements

**Short-term**:
- Add seasonality features (month, quarter dummies)
- Include product category as predictor
- Test alternative algorithms (LightGBM, CatBoost) for potential accuracy gains
- Implement monthly model retraining with drift monitoring

**Medium-term**:
- Expand to multi-country analysis
- Incorporate customer demographics (age, location) if available
- Build category-level CLV for targeted cross-sell optimization
- Develop real-time churn prediction system with daily/weekly scoring

**Long-term**:
- Integrate with CRM/BI platforms for operational dashboards
- Implement automated A/B testing framework for CLV-driven campaigns
- Add customer satisfaction scores (NPS, reviews) to feature set
- Develop tiered customer strategies with automated playbook execution

---

## Tech Stack

### Core Technologies
| Component | Tool | Version |
|-----------|------|---------|
| **Language** | Python | 3.10+ |
| **Data Processing** | Pandas | 1.5+ |
| **Numerical Computing** | NumPy | 1.23+ |
| **ML/Modeling** | XGBoost, Scikit-learn | 1.7+, 1.3+ |
| **Visualization** | Matplotlib, Seaborn | 3.5+, 0.12+ |
| **Notebook Environment** | Jupyter | 1.0+ |

### Dependencies (requirements.txt)
```
pandas>=1.5.0
numpy>=1.23.0
scikit-learn>=1.3.0
xgboost>=1.7.0
matplotlib>=3.5.0
seaborn>=0.12.0
jupyter>=1.0.0
```

---

## Documentation

### Detailed Report
For an in-depth analysis including methodology, visualizations, and extended insights, refer to:

** [RFM analysis.pdf](RFM%20analysis.pdf)** - Project report 

The report includes:
- Executive summary with key findings
- Detailed methodology and data preprocessing steps
- Complete RFM segmentation analysis with visual breakdowns
- CLV calculation methodology and results
- Model development, validation, and performance metrics
- Business recommendations with ROI projections
- Limitations discussion and future enhancement roadmap

---

## Citation & License

### License
This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.

### Citation
If you use this analysis or code in your research or projects, please cite as:

```bibtex
@misc{kumar2025clv,
  title={Customer Lifetime Value Analysis: RFM Segmentation & Predictive Modeling},
  author={Kumar, Dipanshu},
  year={2025},
  howpublished={\url{https://github.com/yourusername/customer-lifetime-value-analysis}},
  note={Accessed: 2025-11-19}
}
```

### Acknowledgments
- **Online Retail Dataset**: UCI Machine Learning Repository
- **Python Ecosystem**: Pandas, Scikit-learn, XGBoost, and the broader open-source community
- **Research Foundation**: Built on established RFM segmentation and CLV modeling methodologies

---

## Contact

**Dipanshu Kumar**  
Data Analyst | Data Science Professional  

📧 Email: kayhiusy@gmail.com  
🔗 LinkedIn: [linkedin.com/in/yourprofile]([https://linkedin.com/in/yourprofile](https://www.linkedin.com/in/dipanshu-kumar-61a21322a/))  
🐙 GitHub: [@yourusername](https://github.com/DipanshuK2003)  

**Focus Areas**: Production-grade analytics, predictive modeling, customer analytics, and data-driven business strategy with measurable ROI impact.

---

## Project Statistics

- **Development Time**: ~2-3 weeks (EDA, modeling, validation, documentation)
- **Code Base**: 1,500+ lines (notebook + utilities)
- **Key Deliverables**: 10+ actionable insights with quantified business impact
- **Model Accuracy**: 51.4% R² (suitable for strategic use)
- **ROI Potential**: +2.6% incremental revenue (~£146k annually)
- **Documentation**: Comprehensive 10-page report + production-ready code

---

## Project Status

**Current Status**: Production-Ready

**Last Updated**: November 2025  
**Data Coverage**: December 2010 – December 2011  
**Maintenance**: Active  

**Planned Updates**:
- Quarterly model retraining with updated data
- Feature engineering enhancements
- Extended documentation with use case examples
- Community contributions integration

---

## Quick Links

- ** [Jupyter Notebook](Customer_Lifetime_Value_Analysis.ipynb)** - Complete analysis code
- ** [Detailed Report](RFM%20analysis.pdf)** - Full project documentation
- ** [Download Dataset](https://drive.google.com/file/d/1N-WfPSe9rUtBIHPPOpBYVbZev_nRsV3w/view?usp=drive_link)** - Google Drive (public access)
---

*This project demonstrates end-to-end analytics capabilities from data preprocessing to actionable business insights, suitable for portfolio showcasing and practical business applications.*
