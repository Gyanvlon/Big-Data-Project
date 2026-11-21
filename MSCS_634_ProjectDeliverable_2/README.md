**Group name: Group Alpha**

**Members:**

- Anuj Shrestha
- Pawan Sharma
- Gyan Bahadur Tamang
- Aashish Shrestha
- Urusha Budhathoki

# Deliverable 2 Climate Data Mining

The dataset is Berkeley Earth's GlobalLandTemperaturesByCity.csv, distributed by Kaggle. The original city file contains 8,599,212 rows and seven attributes; four recorded global temperature fields from GlobalTemperatures.csv are joined by month, giving eleven sourced input attributes. A predefined panel retains 20,088 city-month records across 27 cities from January 1951 to December 2012. Calendar, signed coordinates, climatology and strictly past-only lag features expand the analytical schema to 29 columns. These are derived features rather than extra independent measurements. The dataset fits the record requirement and supports climate-related regression, classification and descriptive pattern discovery.

The panel spans multiple continents but is a convenience sample, not a representative or area-weighted global mean. All selected months are retained before cleaning. Complete 2012 is the endpoint; incomplete 2013 is excluded. Source-native names are preserved, including Peking, Bombay and Mexico. The city list, source/member hashes and selection rule are in data/provenance.json. The joined input meets the attribute criterion before feature engineering. The four global fields describe monthly context, are repeated across cities and are excluded from contemporaneous forecast predictors. The many-to-one date join preserves the city-month observation count.

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

## Challenges and decisions

Challenges included joining global context to the seven city attributes, seasonality, strong local climate differences, temporal leakage risks, missing values and correlated observations. Solutions were transparent feature engineering, city-month reference climatology, past-only lag features, chronological group validation, fold-fitted preprocessing and retained plausible extremes. Remaining limitations are convenience sampling, historical coverage ending in 2012 and unpropagated measurement uncertainty.

## Reproduce

Open the notebook using a Python environment with `requirements.txt` installed; run all cells from this folder. The source subset is bundled, so analysis needs no download. The complete analytical schema is generated inside the notebook. Figures and measured tables are saved in `outputs/`.

## Source and license

Berkeley Earth, *Climate Change: Earth Surface Temperature Data*, distributed by Kaggle: https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data . Provider: https://berkeleyearth.org/data/ . Data card license: CC BY-NC-SA 4.0 (https://creativecommons.org/licenses/by-nc-sa/4.0/). This subset and its engineered data are educational noncommercial adaptations with attribution under the same data license. See `data/provenance.json` for source hashes and selection. Do not interpret this historical city panel as an area-weighted global mean.
