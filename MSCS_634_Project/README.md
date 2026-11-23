**Group name: Group Alpha**

**Members:**

- Anuj Shrestha
- Pawan Sharma
- Gyan Bahadur Tamang
- Aashish Shrestha
- Urusha Budhathoki

# MSCS_634_Project Climate Data Mining

**GitHub repository:** [Gyanvlon/Big-Data-Project](https://github.com/Gyanvlon/Big-Data-Project)

Presentation Link https://somup.com/cOXroyVfahL

The dataset is Berkeley Earth's GlobalLandTemperaturesByCity.csv, distributed by Kaggle. The original city file contains 8,599,212 rows and seven attributes; four recorded global temperature fields from GlobalTemperatures.csv are joined by month, giving eleven sourced input attributes. A predefined panel retains 20,088 city-month records across 27 cities from January 1951 to December 2012. Calendar, signed coordinates, climatology and strictly past-only lag features expand the analytical schema to 29 columns. These are derived features rather than extra independent measurements. The dataset fits the record requirement and supports climate-related regression, classification and descriptive pattern discovery.

The panel spans multiple continents but is a convenience sample, not a representative or area-weighted global mean. All selected months are retained before cleaning. Complete 2012 is the endpoint; incomplete 2013 is excluded. Source-native names are preserved, including Peking, Bombay and Mexico. The city list, source/member hashes and selection rule are in data/provenance.json. The joined input meets the attribute criterion before feature engineering. The four global fields describe monthly context, are repeated across cities and are excluded from contemporaneous forecast predictors. The many-to-one date join preserves the city-month observation count.

## Cleaning and EDA

The cleaning audit reports 0 missing temperatures, 0 missing uncertainty values, 0 duplicate removals and 0 structurally invalid temperatures. It excludes 0 missing supervised targets. City-country-date keys are unique, coordinates retain hemisphere signs, and calendar continuity is checked before lags. Temperatures outside -90 to 60 C are marked invalid; plausible IQR extremes remain. Missing predictors are imputed from training folds. Missing outcomes are never imputed.

Training-only EDA visualizes anomalies, class balance, local outliers, correlations and the development-period annual curve. Remove calendar seasonality before warming comparisons; use regularization for correlated predictors and chronological folds for evaluation.

## Evaluation boundary

Climatology is the mean for each city and calendar month in 1951-1980, with at least 25 available baseline observations required per city-month. Anomaly is observed temperature minus this climatology. Training uses 1981-2000 (6,480 rows), and final testing uses 2001-2012 (3,888 rows). Three expanding folds validate 1989-1992, 1993-1996 and 1997-2000 using only earlier years. All cities for a month stay together. Earlier test observations become available for later one-month forecasts; this is rolling evaluation, not a fixed-origin twelve-year forecast.

## Feature engineering and regression

Predictors are time since 1981, cyclic month sine/cosine, signed latitude and longitude, absolute latitude, previous-month anomaly, previous-year anomaly, the mean of the prior twelve anomalies, prior-month uncertainty and one-hot city identity. The current temperature, anomaly, uncertainty, climatology and warm label are excluded from predictors. Shifting occurs within each city over a complete monthly grid. Preprocessing is refitted inside each chronological fold. Past uncertainty is a quality proxy and is not interpreted as a climate driver.

Expanding-window CV selects Ridge, with mean CV RMSE 1.264 C and fold SD 0.027 C. Its test RMSE is 1.241 C, MSE 1.539 C squared and R squared 0.061. The zero-anomaly climatology baseline has test RMSE 1.497 C and lag-one persistence has RMSE 1.591 C. Selection uses training CV, not the test comparison. A training-only feature comparison gives ridge CV RMSE 1.322 C with calendar/location and 1.264 C after adding past climate features. City-specific test RMSE spans 0.314-2.580 C; the exported city_errors.csv exposes uneven local reliability. Linear and ridge performance is nearly identical, so the small numerical difference should not be treated as a significant superiority claim.

| Model | cv_rmse_mean | cv_rmse_sd | test_mse | test_rmse | test_r2 |
| --- | --- | --- | --- | --- | --- |
| Mean baseline | 1.3376 | 0.0330 | 1.7947 | 1.3397 | -0.0955 |
| Linear regression | 1.2647 | 0.0274 | 1.5393 | 1.2407 | 0.0604 |
| Ridge | 1.2639 | 0.0269 | 1.5390 | 1.2406 | 0.0606 |
| Climatology zero |  |  | 2.2417 | 1.4972 | -0.3684 |
| Persistence |  |  | 2.5304 | 1.5907 | -0.5446 |

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

## Final insights

Across the fixed city panel, the equal-city 2001-2012 mean anomaly is 0.777 C relative to local 1951-1980 monthly means. Annual curves require twelve available months per city-year. This is a descriptive comparison over this historical sample, not an estimate of current global warming or a causal attribution.

## Ethics and recommendations

Environmental estimates contain no personal identifiers. Geographic selection and uneven observation coverage still affect fairness: rural areas and unselected communities are not represented. Nearby city estimates can share spatial information, so records are not independent. The source reconstruction already applies processing; these notebooks cannot reproduce Berkeley Earth's station homogenization. Source measurement uncertainty is retained but not fully propagated into forecast intervals. Mitigations implemented here include a fixed city panel with a documented selection rule, strictly chronological validation, held-out evaluation, no imputed outcomes, retained plausible extremes, and an exported city-specific error audit. These checks make geographic reliability differences visible; they do not establish representative coverage or eliminate reconstruction bias. Errors and associations must be communicated without overstating certainty or assigning causality.

Use local anomalies for comparisons across climates. Maintain chronological splits and realistic forecast horizons. Expand to more cities and newer independent observations before operational use; compare stronger time-series baselines, assess uncertainty and city-specific error. Use gridded area weights for global summaries and avoid allocating resources solely from the convenience panel. Follow dataset attribution and share-alike terms.

## Final artifacts

- `Deliverable_4.ipynb`: consolidated executed analysis.
- `Final_Report.pdf`: professionally formatted 15-page report with numbered sections, a table of contents, results tables, figure captions, and page numbers.
- `Final_Report.docx`: editable Word version with the same report content, charts, dataset/repository links, and automatic page-number fields.
- `Presentation_Slides.pdf`: eight slides.

The project repository is [Gyanvlon/Big-Data-Project](https://github.com/Gyanvlon/Big-Data-Project), as supplied by the project owner. Upload of the latest files, instructor access, and course submission have not been verified. The Deliverable 4 requirements supplied for this review do not explicitly require a video, a presentation duration, or a slide count. Slides and narration are supporting materials; check any separate instructor presentation instructions before treating a recording as mandatory.

Validation limitation: `Project/verify_project.py` passed saved notebook execution, data hash, cross-deliverable output consistency, chronology, and archive integrity checks, then stopped on missing `MSCS_634_ProjectDeliverable_1/outputs/anomaly_trend.png`. The full project verification therefore has not passed. Its final recorded-video reminder also reflects an additional requirement not stated in the supplied Deliverable 4 text.

## Challenges and decisions

Challenges included joining global context to the seven city attributes, seasonality, strong local climate differences, temporal leakage risks, missing values and correlated observations. Solutions were transparent feature engineering, city-month reference climatology, past-only lag features, chronological group validation, fold-fitted preprocessing and retained plausible extremes. Remaining limitations are convenience sampling, historical coverage ending in 2012 and unpropagated measurement uncertainty.

## Reproduce

Open the notebook using a Python environment with `requirements.txt` installed; run all cells from this folder. The source subset is bundled, so analysis needs no download. The complete analytical schema is generated inside the notebook. Figures and measured tables are saved in `outputs/`.

## Source and license

Berkeley Earth, *Climate Change: Earth Surface Temperature Data*, distributed by Kaggle: https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data . Provider: https://berkeleyearth.org/data/ . Data card license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/). This subset and its engineered data are educational noncommercial adaptations with attribution under the same data license. See `data/provenance.json` for source hashes and selection. Do not interpret this historical city panel as an area-weighted global mean.
