Executing Summary:

In this example, I consider energy price forecast problem using basic Facebook Prophit and Gaussian Processes models.

Data cleaning. Forecast_wind_mw has 4 days of misssing values (06/10/2019 23:00, 07/10/2019 00:00-22:00, 02/02/2020 00:00-23:00, 03/02/2020 00:00-23:00, 28/02/2021 00:00-23:00) I replaced by median for the corresponding hour using previous dates. 
Forecast_wind_mw has three outliers that I replaced by median as well: <br>
18/07/2019 02:00,  999999  <br>
12/09/2019 01:00, -999999  <br>
13/11/2019 09:00,  999999  <br>
national_demand_mw has outlier at 21/09/2019  06:00:00, it has value -999999. We replace it by median. <br>

We have negative prices but we were told it is fine. The high prices in Jan 2021 are explained in https://auroraer.com/insight/gb-wholesale-market-summary-january-2021/  <br>

We have created the following features. For the previous values of "forecast_wind_mw", "national_demand_mw", "embedded_solar_generation_mw" and "price_gbp" given hour we computed statistics "median", "std", "Q25", "Q75", "max" and  "ORDER" (order define the lag). We also use features such as season (winter, spring, summer and autumn) and hour (0-23).

We use the following time interval for train, validation and test datasets: <br>
1) train, 2019-07-10 00:00:00 - 2020-09-17 18:00:00; <br>
2) validation, 2020-09-17 19:00:00  2021-02-07 08:00:00; <br>
3) test, 2021-02-07 09:00:0  2021-06-29 23:00:00. <br>

We use three models: 1) we use the entire training dataset to make one model (let us denote it as GP ALL, see Fig. 1 and Table 1); 2) We use Gaussian Processes in autoregressive manner using 5 previous prices, one model per hour, we denote it as GP SW (see Fig. 2 and Table 1);; 3) Facebook Prophet using only timestamp and price values (see Fig. 3); 4) Facebook Prophet with extra inputs (covariates) selected by forward feature selection algorithm using Mean Relative Absolute Error (MRAE), selected featues: ['season', 'Hour', 'forecast_wind_mw_ORDER_4', 'forecast_wind_mw_std']  (see Fig. 4); 5) Facebook Prophet that uses all features  (see Fig. 5).  

It seems GP SW perform better than GP ALL but we have not optimise it performance using different features, kernels and etc.

The Prophet procedure is an additive regression model with four main components — a piecewise linear logistic growth curve trend; a yearly seasonal component modelled using Fourier series; a weekly seasonal component created using dummy variables; a user-provided list of important holidays. We did not use Bank holidays in our model but it is really easy to update the model with this functionality.

The best model in MRAE sense obtained by forward feature selection algorithm 