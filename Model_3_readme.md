"

# Model 3 – Predicted Selling Price Model

**AI-Adjusted After-Repair Value (ARV) with Renovation, Eco, and Customer Preference Premiums**

---

## Overview

Model 3 estimates the **predicted selling price** (After-Repair Value / ARV) for shortlisted properties by adjusting Model-2’s acquisition valuation for expected renovation impact, eco-upgrades, and customer experience premiums. This model bridges the gap between “fair buy price” and “expected exit price,” enabling a realistic profitability view for downstream investment decisions in Model 4.

The model produces:

* **Estimated Renovation Tier (`Light / Medium / Heavy`)**
* **Estimated Renovation Cost**
* **Renovation Time Estimate**
* **Eco + Customer Preference Strategy Flags**
* **Total Value Uplift Percentage**
* **Predicted Selling Price (Base / Bull / Bear)**

Model 3 is designed as a **regression + scenario testing layer**, where renovation and premium assumptions can be calibrated based on contractor market data and operational efficiency.

---

## Key Objectives

* Predict a realistic **post-renovation selling price**
* Translate renovation scope into cost + timeline estimates
* Incorporate **eco-upgrade and customer preference premiums**
* Provide **multi-scenario ARV projections** (Base/Bull/Bear)
* Generate standardized outputs for **Model-4 Buy/No-Buy underwriting**

---

## Data Sources Used

### 1. Model-2 Property-Level Outputs

Provides the acquisition foundation:

* `predicted_fair_price`
* `max_offer_price`
* `fair_low_95`, `fair_high_95`
* Structural features (`beds`, `baths`, `sqft`, `lot_size`, `age`)
* Location identifiers (`city`, `state`, `zip`, `CBSA`)
* Metro signal (`Final_City_Score`)

### 2. Renovation Strategy Assumptions (MVP)

For midterm implementation, renovation cost and uplift parameters are generated using structured, explainable rules based on:

* Property age tiering
* Market strength proxy via `Final_City_Score`
* Conservative renovation cost caps to avoid unrealistic budgets

---

## Feature Engineering Pipeline

Model 3 introduces a renovation and premium adjustment layer on top of Model 2.

### Renovation Tier Assignment

Renovation tier is assigned using property age:

* `Light` → newer homes
* `Medium` → mid-age homes
* `Heavy` → older homes

A **distress-based tier bump** is optionally applied if:

* `closed_price` is significantly below `fair_low_95`

This imitates likely repair needs when the last sale indicates undervaluation due to property condition.

---

## Renovation Cost Estimation

Renovation cost is estimated using:

* **Cost per sqft ranges** by tier
* **Bath premium add-on**
* A **hard cap** as a percentage of fair value

This cap is critical for stability:

```
estimated_reno_cost ≤ predicted_fair_price × cap_pct
```

This ensures that synthetic renovation budgets remain aligned with scalable contractor operations and avoids overestimating rehab cost.

---

## Strategy Flags (Premium Drivers)

Model 3 uses strategy flags to support premium-based ARV uplift logic:

* `eco_upgrade_flag`
* `warranty_flag`
* `fast_close_flag`

These approximate buyer preference uplifts using a market strength proxy.

### Market-Driven Logic (MVP)

Higher `Final_City_Score` markets are more likely to justify:

* eco upgrades
* faster-close incentives
* higher perceived buyer convenience premiums

---

## Value Uplift Estimation

Model 3 decomposes selling price uplift into three parts:

### Renovation Uplift

Derived from reno tier using bounded ranges.

### Eco Uplift

Applied only if:

* `eco_upgrade_flag = 1`

### Customer Preference Uplift

Includes:

* warranty premium
* fast-close premium

---

## Predicted Selling Price

The estimated total uplift is applied to Model-2 fair input:

```
predicted_sell_price_base
= predicted_fair_price × (1 + total_uplift_pct)
```

Scenario outputs:

```
predicted_sell_price_bull = base × 1.04
predicted_sell_price_bear = base × 0.96
```

These scenarios support risk-adjusted exit planning.

---

## Model-3 Output Columns

| Column                      | Description                        |
| --------------------------- | ---------------------------------- |
| `reno_tier`                 | Estimated renovation category      |
| `cost_per_sqft`             | Tier-based cost intensity          |
| `estimated_reno_cost`       | Projected rehab budget             |
| `estimated_reno_days`       | Rehab duration estimate            |
| `eco_upgrade_flag`          | Eco strategy indicator             |
| `warranty_flag`             | Buyer confidence premium indicator |
| `fast_close_flag`           | Speed-to-close premium indicator   |
| `reno_uplift_pct`           | ARV uplift from renovation         |
| `eco_uplift_pct`            | ARV uplift from eco upgrades       |
| `cx_uplift_pct`             | ARV uplift from buyer experience   |
| `total_uplift_pct`          | Combined uplift                    |
| `predicted_sell_price_base` | Base ARV                           |
| `predicted_sell_price_bull` | Bull-case ARV                      |
| `predicted_sell_price_bear` | Bear-case ARV                      |


---

## How to Run Model-3

1. Ensure Model-2 output exists:

   Model2_AcquisitionPrices_combined.csv

2. Open notebook:

   Model_3/Model3_PredictedSellingPrice.ipynb

3. Run all cells.

The notebook will:

* Load Model-2 outputs
* Assign renovation tiers
* Estimate renovation cost + duration
* Generate eco/CX flags
* Compute uplift components
* Produce Base/Bull/Bear ARV
* Export Model-3 output CSV

---

## Example Output (Sample Row)

```
property_id:  10392
predicted_fair_price: 186,893
reno_tier: Medium
estimated_reno_cost: 32,500
total_uplift_pct: 0.16
predicted_sell_price_base: 216,796
predicted_sell_price_bull: 225,468
predicted_sell_price_bear: 208,124
```

---

## Why Model-3 Is Critical

Model-3 establishes:

* A consistent **ARV estimation layer**
* Translation of renovation strategy into measurable uplift
* A quantified **risk-adjusted selling range**
* A clean feed into **profit modeling and acquisition gatekeeping (Model-4)**

It converts acquisition valuation into **exit-driven investment intelligence**.

---


## Conclusion

Model-3 refines Model-2’s acquisition valuation into a realistic **post-renovation exit price**. By combining renovation cost discipline with premium-based uplift logic and scenario testing, it provides the essential ARV backbone for Model-4 investment decisioning.

"

