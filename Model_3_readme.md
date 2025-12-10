# Model 3 – Predicted Selling Price Estimation Model

**AI-Adjusted After-Repair Value (ARV) for Renovation, Eco, and Customer Preference Premiums**

---

## Overview

Model 3 estimates the **predicted selling price** for residential properties after planned improvements. It builds directly on Model 2’s acquisition pricing output and introduces a structured adjustment layer for **renovation scope, eco-upgrades, and customer experience premiums**. The goal is to produce a realistic, explainable **After-Repair Value (ARV)** under multiple scenarios that can be used by Model 4 for disciplined Buy/No-Buy decisions.

The model produces:

* **Predicted Selling Price (Base Case ARV)**
* **Bull and Bear Scenario Selling Prices**
* **Estimated Renovation Cost and Timeline**
* **Uplift Breakdown (Renovation + Eco + Customer Experience)**

Model 3 is the second pricing layer that directly impacts **margin projections**, **stop-loss thresholds**, and **risk-adjusted feasibility.

---

## Key Objectives

* Forecast an explainable post-renovation resale price at scale
* Quantify value creation from renovation intensity
* Capture buyer preference premiums through simple, controllable strategy flags
* Support market uncertainty using scenario testing
* Avoid heavy dependency on new third-party datasets in MVP
* Produce standardized outputs for Model 4 profitability and risk gating

---

## Data Sources Used

### 1. Model-2 Outputs (Primary Input)

Provides acquisition-side baseline plus property/metro context:

* `predicted_fair_price`
* `fair_low_95`, `fair_high_95`
* `max_offer_price`
* `review_flag`
* Structural + location attributes
* `Final_City_Score` from Model 1

### 2. Redfin Property-Level Attributes (Inherited via Model 2)

Used as renovation intensity and scale proxies:

* Beds, baths, square footage
* Lot size
* Closed price
* City, state, ZIP
* Year built → **age**

### 3. Model-1 Outputs (Inherited via Model 2)

Used as a soft demand/quality signal:

* **Final City Score**

### 4. Internal Underwriting Assumptions (MVP)

Generated programmatically for scalability:

* Renovation tier assignment
* Tier-based cost-per-sqft bands
* Eco and customer experience strategy flags
* Scenario configuration parameters

---

## Feature Engineering Pipeline

Model 3 uses an explainable adjustment framework anchored on Model 2’s fair price.

### Baseline Inputs (from Model 2)

* `property_id`
* `city`, `state`, `zip`
* `CBSA`, `CBSA_NAME`
* `beds`, `baths`, `sqft`, `lot_size`, `age`
* `closed_price`
* `predicted_fair_price`
* `fair_low_95`, `fair_high_95`
* `max_offer_price`
* `review_flag`
* `Final_City_Score`

### New Model-3 Engineered Features

**Renovation Plan Features**

* `reno_tier` (Light / Medium / Heavy)
* `cost_per_sqft` (helper)
* `estimated_reno_cost`
* `estimated_reno_days`

**Upgrade Strategy Features**

* `eco_upgrade_flag` (0/1)
* `warranty_flag` (0/1)
* `fast_close_flag` (0/1)

**Value Premium Features**

* `reno_uplift_pct`
* `eco_uplift_pct`
* `cx_uplift_pct`
* `total_uplift_pct`

**Scenario Outputs**

* `predicted_sell_price_base`
* `predicted_sell_price_bull`
* `predicted_sell_price_bear`

### Data Cleaning Steps

* Reuse Model 2’s cleaned and merged dataset
* Validate numeric fields for `sqft`, `age`, `beds`, `baths`
* Ensure price columns are numeric
* Handle missing values using conservative defaults where needed
* Apply deterministic seed-based estimation for reproducibility in MVP

---

## Modeling Approach

Model 3 is implemented as a **hybrid ARV adjustment model** in Phase 1:

* Rule-based renovation inference
* Controlled renovation cost and timeline estimation
* Structured uplift modeling
* Scenario-based resale forecasting

This approach is intentionally lightweight, explainable, and scalable across large property batches.

---

## Renovation Tier Assignment

We use property age as a stable condition proxy:

* `age ≤ 20` → **Light**
* `21–50` → **Medium**
* `> 50` → **Heavy**


---

## Renovation Cost Estimation (MVP)

Tier-based cost-per-sqft bands:

* Light: **$15–$30 / sqft**
* Medium: **$30–$60 / sqft**
* Heavy: **$60–$100 / sqft**

Computation:

```
estimated_reno_cost = sqft * cost_per_sqft
```


---

## Renovation Timeline Estimation (MVP)

