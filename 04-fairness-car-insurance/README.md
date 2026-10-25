# 4. Algorithmic Discrimination in Car Insurance

> Group project (6 students) · [Report (PDF)](report.pdf) · [← Back to overview](../README.md)

**Goal:** study how algorithmic insurance pricing can keep discriminating against protected groups even after a law bans it. The project has two parts: a case study of Michigan's 2019 car-insurance reform, and a hands-on bias analysis of real insurer pricing data.

## Part I: Case study (Michigan, 2019 reform)
- **Context:** Detroit had the most expensive car insurance in the US. Pricing algorithms used non-driving factors (zip code, sex, marital status, home ownership, education, occupation, credit score), and these hit predominantly Black neighbourhoods hardest.
- **The reform:** in 2019 Michigan banned non-driving factors in rate setting. Statewide rates fell, but Detroit stayed the most expensive city.
- **How the bias survived:** insurers swapped the banned features for **proxy variables**. Credit scores became "insurance scores", and zip codes became proprietary territory grids (84% of Allstate's grid borders still follow zip code lines). After the reform, Black residents still faced the highest location-based premium adjustments.
- **Accountability:** pricing models were black boxes. The bias was exposed by data journalists (The Markup), who reverse-engineered public rate filings. In 2024 the Michigan regulator (DIFS) issued Bulletin 2024-20-INS, which requires insurers to document and govern their AI systems.

## Part II: Technical analysis
**Data:** *"Suckers List: How Allstate's Secret Auto Insurance Algorithm Squeezes Big Spenders"* (Kaggle). This is the Allstate Maryland rate dataset used by The Markup: zip code, gender, birth date, and current, selected and indicated premiums per customer. It was enriched with **census data** on the non-white population share per county.

**Pipeline:**
1. **Profiling & preparation:** data profiling report. Variables with no public documentation (underlying premium, total expenses) were dropped, and each customer was labelled by price transition: *Decrease or no change*, *Low increase* or *High increase*.
2. **Fairness metric:** the **disparate impact ratio** of indicated premium by territory, plus a chi-square test.
3. **Predictive modelling:** OLS regressions of selected premium (R² = 0.994) and indicated premium (R² = 0.831). Residuals were used to flag anomalous price transitions.
4. **Subgroup analysis:** distribution of the transition categories by gender, age band and the non-white population share of the area.
5. **Explainability:** **SHAP** values to attribute premium predictions to individual features.

## Findings
- **Territory:** the disparate impact ratio pointed to possible bias against some territories, even though the chi-square test was inconclusive.
- **Unexplained pricing:** customers with price changes above 9.5% had consistently larger residuals. The regression could not predict their transition prices well, which points to imprecise, hard-to-justify pricing for that group.
- **Race (area level):** as the non-white share of an area goes from 0-25% to 75-100%, *Low increase* transitions drop from 46.9% to 40.3%, and *Decrease or no change* rises from 51.4% to 56.6%. *High increase* stays at 1.8-2.7%. Across Maryland counties, the **median current premium rises with the share of Black residents**.
- **Gender and age:** men dominate the *High increase* category in older age bands (over 70% for ages 60-80). *High increase* peaks around ages 40-50.
- **SHAP:** found no direct effect of gender, age or race on predicted premiums. This is consistent with the case-study finding that bias tends to enter through **proxies such as geography**, not through protected attributes directly.

## Limitations
- The regression residuals are non-normal (Omnibus and Jarque-Bera tests) and there are signs of multicollinearity (condition number 3.4e3).
- Two variables (underlying premium, total expenses) had no public documentation and were excluded.
- Race was only available at area level (census), not per customer.

## Authors
Adriano Machado, André Silva, Cristiano Rocha, **Francisco da Ana**, Gonçalo Santos, João Lima. Faculty of Sciences & Faculty of Engineering, University of Porto.

Key sources: The Markup ([Suckers List](https://themarkup.org/allstates-algorithm/2020/02/25/suckers-list-how-allstates-secret-auto-insurance-algorithm-squeezes-big-spenders), [Premium Penalty](https://themarkup.org/premium-penalty/2024/07/18/michigans-fair-and-reasonable-reforms-allowed-car-insurers-to-charge-more-in-black-neighborhoods)), ProPublica, Michigan DIFS. The full reference list is in the [report](report.pdf).
