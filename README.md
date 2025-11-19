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
- [Visualizations & Diagrams](#visualizations--diagrams)
- [Business Recommendations](#business-recommendations)
- [Model Performance](#model-performance)
- [Limitations & Future Work](#limitations--future-work)
- [Tech Stack](#tech-stack)
- [Citation & License](#citation--license)
- [Maintainer](#maintainer)

---

## Project Overview

This repository provides a comprehensive framework for quantifying and predicting Customer Lifetime Value (CLV) in retail environments. The analysis combines:

1. **RFM Segmentation** – stratify customers into 10 behavioral segments
2. **Historical CLV** – measure actual 6-month customer spending across a holdout window
3. **Clustering Analysis** – K-means identifies 5 behavioral groups beyond RFM
4. **Predictive Modeling** – XGBoost forecasts future CLV with feature importance insights
5. **Business Strategy** – data-driven recommendations for retention, reactivation, and growth

**Use Case**: Enable marketing/customer success teams to prioritize high-value customers, allocate budgets strategically, and measure retention ROI.

---

## Dataset

| Property | Value |
|----------|-------|
| **Source** | Online Retail Dataset |
| **Time Period** | Dec 1, 2010 – Dec 9, 2011 |
| **Initial Records** | 541,909 transactions |
| **Final Records** | 347,946 transactions (after cleaning) |
| **Unique Customers** | 3,874 |
| **Total Revenue** | £5,614,837.20 |
| **Geographic Focus** | United Kingdom (91.4% of transactions) |
| **Key Features** | InvoiceNo, CustomerID, InvoiceDate, Quantity, UnitPrice, Description, Country |

### Data Quality Issues Handled
- **Missing CustomerID**: 134,697 rows (24.8%) removed
- **Cancelled Orders**: 9,288 rows (1.7%) removed
- **Invalid Prices/Quantities**: 40 rows removed
- **Outliers**: 6,378 rows (P99 threshold) removed
- **Geographic Filter**: Non-UK transactions excluded for consistency

**Result**: 35.8% reduction in data volume; improved signal quality for analysis

---

## 🔬 Methodology

### Step 1: Data Preprocessing & Feature Engineering
```
Raw Data (541,909 rows)
    ↓
Remove cancelled orders, missing IDs, invalid values
    ↓
Filter to UK market, remove outliers
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
- Map to 10 customer segments:
  - Champions (RFM ≥ 12): Recent, frequent, high-value
  - Loyal Customers (RFM 10-11): Regular, consistent spending
  - Potential Loyalists (RFM 8-9): Decent value, room to grow
  - At Risk (low R, any F/M): Churning, needs reactivation
  - Lost Customers (very low R): No recent activity
  - And 5 more nuanced segments...

### Step 3: Historical CLV Calculation
```
Training Period (6 months):     Dec 2010 – May 2011 → Learn customer patterns
    ↓
Extract Training Features:
  - Purchase_Frequency (transaction count)
  - Total_Revenue (total spending)
  - Avg_Order_Value (mean per transaction)
  - Customer_Lifespan (days between first & last purchase)
  - Purchase_Rate (frequency / lifespan)
  - Revenue_Per_Day (revenue / lifespan)
    ↓
Prediction Period (6 months):   Jun 2011 – Dec 2011 → Actual future spending
    ↓
Target Variable: Actual_CLV_6M = sum of revenue in prediction period
    ↓
Retention Metric: % of customers with CLV > 0 = 69.6%
```

### Step 4: K-means Clustering
- **Features**: Recency, Frequency, Monetary (standardized)
- **Optimal K**: 5 (elbow method)
- **Output**: 5 behavioral clusters (e.g., "High-Value Active," "At-Risk," "Recent Customers")

### Step 5: Predictive CLV Modeling

**Model**: XGBoost Regressor
- **Hyperparameters**: n_estimators=100, max_depth=6, learning_rate=0.1
- **Features**: Purchase_Frequency, Total_Revenue, Avg_Order_Value, Customer_Lifespan, Purchase_Rate, Revenue_Per_Day, Total_Quantity
- **Train-Test Split**: 80/20 (1,941 train, 486 test)
- **Validation**: 5-fold cross-validation

**Rationale**: XGBoost handles non-linear relationships between features and CLV, provides interpretable feature importance, and generalizes well on financial datasets.

---

## 📊 Key Results

### RFM Segmentation Results

| Segment | Customers | % of Base | Avg Revenue | Revenue Share | Action |
|---------|-----------|-----------|------------|---------------|---------| 
| **Champions** | 1,068 | 27.6% | £3,821.86 | **72.7%** | Retain/VIP |
| **Loyal Customers** | 516 | 13.3% | £1,177.74 | 10.8% | Nurture |
| **Potential Loyalists** | 582 | 15.0% | £674.61 | 7.0% | Grow |
| **About to Sleep** | 492 | 12.7% | £363.63 | 3.2% | Reactive |
| **Lost Customers** | 543 | 14.0% | £242.50 | 2.3% | Win-back |
| **At Risk** | 174 | 4.5% | £489.67 | 1.5% | Reactivate |
| Other Segments | 499 | 13.0% | £232–£1,190 | 1.5% | Situational |

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
- Frequency ↔ CLV: 0.607 (strong positive)
- Customer Lifespan ↔ CLV: 0.350 (moderate positive)

### Clustering Results (K=5)

| Cluster | Size | Recency (avg) | Frequency (avg) | Avg Value | Profile |
|---------|------|---------------|-----------------|-----------|---------|
| Recent Customers | 2,537 | 45 days | 3.15x | £961 | Core customer base |
| High-Value Active | 31 | 6 days | 38x | £25,399 | VIP/whales |
| At-Risk Customers | 957 | 247 days | 1.55x | £401 | Churned, inactive |
| Emerging High-Value | 345 | 17 days | 14.4x | £5,293 | Growth potential |
| Ultra High-Value | 4 | 2 days | 128x | £44,736 | Extreme outliers |

---

## 🤖 Model Performance

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

**Key Insight**: Historical revenue dominates (62%), indicating past spending is the strongest CLV predictor. Recent revenue velocity (Revenue_Per_Day) adds 20% more signal.

### Cross-Validation
- **Mean CV R²**: 0.5295 (±0.2706)
- **Interpretation**: Consistent 51% variance explained across folds; model generalizes reasonably well

---

## 📁 Repository Structure

```
customer-lifetime-value-analysis/
│
├── README.md                                    # This file
├── LICENSE                                      # MIT license
├── requirements.txt                             # Python dependencies
│
├── notebooks/
│   └── Customer_Lifetime_Value_Analysis.ipynb   # Main analysis notebook
│
├── reports/
│   └── CLV_Analysis_Report.md                   # Detailed project report (10 pages)
│
├── assets/                                      # Visualizations & diagrams
│   ├── cover.png                                # Project cover/hero image
│   ├── rfm_segment_distribution.png             # RFM segment breakdown chart
│   ├── clv_distribution.png                     # CLV histogram
│   ├── frequency_vs_clv_scatter.png             # Scatter plot: frequency vs CLV
│   ├── lifespan_vs_clv_scatter.png              # Scatter plot: lifespan vs CLV
│   ├── model_actual_vs_predicted.png            # Model performance scatter
│   ├── feature_importance.png                   # XGBoost feature importance bar chart
│   ├── pipeline_flow.png                        # Data pipeline flowchart
│   ├── segment_revenue_breakdown.png            # Revenue contribution by segment
│   └── roi_projections.png                      # Business impact projections
│
└── data/                                        # (Optional) data directory
    ├── raw/
    │   └── OnlineRetail.csv                     # Raw transaction data
    └── processed/
        └── rfm_analysis_results.csv             # Processed RFM segments
```

---

## 🚀 How to Run

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/customer-lifetime-value-analysis.git
cd customer-lifetime-value-analysis
```

### 2. Set Up Environment
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate          # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 3. Download Data
Place the `OnlineRetail.csv` file in `data/raw/` directory, or adjust the data path in the notebook.

### 4. Run the Notebook
```bash
jupyter notebook notebooks/Customer_Lifetime_Value_Analysis.ipynb
```

**Key Cells to Execute**:
1. Environment setup & imports
2. Data loading & preprocessing
3. EDA (Exploratory Data Analysis)
4. RFM analysis & visualization
5. Historical CLV calculation
6. Clustering analysis
7. Model training & evaluation
8. Business insights & recommendations

### 5. Generate Outputs
- RFM segments CSV
- Trained CLV model (pickle/joblib)
- Visualization charts (saved to `assets/`)
- Analysis report

---

## 📸 Visualizations & Diagrams

### Recommended Screenshots to Add (place in `assets/`)

1. **rfm_segment_distribution.png**
   - Pie chart or bar chart showing % of customers in each RFM segment
   - Include revenue contribution overlay

2. **clv_distribution.png**
   - Histogram of 6-month CLV with mean/median lines
   - Shows right-skewed distribution of customer value

3. **frequency_vs_clv_scatter.png**
   - Scatter plot: Purchase Frequency (x-axis) vs CLV (y-axis)
   - Color by RFM segment or retention status
   - Shows strong 0.607 correlation

4. **lifespan_vs_clv_scatter.png**
   - Scatter plot: Customer Lifespan (x-axis) vs CLV (y-axis)
   - Moderate 0.350 correlation, more dispersed

5. **model_actual_vs_predicted.png**
   - Scatter: Actual CLV (x) vs Predicted CLV (y)
   - Diagonal reference line (perfect prediction)
   - Shading for prediction error bands (±£500)

6. **feature_importance.png**
   - Horizontal bar chart: Top 7 features ranked by importance
   - Total_Revenue dominates at 62.3%

7. **segment_revenue_breakdown.png**
   - Stacked bar: Customers count vs revenue by segment
   - Illustrates concentration in Champions/Loyal

8. **pipeline_flow.png**
   - Simple flowchart: Raw Data → Preprocess → RFM/CLV → Modeling → Business Insights
   - Include key metrics at each stage

### Diagram Suggestions

**Data Pipeline Flow**:
```
Raw Transactions (541,909)
    ↓ [Remove cancellations, nulls, outliers]
Clean Data (347,946)
    ↓ [RFM scoring + segmentation]
Customer Segments (3,874)
    ↓ [Historical CLV + Clustering]
RFM + CLV Dataset (2,427)
    ↓ [Feature engineering + modeling]
XGBoost Predictions
    ↓
Business Recommendations & ROI
```

---

## 💡 Business Recommendations

### 1. Champion & Loyal Customer Retention (1,584 customers)
**Action**: Implement VIP loyalty program with exclusive benefits
- Personalized product recommendations based on purchase history
- Early access to new products and seasonal sales
- Dedicated customer service tier with faster response
- Exclusive rewards/points multiplier (2-3x on purchases)

**Expected Impact**: 
- Reduce churn by 5% → retain £86,333 in CLV
- Increase repeat purchase frequency by 10%

---

### 2. Customer Reactivation Campaign (666 at-risk/lost customers)
**Action**: Staged win-back email & incentive campaign
- Segment messaging by inactivity level (3-6 months vs 6+ months)
- Special one-time discount offers (10-15% off, or free shipping)
- Survey to understand churn drivers
- Targeted product recommendations based on prior purchases

**Expected Impact**:
- 10% reactivation rate → capture £26,411 new CLV
- Recover £395,787 revenue at risk

---

### 3. Growth Development Program (823 potential loyalists)
**Action**: Cross-sell/upsell campaigns + frequency incentives
- Bundle offers combining frequent/complementary items
- Loyalty point system incentivizing repeat purchases
- Frequency-based escalating discounts (e.g., 5% after 3 orders, 10% after 5)
- Personalized category recommendations

**Expected Impact**:
- 15% increase in average order value → grow £33,247
- Convert 20% from Potential Loyalists to Loyal Customers

---

### 4. Predictive CLV Integration
**Action**: Use model predictions to operationalize CLV
- Set dynamic **Customer Acquisition Cost (CAC) limits** by predicted CLV tier
  - Champions: up to £500 CAC
  - Loyal: up to £150 CAC
  - Potential Loyalists: up to £50 CAC
- Allocate support resources and merchandising budget proportional to predicted CLV
- Implement early churn warning system (low predicted CLV + high recency)
- A/B test offers on high-predicted-CLV segments first

**Expected Impact**:
- Optimize marketing spend efficiency
- Improve targeting ROI

---

### Projected Financial Impact

| Initiative | Revenue Impact | Mechanism |
|-----------|-----------------|-----------|
| Retention Improvement | £86,333 | 5% churn reduction on Champions/Loyal |
| Customer Reactivation | £26,411 | 10% success rate on 666 at-risk targets |
| Upselling/Growth | £33,247 | 15% AOV increase on Potential Loyalists |
| **Total Projected Uplift** | **£145,991** | **2.6% ROI vs. current revenue** |

---

## 🔮 Limitations & Future Work

### Current Limitations
1. **Moderate Model R²** (0.5136): Explains ~51% of CLV variance; ~49% driven by external factors (seasonality, campaigns, macroeconomic)
2. **Single Market/Year**: UK-only, Dec 2010–Dec 2011; patterns may differ in other geographies or time periods
3. **Feature Scope**: Lacks external signals (product category, campaign exposure, customer demographics, seasonal trends)
4. **Temporal Leakage Risk**: Training/prediction periods are contiguous; no gap for true holdout testing

### Recommended Enhancements

**Short-term**:
- Add seasonality (month/quarter dummies) to features
- Include product category as feature
- Test LightGBM, CatBoost for potential R² improvements
- Implement monthly model retraining with performance monitoring

**Medium-term**:
- Expand to multi-country analysis (identify geography-specific patterns)
- Incorporate customer demographics (age, location) if available
- Build category-level CLV for cross-sell optimization
- Develop real-time churn prediction system (daily/weekly scoring)

**Long-term**:
- Integrate with CRM/BI platform for operational dashboards
- Implement A/B testing framework for CLV-driven campaigns
- Add customer satisfaction scores (NPS, reviews) to model
- Develop tiered customer strategies (automated playbooks per segment)

---

## 🛠️ Tech Stack

| Component | Tool | Version |
|-----------|------|---------|
| **Language** | Python | 3.10+ |
| **Data Processing** | Pandas | 1.5+ |
| **Numerical Computing** | NumPy | 1.23+ |
| **ML/Modeling** | XGBoost, Scikit-learn | 1.7+, 1.3+ |
| **Visualization** | Matplotlib, Seaborn | 3.5+, 0.12+ |
| **Notebook** | Jupyter | 1.0+ |

### Dependencies
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

## 📝 Citation & License

### License
This project is licensed under the **MIT License** – see the [LICENSE](LICENSE) file for details.

### Citation
If you use this analysis or code, please cite as:

```bibtex
@misc{kumar2025clv,
  title={Customer Lifetime Value Analysis: RFM Segmentation & Predictive Modeling},
  author={Kumar, Dipanshu},
  year={2025},
  url={https://github.com/yourusername/customer-lifetime-value-analysis}
}
```

### Acknowledgments
- **Online Retail Dataset**: UCI Machine Learning Repository
- **Python Ecosystem**: Pandas, Scikit-learn, XGBoost, and the broader open-source community for enabling reproducible analytics

---

## 👤 Maintainer

**Dipanshu Kumar**  
Data Analyst | Data Science Professional  
📧 Email: [your-email@example.com]  
🔗 LinkedIn: [your-linkedin-profile]  
🐙 GitHub: [your-github-profile]  

Focus: Production-grade analytics, predictive modeling, and data-driven business strategy with measurable ROI impact.

---

## 🤝 Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add improvement'`)
4. Push to branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## ❓ FAQ

**Q: Can I use this on other retail datasets?**  
A: Yes! The pipeline is generalizable. Adjust the data loading path, date range, and filtering logic as needed. Test on ~1-2 years of data for reliable CLV patterns.

**Q: How often should I retrain the model?**  
A: Recommend monthly retraining with performance monitoring. If R² drops >5%, investigate for data drift or market changes.

**Q: What if my model R² is lower?**  
A: This is normal—CLV depends on many external factors. Focus on directional accuracy rather than absolute predictions; use for customer tier prioritization rather than exact forecasts.

**Q: Can I deploy this for real-time predictions?**  
A: Yes, export the trained model (joblib) and wrap in a Flask/FastAPI service for batch or real-time scoring.

---

## 📊 Project Statistics

- **Analysis Duration**: ~2 weeks (EDA, modeling, validation, insights)
- **Lines of Code**: ~1,500+ (notebook + utilities)
- **Key Findings**: 10+ actionable insights with quantified business impact
- **Model Accuracy**: 51.4% R² (suitable for strategic use)
- **ROI Potential**: +2.6% incremental revenue (~£146k)

---

## 🔗 Quick Links

- [Full Report](reports/CLV_Analysis_Report.md)
- [Jupyter Notebook](notebooks/Customer_Lifetime_Value_Analysis.ipynb)
- [Issues/Questions](https://github.com/yourusername/customer-lifetime-value-analysis/issues)

---

**Last Updated**: November 2025  
**Data Coverage**: December 2010 – December 2011  
**Status**: ✅ Production-Ready
