Model 2 – Acquisition Price Estimation Model
AI-Driven Real Estate Valuation for Safe, Data-Backed Acquisition Decisions
🧩 Overview

Model 2 estimates the fair acquisition price for residential properties across selected U.S. metros using property-level features and metro quality indicators. This model ensures disciplined pricing, avoids overpayment, and feeds into downstream profitability models (Model 3 and Model 4).

The model produces:

Predicted Fair Market Price

Fair Price Range (±25 percent)

Maximum Offer Price (5 percent safety margin)

AI Confidence Flag (OK / REVIEW_REQUIRED)

Model 2 is the first ML component that directly affects financial decisions, offer strategy, and risk management.

📊 Key Objectives

Predict a fair, explainable value for each property

Apply business rules to enforce disciplined acquisition pricing

Quantify uncertainty using a consistent investor-friendly range

Enable a human-in-the-loop system for uncertain cases

Prepare clean outputs for profitability and ROI models

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

Adds metro-level quality indicators:

Economic strength

Housing growth

Migration

Crime & safety

Affordability

Final City Score

4. Engineered Features

Generated during preprocessing:

Property age

Metro categorical encoding

Median-imputed fields

🛠️ Feature Engineering Pipeline

Model 2 uses a cleaned, engineered feature set for Gradient Boosting Regression.

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

ZIP → CBSA merge

Final_City_Score from Model 1

Data Cleaning Steps

Remove non-data header rows

Standardize column names

Convert ZIP to 5-digit string

Median-impute missing numeric fields

Merge all cleaned metro datasets into one master file

🤖 Modeling Approach

Model-2 uses a Gradient Boosting Regressor (GBR) because of:

Strong nonlinear performance

Proven accuracy on tabular housing data

Explainable feature importance

Stable results across diverse metros

Training Details
Metric	Value
Train/Test Split	80/20
MAE	≈ 46,000 USD
R²	≈ 0.87

These metrics align with typical variance in U.S. residential housing.

🏷️ Fair Price Estimation

The ML model outputs:

predicted_fair_price

🎯 Fair Price Range (95 percent interval)
fair_low_95  = predicted_fair_price × 0.75
fair_high_95 = predicted_fair_price × 1.25


This ±25 percent range:

Avoids unstable statistical CI assumptions

Fits typical appraisal spread

Works consistently across all markets

💰 Maximum Offer Price

A conservative 5 percent buffer:

max_offer_price = predicted_fair_price × 0.95


Ensures:

Avoiding overpayment

Coverage for renovation + holding costs

Margin for profitable resale

🏳️ Review Flag (AI Confidence Measure)

We quantify uncertainty:

relative_bandwidth = (fair_high_95 - fair_low_95) / predicted_fair_price

Decision Rule
Condition	Flag
relative_bandwidth > 0.50	REVIEW_REQUIRED
else	OK

OK
Model is sufficiently confident for automated pricing.

REVIEW_REQUIRED
Human validation required due to high uncertainty.

📦 Model-2 Output Columns
Column	Description
property_id	Unique property ID
city, state, zip	Geographic fields
beds, baths, sqft, lot_size, age	Structural features
closed_price	Last recorded sale
predicted_fair_price	ML estimated acquisition price
fair_low_95, fair_high_95	±25 percent interval
max_offer_price	Safe maximum bid
review_flag	OK / REVIEW_REQUIRED
CBSA, CBSA_NAME	Metro identifiers
Final_City_Score	From Model-1
🗂️ Repository Structure (Model-2 Folder)
Model_2/
│
├── Model2_AcquisitionPrice.ipynb        # Main notebook
├── Model2_AcquisitionPrices_combined.csv
├── redfin_data_raw/                     # Raw Redfin exports
├── cleaned_data/                        # Preprocessed metro datasets
└── zip_cbsa_crosswalk.csv               # ZIP → CBSA mapping

▶️ How to Run Model-2
1. Install dependencies
pip install -r requirements.txt

2. Open the notebook
Model_2/Model2_AcquisitionPrice.ipynb

3. Run all cells

The notebook will:

Load & clean Redfin data

Merge CBSA + Model-1 metrics

Train Gradient Boosting model

Generate fair price & uncertainty bands

Export final CSV

🔍 Example Output (Sample Row)
property_id:         10392
beds:                3
baths:               1.5
sqft:                1897
predicted_fair_price: 186,893
fair_low_95:          140,170
fair_high_95:         233,617
max_offer_price:      177,548
review_flag:          REVIEW_REQUIRED

🚀 Why Model-2 Is Critical

Model-2 establishes:

A consistent acquisition pricing standard

Protection against overbuying

Input for profitability modeling (Model-3)

Foundation for Buy/No-Buy logic (Model-4)

Inputs for financial statements: IS, CF, BS

It creates a repeatable scalable pricing engine for real estate investing.

📈 Future Improvements

Hyperparameter tuning using XGBoost / LightGBM

Integrate neighborhood-level crime & school quality

Add Zillow rent data for yield sensitivity

Use SHAP for deeper explainability

Deploy model as an API for real-time pricing

🏁 Conclusion

Model-2 converts raw property data into actionable acquisition price recommendations using ML and clear business rules. It is a foundational component of the full AI-driven real estate investment platform.
