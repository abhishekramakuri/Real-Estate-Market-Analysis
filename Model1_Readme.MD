#  Model 1: City Selection Model  
*A Data-Driven Framework for Identifying High-Potential U.S. Metro Areas*

This model ranks all U.S. metropolitan statistical areas (CBSAs) based on economic fundamentals, housing market strength, migration dynamics, climate risk, and affordability. The final output is a **composite score (0–1)** indicating overall market attractiveness for real estate investment and long-term stability.

---

#  Datasets Used

We merged **five datasets** using `CBSA` as the key:

### 1. `metro_master_dataset.csv`
- Housing growth (1yr, 3yr, 5yr)  
- Momentum (12m, 3m)  
- Housing volatility  
- Migration inflows/outflows  
- Climate risk scores (RISK, EAL, SOVI)  
- Community resilience (RESL)

### 2. `metro-annual-unemployment-rates.xlsx`
- `Unemployment_rate_2024`

### 3. `GDP_Metros_Cleaned.xlsx`
- `GDP_2023`

### 4. `Income_per_person_metros_Cleaned.xlsx`
- Per capita income  
- Total personal income  
- Metro population

### 5. `RPP_Metro_Cleaned.xlsx`
- Cost-of-living index (`RPP_2023`)

Final merged dataset: **377 metros × 41 columns**

---

#  Data Cleaning & Standardization

- Standardized column names  
- Fixed CBSA formatting inconsistencies  
- Preserved missing values for:
  - GDP (9 metros)  
  - RPP (9 metros)  
  - Income metrics (9 metros)  
  - Climate risk scores (4 metros)
- Demographics removed (not used in Model 1)

---

#  Feature Normalization

All features were scaled using 0–1 normalization:

- `normalize()` → higher is better  
- `normalize_inverted()` → lower is better  

### **Positive-effect variables** (higher = better)
- GDP  
- Per capita income  
- Household income (median/mean)  
- Housing growth  
- Migration inflow  
- Net migration  
- Resilience score  

### **Negative-effect variables** (higher = worse → inverted)
- Unemployment rate  
- Housing volatility  
- Migration outflows  
- Climate risk (RISK, EAL, SOVI)  
- Cost of living index (RPP)

---

#  Model Dimensions

## 1️. Economic Strength (25%)
- `Unemployment_rate_2024_norm` *(inverted)*  
- `GDP_2023_norm`  
- `PerCapitaIncome_norm`  
- `PersonalIncome_norm`  
- `Population_norm`  
- `MedianHHIncome_norm`  
- `MeanHHIncome_norm`

---

## 2️. Housing Growth & Stability (25%)
Growth metrics:  
- `growth_1yr_norm`  
- `growth_3yr_norm`  
- `growth_5yr_norm`  
- `mom12_norm`  
- `mom3_norm`

Stability (inverted):  
- `vol_full_norm`  
- `vol12_norm`

---

## 3️. Migration Dynamics (20%)
Positive:  
- `inflow_returns_norm`  
- `inflow_pop_norm`  
- `net_returns_norm`  
- `net_pop_norm`  
- `inflow_per_1k_norm`  
- `net_per_1k_norm`

Negative (inverted):  
- `outflow_returns_norm`  
- `outflow_pop_norm`  
- `outflow_per_1k_norm`

---

## 4️. Climate Risk & Resilience (15%)
Risk (inverted):  
- `RISK_SCORE_norm`  
- `EAL_SCORE_norm`  
- `SOVI_SCORE_norm`

Resilience:  
- `RESL_SCORE_norm`

---

## 5️. Affordability (15%)
- `RPP_norm` *(inverted → lower cost = higher score)*

---

#  Final Composite Score

We applied **industry-standard weights**:

| Dimension | Weight |
|----------|--------|
| Economic Strength | **25%** |
| Housing Growth & Stability | **25%** |
| Migration Dynamics | **20%** |
| Climate Risk & Resilience | **15%** |
| Affordability | **15%** |

## Final Score Formula:

Final_City_Score =
ES_score * 0.25 +
HG_score * 0.25 +
MG_score * 0.20 +
CL_score * 0.15 +
AF_score * 0.15


Higher score → better metro.

Observed range: **0.35 → 0.61**

---

#  Model Outputs

### **1. Full normalized dataset**
Includes all raw variables + normalized features + dimension scores  
→ `FinalCityScores.xlsx`

### **2. Scores-only dataset**
Contains:
- ES_score  
- HG_score  
- MG_score  
- CL_score  
- AF_score  
- Final_City_Score  
→ `CityScores_Only.xlsx`

### **3. Sortable ranking**
Higher `Final_City_Score` = stronger market.

---

#  Conclusion

Model 1 integrates multi-source metro-level data to create a ranked, weighted scoring system that identifies the strongest U.S. metros based on:

- Economic fundamentals  
- Housing market strength  
- Migration momentum  
- Climate-adjusted risk  
- Cost of living  

This model forms the **foundation** for:
- Model 2 (Acquisition Price)  
- Model 3 (Predicted Selling Price)  
- Model 4 (Buy/No-Buy Decision)

---
