# 🌍 Ireland ESG Emissions Tracker

## Overview
An end-to-end ESG data analysis project 
tracking Ireland's carbon emissions and 
renewable energy progress against the 
legally binding 2030 Climate Action Plan targets.

Built as part of an ESG Analyst portfolio to 
demonstrate skills in data engineering, 
SQL analysis, machine learning and 
interactive dashboard development.

---

## 🎯 Project Objectives
- Track Ireland's GHG emissions from 
  1990 to 2024 across all sectors
- Analyse renewable energy growth 
  and progress toward 2030 targets
- Identify Ireland's key emissions 
  challenges using data-driven insights
- Forecast emissions and renewable % 
  to 2030 using machine learning
- Visualise findings in an interactive 
  Power BI dashboard

---

## 🔑 Key Findings

### Finding 1: Ireland Is Off Track For 2030
```
Current emissions (2024): 53.75 Mt CO₂eq
2030 Target:              30.13 Mt CO₂eq
Gap remaining:            23.62 Mt CO₂eq
Required pace:            3.5x faster than current
```

### Finding 2: Agriculture - Ireland's Unique Challenge
```
Agriculture share of emissions: 37.97% (2024)
EU average agriculture share:   ~10%
Ireland is 4x the EU average!

Agricultural emissions in 2024 (20.41 Mt)
virtually identical to 1990 (20.57 Mt)
= Zero meaningful progress in 34 years!
```

### Finding 3: Renewable Energy Growing But Insufficient
```
Renewable generation growth 2005-2024: +387%
Current renewable % of total energy:    14.6%
2030 Target:                            43.0%
Gap:                                   -28.4%
Growing 6.7x too slowly to hit target!
```

### Finding 4: Data Centres Creating New Challenge
```
Services sector energy consumption: +51% (2005-2024)
Driven by Big Tech data centres hosted in Ireland
Ireland uses more electricity on data centres
than on all urban homes combined!
```

### Finding 5: Post-COVID Fragility
```
2020: Emissions fell to 57.52 Mt (COVID effect)
2021: Emissions jumped to 60.10 Mt (rebound)
One year wiped out two years of progress!
Shows need for structural not behavioural change
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| Python 3.x | Data cleaning, EDA, ML forecasting |
| PostgreSQL | Data storage and SQL analysis |
| Power BI Desktop | Interactive dashboard |
| pandas | Data manipulation |
| scikit-learn | Machine learning models |
| sqlalchemy | Python-PostgreSQL connection |

---

## 📁 Project Structure

```
ireland_esg_tracker/
│
├── 📂 data/
│   ├── raw/
│   │   ├── epa_ghg_emissions.xlsx
│   │   ├── seai_renewable_energy.xlsx
│   │   └── seai_energy_balance.xlsx
│   └── processed/
│       ├── ghg_detail_clean.csv
│       ├── ghg_summary_clean.csv
│       ├── renewable_energy_clean.csv
│       ├── energy_balance_clean.csv
│       └── energy_overview_clean.csv
│
├── 📂 python/
│   ├── 01_data_cleaning.py
│   ├── 02_load_seai_data.py
│   └── 04_ml_forecasting.py
│
├── 📂 sql/
│   └── queries.sql
│
├── 📂 powerbi/
│   └── esg_dashboard.pbix
│
└── README.md
```

---

## 📊 Data Sources

| Dataset | Source | Years | Description |
|---------|--------|-------|-------------|
| GHG Emissions by Sector | EPA Ireland | 1990-2024 | CO₂, CH₄, N₂O by sector |
| Renewable Energy Generation | SEAI Ireland | 2005-2024 | Total renewable generation ktoe |
| Energy Balance by Sector | SEAI Ireland | 2005-2024 | Final energy consumption Mtoe |

### Data Links:
- **EPA Ireland:** https://www.epa.ie/our-services/monitoring--assessment/climate-change/ghg/
- **SEAI Statistics:** https://www.seai.ie/data-and-insights/seai-statistics/key-statistics/

---

## 🗄️ Database Schema

```sql
-- 8 tables in PostgreSQL database: ireland_esg_db

