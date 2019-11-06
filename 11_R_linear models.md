<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (11) Linear models in R

Above all, **R** is a statistical tool. In traditional statistics, 
linear models are the typical choice of methods for analysis. While 
there are numerous packages in **R** that provide more sophisticated 
regression implementations, the functions available in base-**R** `stats` 
package are often sufficient.

<br>

# OLS models
In standard regression/analysis of variance a rather strong 
assumption is that the *residuals* need to be normally distributed. 
If this is the case, one can use the `lm()` function for analysis, 
which is used to fit linear models. It can be used to carry out 
regression, single stratum analysis of variance and analysis of 
covariance (although `aov()` may provide a more convenient 
interface for these).

<br>

## *Model fitting*
For example, let's try to model male fertility in French-speaking 
provinces of Switzerland (at about year 1888):

```{r}
# Get data:
data(swiss)

# Fit linear model:
model = lm(Fertility ~ Catholic + Education, data = swiss)

# Get model summary:
summary(model)
```

The model suggest that the % of catholics and the % education beyond 
primary school in a province are significantly associated with fertility. 
These features explain about 57% of total variation in fertility.

<br>

## *Model evaluation*
To see how well the model fits the data, as well is the assumption of 
normality met, an easy way is to make a diagnostic plot of the model:

```{r,fig.height=5,fig.width=5}
par(mfrow=c(2,2))
plot(model)
```
To note here, especially, is that the average of the residuals across 
the fitted values is approximately zero and that the points in the 
Normal Q-Q plot land close to the diagonal. The former indicates that 
there are no unmodelled non-linearities in the data, whereas the latter 
indicates that the residuals conform to the normality assumption.

What about the role of the explanatory variables then? To see how 
exactly do the predictors associate with the response, we can examine 
the model *coefficients*. Here the model coefficients indicate that where 
there are more catholics fertility is higher, whereas highher education 
tends to reduce fertility:

```{r}
model$coefficients
```
The `Intercept` indicates the average fertility in the whole data.

We can also visialize these associations:

```{r, message=FALSE}
library(tidyr)
library(dplyr)
library(ggplot2)

swiss %>%
  select(Fertility, Catholic, Education) %>%
  gather('Predictor','Value',-Fertility) %>%
  ggplot(aes(Value,Fertility)) + 
    geom_point() + 
    geom_smooth(method = 'lm') +
    facet_wrap(~Predictor, scales='free') + 
    theme_bw()
```

<br>

## *Refining the model*
From the plot above it looks like the % of catholics is actually more 
like a binary variable than a continuous value, as only a few provinces 
have intermediate %-values. What we could do here is to categorize 
the `Catholic` variable and fit a new model:

```{r, message=FALSE}
df = mutate(swiss,c_Catholic = ifelse(Catholic<50,
                             'Non-catholic','Catholic'))

model2 = lm(Fertility~c_Catholic * Education, data=df)

summary(model2)
```

Now an interaction between the predictors is included and we have a slight 
improvent in model fit (as judged by the `R-squared` value).

The interaction becomes evident in the following plot:

```{r}
df %>%
  select(Fertility, c_Catholic, Education) %>%
  ggplot(aes(Education,Fertility, 
             col = c_Catholic, fill = c_Catholic)) + 
    geom_point() + 
    geom_smooth(method = 'lm') +
    theme_bw()
```

---

<br>

# GLM models
In **R**, function `glm()` is used to fit generalized linear models. 
The generalisation means that the user can specify the shape of the 
error distribution.

<br>

## *Logistic regression*
Logistic regression is used when the response variable is binary. 
Such an analysis is made when setting the `family` argument in `glm()` 
equal to `"binomial"`. Here the regression fit is not linear, but a 
sigmoidal curve.

For example:

```{r}
# Get data:
data(iris)

# Make response:
iris$is_setosa = ifelse(iris$Species=='setosa',1,0)

# Fit model:
model = glm(is_setosa~Sepal.Length, family="binomial",data = iris)

# Calculate R-squared:
round(1 - (model$deviance/model$null.deviance),2)
```

To visualize the regression:

```{r}
ggplot(iris,aes(Sepal.Length,is_setosa)) +
  geom_point() +
  stat_smooth(method='glm',
              method.args = list(family='binomial')) +
  theme_bw()
```

<br>

## *Poisson regression*
If the response variable is a positive integer, it technically 
follows a *Poisson* distribution. Such data naturally arises 
when counts are recorded or when counts result from 
cross-tabulation of data.

```{r}
# Make example data:
counts = c(18,17,15,28,25,16,14,11,9)
outcome = gl(3,1,9)
treatment = gl(3,3)
df = data.frame(treatment, outcome, counts)
df
```

This imaginary experimental data has a treatment with 3 levels and 
for each treatment there are 3 outcomes. For each combination 
there is a count of observations.

We can then fit a `glm` model, this time setting the error 
distribution to `"poisson"`:

```{r}
# Fit model:
model = glm(counts ~ outcome + treatment, family = 'poisson')

# Get statistics from a Chi-square test:
anova(model, test = 'Chi')
```