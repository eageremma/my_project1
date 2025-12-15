# Regional and Local Patterns of Happiness in the UK 📊







##

This repository contains a project demonstrating end‑to‑end analytical skills using real UK government data. It showcases my ability to:

- Clean and validate messy real‑world datasets
- Perform exploratory data analysis (EDA)
- Compare trends across time and geography
- Interpret statistical relationships clearly for non‑technical audiences
- Communicate insights with visualisations and structured narrative

The project is intentionally scoped as a **mini analytical report**.

---

## Project Overview

This project analyses **subjective wellbeing trends in the United Kingdom** using Office for National Statistics (ONS) data. The focus is on understanding how happiness and related wellbeing measures vary **over time**, **across regions**, and **between local authority districts**.

---

## Business & Policy Questions

Framed in a way relevant to analyst roles, this project answers:

- How has population happiness changed over time?
- Are there meaningful differences between regions or countries?
- Which local areas perform significantly above or below average?
- How strongly is happiness associated with anxiety and life satisfaction?
- Which areas show improving or declining wellbeing trends?

These questions mirror those faced in **public policy, local government, health analytics, and social research** roles.

---

## Data Source

**Office for National Statistics (ONS) – Personal Wellbeing Data**

The analysis uses a merged dataset:

`uk_wellbeing_full_dataset.csv`

This file combines four ONS wellbeing indicators:

- Life Satisfaction
- Happiness
- Worthwhile
- Anxiety

The original datasets were provided as **wide‑format Excel files** and reshaped into a **tidy, long‑format structure** suitable for analysis.

---

## Dataset Structure

Each row represents:

> *In a given area, in a given year, the average wellbeing scores were…*

**Core fields:**

- `Region` – UK country, NUTS1 region, or local authority district
- `Year` – End year of the reporting period
- `Life_satisfaction` – Average score (0–10)
- `Happiness` – Average score (0–10)
- `Worthwhile` – Average score (0–10)
- `Anxiety` – Average score (0–10, higher = worse)

---

## Tools & Skills Demonstrated

- **Excel and Python** – for data manipulation and analysis
- **pandas / NumPy** – cleaning, aggregation, reshaping
- **matplotlib** – exploratory visualisation
- **Exploratory Data Analysis (EDA)**
- **Trend analysis & correlations**
- **Data validation & assumptions checking**

---

## Data Cleaning & Preparation

Key steps included:

- Converting ONS symbols and blanks into numeric values
- Handling missing values with a targeted, minimal‑loss strategy
- Standardising region labels to avoid grouping errors
- Separating **regional** and **district‑level** views for analysis

This mirrors real‑world analyst work, where data is rarely analysis‑ready.

---

## Analysis Summary

### 1. UK Happiness Over Time

- Aggregated happiness scores by year
- Identified long‑term trends and pandemic disruption

**Insight:** UK happiness shows gradual improvement over time, with a clear dip during COVID‑19 and partial recovery afterward.

---

### 2. Regional Comparisons

- Compared UK countries and English NUTS1 regions

**Insight:** Regional differences exist but are modest, with most regions clustered between 7–8 on the happiness scale.

---

### 3. Local Authority (District) Analysis

- Ranked hundreds of districts by happiness in the latest year

**Insight:** Variation is far greater at the local level than at the regional level, highlighting the importance of place‑based analysis.

---

### 4. Happiness vs Anxiety

- Correlation and scatter analysis across all years and areas

**Insight:** Moderate negative correlation (≈ −0.5). Higher anxiety is associated with lower happiness.

---

### 5. Wellbeing Correlations

- Strong positive links between happiness, life satisfaction, and feeling worthwhile
- Anxiety consistently moves in the opposite direction

This validates the internal consistency of the dataset.

---

### 6. District Trends Over Time

- Calculated linear trends (slopes) for each district

**Insight:** Identifies areas improving or declining over time, not just current rankings — useful for monitoring and policy evaluation.

---

## How to Run the Analysis

1. Clone the repository:

```bash
git clone https://github.com/eageremma/uk-wellbeing-analysis.git
```

2. Install required packages:

```bash
pip install pandas numpy matplotlib
```

3. Open the notebook:

- Run locally using Jupyter Notebook **or**
- Upload to **Google Colab** (recommended)

4. Ensure `uk_wellbeing_full_dataset.csv` is in the correct directory

5. Run cells sequentially to reproduce all results and figures

---

## Limitations & Assumptions

- Data is **self‑reported** and subjective
- Results are **area‑level averages**, masking inequalities
- Aggregations are **not population‑weighted**
- Correlation ≠ causation
- Linear trends simplify complex real‑world dynamics

Explicitly stating these reflects good analytical practice.



