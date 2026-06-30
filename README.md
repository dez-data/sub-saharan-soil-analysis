# Sub-Saharan Soil Analysis: Agricultural Recommendation Feature Engineering

Analysis of the iSDA Soil Dataset, covering 42 sub-Saharan African countries at 30m resolution, built to evaluate how soil chemistry data can drive crop recommendation logic for agricultural advisory systems.

## Why This Dataset

Soil nutrient profiles determine which crops can realistically succeed at a given site, yet most recommendation systems in the region work off country-level averages rather than measured site data. The iSDA dataset closes that gap: topsoil and subsoil readings for nitrogen, organic carbon, pH, calcium, magnesium, potassium, aluminium, iron, and manganese across the continent. That granularity is what makes site-specific, rather than regional, recommendation logic possible.

Sourced from [Kaggle](https://www.kaggle.com), originally derived from iSDA Africa's machine-learning soil property predictions.

## What's in This Repo

- **`notebooks/soil_analysis.ipynb`** — full analytical pipeline: cleaning, EDA, feature engineering, and a rule-based crop recommendation function, with code and inline insights.
- **`reports/Soil_Analysis_Report.docx`** — formal write-up covering all five analysis stages plus an LLM-assisted insight generation exercise.

## Data Cleaning

Six columns (boron, electrical conductivity, phosphorus, sodium, sulphur, zinc extractable) were fully null across the dataset and were dropped rather than imputed; imputing a column with zero observed values fabricates data rather than recovering it. `carbon_total` nulls were handled with median imputation where the gap was partial. Outliers in key nutrient columns were capped using the IQR method rather than removed, since soil data legitimately contains extremes at sites with unusual parent material, and removing those records would shrink geographic coverage exactly where more data is needed, not less.

## Key Findings

Nutrient distributions are right-skewed across the board except pH, meaning most sites carry low-to-moderate nutrient concentrations with a smaller number of high-fertility outliers. Topsoil carries significantly higher nitrogen and organic carbon than subsoil, consistent with surface-concentrated organic matter decomposition. Acidic soils (pH below 5.5) cluster in the equatorial belt across West and Central Africa, while alkaline soils concentrate in the Horn of Africa and the southern tip of the continent, a pattern that follows precipitation gradients rather than arbitrary geography.

Aluminium extractability rises sharply below pH 5.5, identifying a reliable, practical threshold for flagging toxicity risk to sensitive crops like maize, beans, and soybeans. Carbon and nitrogen correlate at r=0.88, and calcium and pH correlate at r=0.78, both findings with direct implications for how a recommendation system should weight measurements that move together.

## Feature Engineering

Three features were built for recommendation system use: a soil organic matter score (converted from organic carbon via the Van Bemmelen factor, the agronomic standard practitioners recognize), a binary aluminium toxicity flag (requiring both elevated aluminium and low pH, since high aluminium at neutral pH stays chemically bound and is not actually toxic), and a topsoil indicator enabling depth-weighted recommendations without conditional branching in the model pipeline.

## Recommendation Logic

A rule-based scoring system matches soil measurements against threshold requirements across five crops (maize, cassava, sorghum, groundnuts, beans), scoring each crop on pH range, minimum nitrogen, minimum potassium, and maximum tolerable aluminium. Rule-based logic was chosen over a trained model for three reasons: the dataset has no yield outcome labels to train against, agronomic thresholds for these crops are already well-established in extension guidance across the region, and rule-based output is interpretable, meaning a field agent can explain a recommendation to a farmer by pointing to the exact soil numbers behind it. The design is modular: as yield outcome data becomes available, thresholds can be calibrated empirically and the scoring function replaced with a trained classifier without restructuring the pipeline around it.

## Tools

Python (pandas, numpy, matplotlib, seaborn), Jupyter Notebook.

---

*Built as part of a data science assessment evaluating agricultural data analysis capability. Dataset and assessment context are independent of any specific organization; all analysis and conclusions are original.*
