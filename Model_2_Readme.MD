(Model 2 – Acquisition Price Estimation Model)
AI-Driven Real Estate Valuation for Safe, Data-Backed Acquisition Decisions
🧩 Overview

Model 2 estimates the fair acquisition price for residential properties across selected U.S. metros using property-level features and metro quality indicators. This model ensures we never overpay, provides a standardized pricing method for acquisitions, and creates the foundation for downstream profitability modeling (Model 3 and Model 4).

The model produces:

Predicted Fair Market Price

Fair Price Range (±25 percent)

Maximum Offer Price (5 percent safety margin)

AI Confidence Flag (OK / REVIEW_REQUIRED)

Model 2 is the first ML component that directly affects financial decisions, offer strategy, and risk management.

📊 Key Objectives

Predict a fair, explainable value for each property

Apply business rules to set a disciplined acquisition price

Quantify uncertainty using a consistent, investor-friendly range

Create a human-in-the-loop system for risky or uncertain predictions

Prepare clean outputs for subsequent pricing and ROI models

📚 Data Sources Used
1. Redfin Property-Level Data

Provides structural attributes and transaction history:

Beds, baths, square footage

Lot size, year built

Closed price

City, state, ZIP

2. ZIP → CBSA Crosswalk

Used to map each property to its official metropolitan area.

3. Model-1 Outputs

Adds metro-level quality scores:

Economic strength

Housing growth

Migration

Crime & safety

Affordability

Final City Score

4. Engineered Features

Created during preprocessing:

Property age

Metro categorical encoding

Median-imputed fields

🛠️ Feature Engineering Pipeline

Model-2 uses a clean set of engineered features structured for Gradient Boosting Regression.

Structural Features

beds

baths

sqft

lot_size

age

hoa_fee

dom (if available)

Location Features

metro_cat (numeric encoding)

zip → CBSA merge

Final_City_Score from Model 1

Data Cleaning Steps

Remove non-data header disclaimer rows

Standardize column names

Convert ZIP to 5-digit string

Median-impute missing numeric values

Merge all cleaned metro files into one dataset

🤖 Modeling Approach

We use a Gradient Boosting Regressor (GBR) due to:

Robust performance on nonlinear relationships

Strong results on tabular housing data

High interpretability of feature importance

Stable performance across diverse metro types

Training Details

Train/Test Split: 80/20

MAE ≈ $46,000

R² ≈ 0.87

These metrics are consistent with expected variance in U.S. residential housing.

🏷️ Fair Price Estimation

The ML model produces:

predicted_fair_price


We then apply a transparent uncertainty interval:

🎯 Fair Price Range (95% Interval)

To keep pricing interpretable and business-aligned, we use:

fair_low_95  = predicted_fair_price × 0.75
fair_high_95 = predicted_fair_price × 1.25


This ±25 percent range:

Avoids unstable sigma-based CI

Matches typical appraisal & Zestimate variance

Works consistently across markets

💰 Maximum Offer Price

We apply a conservative 5 percent safety margin:

max_offer_price = predicted_fair_price × 0.95


This ensures:

We do not overpay

Renovation & holding costs are covered

Margins exist for profitable resale

🏳️ Review Flag (AI Confidence Measure)

We quantify uncertainty using:

relative_bandwidth = (high_95 - low_95) / predicted_price


Decision rule:

If relative_bandwidth > 0.50 → REVIEW_REQUIRED
Else → OK


OK
→ Model is confident enough for automated pricing

REVIEW_REQUIRED
→ Human analyst must validate price due to high uncertainty

This creates an AI + Human Hybrid Workflow.

📦 Model-2 Output Columns

The exported CSV includes:

Column	Description
property_id	Unique ID
city, state, zip	Location fields
beds, baths, sqft, lot_size, age	Structure features
closed_price	Last recorded sale
predicted_fair_price	ML-estimated acquisition price
fair_low_95, fair_high_95	±25 percent range
max_offer_price	Safe maximum bid
review_flag	OK / REVIEW_REQUIRED
CBSA, CBSA_NAME	Metro identifiers
Final_City_Score	Score from Model 1
🗂️ Repository Structure (Model-2 Folder)
Model_2/
│
├── Model2_AcquisitionPrice.ipynb     # Main notebook
├── Model2_AcquisitionPrices_combined.csv
├── redfin_data_raw/                  # Raw Redfin exports
├── cleaned_data/                     # Preprocessed metro datasets
└── zip_cbsa_crosswalk.csv            # ZIP → CBSA mapping

▶️ How to Run Model-2
1. Install dependencies
pip install -r requirements.txt

2. Open the notebook
Model_2/Model2_AcquisitionPrice.ipynb

3. Run all cells

The notebook will:

Load + clean Redfin data

Merge CBSA & Model-1 scores

Train Gradient Boosting Model

Generate fair price + uncertainty bands

Export output CSV

🔍 Example Output (Sample Row)
property_id: 10392
beds: 3
baths: 1.5
sqft: 1897
predicted_fair_price: 186,893
fair_low_95:         140,170
fair_high_95:        233,617
max_offer_price:     177,548
review_flag:         REVIEW_REQUIRED

🚀 Why Model-2 Is Critical

Model-2 establishes:

A consistent pricing standard for all acquisitions

Protection against overpaying

A feed into profit modeling (Model-3)

A foundation for Buy/No-Buy decisioning (Model-4)

Inputs for Income Statement, Cash Flow, Balance Sheet

It creates a repeatable, scalable pricing engine for real estate investing.

📈 Future Improvements

Integrate XGBoost / LightGBM performance tuning

Add neighborhood-level crime + school quality data

Add Zillow rent estimates to refine yields

Use Shapley values for model explainability

Deploy model as an API for real-time pricing

🏁 Conclusion

Model-2 transforms raw property data into actionable acquisition insights using a structured ML workflow and transparent business rules. It is a key component in building a full AI-driven real estate investment platform.
