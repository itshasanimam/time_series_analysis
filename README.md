# SARIMA Time Series Forecasting

This repository contains an implementation of **SARIMA (Seasonal AutoRegressive Integrated Moving Average)** for time series forecasting. The project demonstrates both **in-sample predictions** and **out-of-sample forecasts**, with a focus on proper transformation handling and variance correction.

---

## Features

* In-sample predictions aligned with historical data
* Out-of-sample forecasts (configurable steps ahead)
* Log transformation and **bias correction** using log-normal expectation
* Robust handling of `NaN` and `inf` values
* Forecast indexing with future monthly dates

---

## Code Overview

### In-sample Forecast

```python
pred_in = sarima_fit.get_prediction(start=0, end=len(data_log)-1, dynamic=False)
mu_in = pred_in.predicted_mean
sigma2_in = pred_in.var_pred_mean.clip(lower=0).fillna(0)

forecast_in_sample = np.exp(mu_in + 0.5 * sigma2_in)
forecast_in_sample = pd.Series(forecast_in_sample, index=data.index)
forecast_in_sample = forecast_in_sample.replace([np.inf, -np.inf], np.nan).fillna(method="ffill")
```

### Out-of-sample Forecast

```python
n_forecast = 24
pred_out = sarima_fit.get_forecast(steps=n_forecast)
mu_out = pred_out.predicted_mean
sigma2_out = pred_out.var_pred_mean.clip(lower=0).fillna(0)

forecast_out_sample = np.exp(mu_out + 0.5 * sigma2_out)
forecast_out_sample = pd.Series(
    forecast_out_sample, 
    index=pd.date_range(
        start=data.index[-1] + pd.offsets.MonthEnd(1),
        periods=n_forecast,
        freq='M'
    )
)
forecast_out_sample = forecast_out_sample.replace([np.inf, -np.inf], np.nan).fillna(method="ffill")
```

---

## Requirements

* Python 3.8+
* pandas
* numpy
* statsmodels
* matplotlib / seaborn (for visualization)


---

## Usage

1. Load your time series data into a pandas DataFrame.
2. Apply log transformation before fitting SARIMA.
3. Run the notebook or script to generate forecasts.
4. Visualize in-sample and out-of-sample predictions for insights.

---

## Next Steps

* Add confidence intervals for forecasts
* Visualize forecasts alongside historical data
* Experiment with different seasonal orders

---
