Part B: Business Case Analysis

B1. Problem Formulation

(a) Machine Learning Formulation

This can be formulated as a supervised regression problem.

. Target Variable: items_sold (number of items sold per store per month)
. Input Features:
----> Store attributes: store_id, store_size, location_type
----> Promotion type
----> Customer behavior: footfall, past sales
----> Temporal features: month, seasonality, festivals, weekends
----> Competition: competition_density

Justification:
The goal is to predict a continuous numeric value (items sold), which makes this a regression problem. The predictions can then be used to choose the best promotion for each store.

(b) Why Items Sold is Better than Revenue

Revenue can be misleading because:

. Different promotions affect pricing (discounts reduce revenue but may increase sales volume)
. High revenue does not always mean high customer engagement

Items sold is better because:

. It directly reflects customer demand and promotion effectiveness
. It is not distorted by pricing strategies

Broader Principle:
The target variable should align closely with the actual business objective. Choosing the wrong target can lead to misleading conclusions and poor decisions.

(c) Alternative Modelling Strategy

Instead of one global model, use a segmented or hierarchical modelling approach, such as:

. Separate models for urban, semi-urban, and rural stores
. Or include interaction features (e.g., promotion × location)

Justification:
Different stores respond differently to promotions due to demographics and competition. A segmented approach captures these variations better and improves prediction accuracy.




B2. Data and EDA Strategy

(a) Data Joining and Grain

The four tables should be joined as follows:

. Transactions joined with store attributes using store_id
. Promotions joined using promotion_type
. Calendar joined using transaction_date

Final dataset grain:
---> One row = one store per month

Aggregations:

. Total items sold per store per month
. Average basket size
. Total transactions
. Promotion applied in that month
. Flags for weekend/festival

(b) Exploratory Data Analysis (EDA)
1. Sales by Promotion Type (Bar Chart)
   .  Identify which promotions drive higher sales
   .  Helps in feature importance and baseline understanding
2. Time Series Plot (Sales over Months)
   .  Detect seasonality and trends
   .  Helps in adding time-based features
3. Box Plot (Sales by Location Type)
   .  Compare urban vs rural performance
   .  Supports segmentation strategy
4. Correlation Heatmap
   .  Identify relationships between variables
   .  Helps in feature selection and avoiding multicollinearity
5. Histogram of Items Sold
   .  Understand distribution (skewness, outliers)
   .  Helps decide transformations if needed


(c) Handling Imbalance (80% No Promotion)

Problem:

.  Model may learn to ignore promotions
.  Bias toward predicting “no promotion effect”

Solutions:

.  Use stratified sampling or balanced subsets
.  Add promotion-related features carefully
.  Evaluate performance separately for promoted vs non-promoted cases



B3. Model Evaluation and Deployment

(a) Train-Test Split & Metrics

Split Strategy:

. Use time-based split
. Train on first ~2.5 years
. Test on last ~6 months

Why not random split?

. Random split mixes past and future data
. Causes data leakage and unrealistic performance

Evaluation Metrics:

. RMSE: Penalizes large errors (important for forecasting accuracy)
. MAE: Easier to interpret (average error in items sold)

Interpretation:

. Lower RMSE → fewer large mistakes
. Lower MAE → consistent prediction accuracy


(b) Explaining Model Decisions

To explain why different promotions are recommended:

. Check feature importance from the model
. Analyze key factors like:
---> Month (seasonality, e.g., December shopping spike)
---> Festival or holiday indicators
---> Past promotion performance

Communication to marketing team:

. Example:
---> Loyalty Points work better in December due to repeat purchases
---> Flat Discounts work better in March due to price sensitivity

This makes model decisions interpretable and actionable.



(c) Deployment Strategy

1. Model Saving:

.  Save trained pipeline using joblib or pickle

2. Monthly Prediction Process:

.  Collect new monthly data
.  Apply same preprocessing pipeline
.  Generate predictions for each store

3. Recommendation System:

.  Run predictions for all promotion types
.  Select promotion with highest predicted sales

4. Monitoring:

.  Track prediction vs actual sales
.  Monitor metrics like RMSE over time
.  Detect drift in data (e.g., changing customer behavior)

5. Retraining:

.  Retrain model periodically (e.g., every 3–6 months)
.  Or when performance drops significantly