**Group name: Group Alpha**

**Members:**

- Anuj Shrestha
- Pawan Sharma
- Gyan Bahadur Tamang
- Aashish Shrestha

# Deliverable 1 Climate Data Mining

The dataset is Berkeley Earth's GlobalLandTemperaturesByCity.csv, distributed by Kaggle. The original city file contains 8,599,212 rows and seven attributes; four recorded global temperature fields from GlobalTemperatures.csv are joined by month, giving eleven sourced input attributes. A predefined panel retains 20,088 city-month records across 27 cities from January 1951 to December 2012. Calendar, signed coordinates, climatology and strictly past-only lag features expand the analytical schema to 29 columns. These are derived features rather than extra independent measurements. The dataset fits the record requirement and supports climate-related regression, classification and descriptive pattern discovery.

The panel spans multiple continents but is a convenience sample, not a representative or area-weighted global mean. All selected months are retained before cleaning. Complete 2012 is the endpoint; incomplete 2013 is excluded. Source-native names are preserved, including Peking, Bombay and Mexico. The city list, source/member hashes and selection rule are in data/provenance.json. The joined input meets the attribute criterion before feature engineering. The four global fields describe monthly context, are repeated across cities and are excluded from contemporaneous forecast predictors. The many-to-one date join preserves the city-month observation count.

## Cleaning and EDA

The cleaning audit reports 0 missing temperatures, 0 missing uncertainty values, 0 duplicate removals and 0 structurally invalid temperatures. It excludes 0 missing supervised targets. City-country-date keys are unique, coordinates retain hemisphere signs, and calendar continuity is checked before lags. Temperatures outside -90 to 60 C are marked invalid; plausible IQR extremes remain. Missing predictors are imputed from training folds. Missing outcomes are never imputed.

Training-only EDA visualizes anomalies, class balance, local outliers, correlations and the development-period annual curve. Remove calendar seasonality before warming comparisons; use regularization for correlated predictors and chronological folds for evaluation.

## Key EDA findings and next steps

- **Class balance:** 25.0% of the 1981-2000 observations exceed the local +1 C anomaly threshold. Later classification should report warm-event F1 and recall alongside accuracy.
- **Variability:** Training anomalies have a standard deviation of 1.303 C. In this city sample, anomaly standard deviation is 1.454 C in the Northern Hemisphere and 0.843 C in the Southern Hemisphere. These sample differences motivate checking performance by location.
- **Temporal relationships:** The correlation between current and previous-month training anomalies is 0.259. Previous anomalies are plausible predictors, but their usefulness must be evaluated using chronological validation.
- **Unusual observations:** The development-period city-month IQR check flags 262 observations. They are retained because an IQR flag alone does not establish a measurement error; removing them could erase meaningful weather extremes.
- **Data quality:** The selected source panel has no missing temperatures, missing uncertainties, duplicate keys or structurally invalid temperatures. Early lag features are missing where no prior observation is available; later pipelines impute predictors using only their training folds.

These findings describe the selected cities, not a representative global climate sample. Local monthly climatology removes seasonality before interpreting anomalies.

## Evaluation boundary

Climatology is the mean for each city and calendar month in 1951-1980, with at least 25 available baseline observations required per city-month. Anomaly is observed temperature minus this climatology. Training uses 1981-2000 (6,480 rows), and final testing uses 2001-2012 (3,888 rows). Three expanding folds validate 1989-1992, 1993-1996 and 1997-2000 using only earlier years. All cities for a month stay together. Earlier test observations become available for later one-month forecasts; this is rolling evaluation, not a fixed-origin twelve-year forecast.

## Challenges and decisions

Challenges included joining global context to the seven city attributes, seasonality, strong local climate differences, temporal leakage risks, missing values and correlated observations. Solutions were transparent feature engineering, city-month reference climatology, past-only lag features, chronological group validation, fold-fitted preprocessing and retained plausible extremes. Remaining limitations are convenience sampling, historical coverage ending in 2012 and unpropagated measurement uncertainty.

## Reproduce

From this deliverable folder, create a local environment and install dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Open `Deliverable_1.ipynb` in VS Code or Jupyter, select that environment as the kernel, and run all cells from this folder. The source subset is bundled, so analysis needs no download. The complete analytical schema is generated inside the notebook. Figures and measured tables are saved in `outputs/`.

## Source and license

Berkeley Earth, *Climate Change: Earth Surface Temperature Data*, distributed by Kaggle: https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data . Provider: https://berkeleyearth.org/data/ . Data card license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/). This subset and its engineered data are educational noncommercial adaptations with attribution under the same data license. See `data/provenance.json` for source hashes and selection. Do not interpret this historical city panel as an area-weighted global mean.
