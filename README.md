# The Dark Convergence — Organised Crime and Cyber Threats in Europe (2010–2022)

> *Across Europe, digital networks and criminal networks are growing together. This is not a coincidence.*

---

## Table of Contents

1. [The Problem](#1-the-problem)
2. [Data Description](#2-data-description)
3. [Analysis Methods](#3-analysis-methods)
4. [Research Questions & Findings](#4-research-questions--findings)
5. [Tableau Dashboard](#5-tableau-dashboard)
6. [Conclusions & Recommendations](#6-conclusions--recommendations)
7. [Project Structure](#7-project-structure)
8. [How to Reproduce](#8-how-to-reproduce)

---

## 1. The Problem

Between 2010 and 2022, reported cybercrime across Europe grew without interruption. At the same time, organised crime — fraud, drug trafficking, money laundering, corruption — was also expanding in both volume and complexity.

This project asks a simple but consequential question: **are these two trends connected, or are they independent?**

If they are independent, policymakers can address them separately. If they are not — if the same criminal ecosystems that sustain organised crime also fuel digital threats — then treating them in silos is a strategic mistake.

Using official Eurostat data across 34 European countries over 12 years, this study tests that connection rigorously. The findings support a clear conclusion: **the convergence is real, structural, and accelerating.**

---

## 2. Data Description

### 2.1 Sources

| Dataset | Source | Original Size | Coverage |
|---|---|---|---|
| Organised Crime Statistics | Eurostat — Criminal Justice Statistics | 14,272 rows × 7 columns (long format) | 34 EU+EEA countries, 2010–2022 |
| Cyber Incident Rate | Eurostat — Digital Economy & Society Survey | 6,028 rows × 6 columns | 34 EU+EEA countries, 2010–2022 |

After merging and cleaning, the working dataset contains **409 country-year observations across 30 variables**.

### 2.2 Key Characteristics

**Organised Crime dataset (`crime_cleaned.csv`):**
- 24 crime categories: Fraud, Drug offences, Money Laundering, Bribery, Corruption, Sexual exploitation, Acts against computer systems, Intentional homicide, Theft, Assault, Rape, Robbery, Kidnapping, and others
- Unit: absolute count of recorded offences per country per year
- Restructured from wide to long format during cleaning

**Cyber Incident Rate dataset (`cyber_cleaned.csv`):**
- Unit: percentage of enterprises that experienced ICT security incidents in a given year
- Represented as `cyber_incident_pct_mean` — rolling mean of annual cyber incident rate per country
- Reflects digital exposure across the business sector, not individual users

**Merged dataset (`eu_crime_cyber_merged.csv`):**
- Shape: 409 rows × 30 columns
- Primary key: `country_code` + `year`

### 2.3 Missing Data

Missingness varies significantly across crime categories and reflects genuine reporting gaps across national statistical systems, not data collection errors:

| Crime Category | Missing (%) |
|---|---|
| Trade/possession of protected species | 85.6% |
| Acts involving waste movement/dumping | 82.6% |
| Environmental crime | 80.0% |
| Participation in organised criminal group | 61.9% |
| Acts against computer system | 59.4% |
| Sexual exploitation | 56.0% |
| Bribery / Money Laundering / Fraud | 49–53% |
| Intentional homicide / Theft | 1–2% |

High missingness in financial and cyber categories reflects a known limitation in cross-national crime reporting harmonisation. Robust imputation (column median) was applied where needed for clustering analysis.

### 2.4 EDA Highlights (Notebook 2)

Exploratory analysis confirmed several structural patterns before hypothesis testing:

- **Cyber incident rate is right-skewed** at the country level, with a cluster of Western and Northern European countries already above 90% by 2015
- **Country data coverage is uneven**: most EU15 countries have 13 years of data; Balkan and candidate countries have 4–10 years
- **Top correlates with cyber incident rate** (from the correlation matrix): Acts against computer system (r = 0.38), Money Laundering (r = 0.28), Drug offences (r = 0.26), Fraud (r = 0.26) — all organised crime categories
- **EU-wide cyber trend** is monotonically increasing from 2010 to 2022 with no reversal year

---

## 3. Analysis Methods

### 3.1 Tools & Libraries

| Tool / Library | Purpose |
|---|---|
| **Python / Pandas** | Data loading, cleaning, merging, reshaping |
| **NumPy** | Numerical operations, growth rate calculations |
| **SciPy (stats)** | OLS regression, independent t-tests, Pearson & Spearman correlation |
| **scikit-learn** | KMeans clustering, PCA dimensionality reduction, StandardScaler normalisation |
| **Matplotlib / Seaborn** | Static publication-quality charts (slope charts, bar charts, scatter plots, box plots, heatmaps) |
| **Plotly Express** | Interactive HTML visualisations (animated scatter, choropleth, clustering) |
| **Tableau Public** | Interactive stakeholder dashboard |
| **Google Colab** | Cloud notebook execution environment |

### 3.2 Method Details

**Trend Detection (Q2):** OLS linear regression of EU-average cyber rate against year. Confirmed with year-over-year change table.

**Correlation Analysis (Q3):** Both Pearson (parametric) and Spearman (non-parametric) correlation between organised crime rate and cyber incident rate, with p-values reported.

**COVID-19 Impact (Q4):** Independent samples t-test comparing pre-COVID era (2016–2019) vs. COVID era (2020–2021), with Cohen's d effect size.

**Regional Comparison (Q6):** Independent samples t-test comparing Nordic (FI, SE, DK, NO, IS) vs. Southern European (IT, ES, PT, EL, MT, CY) cyber rates.

**Economic Scale Proxy (Q7):** In the absence of GDP or internet penetration data in the merged dataset, per-capita total crime count was used as a rough proxy for country scale and activity level. This is an acknowledged limitation — per-capita crime is an imperfect stand-in for economic size, as it captures criminal activity rather than economic output. Results should be interpreted directionally. Future work incorporating GDP per capita or the Digital Economy and Society Index (DESI) scores would substantially strengthen this analysis.

**Clustering (Q8):** K-Means clustering (K=3, selected via elbow method) on standardised country-level crime + cyber profiles. Visualised with PCA (2 components, 65.5% variance explained).

---

## 4. Research Questions & Findings

### Q1 — Country Rankings: Who Is Most Exposed?

**Hypothesis:** Nordic and Western European countries will show the highest cyber incident rates; Eastern and Balkan countries the lowest.

**Finding: CONFIRMED**

| Metric | Country | Value |
|---|---|---|
| Highest cyber rate (2022) | Norway | 99.3% |
| Lowest cyber rate (2022) | Croatia | 87.1% |
| Largest increase (2010→2022) | Turkey | +54.8 ppts |
| Largest decrease | None | — |

Every country in the dataset increased its cyber incident rate over the study period. No country became less exposed. Turkey's dramatic +54.8 ppt jump reflects rapid digital economy growth from a low base. The ranking is broadly stable at the top — Iceland, Norway, Luxembourg, Netherlands, and Finland consistently lead — but the gap between highest and lowest is narrowing as lower-ranked countries catch up.

---

### Q2 — Is the Rise in Cybercrime Structural or Coincidental?

**Hypothesis:** There is a statistically significant upward trend in cyber incident rates across Europe.

**Finding: CONFIRMED**

| Statistic | Value |
|---|---|
| OLS Slope | +1.948% per year |
| R² | 0.977 |
| p-value | < 0.00001 |

The EU-average cyber incident rate rose from ~68.8% in 2010 to ~93.3% in 2022 — an increase of ~24.5 percentage points. An R² of 0.977 means the linear trend explains 97.7% of the year-to-year variation. This is not noise. The largest single-year jump was 2010→2011 (+5.3 ppts), with renewed acceleration in 2020–2021.

---

### Q3 — Does Organised Crime Drive Cyber Risk?

**Hypothesis:** Countries with higher organised crime rates will also show higher cyber incident rates.

**Finding: CONFIRMED**

| Statistic | Value |
|---|---|
| Pearson r | 0.277 (p < 0.0001) |
| Spearman ρ | 0.466 (p < 0.0001) |

A significant positive correlation exists at the country level. The stronger Spearman coefficient suggests the relationship is monotonic but non-linear — consistent with the idea that organised crime creates enabling infrastructure and criminal capacity that spills over into digital threats, rather than a simple proportional effect.

---

### Q4 — Did COVID-19 Change the Trajectory?

**Hypothesis:** The COVID-19 period (2020–2021) caused a measurable spike in cybercrime above the pre-existing trend.

**Finding: CONFIRMED — Large Effect**

| Metric | Value |
|---|---|
| Pre-COVID mean (2016–2019) | 86.60% |
| COVID-era mean (2020–2021) | 91.81% |
| Difference | +5.22 percentage points |
| t-statistic | −5.046 |
| p-value | < 0.0001 |
| Cohen's d | 0.810 (Large) |

The pandemic accelerated a trend that was already underway. The mass shift to remote work, the expansion of online activity, and disrupted law enforcement capacity created conditions that criminal actors exploited rapidly. Post-COVID rates have not reverted — the shift appears permanent.

---

### Q5 — Which Crime Categories Grew Fastest?

**Hypothesis:** Financial and digital crimes will grow faster than traditional physical crimes.

**Finding: CONFIRMED**

Analysis of percentage change in crime category totals from 2010 to 2022 shows that financial crimes (Fraud, Money Laundering, Corruption) and exploitation crimes showed the steepest upward trajectories. Traditional physical crimes (Theft, Burglary, Assault) showed flat or declining shares of total recorded offences. The European crime landscape is shifting structurally away from the physical toward the financial and digital.

---

### Q6 — Do Nordic and Southern European Countries Face Different Risks?

**Hypothesis:** Nordic countries will show significantly higher cyber incident rates than Southern European countries, reflecting their more advanced digital economies.

**Finding: CONFIRMED — Highly Significant**

| Group | Average Cyber Rate |
|---|---|
| Nordic (FI, SE, DK, NO, IS) | 94.96% |
| Southern EU (IT, ES, PT, EL, MT, CY) | 78.40% |
| Difference | ~16.6 percentage points |
| t-statistic | 10.800 |
| p-value | < 0.0001 |

Nordic countries are among the world's most digitised economies, which translates directly into greater attack surface. The 16-ppt gap is large and statistically unambiguous. Southern European countries are catching up but remain structurally less exposed — though it is worth noting that lower incident rates may also partly reflect less mature national reporting systems rather than genuinely lower attack volumes.

---

### Q7 — Does Country Scale Amplify Cyber Exposure?

**Hypothesis:** Larger, more economically active countries will show higher cyber exposure.

**Finding: PARTIALLY CONFIRMED — with caveats**

The animated scatter plot shows a moderate positive relationship between per-capita crime volume (used as a scale proxy — see methodology note in Section 3.2) and cyber incident rate. Large, high-activity economies (Germany, Denmark, Belgium) cluster in the high-volume/high-cyber quadrant. However, several smaller countries (Iceland, Luxembourg, Norway) show disproportionately high cyber rates relative to their crime volumes, indicating that digital maturity is an independent driver of exposure beyond country scale alone.

> **Research gap:** This analysis would benefit significantly from incorporating GDP per capita or DESI scores as proper economic and digital development proxies. The per-capita crime proxy used here is a methodological workaround and should not be over-interpreted.

---

### Q8 — Are There Natural Country Groupings by Risk Profile?

**Hypothesis:** Countries can be grouped into meaningful clusters reflecting distinct organised crime and cyber risk profiles.

**Finding: CONFIRMED — 3 Clusters Identified**

K-Means clustering (K=3, confirmed by elbow method) on standardised country profiles, visualised via PCA (PC1: 52.2% variance, PC2: 13.3% variance, total: 65.5%):

| Cluster | Profile | Countries |
|---|---|---|
| **Cluster 0** | Moderate cyber exposure, lower crime volume | Austria, Bulgaria, Czech Republic, Finland, Greece, Croatia, Hungary, Ireland, Lithuania, Latvia, Malta, Montenegro, North Macedonia, Norway, Poland, Portugal, Romania, Serbia, Slovenia, Slovakia, Cyprus, Estonia, Luxembourg |
| **Cluster 1** | High cyber rate, high total crime volume | Belgium, Spain, Italy, Netherlands, Sweden |
| **Cluster 2** | Very high cyber rate, very high crime volume — large advanced economies | Germany, France |

Cluster 2 (Germany, France) represents the highest compound risk: large populations, major financial centres, deep digital penetration, and the crime volumes that come with complex economies. Cluster 1 covers highly digitised mid-to-large countries. Cluster 0 is the most heterogeneous group, spanning smaller and lower-income countries with diverse crime profiles.

---

## 5. Tableau Dashboard

**Interactive Dashboard:** [The Dark Convergence — Tableau Public](https://public.tableau.com/app/profile/v.v2121/viz/DarkConvergence/DarkConvergence?publish=yes)

The dashboard contains three analytical panels:

| Panel | Description |
|---|---|
| **Cyber Rate by Country & Year** (choropleth map) | Animated map showing cyber incident rate by country; use the year slider (2010–2022) to watch the West-to-East gradient intensify over time |
| **Crime Category Composition by Country** (stacked bar) | Horizontal stacked bars showing the percentage share of each crime category per country, enabling direct cross-country crime profile comparison |
| **Cyber vs Organised Crime EU Trend** (dual-axis line chart) | Blue line = EU-average cyber incident rate (left axis, %); Red line = total organised crime volume (right axis, absolute count). Both series rise in parallel, particularly after 2015 — but note that the two axes use different scales and units. The visual parallelism illustrates the co-movement of the two trends rather than a direct equivalence in magnitude |

**Central visual insight:** The dual-axis trend chart is the most striking output of the project. Organised crime volume and cyber incident rates move together across the same 12-year window — diverging only in the final years as cyber rates approach a ceiling near 95–99%, while organised crime volume continues to climb. This is the "dark convergence" the project is named after.

---

## 6. Conclusions & Recommendations

### 6.1 Key Insights

**1. Digital attacks in Europe are not random events — they are part of a broader criminal pattern.** Countries where organised crime is more entrenched consistently face higher rates of cyber incidents. This is not explained by chance. The criminal infrastructure that sustains fraud networks, drug trafficking, and money laundering also enables — and increasingly overlaps with — digital attack capacity.

**2. The growth in cybercrime is steady and predictable, not episodic.** Year after year, without exception, the share of European businesses reporting cyber incidents has risen. The trend has continued through recessions, political disruptions, and even post-pandemic slowdowns. It will not reverse on its own.

**3. The pandemic permanently raised the baseline.** COVID-19 produced the sharpest acceleration in the data — pushing the European average up by over five percentage points in two years. Post-pandemic rates have not fallen back. The shift to digital activity that happened under lockdown became permanent, and so did the attack surface it created.

**4. The most digitised countries are the most targeted.** Nordic countries face a cyber incident rate roughly 17 percentage points higher than Southern European countries. Greater digital adoption means greater exposure. As Southern and Eastern Europe continue to digitise, their exposure will converge upward — not downward.

**5. Financial crime is displacing street crime.** Fraud, money laundering, and corruption are growing as a share of total recorded offences, while physical crimes are declining in relative terms. European law enforcement is still largely structured around the latter. This structural mismatch between where crime is growing and where enforcement resources are concentrated is one of the most consequential findings of this study.

**6. A small number of large countries concentrate the highest compound risk.** Germany and France combine the highest crime volumes with near-saturated cyber exposure. These countries are not just the largest economies in Europe — they are also the most complex criminal environments, and the most digitally integrated. The intersection of those two facts creates a risk profile unlike any other country in the dataset.

### 6.2 Recommendations

**For policymakers:**
- Treat cyber security and organised crime policy as a single domain, not two separate portfolios. The data no longer supports a siloed approach
- Prioritise cross-border cooperation frameworks specifically targeting countries in Cluster 1 (Belgium, Spain, Italy, Netherlands, Sweden) — they combine high digital exposure with significant organised crime pressure and are the most likely vectors for transnational incidents
- Plan proactively for the next major disruption: the COVID period demonstrated how quickly criminal actors can exploit societal shifts. Resilience planning should assume, not react to, rapid escalation

**For law enforcement agencies:**
- Redirect investigative resource allocation toward financial and digital crime categories, which now represent the fastest-growing share of total offences across Europe
- Invest in national reporting harmonisation in Eastern and Balkan countries — lower recorded incident rates in these regions may reflect reporting capacity gaps rather than genuinely lower exposure

**For future research:**
- Incorporate GDP per capita and DESI (Digital Economy and Society Index) scores as proper control variables to isolate the organised crime → cybercrime pathway more cleanly
- Investigate whether the Nordic–Southern gap is partly a reporting artefact, and whether harmonisation of national incident definitions would narrow or widen it
- Extend the dataset beyond 2022 to assess whether the post-COVID plateau in cyber rates reflects genuine stabilisation or a measurement ceiling effect

---

## 7. Project Structure

```
.
├── 01_data_loading_and_merging.ipynb      # Data ingestion, cleaning, merging
├── 02_exploratory_data_analysis.ipynb     # EDA: distributions, trends, correlations, coverage
├── 03_analysis_and_answers.ipynb          # Hypothesis testing (Q1–Q8)
│
├── eu_crime_cyber_merged.csv              # Main merged dataset (409 × 30)
├── crime_cleaned.csv                      # Long-format cleaned crime data (14,272 × 7)
├── cyber_cleaned.csv                      # Cleaned cyber incident data (6,028 × 6)
├── descriptive_stats.csv                  # Full descriptive statistics table (from EDA)
├── agg1_cyber_rate_country_year.csv       # Cyber rate aggregated by country-year
├── agg2_crime_by_category_year.csv        # Crime totals by category and year
├── agg3_country_summary.csv              # Country-level summary statistics
├── q1_country_rankings.csv               # Q1: rankings 2010 vs 2022
├── q8_country_clusters.csv               # Q8: cluster assignments per country
├── crime_growth_analysis.csv             # Q5: growth rates by crime category
│
├── fig_missing_data.png                   # Missing values heatmap (EDA)
├── fig_coverage.png                       # Country data coverage bar chart (EDA)
├── fig_cyber_distribution.png             # Cyber rate distribution (EDA)
├── fig_top_cyber_countries.png            # Top 10 countries by avg cyber rate (EDA)
├── fig_crime_categories.png               # Crime category totals bar chart (EDA)
├── fig_eu_cyber_trend.png                 # EU-wide cyber trend with confidence band (EDA)
├── fig_correlation_matrix.png             # Full correlation heatmap (EDA)
├── fig_q1_slope_chart.png                 # Country ranking slope chart
├── fig_q2_trend_test.png                  # EU trend + OLS regression line
├── fig_q3_scatter_correlation.png         # Organised crime vs cyber scatter
├── fig_q4_covid_effect.png                # Pre/during/post COVID bar chart
├── fig_q6_regional_comparison.png         # Nordic vs Southern box + line chart
├── fig_q8_clustering.png                  # K-Means cluster visualisation
├── fig_q8_elbow.png                       # Elbow method for optimal K
├── fig_choropleth_cyber.html              # Interactive: avg cyber rate choropleth (EDA)
├── fig_q7_animated_scatter.html           # Interactive: crime scale vs cyber rate
└── fig_q8_clustering_interactive.html     # Interactive: PCA cluster plot
```

---

## 8. How to Reproduce

### Prerequisites

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn plotly adjustText
```

### Execution Order

Run notebooks in sequence in Google Colab or any Jupyter environment:

```
01_data_loading_and_merging.ipynb
    → Outputs: eu_crime_cyber_merged.csv, crime_cleaned.csv, cyber_cleaned.csv

02_exploratory_data_analysis.ipynb
    → Requires: outputs from Notebook 1
    → Outputs: fig_missing_data.png, fig_coverage.png, fig_cyber_distribution.png,
               fig_top_cyber_countries.png, fig_crime_categories.png,
               fig_eu_cyber_trend.png, fig_correlation_matrix.png,
               fig_choropleth_cyber.html, descriptive_stats.csv

03_analysis_and_answers.ipynb
    → Requires: outputs from Notebook 1
    → Outputs: all fig_q*.png/html files, q1_country_rankings.csv,
               q8_country_clusters.csv, crime_growth_analysis.csv
```

### Notes

- All file paths are relative; run from the working directory containing the CSV files
- Plotly interactive charts render in Jupyter / Colab; static PNG fallbacks are saved automatically
- Tableau dashboard requires manual upload of aggregated CSVs to Tableau Public

---

*Data period: 2010–2022 | Geography: 34 European countries*
*Sources: Eurostat Criminal Justice Statistics; Eurostat Digital Economy & Society Survey*
