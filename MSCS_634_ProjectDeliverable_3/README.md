**Group name: Group Alpha**

**Members:**

- Anuj Shrestha
- Pawan Sharma
- Gyan Bahadur Tamang
- Aashish Shrestha
- Urusha Budhathoki

# Deliverable 3 Climate Data Mining

The dataset is Berkeley Earth's GlobalLandTemperaturesByCity.csv, distributed by Kaggle. The original city file contains 8,599,212 rows and seven attributes; four recorded global temperature fields from GlobalTemperatures.csv are joined by month, giving eleven sourced input attributes. A predefined panel retains 20,088 city-month records across 27 cities from January 1951 to December 2012. Calendar, signed coordinates, climatology and strictly past-only lag features expand the analytical schema to 29 columns. These are derived features rather than extra independent measurements. The dataset fits the record requirement and supports climate-related regression, classification and descriptive pattern discovery.

The panel spans multiple continents but is a convenience sample, not a representative or area-weighted global mean. All selected months are retained before cleaning. Complete 2012 is the endpoint; incomplete 2013 is excluded. Source-native names are preserved, including Peking, Bombay and Mexico. The city list, source/member hashes and selection rule are in data/provenance.json. The joined input meets the attribute criterion before feature engineering. The four global fields describe monthly context, are repeated across cities and are excluded from contemporaneous forecast predictors. The many-to-one date join preserves the city-month observation count.

## Evaluation boundary

Climatology is the mean for each city and calendar month in 1951-1980, with at least 25 available baseline observations required per city-month. Anomaly is observed temperature minus this climatology. Training uses 1981-2000 (6,480 rows), and final testing uses 2001-2012 (3,888 rows). Three expanding folds validate 1989-1992, 1993-1996 and 1997-2000 using only earlier years. All cities for a month stay together. Earlier test observations become available for later one-month forecasts; this is rolling evaluation, not a fixed-origin twelve-year forecast.

## Classification

Warm month means a local anomaly strictly above +1 C, not a heatwave definition or the international global warming threshold. Logistic regression reaches test accuracy 0.653, F1 0.439, warm recall 0.380 and AUC 0.673. The tuned tree reaches F1 0.331. Grid search chooses depth 8 and minimum leaf size 50 from six combinations across the three chronological folds. Positive-class F1 guides tuning.

| Model | accuracy | f1 | warm_recall | precision | auc |
| --- | --- | --- | --- | --- | --- |
| Majority baseline | 0.6422 | 0.0000 | 0.0000 | 0.0000 | 0.5000 |
| Logistic regression | 0.6530 | 0.4391 | 0.3796 | 0.5207 | 0.6733 |
| Tuned decision tree | 0.6469 | 0.3306 | 0.2437 | 0.5136 | 0.6694 |

Confusion matrices use true labels in rows and predicted labels in columns, ordered Other then Warm. ROC curves show threshold-dependent discrimination.

## Clustering

Cluster 0: 13 cities, mean temperature 11.27 C, seasonal range 21.51 C and warm fraction 35.1%. Cluster 1: 14 cities, mean temperature 21.11 C, seasonal range 7.77 C and warm fraction 15.5%. KMeans uses 1981-2000 city summaries only; k=2-5 is selected by silhouette. PCA visualizes standardized summaries. Numeric group identifiers are arbitrary and describe the sample rather than discrete global climate zones.

## Association mining

Apriori retains 2 warm-anomaly rules. The highest lift rule is north, prior_month_warm implies warm_anomaly, with support 0.089, confidence 0.462 and lift 1.850. A sensitivity check at confidence .55 produced no qualifying warm rules; .40 is used for descriptive exploration, not confirmatory inference. Baskets use training observations, with minimum support .08, confidence .40, lift above 1.1 and at most three items. Warm anomaly is the sole consequent, avoiding geographical tautologies. Serial dependence and spatial correlation mean rules are descriptive, not causal or independently significant.

Practical relevance: study climate group differences and persistence, prioritize local monitoring hypotheses and test patterns on independent data. No causal conclusions follow from these models.

## Challenges and decisions

Challenges included joining global context to the seven city attributes, seasonality, strong local climate differences, temporal leakage risks, missing values and correlated observations. Solutions were transparent feature engineering, city-month reference climatology, past-only lag features, chronological group validation, fold-fitted preprocessing and retained plausible extremes. Remaining limitations are convenience sampling, historical coverage ending in 2012 and unpropagated measurement uncertainty.

## Reproduce

Open the notebook using a Python environment with `requirements.txt` installed; run all cells from this folder. The source subset is bundled, so analysis needs no download. The complete analytical schema is generated inside the notebook. Figures and measured tables are saved in `outputs/`.

## Source and license

Berkeley Earth, *Climate Change: Earth Surface Temperature Data*, distributed by Kaggle: https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data . Provider: https://berkeleyearth.org/data/ . Data card license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/). This subset and its engineered data are educational noncommercial adaptations with attribution under the same data license. See `data/provenance.json` for source hashes and selection. Do not interpret this historical city panel as an area-weighted global mean.