Tier-based duration bands:

* Light: **14–30 days**
* Medium: **30–60 days**
* Heavy: **60–120 days**

---

## Value Uplift Computation

Model 3 applies structured state-of-plan premiums:

**Renovation uplift ranges**

* Light: 4%–8%
* Medium: 8%–15%
* Heavy: 15%–25%

**Eco uplift**

* If `eco_upgrade_flag = 1` → +1%–3%
* Else → 0%

**Customer experience (CX) uplift**

* Warranty premium: +0.5%–1.5%
* Fast-close premium: +0.5%–1.0%

Total uplift definition:

```
total_uplift_pct = reno_uplift_pct + eco_uplift_pct + cx_uplift_pct
```

---

## Predicted Selling Price (Base Case ARV)

Model 3 anchors resale value to Model 2’s fair price:

```
predicted_sell_price_base = predicted_fair_price * (1 + total_uplift_pct)
```

This ensures resale forecasting is directly tied to disciplined acquisition valuation.

---

## Scenario Testing

To reflect market uncertainty, Model 3 generates three cases:

```
predicted_sell_price_bull = predicted_sell_price_base * 1.04
predicted_sell_price_bear = predicted_sell_price_base * 0.96
```

Scenario parameters are configurable and can be tuned later based on historical performance.

---

## Model-3 Output Columns

| Column                                     | Description                   |
| ------------------------------------------ | ----------------------------- |
| `property_id`                              | Unique ID                     |
| `city`, `state`, `zip`                     | Location fields               |
| `beds`, `baths`, `sqft`, `lot_size`, `age` | Structural features           |
| `closed_price`                             | Last recorded sale            |
| `predicted_fair_price`                     | Model 2 acquisition baseline  |
| `fair_low_95`, `fair_high_95`              | Model 2 uncertainty band      |
| `max_offer_price`                          | Model 2 safe maximum bid      |
| `review_flag`                              | Model 2 confidence flag       |
| `CBSA`, `CBSA_NAME`                        | Metro identifiers             |
| `Final_City_Score`                         | Score from Model 1            |
| `reno_tier`                                | Light / Medium / Heavy        |
| `cost_per_sqft`                            | Tier-based helper estimate    |
| `estimated_reno_cost`                      | Estimated renovation budget   |
| `estimated_reno_days`                      | Estimated renovation duration |
| `eco_upgrade_flag`                         | Eco upgrade inclusion         |
| `warranty_flag`                            | Warranty inclusion            |
| `fast_close_flag`                          | Fast-close positioning        |
| `reno_uplift_pct`                          | Renovation value premium      |
| `eco_uplift_pct`                           | Eco value premium             |
| `cx_uplift_pct`                            | Customer experience premium   |
| `total_uplift_pct`                         | Total premium applied         |
| `predicted_sell_price_base`                | Base-case ARV                 |
| `predicted_sell_price_bull`                | Upside-case ARV               |
| `predicted_sell_price_bear`                | Downside-case ARV             |

---

## How to Run Model-3

1. Install dependencies:

   pip install -r requirements.txt

2. Open the notebook:

   Model_3/Model3_PredictedSellingPrice.ipynb

3. Run all cells.

The notebook will:

* Load Model 2 output
* Assign renovation tiers
* Estimate renovation costs and timelines
* Apply eco + CX premiums
* Generate base/bull/bear selling prices
* Export Model 3 CSV

---

## Example Output (Sample Row)

```
property_id:              10392
beds:                     3
baths:                    1.5
sqft:                     1897
age:                      42
predicted_fair_price:     186,893
reno_tier:                Medium
estimated_reno_cost:      ~75,000–85,000 (MVP range)
total_uplift_pct:         ~0.10–0.14
predicted_sell_price_base: ~205,000–213,000
predicted_sell_price_bull: ~213,000–221,000
predicted_sell_price_bear: ~197,000–205,000
```

---

## Why Model-3 Is Critical

Model 3 establishes:

* A standardized, scalable ARV estimation layer
* A structured way to monetize planned improvements
* Scenario-aware safeguards before capital deployment
* A clean feed into Model 4’s margin, liquidity, and stop-loss logic

It ensures resale forecasts reflect **intentional value creation**, not only market averages.


---

## Conclusion

Model 3 transforms Model 2’s disciplined acquisition estimates into realistic, scenario-aware resale forecasts by explicitly modeling renovation impact, eco-upgrade premiums, and customer experience uplift. Its lean, explainable architecture enables scalable underwriting today and creates a clear path toward a fully trained ARV model once post-renovation sale outcomes are available.
