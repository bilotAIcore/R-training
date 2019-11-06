<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (13) Intro to timeseries analysis
According to [Wikipedia](https://en.wikipedia.org/wiki/Time_series), 
Time series analysis comprises methods for analyzing time series data 
in order to extract meaningful statistics and other characteristics of 
the data. Time series forecasting is the use of a model to predict 
future values based on previously observed values. While regression 
analysis is often employed in such a way as to test theories that the 
current values of one or more independent time series affect the current 
value of another time series, this type of analysis of time series is not 
called "time series analysis", which focuses on comparing values of a 
single time series or multiple dependent time series at different points in time.

<br>

# Timeseries visualization
## *autoplot*
A convenient method for plotting time series is the `autoplot()` function.
```{r,warning=FALSE,message=FALSE}
library(forecast)

# Load time series data:
data(LakeHuron)

autoplot(LakeHuron) +
  theme_minimal()
```

## *Autocorrelation*
Autocorrelation, also known as serial correlation, is the correlation 
of a signal with a delayed copy of itself as a function of delay:

```{r}
ggAcf(x = LakeHuron, lag.max = 36) + 
  theme_minimal()
```

# Seasonality
As an example, we can use the `ldeaths` data, giving the monthly deaths 
from bronchitis, emphysema and asthma in the UK, 1974–1979. 
The data looks like this:

```{r}
autoplot(ldeaths) +
  theme_minimal()
```

<br>

## *Seasonal decomposition*
The time series is clearly periodic. We can extract the seasonality, 
possible long-term trend, and any remaining variation using the `stl()` 
function: 

```{r}
# make decomposition:
decomp = stl(ldeaths,'periodic')

# Visualize:
plot(decomp,col='blue3')
```

<br>

## *Detrending*
Removing a trend from data enables one to focus on  
fluctuations in the data that are independent of the trend. While trends can be 
meaningful, some types of analyses yield better insight once you remove trends.

In this case, the resulting decomposition can be used to *detrend* 
the original time series:

```{r}
detrended = ldeaths - decomp$time.series[,1] -
  decomp$time.series[,2]
autoplot(detrended) +
  theme_minimal()
```

---

<br>

# Timeseries prediction

## *Exponential smoothing*
If you have a time series that can be described using an additive model with 
increasing or decreasing trend and seasonality, you can use Holt-Winters 
exponential smoothing to make short-term forecasts.
Holt-Winters exponential smoothing estimates the level, slope and seasonal 
component at the current time point.

```{r}
# Fit model:
HW = HoltWinters(ldeaths)

# Forecast 12 months ahead:
FC = forecast(HW,h =12)

# Plot model fit & forest:
par(mfrow = c(1,2),mar=c(3,3,1,1),mgp=c(2,.8,0))
plot(HW)
plot(FC1)
```

How well does the model fit the data? We can e.g. calculate the residual mean 
square error (*RMSE*) and then calculate the percentual error relative to the 
mean in the data:

```{r}
library(Metrics)

# RMSE:
RMSE = rmse(actual = ldeaths,
            predicted = HW$fitted[,'xhat'])
RMSE

# %-error:
round(100*RMSE/mean(ldeaths),0)
```

Finally, we need to make sure that some assumptions are met. If one want to 
make prediction intervals for forecasts made using exponential smoothing 
methods, the prediction intervals require that the forecast errors are 
uncorrelated and are normally distributed with mean zero and constant variance.  

```{r}
par(mfrow=c(1,2))
acf(na.exclude(FC$residuals), lag.max=12,main='')
hist(na.exclude(FC$residuals),main='Residual distribution',font.main=1)
```


<br>

## *ARIMA models*
While exponential smoothing methods do not make any assumptions about time 
series autocorrelation, in some cases you can make a better predictive model 
by taking correlations in the data into account. Autoregressive Integrated 
Moving Average (ARIMA) models include an explicit statistical model for the 
irregular component of a time series, that allows for non-zero autocorrelations 
in the irregular component. An important point to keep in mind is that ARIMA 
models are defined for stationary time series.

A simple way to make the data statrionary is to difference it. In addition, 
to reduce variance, it is often usefull to take the logarithm of the data 
prior to differencing:

```{r}
diffed = diff(log(ldeaths))
autoplot(diffed) +
  theme_minimal()
```

Then we can fit an **ARIMA** model, using an automated model selection 
procedure in `auto.arima()` function:

```{r}
# Fit ARIMA model:
AA = auto.arima(diffed)

# Make forescast:
FC = forecast(AA,h = 12)

# Plot model fit & forest:
par(mfrow = c(1,2),mar=c(3,3,1,1),mgp=c(2,.8,0))
plot(diffed); lines(AA$fitted, col=2)
plot(FC)
```

The model coefficients are the following:
```{r}
round(AA$coef,3)
```
which means that autoregressive components with orders 1–3 have been 
fitted, as well as a 1st order seasonal autoregressive term. If the 
parameter is positive the linear dependence of the present on past values 
is always positive, whereas if the parameter is negative this dependence 
is positive for even lags and negative for odd ones.

We can again calculate the RMSE, but unfortunal this cannot be directly 
compared with that produced by the Holt-Winters model:

```{r}
# RMSE:
RMSE = rmse(actual = diffed,
            predicted = AA$fitted)
round(RMSE,3)
```