ghg_emissions          -- 6,160 rows: sector emissions by gas type
ghg_national_totals    -- 280 rows:   national total emissions
renewable_energy       -- 20 rows:    renewable generation 2005-2024
energy_balance         -- 100 rows:   energy consumption by sector
energy_overview        -- 20 rows:    combined energy overview
ml_emissions_forecast  -- 6 rows:     ML forecast 2025-2030
ml_renewable_forecast  -- 6 rows:     ML renewable forecast 2025-2030
powerbi_master         -- 41 rows:    combined table for Power BI
```

---

## 🔍 SQL Analysis

Six analytical queries were developed:

```sql
-- Example: 2030 Target Gap Analysis
WITH baseline AS (
    SELECT value_mt_co2eq AS baseline_2018
    FROM   ghg_national_totals
    WHERE  sector   = 'National Total'
    AND    gas_type = 'GHG'
    AND    year     = 2018
)
SELECT
    g.year,
    ROUND(g.value_mt_co2eq::numeric, 2) AS actual_mt,
    ROUND((b.baseline_2018 * 0.49)::numeric, 2) AS target_2030_mt,
    ROUND((g.value_mt_co2eq - 
          (b.baseline_2018 * 0.49))::numeric, 2) AS gap_to_target_mt
FROM  ghg_national_totals g
CROSS JOIN baseline b
WHERE g.sector   = 'National Total'
AND   g.gas_type = 'GHG'
AND   g.year    >= 2018
ORDER BY g.year;
```

---

## 🤖 Machine Learning

### Models Built:
Two forecasting models predicting to 2030:

**Model 1: Emissions Forecast**
| Metric | Value |
|--------|-------|
| Algorithm | Polynomial Regression (degree 2) |
| Training period | 2010-2020 |
| Test period | 2021-2024 |
| R² Score | 0.018 |
| MAE | 2.08 Mt per year |
| 2030 Forecast | 52.49 Mt |
| 2030 Target | 30.13 Mt |
| Status | 🔴 Will Miss Target |

**Model 2: Renewable Energy Forecast**
| Metric | Value |
|--------|-------|
| Algorithm | Linear Regression |
| Training period | 2005-2020 |
| Test period | 2021-2024 |
| R² Score | 0.627 |
| MAE | 0.26% per year |
| 2030 Forecast | 19.39% |
| 2030 Target | 43.00% |
| Status | 🔴 Will Miss Target |

### Note on Model Performance:
```
With only 15-35 years of annual data,
these models provide directional forecasts
with acknowledged uncertainty (±8% bands).
The emissions R² of 0.018 reflects the 
genuine difficulty of predicting emissions
which are affected by economic cycles,
weather patterns and policy changes.
The renewable model (R²=0.627) performs
considerably better on its test period.
```

---

## 📈 Power BI Dashboard

Five page interactive dashboard:

| Page | Content |
|------|---------|
| 1. Executive Summary | KPI cards, emissions trend 1990-2030 |
| 2. Emissions Analysis | Sector breakdown, gas types, slicer |
| 3. Agriculture | Ireland's unique challenge deep dive |
| 4. Renewable Energy | Growth trends, sector consumption |
| 5. 2030 Target Tracker | ML forecast vs targets |

---

## 💡 ESG Implications

```
This analysis reveals three structural 
challenges Ireland must address:

1. AGRICULTURE REFORM
   Ireland cannot meet 2030 targets 
   without addressing agricultural emissions
   (37.97% of total, zero progress since 1990)
   This requires politically difficult decisions
   around beef and dairy herd sizes

2. RENEWABLE ACCELERATION  
   Current renewable growth rate is 6.7x 
   too slow to hit 43% target by 2030
   Massive offshore wind investment needed

3. DEMAND MANAGEMENT
   Data centre growth (+51% Services energy)
   threatens to offset gains elsewhere
   Regulatory framework needed for 
   Big Tech energy consumption
```

---

## ⚙️ How To Run This Project

### Prerequisites:
```
Python 3.8+
PostgreSQL 14+
Power BI Desktop
```

### Installation:
```bash
pip install pandas numpy matplotlib seaborn 
            sqlalchemy psycopg2-binary 
            scikit-learn openpyxl plotly
```

### Setup:
```
1. Create PostgreSQL database:
   CREATE DATABASE ireland_esg_db;

2. Download raw data files from 
   sources listed above

3. Run Python scripts in order:
   python 01_data_cleaning.py
   python 02_load_seai_data.py
   python 04_ml_forecasting.py

4. Open Power BI dashboard:
   powerbi/esg_dashboard.pbix
   Update data source to your 
   PostgreSQL credentials
```

---

## 👤 Author
```
Sajan Yerra
ESG Data Analyst
LinkedIn: (https://www.linkedin.com/in/sajanyerra/)
```

---

## 📌 Acknowledgements
```
Data provided by:
→ Environmental Protection Agency Ireland
→ Sustainable Energy Authority of Ireland
→ Ireland Climate Action Plan 2023 (Gov.ie)
```
