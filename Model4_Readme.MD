# Model 4 – Buy/No-Buy Decision Model

**Risk-Adjusted Investment Underwriting with Margin, Liquidity, Stop-Loss & Multi-Horizon Profitability**

---

## Overview

Model 4 is the final underwriting layer that determines whether a property should be acquired. The model combines acquisition price discipline from **Model 2** and ARV + renovation economics from **Model 3** to compute expected profitability under structured business rules.

Model 4 operates as an **AI + policy-based investment decision engine**.

The model produces:

* **Total Project Cost**
* **Base/Bull/Bear Profit + Margin**
* **Liquidity Check**
* **Reno Risk Flag**
* **Stop-Loss Price**
* **Buy/No-Buy/Review Decision**
* **Multi-Horizon Profit Projections**

  * **Q4 (4 months)**
  * **6 months**
  * **Year 1**
  * **Year 2**
  * **Year 3**

---

## Key Objectives

* Decide acquisition eligibility using consistent rules
* Ensure profitability is aligned to the **≥9% debt-financing hurdle**
* Incorporate **stop-loss and liquidity screening**
* Route uncertain cases to analysts
* Provide horizon-based profit sensitivity for strategy planning

---

## Data Sources Used

### 1. Model-2 Outputs

* `max_offer_price`
* `predicted_fair_price`
* `review_flag`
* `Final_City_Score`

### 2. Model-3 Outputs

* `estimated_reno_cost`
* `reno_tier`
* `predicted_sell_price_base`
* `predicted_sell_price_bull`
* `predicted_sell_price_bear`

### 3. Business & Financial Assumptions (MVP)

* buy closing cost %
* sell closing cost %
* monthly holding cost %
* tier-based holding duration
* base/bull/bear appreciation assumptions for longer-hold horizons

---

## Feature Engineering Pipeline

Model-4 builds investment economics using:

### Core Cost Stack

```
total_cost
= max_offer_price
+ estimated_reno_cost
+ buy_closing_cost
+ holding_cost
+ sell_closing_cost
```

Where:

* `holding_cost = holding_cost_monthly × expected_holding_months`

### Base Profit & Margin

```
profit_base = sell_base - total_cost
margin_base = profit_base / total_cost
```

---

## Risk Controls

### 1. Liquidity Proxy

Model-4 uses a market-quality proxy:

* `Final_City_Score`

Decision use:

* Score ≥ threshold → `OK`
* Else → `REVIEW`

This avoids heavy data dependency at MVP stage while preserving market-awareness.

### 2. Renovation Risk Flag

A cost sanity ratio protects against over-capitalized rehabs:

```
reno_to_buy_ratio = estimated_reno_cost / max_offer_price
```

If this ratio exceeds a threshold:

* validation is required

### 3. Stop-Loss Price

A downside exit floor is computed using bear-case ARV:

```
stop_loss_price = sell_bear × (1 - buffer)
```

This formalizes capital protection rules for high-risk markets.

---

## Multi-Horizon Profitability

To support strategic flexibility, Model-4 computes base-case profit and margin under:

* `Q4 (4 months)`
* `6 months`
* `Year 1`
* `Year 2`
* `Year 3`

This is calculated using appreciation-based sensitivity assumptions applied to `sell_base`.

These outputs enable:

* short-term flip viability
* medium-hold fallback strategies
* longer-hold risk awareness

---

## Decision Logic

Model-4 uses a tiered rule-based gate:

* If base margin is below a lower threshold → `NO_BUY`
* If base margin is borderline → `REVIEW_REQUIRED`
* If base margin meets the buy threshold and passes:

  * liquidity check
  * reno risk check
  * Model-2 uncertainty check
  * ≥9% hurdle requirement
    → `BUY`

This creates a disciplined AI + policy underwriting workflow.

---

## Model-4 Output Columns

| Column                                      | Description                      |
| ------------------------------------------- | -------------------------------- |
| `max_offer_price_used`                      | Final acquisition ceiling used   |
| `estimated_reno_cost_used`                  | Final renovation budget used     |
| `expected_holding_months`                   | Tier-based hold estimate         |
| `total_cost`                                | Full project cost                |
| `sell_base_used`                            | Base ARV                         |
| `sell_bear_used`                            | Downside ARV                     |
| `profit_base`, `profit_bull`, `profit_bear` | Flip profitability               |
| `margin_pct_base`, `margin_pct_bear`        | Flip margins                     |
| `meets_9pct_hurdle_flag`                    | Financing-aligned hurdle check   |
| `liquidity_flag`                            | Market liquidity proxy           |
| `reno_to_buy_ratio`                         | Reno sanity ratio                |
| `reno_risk_flag`                            | Reno risk routing                |
| `stop_loss_price`                           | Risk control exit floor          |
| `buy_decision`                              | `BUY / NO_BUY / REVIEW_REQUIRED` |
| `decision_reason`                           | Explains rule trigger            |
| `profit_base_q4_4m`                         | Q4 horizon profit                |
| `profit_base_m6`                            | 6-month horizon profit           |
| `profit_base_y1`                            | 1-year horizon profit            |
| `profit_base_y2`                            | 2-year horizon profit            |
| `profit_base_y3`                            | 3-year horizon profit            |
| `margin_base_*`                             | Horizon margins                  |

---

## Repository Structure (Model-4 Folder)

```
Model_4/
│
├── Model4_BuyNoBuy.ipynb
├── Model4_Buy_NoBuy.csv
└── inputs/
    ├── Model2_AcquisitionPrices_combined.csv
    └── Model3_SellingPrices_combined.csv
```

---

## How to Run Model-4

1. Ensure Model-3 output exists:

   Model3_SellingPrices_combined.csv

2. Open notebook:

   Model_4/Model4_BuyNoBuy.ipynb

3. Run all cells.

The notebook will:

* Load Model-3 outputs
* Build cost stack
* Compute Base/Bull/Bear profits
* Apply liquidity + reno risk checks
* Enforce ≥9% hurdle logic
* Generate multi-horizon profitability
* Export final decision CSV

---

## Example Output (Sample Row)

```
property_id: 10392
max_offer_price_used: 177,548
estimated_reno_cost_used: 32,500
total_cost: 222,100
sell_base_used: 246,000
profit_base: 23,900
margin_pct_base: 0.108
liquidity_flag: OK
buy_decision: BUY
profit_base_m6: 25,400
profit_base_y1: 29,800
```

---

## Why Model-4 Is Critical

Model-4 establishes:

* A disciplined final acquisition gate
* Transparent profitability and risk logic
* A structured AI + human review loop
* A portfolio-ready output usable for:

  * investment memos
  * underwriting dashboards
  * pro forma financial statements
  * scenario-driven expansion planning

---

## Future Improvements

* Add true liquidity signals (DOM, absorption rates)
* Integrate neighborhood-level rent + yield for hold strategies
* Replace proxy risk flags with hazard and insurance datasets
* Build a portfolio optimizer for capital allocation
* Deploy decision API to support real-time bidding

---

## Conclusion

Model-4 converts acquisition price and ARV predictions into a structured, risk-adjusted investment decision. By combining cost discipline, scenario margins, liquidity and stop-loss controls, and multi-horizon profitability, it completes the full AI-driven real estate investment pipeline.

