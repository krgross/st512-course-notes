# Regression models with several predictors



In this chapter, we examine regression models that contain several predictor variables.  Thankfully, many of the ideas from the simple linear regression also apply to regression models with several predictors.  After an overview of the basics, this chapter will focus on the new aspects of regression modeling that arise when considering several predictors.

## Multiple regression basics

### The multiple regression model

Just as SLR was used to characterize the relationship between a single predictor and a response, multiple regression can be used to characterize the relationship between several predictors and a response.

*Example.*  In the BAC data, we also know each individual's weight and gender:

```r
beer <- read.csv("data/beer2.csv", head = T, stringsAsFactors = T)
head(beer)
```

```
##     BAC weight beers
## 1 0.100    132     5
## 2 0.030    128     2
## 3 0.190    110     9
## 4 0.120    192     8
## 5 0.040    172     3
## 6 0.095    250     7
```
A plot of the residuals from the BAC vs. beers consumed model against weight strongly suggests that some of the variation in BAC is attributable to differences in weight:

```r
fm1 <- with(beer, lm(BAC ~ beers))
plot(x = beer$weight, y = resid(fm1), xlab = "weight", ylab = "residual")
abline(h = 0, lty = "dotted")
```

<div class="figure" style="text-align: center">
<img src="02-MultipleRegression_files/figure-html/unnamed-chunk-2-1.png" alt="SLR residuals vs. weight." width="480" />
<p class="caption">(\#fig:unnamed-chunk-2)SLR residuals vs. weight.</p>
</div>

To simultaneously characterize the effect that the variables "beers" and "weight" have on BAC, we might want to entertain a model with both predictors.  In words, the model is
\[
\mbox{BAC} = \mbox{intercept} + \mbox{(parameter associated with beers)} \times \mbox{beers}  + \mbox{(parameter associated with weight)} \times \mbox{weight} + \mbox{error}
\]
where (for the moment)  we are intentionally vague about what we mean by "parameter associated with beers".  As in SLR, the error term can be interpreted as a catch-all term that includes all the variation not accounted for by the linear associations betwen the response and the predictors  "beers" and "weight".

In mathematical notation, we can write the model as
\begin{equation}
y = \beta_0 +\beta_1 x_1 +\beta_2 x_2 +\varepsilon 
(\#eq:beer-two-predictors)
\end{equation}
We use subscripts to distinguish different predictors.  In this case, $x_1$ is the number of beers consumed and $x_2$ is the individual's weight.  Of course, the order in which we designate the predictors is arbitrary.

There are a variety of ways to think about this model.  As in SLR, we can separate this model into a mean or signal component $\beta_0 +\beta_1 x_1 +\beta_2 x_2$ and an error component $\varepsilon$.  Note that the mean component is now a function of two variables, and suggests that the relationship between the average response and either predictor is linear.  If we wish to make statistical inferences about the parameters $\beta_0$, $\beta_1$ and $\beta_2$ (which we do), then we need to place the standard assumptions on the error component: independence, constant variance, and normality.  In notation, $\varepsilon \sim \mathcal{N}\left(0,\sigma_{\varepsilon}^2 \right)$.

We can also think about this model geometrically.  Recall that in SLR, we could interpret the SLR model as a line passing through a cloud of data points.  With 2 predictors, we are now fitting a plane to data points that "exist" in a three- dimensional data cloud. 
<!-- ```{r fig.height = 3, echo = FALSE} -->
<!-- knitr::include_graphics("images/regression-schematic.png", dpi = 100) -->
<!-- ``` -->
As in SLR, we use the least squares criteria to find the best-fitting parameter estimates.  That is to say, we will agree that the best estimates of the parameters $\beta_0$, $\beta_1$ and $\beta_2$ are the values that minimize
\begin{eqnarray*}
SSE & = & \sum_{i=1}^n e_i^2 \\
 & = & \sum_{i=1}^n \left(y_i -\hat{y}_i \right)^2 \\
 & = & \sum_{i=1}^n\left(y_i -\left[\hat{\beta}_0 +\hat{\beta}_1 x_{i1} +\hat{\beta}_{2} x_{i2} \right]\right)^2  
\end{eqnarray*}
In R, we can fit this model by adding a new term to the right-hand side of the model formula in the call to the function 'lm':

```r
fm2 <- lm(BAC ~ beers + weight, data = beer)
summary(fm2)
```

```
## 
## Call:
## lm(formula = BAC ~ beers + weight, data = beer)
## 
## Residuals:
##        Min         1Q     Median         3Q        Max 
## -0.0162968 -0.0067796  0.0003985  0.0085287  0.0155621 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  3.986e-02  1.043e-02   3.821  0.00212 ** 
## beers        1.998e-02  1.263e-03  15.817 7.16e-10 ***
## weight      -3.628e-04  5.668e-05  -6.401 2.34e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01041 on 13 degrees of freedom
## Multiple R-squared:  0.9518,	Adjusted R-squared:  0.9444 
## F-statistic: 128.3 on 2 and 13 DF,  p-value: 2.756e-09
```
Thus, we see that the LSEs are $\hat{\beta}_0 = 0.040\%$, $\hat{\beta}_1 = 0.020\%$ per beer consumed, and $\hat{\beta}_{2} = -0.0003\%$ per pound of body weight.

As in SLR, we can define the fitted value associated with the $i$th data point as $\hat{y}_i =\hat{\beta}_0 +\hat{\beta}_1 x_{i1} +\hat{\beta}_{2} x_{i2}$, and the residual associated with the $i$th data point as $e_i =y_i -\hat{y}_i$.  Here, we require a double subscripting of the $x$'s, with the first subscript is used to distinguish the individual observations and the second subscript is used to distinguish different predictors.  For example, $x_{i2}$ is the value of the second predictor for the $i$th data point. 

*Example.* Find the fitted value and residual for the first observation in the data set, a $x_2=132$ lb person who drank $x_1=5$ beers and had a BAC of $y=0.1$.  Answer: $\hat{y}_1 =0.092$ and $e_1 =0.008$.

We can define the error sum of squares as $SSE=\sum_{i=1}^n e_i^2 = \sum_{i=1}^n \left(y_i -\hat{y}_i \right)^2$.   How many df are associated with the SSE?  In this model, there are $n-3$ df associated with the SSE, because 3 parameters are needed to determine the mean component of the model.  As in SLR, we can estimate the error variance $\sigma_{\varepsilon}^2$ with the MSE, although now we must be careful to divide by the appropriate df:
\[
s_\varepsilon^2 = MSE = \frac{SSE}{n-3}.
\] 
For the model above, $s_\varepsilon = 0.010\%$.

In general, the equation for an MLR model with any number of predictors can be written:  
\[
y_i =\beta_0 +\beta_1 x_{i1} +\beta_2 x_{i2} +\ldots +\beta_k x_{ik} +\varepsilon_i 
\] 
The error term is subject to the standard assumptions of independence, constant variance, and normality.  We will use the notation that $k$ is the number of parameters that need to be estimated in the mean component of the model excluding the intercept.  (When counting parameters, some texts include the intercept, while others do not.  If you consult a text, check to make sure you know what definition is being used.)  The SSE will be associated with $n - (k + 1)$ df.  Thus, the estimate of $\sigma_{\varepsilon}^2$ will be
\[
s_\varepsilon^2 = MSE = \frac{SSE}{n-(k+1)}.
\] 

#### Sums of squares decomposition and $R^2$.  
The sums-of-squares decomposition also carries over from SLR.  We still have ${\rm SS(Total)} = \sum_{i=1}^n \left(y_i -\bar{y}\right)^2$, ${\rm SS(Regression)} = \sum_{i=1}^n \left(\hat{y}_i -\bar{y}\right)^2$, and ${\rm SS(Total) = SS(Regression) + SSE}$.  Thus, we can define $R^2$ using the same formula:
\[
R^2 = \frac{{\rm SS(Regression)}}{{\rm SS(Total)}} = 1- \frac{{\rm SSE}}{{\rm SS(Total)}} 
\] 
We still interpret $R^2$ as a measure of the proportion of variability in the response that is explained by the regression model.  In the BAC example, $R^2=0.952$.

### Interpreting partial regression coefficients.
The $\hat{\beta}$'s in a MLR model are called partial regression coefficients (or partial regression slopes).  Their interpretation is subtly different from SLR regression slopes.  Misinterpretation of partial regression coefficients is one of the most common sources of statistical confusion in the scientific literature.

We can interpret the partial regression coefficients geometrically.  In this interpretation, $\beta_j$ is the slope of the regression plane in the direction of the predictor $x_j$.  Imagine taking a "slice" of the regression plane.  In the terminology of calculus, $\beta_j$ is also the partial derivative of the regression plane with respect to the predictor $x_j$ (hence the term "partial regression coefficient").

Another ways to express this same idea in everyday language is that $\beta_j$ quantifies the linear association between predictor $j$ and the response when the other predictors are held constant, or while controlling for the effects of the other predictors. This is different from an SLR slope, which we can interpret as the slope of the linear association between $y$ and $x_j$ while ignoring all other predictors.  Thus, the interpretation of a partial regression coefficient *depends on the model in which the parameter is found*.  

Compare the estimated regression coefficients for the number of beers consumed in the SLR model and the MLR model that includes weight.

* Estimated SLR coefficient for no. of beers consumed: 0.018
* Estimated MLR coefficient for no. of beers consumed: 0.020

The coefficients differ because they estimate different parameters that mean different things.  The SLR coefficient estimates a slope that does not account for the effect of weight, while the MLR coefficient estimates a slope that does account for the effect of weight.

@gelman2020regression's interpretation of regression coefficients extends nicely here.  Recall that we interpreted the SLR slope as saying that if we compare two individuals who consume different numbers of beers (call those values $x_1$ and $x_2$), then the average difference in the individuals' BAC equals $0.018 \times (x_1 - x_2)$.  In a multiple regression context, we would now say that if we compare two individuals *who weigh the same* but who consume different numbers of beers, then the average difference in the individuals' BAC equals $0.020 \times (x_1 - x_2)$.  Thus, the partial regression coefficient tells us something different than the simple regression coefficient.  Therefore, we are not surprised that the values differ.

As a final point, note that our interpretation of the partial regression coefficient 0.020 above does not depend on the particular weight of the two individuals that we are comparing; all that matters is that the two individuals compared weigh the same.  Thus, if we are comparing two 100-pound individuals, one of whom has comsumed 1 beer and the other who has consumed 3 beers, then we estimate that the person who drank 3 beers would have a BAC 0.040 larger than the person who drank 1 beer.  Under the current model, this would also be true if we compared two 250-point individuals, one of whom had comsumed 1 beer and the other who had consumed 3 beers.  All that matters, so far, is that the individuals to be compared weigh the same.  That said, knowing what we do about human physiology, we might that this value should differ depending on whether we are comparing two 100-pound individuals or two 250-pound individuals.  This idea --- that the association between one predictor and the response depends on the value of another predictor --- is the idea of a *statistical interaction*.  We will encounter interactions soon.

Here's another example from the world of food science.  As cheese ages, various chemical processes take place that determine the taste of the final product.  These data are from the @moore1989introduction.  The response variable is the taste scores averaged from several tasters.  There are three predictors that describe the chemical content of the cheese.  They are:

* acetic: the natural log of acetic acid concentration
* h2s: the natural log of hydrogen sulfide concentration
* lactic: the concentration of lactic acid

Here is a "pairs plot" of the data.  In this plot, each panel is a scatterplot showing the relationship between two of the four variables in the model.  Pairs plots are useful ways to gain a quick grasp of the structure in the data and how the constituent variables are related to one another.

```r
cheese <- read.table("data/cheese.txt", head = T, stringsAsFactors = T)
pairs(cheese)
```

<img src="02-MultipleRegression_files/figure-html/unnamed-chunk-4-1.png" width="480" style="display: block; margin: auto;" />
Let's entertain a model that uses all three predictors.  

```r
cheese_regression <- lm(taste ~ Acetic + H2S + Lactic, data = cheese)
summary(cheese_regression)
```

```
## 
## Call:
## lm(formula = taste ~ Acetic + H2S + Lactic, data = cheese)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -17.390  -6.612  -1.009   4.908  25.449 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept) -28.8768    19.7354  -1.463  0.15540   
## Acetic        0.3277     4.4598   0.073  0.94198   
## H2S           3.9118     1.2484   3.133  0.00425 **
## Lactic       19.6705     8.6291   2.280  0.03108 * 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 10.13 on 26 degrees of freedom
## Multiple R-squared:  0.6518,	Adjusted R-squared:  0.6116 
## F-statistic: 16.22 on 3 and 26 DF,  p-value: 3.81e-06
```

Compare this MLR model with each of the three possible SLR models:

```r
slr1 <- lm(taste ~ Acetic, data = cheese)
summary(slr1)
```

```
## 
## Call:
## lm(formula = taste ~ Acetic, data = cheese)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -29.642  -7.443   2.082   6.597  26.581 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept)  -61.499     24.846  -2.475  0.01964 * 
## Acetic        15.648      4.496   3.481  0.00166 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 13.82 on 28 degrees of freedom
## Multiple R-squared:  0.302,	Adjusted R-squared:  0.2771 
## F-statistic: 12.11 on 1 and 28 DF,  p-value: 0.001658
```

```r
slr2 <- lm(taste ~ H2S, data = cheese)
summary(slr2)
```

```
## 
## Call:
## lm(formula = taste ~ H2S, data = cheese)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -15.426  -7.611  -3.491   6.420  25.687 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -9.7868     5.9579  -1.643    0.112    
## H2S           5.7761     0.9458   6.107 1.37e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 10.83 on 28 degrees of freedom
## Multiple R-squared:  0.5712,	Adjusted R-squared:  0.5558 
## F-statistic: 37.29 on 1 and 28 DF,  p-value: 1.374e-06
```

```r
slr3 <- lm(taste ~ Lactic, data = cheese)
summary(slr3)
```

```
## 
## Call:
## lm(formula = taste ~ Lactic, data = cheese)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -19.9439  -8.6839  -0.1095   8.9998  27.4245 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -29.859     10.582  -2.822  0.00869 ** 
## Lactic        37.720      7.186   5.249 1.41e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 11.75 on 28 degrees of freedom
## Multiple R-squared:  0.4959,	Adjusted R-squared:  0.4779 
## F-statistic: 27.55 on 1 and 28 DF,  p-value: 1.405e-05
```

What do you make of the fact that an SLR analysis suggests that there is a (statistically significant) positive relationship between acetic acid concentration and taste, yet the partial regression coefficient associated with acetic acid concentration is not statistically significant in the MLR model?

---

<span style="color: gray;"> The fact that the interpretation of regression parameters depends on the context of the model in which they are found cannot be overemphasized.  I speculate that much of the confusion surrounding this point flows from the arguably deficient notation that we use to write regression models.  Consider the beer data.  When we compare the simple regression model $y = \beta_0 + \beta_1 x_1 + \varepsilon$ and the multiple regression model $y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \varepsilon$, it seems natural to conclude that the parameter $\beta_1$ means the same thing in both models.  But the parameters differ, as we have seen.  In a different world, we might imagine notation that makes this context-dependence explicit, by writing something like $\beta_1(x_1)$ for the regression coefficient associated with $x_1$ in a model that includes only $x_1$, and $\beta_1(x_1, x_2)$ for the regression coefficient associated with $x_1$ in a model that includes both $x_1$ and $x_2$.  But such notation would quickly become unwieldy.  So we are stuck with the notation that we have, and must avoid the confusion that it can create.</span>

---

### Visualizing a multiple regression model

How do you visualize a multiple regression model?  With two predictors (as in our current BAC model), the regression fit is a (rigid) plane in three-dimensional space.  So, we have a fighting chance of making a picture of the model fit if we have good graphics software handy.  However, once we enocunter models with more than two predictors, the regression fits become increasingly high-dimensional objects that we humans won't be able to visualize in full.  So the best we can do in general is to think about the bivariate relationship implied between each predictor and the response.

We'll continue to use the BAC model as an example.  For starters, we might consider plotting the implied relationship between the predicted response and a predictor when the other predictors are set at their average value.  In the BAC model, that means plotting the relationship between the predicted BAC and beers consumed for an individual of average weight, where by average weight we mean equal to the average weight of the individuals in the data set.  We can couple this with a plot of predicted BAC vs weight for an individual who has consumed an average number of beers, where again we determine this average based on the average value that appears in the data set.  Here are those plots for the BAC model:


```r
par(mfrow = c(1, 2))

b.hat <- coefficients(fm2)  # extract LSEs of regression coefficients
avg.beers <- mean(beer$beers)
avg.weight <- mean(beer$weight)

plot(BAC ~ beers, type = "n", xlab = "beers consumed", data = beer)  # set up axes
abline(a = b.hat[1] + b.hat[3] * avg.weight, b = b.hat[2])  # predicted BAC for average weight individual
legend("topleft", leg = paste("weight = ", round(avg.weight, 0), "lbs"), bty = "n")

plot(BAC ~ weight, type = "n", data = beer)  # set up axes
abline(a = b.hat[1] + b.hat[2] * avg.beers, b = b.hat[3])  # predicted BAC for average weight individual
legend("topleft", leg = paste("beers consumed = ", round(avg.beers, 1)), bty = "n")
```

<img src="02-MultipleRegression_files/figure-html/unnamed-chunk-7-1.png" width="672" style="display: block; margin: auto;" />

It's not clear whether we should overlay the data on these plots.  Data are helpful, but showing the data points would suggest that the fitted line is a simple regression fit.  We want to be clear that it isn't.

### Statistical inference for partial regression coefficients

Statistical inference for partial regression coefficients proceeds in the same way as statistical inference for SLR slopes.  Standard errors for both partial regression coefficients are provided in the R output: $s_{\hat{\beta}_1 }$= 0.0013, $s_{\hat{\beta}_{2} }$= 0.000057.  Under the standard regression assumptions, the quantity $t=(\hat{\beta}_i -\beta _i )/s_{\hat{\beta}_i }$ has a $t$-distribution.  The number of degrees of freedom is the number of df associated with the SSE.  This fact can be used to construct confidence intervals and hypothesis tests.

Most software will do the needed math for us.  For example, to find 99\% CIs for the partial regression coefficients in the BAC model, we can use

```r
confint(fm2, level = 0.99)
```

```
##                     0.5 %        99.5 %
## (Intercept)  0.0084354279  0.0712912780
## beers        0.0161715108  0.0237799132
## weight      -0.0005335564 -0.0001920855
```
Thus a 99\% CI for the partial regression coefficient associated with weight is given by the interval from -0.00053 to -0.00019.

Most statistical software will automatically provide tests of the null $H_0$: $\beta_i =0$ vs. the alternative $H_a$: $\beta_i \ne 0$.  For example, the R output above tells us that the $p$-value associated with this test for the partial regression coefficient associated with weight is $p = 0.000023$. 

If we were reporting this analysis in scientific writing, we might say that when comparing people who have consumed the same number of beers, every 1-lb increase in weight is associated with an average BAC decrease of 3.6% $\times$ 10$^{-4}$ (s.e. 5.7% $\times$ 10$^{-5}$). This association is statistically significant ($t_{13} =-6.40$, $p < .001$).

### Prediction

As in SLR, we distinguish between predictions of the average response of the population at a new value of the predictors vs.\ the value of a single future observation.  The point estimates of the two predictions are identical, but the value of a single future observation is more uncertain.  Therefore, we use a prediction interval for a single observation and a confidence interval for a population average.  The width of a PI or CI is affected by the same factors as in SLR.  In MLR, the width of the PI or CI depends on the distance between the new observation and the "center of mass" of the predictors in the data set.  For example, if we now use the BAC model to predict the BAC of a 170-lb individual who consumes 4 beers:


```r
new.data <- data.frame(weight = 170, beers = 4)
predict(fm2, newdata = new.data, interval = "prediction")
```

```
##          fit        lwr        upr
## 1 0.05808664 0.03480226 0.08137103
```

```r
predict(fm2, newdata = new.data, interval = "confidence")
```

```
##          fit        lwr        upr
## 1 0.05808664 0.05205732 0.06411596
```

## F-tests for several regression coefficients {#F-test}

Consider a general multiple regression model with $k$ predictors:
\[
y=\beta_0 +\beta_1 x_1 +\beta_2 x_2 +...+\beta_k x_k +\varepsilon 
\] 
So far, we've seen how to test whether any one individual partial regression coefficient is equal to zero, i.e., $H_0 :\beta_j =0$  vs.  $H_a :\beta_j \ne 0$.   We will now discuss how to generalize this idea to test if multiple partial regression coefficients are simultaneously equal to zero.  The statistical method that we will use is an $F$-test.  

Unfortunately, we don't yet have a great context for motivating $F$-tests.  We will see more compelling motivations for $F$-tests soon when we consider [categorical predictors](#categorical-predictors).  For now, we'll consider our multiple regression model for the BAC data with the two predictors "beers consumed" and "weight".  Recall that the equation for this model is given in eq. \@ref(eq:beer-two-predictors).  Let's suppose that we were interested in testing the null hypothesis that neither of the predictors have a linear association with the response, versus the alternative hypothesis that at least one predictor (and perhaps both) has a linear association with the response.  In symbols, we would write the null hypothesis as $H_0 :\beta_1 =\beta_2 =0$ and the alternative as $H_a :\beta_1 \ne 0{\rm \; or\; }\beta_2 \ne 0$.  

$F$-tests are essentially comparisons between models.  The model that provides the context for the null hypothesis is the "full" model, in the sense that it will prove to be the more flexible of our two models to be compared.  In the context of our present example, the full model is given by \@ref(eq:beer-two-predictors). The full model is then compared to a "reduced" model, which is the full model constrained by the null hypothesis.  In the present example, when we constrain the full model by the null hypothesis $\beta_1 =\beta_2 =0$, we are left with a reduced model that includes only the intercept:
\begin{equation}
y = \beta_0 + \varepsilon.
\end{equation}

Formally, it is important to note that the reduced model is a special case of the full model.  The statistical jargon for this observation is that the reduced model is "nested" in the full model, or (to say it in the active voice) the full model "nests" the reduced model.

Because the full model nests the reduced model, we know that the full model is guaranteed to explain at least as much of the variation in the response as the reduced model. The operative question is whether the improvement obtained by the full model is statistically significant, that is, whether it is more of an improvement than we would expect by random chance.

An $F$-test proceeds by fitting both the full and reduced model, and for each model recording the SSE and the df associated with the SSE.  An $F$-statistic is then calculated as  
\[
F=\frac{\left[SSE_{reduced} -SSE_{full} \right]/\left[df_{reduced} -df_{full} \right]}{{SSE_{full} / df_{full} }} 
\]
where by $df_{full}$ and $df_{reduced}$ we mean the df associated with the SSE of the full and reduced models, respectively.  Roughly, the numerator of the $F$-statistic quantifies the improvement in fit that the full model provides relative to the reduced model.  The denominator quantifies the variation in the response left unexplained by the full model.  Both numerator and denominator are standardized by their associated df to create an apples-to-apples comparison. The larger the numerator is relative to the denominator, the less likely it is that the improvement in fit was strictly due to random chance.

For the BAC data, we could compute the $F$-statistic "manually" by first fitting both models and extracting the SSE:

```r
full.model <- lm(BAC ~ beers + weight, data = beer)
(sse.full <- sum(resid(full.model)^2))
```

```
## [1] 0.001408883
```

```r
reduced.model <- lm(BAC ~ 1, data = beer)
(sse.full <- sum(resid(reduced.model)^2))
```

```
## [1] 0.029225
```
Then our $F$-statistic evaluates to:
\[
F=\frac{\left[0.0292 - 0.0014 \right]/\left[15 - 13 \right]}{{0.0014 / 13 }} = 128.3 
\]

If the null hypothesis is true, then the $F$-statistic will be drawn from an $F$ distribution with numerator df equal to $df_{reduced} -df_{full}$, and denominator df equal to $df_{full}$.  Evidence against the null and in favor of the alternative comes from large values of the $F$-statistic.  The $p$-value associated with the test is the probability of observing an $F$-statistic at least as large as the one observed if the null hypothesis is true.  

In R, this $p$-values can be found with the command `pf`.

```r
pf(128.3, df1 = 2, df2 = 13, lower = FALSE)
```

```
## [1] 2.760276e-09
```
Thus, our $p$-value is infinitesimal.  In light of these data, it is almost completely implausible that there is no linear association between BAC and both the number of beers consumed and the individual's weight.

We've taken the above calculations slowly so that we can understand their logic.  In R, we can use the `anova` command to execute the $F$-test in one fell swoop.

```r
anova(reduced.model, full.model)
```

```
## Analysis of Variance Table
## 
## Model 1: BAC ~ 1
## Model 2: BAC ~ beers + weight
##   Res.Df       RSS Df Sum of Sq      F    Pr(>F)    
## 1     15 0.0292250                                  
## 2     13 0.0014089  2  0.027816 128.33 2.756e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

### Model utility tests

The test that we have just conducted for the BAC data turns out to be a type of $F$-test called the *model utility test*.  In general, with the general regression model
\[
y=\beta_0 +\beta_1 x_1 +\beta_2 x_2 +...+\beta_k x_k +\varepsilon 
\] 
the model utility test is a test of the null hypothesis that all the regression coefficients equal zero, that is, $H_0 :\beta_1 =\beta_2 =...=\beta_k =0$ vs.\ the alternative that at least one of the partial regression coefficients is not equal to zero.  In other words, it is a test of whether the regression model provides a significant improvement in fit compared a simpler model that assumes the $y$'s are a simple random sample from a Gaussian distirbution.  The model utility test is easy for computer programmers to automate, so it is usually included as part of the standard regression output.  In the R `summary` of a regression model, we can find it at the very end of the output.

```r
full.model <- lm(BAC ~ beers + weight, data = beer)
summary(full.model)
```

```
## 
## Call:
## lm(formula = BAC ~ beers + weight, data = beer)
## 
## Residuals:
##        Min         1Q     Median         3Q        Max 
## -0.0162968 -0.0067796  0.0003985  0.0085287  0.0155621 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  3.986e-02  1.043e-02   3.821  0.00212 ** 
## beers        1.998e-02  1.263e-03  15.817 7.16e-10 ***
## weight      -3.628e-04  5.668e-05  -6.401 2.34e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.01041 on 13 degrees of freedom
## Multiple R-squared:  0.9518,	Adjusted R-squared:  0.9444 
## F-statistic: 128.3 on 2 and 13 DF,  p-value: 2.756e-09
```

Although the model utility test has a grandiose name, it is rarely interesting.  Rejecting the null in the model utility test is usually not an impressive conclusion (@quinn2002experimental).  You may have also noticed that in SLR the model utility test always provided a $p$-value that was exactly equal to the $p$-value generated for the test of $H_0$: $\beta_1 =0$ vs. $H_a$: $\beta_1 \ne 0$.  Can you figure out why this is so?

<!-- ### More on the relationship between $t$-tests and $F$-tests. -->

<!-- This section started to seem unnecessary.  Maybe revisit later and condense to something much shorter? -->

<!-- In linear statistical models (regression and ANOVA), we encounter predominantly two types of tests, $t$-tests and $F$-tests.  We have already seen how $t$-tests work.  $t$-tests are used to test null hypotheses that have a single equality.  In general notation, if we have a generic parameter $\theta$, a $t$-test tests $H_0$: $\theta = \theta_0$ vs. either the two-sided alternative $H_a$: $\theta \ne \theta_0$ or either of the one-sided alternatives $H_a$: $\theta < \theta_0$ or $H_a$: $\theta > \theta_0$.  One calculates a one- or two-tailed $p$-value based on whether the alternative hypothesis is one- or two-sided. -->

<!-- $F$-tests are used to test null hypotheses that either multiple parameters or multiple combinations of parameters are simultaneously equal to 0.  For example, in the fish data, we have just tested hypotheses $H_0 :\beta_2 =\beta_3 =0$ vs. $H_a :\beta_2 \ne 0{\rm \; or\; }\beta_3 \ne 0$.  The mechanics of an $F$-test are such that the only viable alternative is a two-sided alternative.  (This is actually not entirely true, but we can take it as true for our purposes in these notes.)  $P$-values are found by comparing $F$-statistics to $F$-distributions.  $F$-distributions are specified by two separate degrees of freedom, which we call the numerator degrees of freedom (ndf) and the denominator degrees of freedom (ddf).  The ndf will typically be the number of equalities needed to specify the null hypothesis, and the denominator df will be the number of df associated with the SSE for the full model.  -->

<!-- Note that $F$-statistics take only positive values.  The larger the value of the $F$-statistic, the more evidence the data provide against the null.  Thus, with an $F$-test, the $p$-value is always the area to the right of the observed statistic, or the probability of observing a test statistic at least as large as the value observed.  Consequently, the terminology associated with an $F$-test can be mildly confusing: the $p$-value is always a one-tailed $p$-value, but it is used to test a two-sided alternative hypothesis. -->

<!-- Finally, $F$-tests can also be used to test null hypotheses with single equalities.  That is, we can use an $F$-test to test $H_0$: $\theta = \theta_0$, but with an $F$-test we can only consider the two-sided alternative $H_a$: $\theta \ne \theta_0$. (To put this more in the form of an $F$-test, we might re-write the hypotheses as $H_0$: $\theta - \theta_0 = 0$ and $H_a$: $\theta - \theta_0 \ne 0$.)  Thus, in some sense, an $F$-test is a generalization of a $t$-test.  However, only a $t$-test can be used to test $H_0$: $\theta =\theta_0$ vs. the one-sided alternatives $H_a$: $\theta < \theta_0$ or $H_a$: $\theta > \theta_0$.   -->

## Categorical predictors {#categorical-predictors}

So far, we have dealt exclusively with quantitative predictors. Although we haven’t given it much thought, a key feature of quantitative predictors is that their values can be ordered, and that the distance between ordered values is meaningful. For example, in the BAC data, a predictor value of $x=3$ beers consumed is greater than $x=2$ beers consumed. Moreover, the difference between $x=2$ and $x=3$ beers consumed is exactly one-half of the distance between $x=2$ and $x=4$ beers consumed. Another way to think about quantitative predictors is that we could sensibly place all of their values on a number line.

Categorical variables are variables whose values cannot be sensibly placed on a number line. Examples include ethnicity or brand of manufacture. We use indicator variables as devices to include categorical predictors in regression models.^[Whether a variable should be treated as quantitative or categorical is not always clear cut.  For example, consider an individual's political leaning, and suppose that for the sake of argument we could classify each individual's political leaning as either conservative, moderate, or liberal.  Most everyone would agree that these three values have a natural ordering, with moderates occupying a middle ground between conservatives and liberals.  However, the operative question for the analyst is whether to assume that moderates are exactly halfway between conservatives and liberals.  In other words, is the difference between conservatives and moderates exactly the same size as the difference between moderates and liberals?  If we are willing to make this assumption, then we could code political leaning with a quantitative variable, taking values (say) 0, 1, and 2 for conservatives, moderates, and liberals, respectively. If we weren't willing to make this assumption, then we should treat political leaning as a categorical variable with three levels.  There is scope here for thoughtful analysts to disagree.]

*Example.* D. K. Sackett investigated mercury accumulation in the tissues of large-mouth bass (a species of fish) in several lakes in the Raleigh, NC area (@sackett2013influence).  We will examine data from three lakes: Adger, Bennett's Millpond, and Waterville.  From each lake, several fish were sampled, and the mercury (Hg) content of their tissues was measured.  Because fish are known to accumulate mercury in their tissues as they age, the age of each fish (in years) was also determined.  The plot below shows the mercury content (in mg / kg) for each fish plotted vs.\ age, with different plotting symbols used for the three lakes.  To stabilize the variance, we will use the log of mercury content as the response variable.  There are $n=23$ data points in this data set.

```r
fish <- read.table("data/fish-mercury.txt", head = T, stringsAsFactors = T)

with(fish, plot(log(hg) ~ age, xlab = "age (years)", ylab = "log(tissue Hg)", type = "n"))
with(subset(fish, site == "Adger"), points(log(hg) ~ age, pch = "A"))
with(subset(fish, site == "Bennett"), points(log(hg) ~ age, pch = "B"))  
with(subset(fish, site == "Waterville"), points(log(hg) ~ age, pch = "W"))
```

<img src="02-MultipleRegression_files/figure-html/unnamed-chunk-14-1.png" width="480" style="display: block; margin: auto;" />

With these data, we would like to ask: when comparing fish of the same age, is there evidence that tissue mercury content in fish differs among the three lakes?  To do so, we need to develop a set of $indicator$ variables to capture the differences among the lakes.  As is often the case, one has options here.  If you wish, you can construct the indicator variables manually, perhaps using a spreadsheet program.  Alternatively, most computer programs, including R, will create indicator variables automatically.  We'll sketch out the ideas behind indicator variables first and then see how R creates them.

To create a set of indicator variables, we first need to choose one level of the variable as the reference level or baseline. While the scientific context of a problem will sometimes make it more natural to designate one level as a reference, the choice is often arbitrary.  In all cases, the choice of a reference level will not affect the ensuing analysis. For every level other than the reference, we create a separate indicator variable that is equal to 1 for that level and is equal to 0 for all levels. Thus, to include a categorical variable with $c$ different levels, we need $c−1$ indicator variables.

To see how R constructs indicator variables, we can use the `contrast` command^[The `contrast` command does much more than report how indicator variables are coded for a categorical variable, as we will see later.]

```r
contrasts(fish$site)
```

```
##            Bennett Waterville
## Adger            0          0
## Bennett          1          0
## Waterville       0          1
```
In the R output above, each column represents an indicator variable, the rows give the levels of the categorical variable, and the numbers give the coding of each indicator variable.  We see that R has created two indicator variables (as we'd expect), and labeled them "Bennett" and "Waterville".  The names of the indicator variables provide strong hints about their coding, but to be completely sure we can inspect the numerical coding given below.  The indicator labeled "Bennett" takes the value of 1 when `site` equals `Bennett` and takes the value of zero otherwise.  In other words, it is the indicator for Bennett.  The indicator labeled "Waterville" takes the value of 1 when `site` equals `Waterville` and takes the value of zero for the other two sites.  In other words, it is the indicator for Waterville.  Adger, therefore, is the reference site.^[If we prefer, it is easy enough to select a different reference level in R.  Suppose we wanted Bennett to serve as the reference site.  We could do so with the code `contrasts(fish$site) <- contr.treatment(n = 3, base = 2)` See the R documentation for `contr.treatment` for more details.]

Now let's fit a regression model with both `age` and `site` as predictors.

```r
fish_model <- lm(log(hg) ~ age + site, data = fish)
summary(fish_model)
```

```
## 
## Call:
## lm(formula = log(hg) ~ age + site, data = fish)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.47117 -0.08896  0.03796  0.13910  0.31327 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -1.80618    0.15398 -11.730 3.80e-10 ***
## age             0.14309    0.01967   7.276 6.66e-07 ***
## siteBennett     0.07107    0.12910   0.550   0.5884    
## siteWaterville -0.26105    0.10817  -2.413   0.0261 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2084 on 19 degrees of freedom
## Multiple R-squared:  0.7971,	Adjusted R-squared:  0.765 
## F-statistic: 24.88 on 3 and 19 DF,  p-value: 8.58e-07
```
The R output gives us information about the partial regression coefficients associated with `age` and with each of the indicator variables for `site`.  In an equation, we could write this model as
\[
y=\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\beta_3 x_3 +\varepsilon 
\] 
where $x_1$ gives the age of the fish and $x_2$ and $x_3$ are the indicator variables for Bennett and Waterville, respectively. 

While the R output gives us tests of the significance of the individual regression coefficients, we want to determine whether there are significant differences among the three sites when comparing fish of the same age.  To answer this question, we need to test the hypothesis $H_0: \beta_2 = \beta_3 = 0$.  We can do this with an [$F$-test](#F-test).

```r
fish_model_full <- lm(log(hg) ~ age + site, data = fish)
fish_model_reduced <- lm(log(hg) ~ age, data = fish)
anova(fish_model_reduced, fish_model_full)
```

```
## Analysis of Variance Table
## 
## Model 1: log(hg) ~ age
## Model 2: log(hg) ~ age + site
##   Res.Df     RSS Df Sum of Sq      F  Pr(>F)  
## 1     21 1.17251                              
## 2     19 0.82529  2   0.34722 3.9969 0.03558 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```
Thus, we see that there are significant differences among the sites, when comparing fish of the same age ($F_{2, 19} = 4.00$, $p = 0.036$). 

For what it's worth, we also could have obtained this test by running the `anova` command on the full model.

```r
anova(fish_model_full)
```

```
## Analysis of Variance Table
## 
## Response: log(hg)
##           Df  Sum Sq Mean Sq F value   Pr(>F)    
## age        1 2.89448 2.89448 66.6374 1.24e-07 ***
## site       2 0.34722 0.17361  3.9969  0.03558 *  
## Residuals 19 0.82529 0.04344                     
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```
The `anova` command also gives an $F$-test for the null hypothesis of no association between age and the response when comparing fish from the same lake, that is, that $\beta_1 = 0$.  This $F$-test gives the same result as the $t$-test provided in the earlier `summary`, because the two tests are identical.  Arguably, for single regression coefficients, the output from `summary` is more useful, because it gives us the estimate of $\beta_1$, while the  `anova` output only gives us an $F$-test.

Finally, now that we have established that there are significant differences among the sites when comparing fish of the same age, we can go back and make sense of the estimates of $\beta_2$ and $\beta_3$ in the full model.  The partial regression coefficients associated with an indicator variable quantify the difference between the level that the variable is an indicator for and the reference.  In other words, for the fish data, the value $\hat{\beta}_2 = 0.071$ tells us that if we compare two fish of the same age, then a fish from Bennett will have a response that is on average 0.071 larger than a fish from Adger (the reference site).  The value $\hat{\beta}_3 = -0.261$ tells us that, on average, a fish from Waterville will have a response that is 0.261 less than a similarly aged-fish from Adger.  Note that these values also give us enough information to compute the average difference in $y$ between two similarly aged fish from Bennett and Waterville, even though that value isn't directly included in the output.  

## Interactions between predictors {#regression-interactions}

Consider (again) the BAC data, with our working model
\[
y =\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\varepsilon 
\] 
where $y$ is the response (BAC), $x_1$ is beers consumed, $x_2$ is weight, and $\varepsilon$ is iid normal error.  This is an *additive* model, in the sense that the joint association between beers consumed and weight (as a pair) and BAC can be found by adding together the individual associations between each of the two predictors and the response.  However, we might instead want to allow for the possibility that the association between one predictor and the response itself depends on the value of a second predictor.  This state of affairs is called a *statistical interaction*.  More specifically, we call it a statistical interaction between the two predictors with respect to their association with the response.  Note that whether or not two predictors interact has nothing to do with whether the predictors themselves are associated.^[While the possibility of correlations among predictors is important, that is not what we are discussing here.  Instead, correlations among predictors are the subject of *collinearity*.]

An *interaction* between the two predictors allows the effect of beers consumed to depend on weight, and vice versa.  A model with an interaction can be written as:
\[
y =\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\beta_3 x_1 x_2 +\varepsilon 
(\#eq:beer-interaction)
\] 
There are two equally good ways to code this model in R:

```r
fm1 <- lm(BAC ~ beers + weight + beers:weight, data = beer)
```
or

```r
fm2 <- lm(BAC ~ beers * weight, data = beer)
```
In the first notation, the colon (:) tells R to include the interaction between the predictors that appear on either side of the colon.  In the second notation, the asterisk (*) is shorthand for both the individual predictors and their interaction.  

```r
summary(fm2)
```

```
## 
## Call:
## lm(formula = BAC ~ beers * weight, data = beer)
## 
## Residuals:
##        Min         1Q     Median         3Q        Max 
## -0.0169998 -0.0070909  0.0008463  0.0084267  0.0164373 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)   
## (Intercept)   3.010e-02  3.495e-02   0.861  0.40601   
## beers         2.162e-02  5.760e-03   3.754  0.00275 **
## weight       -2.993e-04  2.241e-04  -1.336  0.20646   
## beers:weight -1.066e-05  3.627e-05  -0.294  0.77393   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0108 on 12 degrees of freedom
## Multiple R-squared:  0.9521,	Adjusted R-squared:  0.9402 
## F-statistic: 79.57 on 3 and 12 DF,  p-value: 3.453e-08
```

*Interpreting the interaction*.  
The partial regression coefficient associated with an interaction between two predictors (call them "A" and "B") quantifies the effect that predictor A has on the linear association between predictor B and the response.  Or, equivalently, the same partial regression coefficient quantifies the effect that predictor B has on the linear association between predictor A and the response. (It may not be obvious right away that the same regression coefficient permits both interpretations, but you might be able to convince yourself that this is true by doing some algebra with the regression model.)  Thus, if we reject the null hypothesis that a partial regression coefficient associated with an interaction equals zero, then we conclude that the effects of the two predictors depend on one another.  With the BAC data, the interaction term is not statistically significant.  Thus, these data provide no evidence that the association between beers consumed and BAC depends on weight, or vice versa.  

For the sake of argument, let's examine the estimate of the interaction between beers consumed and weight, despite the fact that it is not statistically significant.  How can we interpret the value $\hat{\beta}_3 = -1.07 \times 10^{-5}$?  This value tells us how the association between beers consumed and BAC changes as weight changes.  In other words, the model predicts that a heavier person's BAC will increase less for each additional beer consumed, compared to a lighter person.  How much less?  If the heavier person weighs 1 lb more than the lighter person, then one additional beer will increase the heavier person's BAC by $1.07 \times 10^{-5}$ less than it increases the lighter person's BAC.  This agrees with our expectations, but these data do not provide enough evidence to declare that this interaction is statistically significant.  Alternatively, $\beta_3$ also tells us how the association between weight and BAC changes as the number of beers consumed changes.  That is, as the number of beers consumed increases, then the association between weight and BAC becomes more steeply negative.  Again, this coincides with our expectation, despite the lack of statistical significance.

*Interpreting partial regression coefficients in the presence of an interaction.*  
There is a major and unexpected complication that ensues from including an interaction term in a regression model.  This complication concerns how we interpret the partial regression coefficients associated with the individual predictors engaged in the interaction, that is, the regression coefficients $\beta_1$ and $\beta_2$ in eq. \@ref(eq:beer-interaction).  It turns out that, in \@ref(eq:beer-interaction), the partial regression coefficient associated with an individual predictor quantifies the relationship between that predictor and the response when the other predictor involved in the interaction equals 0.   Sometimes this interpretation is scientifically meaningful, but usually it isn't.

For example, in the BAC model that includes the interaction above, the parameter $\beta_1$ now quantifies the association between beers consumed and BAC for people who weigh 0 lbs.  Obviously, this is a meaningless quantity.  Alternatively, the parameter $\beta_2$ quantifies the association between weight and BAC for people who have consumed 0 beers.  This is a bit less ridiculous --- in fact, it makes a lot of sense --- but still requires extrapolating the model fit outside the range of the observed data, because there are no data points here for people who have had 0 beers.^[To take this argument a little further, we don't expect an association between weight and BAC for people who have not drunk any beers.  Thus, it is perhaps not surprising that we cannot reject $H_0: \beta_2 = 0$ in favor of $H_0: \beta_2 \ne 0$ in the model that includes the interaction between weight and beers consumed.]

To summarize, the subtlety here is that if we compare the additive model
\[
y =\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\varepsilon
\] 
with the model that includes an interaction
\[
y =\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\beta_3 x_1 x_2 +\varepsilon, 
\] 
the parameters $\beta_1$ and $\beta_2$ have **completely different meanings** in these two models.  In other words, adding an interaction between two predictors changes the meaning of the regression coefficient associated with the individual predictors involved in the interaction.  This is a subtlety that routinely confuses even top investigators.  It's a hard point to grasp, but essential for interpreting models that include interactions correctly.

We can ease the interpretation of partial regression coefficients in a model that includes an interaction by [centering the predictors](#centering-the-predictors).  Recall that centering the predictors means creating new versions of each predictor by subtracting off their respective averages.  For the BAC data, we could create centered versions of the two predictors by:
\[
\begin{array}{l} {x_1^{ctr} =x_1 -\bar{x}_1 } \\ {x_2^{ctr} =x_2 -\bar{x}_{2} } \end{array}
\] 
We then fit the model with the interaction, using the centered predictors instead:

```r
beer$beers.c <- beer$beers - mean(beer$beers)
beer$weight.c <- beer$weight - mean(beer$weight)
head(beer)
```

```
##     BAC weight beers beers.c weight.c
## 1 0.100    132     5  0.1875 -39.5625
## 2 0.030    128     2 -2.8125 -43.5625
## 3 0.190    110     9  4.1875 -61.5625
## 4 0.120    192     8  3.1875  20.4375
## 5 0.040    172     3 -1.8125   0.4375
## 6 0.095    250     7  2.1875  78.4375
```

```r
fm3 <- lm(BAC ~ beers.c * weight.c, data = beer)
summary(fm3)
```

```
## 
## Call:
## lm(formula = BAC ~ beers.c * weight.c, data = beer)
## 
## Residuals:
##        Min         1Q     Median         3Q        Max 
## -0.0169998 -0.0070909  0.0008463  0.0084267  0.0164373 
## 
## Coefficients:
##                    Estimate Std. Error t value Pr(>|t|)    
## (Intercept)       7.402e-02  2.849e-03  25.984 6.45e-12 ***
## beers.c           1.980e-02  1.447e-03  13.685 1.10e-08 ***
## weight.c         -3.506e-04  7.206e-05  -4.865 0.000388 ***
## beers.c:weight.c -1.066e-05  3.627e-05  -0.294 0.773927    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.0108 on 12 degrees of freedom
## Multiple R-squared:  0.9521,	Adjusted R-squared:  0.9402 
## F-statistic: 79.57 on 3 and 12 DF,  p-value: 3.453e-08
```
Note that centering the predictors does not change the estimated interaction or its statistical significance.  The main advantage of centering the predictors is that the partial regression coefficients associated with the centered versions of the predictors have a nice interpretation.  Now, the partial regression coefficients associated with the main effects quantify the relationship between the predictor and the response when the other predictor involved in the interaction equals its average value.   

<!-- Perhaps there is an interaction between gender and the number of beers consumed, with respect to their effect on BAC.^[Of course, we haven't yet found a statistically significant difference between the BAC of men vs.\ women in these data, but it is still edifying to work through this model.]   We test for such an association by including an interaction between the indicator for `male' and the number of beers consumed.  The model equation is: -->
<!-- \[ -->
<!-- y=\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\beta_3 x_3 + \beta_4 x_1 x_3 +\varepsilon  -->
<!-- \]  -->
<!-- Compare this model for males: -->
<!-- \[ -->
<!-- y=\left(\beta_0 +\beta_3 \right)+\left(\beta_1 +\beta_4 \right)x_1 +\beta_2 x_2 +\varepsilon  -->
<!-- \]  -->
<!-- and females: -->
<!-- \[ -->
<!-- y=\beta_0 +\beta_1 x_1 +\beta_2 x_2 +\varepsilon  -->
<!-- \]  -->
<!-- Thus, we see that the coefficient $\beta_4$ will quantify how the relationship between beers consumed and BAC differs for men vs.\ women (after accounting for the effect of weight).  We fit the model in R: -->

<!-- ```{r eval = FALSE} -->
<!-- fm4 <- lm(BAC ~ beers + weight + gender + beers:gender, data = beer) -->
<!-- summary(fm4) -->
<!-- ``` -->

<!-- (Note that we usually don't center indicator variables.)  Thus, we see that after controlling for the effect of weight, there is no evidence for an interaction between beers consumed and gender ($t_{11} =-1.67$, $p = 0.12$).  We could interpret this to mean that the relationship between beers consumed and BAC is the same for men and women, or to mean that the difference in BAC between men and women doesn't depend on the number of beers consumed. -->

<!-- Note: When we included the beer:gender interaction in the model, we also included the indicator male.id alone.  To not have done so would have constrained the model to have the same intercept for men and women, despite the fact that the slopes with respect to the number of beers consumed could have differed.  Such a constraint makes little sense.  This is an example of a general rule of thumb, which is:  If a model includes an interaction between two predictors, it must include each of the individual predictors as well. -->

<!-- %{\em Example:} Amanieu et al. (1989; {\em Oceanologica Acta} 12: 189-199) studied 20 sites in the Thau lagoon in southern France, and at each site measured concentrations of two types of bacteria and three environmental variables.  Here, we focus on the relationship between the concentration between one type of bacteria (`bna', as measured by the concentration of colonies grown on nutrient-rich agar) and two predictors: the total DNA production of all bacteria (`bact'), and the east-west position of the sites, as measured by a location variable `x'.  (Although it isn't entirely clear from the data, one would assume that more negative values of x correspond to more westerly locations, and more positive values of x correspond to more easterly locations.)  The first few records of the data set are: -->
<!-- % -->
<!-- %\renewcommand{\baselinestretch}{1} -->
<!-- %\begin{verbatim} -->
<!-- %    bna  bact     x -->
<!-- %    1 4.615 0.274 -8.75 -->
<!-- %    2 5.226 0.213 -6.75 -->
<!-- %    3 5.081 0.134 -5.75 -->
<!-- %    4 5.278 0.177 -5.75 -->
<!-- %    5 5.756 0.091 -3.75 -->
<!-- %    6 5.328 0.272 -2.75 -->
<!-- %    ... -->
<!-- %\end{verbatim} -->
<!-- %\renewcommand{\baselinestretch}{1.5} -->
<!-- %A model with both predictors and an interaction suggest that the effect of bacterial productivity on the response changes as one moves from west to east: -->
<!-- %\renewcommand{\baselinestretch}{1} -->
<!-- %\begin{verbatim} -->
<!-- %Coefficients: -->
<!-- % -->
<!-- %            Estimate Std. Error t value Pr(>|t|)     -->
<!-- %(Intercept)  5.94209    0.17113  34.723 9.56e-16 *** -->
<!-- %bact        -2.48767    0.57673  -4.313 0.000615 *** -->
<!-- %x           -0.01395    0.03768  -0.370 0.716323     -->
<!-- %bact:x       0.32766    0.13222   2.478 0.025583 *   -->
<!-- %\end{verbatim} -->
<!-- %\renewcommand{\baselinestretch}{1.5} -->

<!-- %% Add visualization here? -->


## (Multi-)Collinearity {#collinearity}

*What is collinearity?* To quote the Quinn \& Keough text (p. 127), "One important issue in multiple linear regression analysis, and one that seems to be ignored by many biologists who fit multiple regression models to their data, is the impact of correlated predictor variables on the estimates of parameters and hypothesis tests.  If the predictors are correlated, then the data are said to be effected by (multi)collinearity. ... Lack of collinearity is also very difficult to meet with real biological data"

Perfect collinearity occurs when there is a linear dependency in the design matrix.  That is to say, one of the predictors is exactly equal to a linear combination of the other predictors.  Assuming that you are on your toes, you should be able to detect and avoid perfect collinearity.  However, if predictors are strongly (but nor perfectly) correlated, trouble still lurks.  Even worse, when there are many predictors relative to the number of data points, collinearity is nearly inevitable.

Mathematically, calculating $\left(\X'\X\right)^{-1}$ with strong (but not perfect) collinearity is numerically unstable, and tends to magnify rounding errors (think of dividing by a number very close to, but not equal to zero).  Geometrically, the "plane" that we are trying to fit to the cloud of data points is not well anchored.  It is unstable, in the sense that small changes in the data can have large impacts on the estimated regression coefficients.  As Quinn \& Keough say in their text (p.\ 127): "Small changes in data or adding or deleting one of the predictor variables can change the estimated regression coefficients considerably, even changing their sign (Bowerman \& O'Connell 1990)".  This instability is not the hallmark of a trustworthy model.  As a consequence of this instability, the standard errors of the partial regression coefficients can be large.  This makes it difficult, if not impossible, to have confidence in our inferences about the estimated partial regression coefficients. 

Collinearity is *not* a problem for prediction, however.  Quoting Quinn \& Keough once more (p. 127) "as long as we are not extrapolating beyond the range of our predictor variables and we are making predictions from data with a similar pattern of collinearity as the data to which we fitted our model, collinearity doesn't necessarily prevent us from estimating a regression model that fits the data well and has good predictive power (Rawlings et al. 1998).  It does, however, mean that we are not confident in our estimates of the model parameters.  A different sample from the same population of observations, even using the same values of the predictor variables, might produce very different parameter estimates."

Collinearity is usually assessed by a variance inflation factor (VIF).  A separate VIF is calculated for each predictor in the model.  To calculate the VIF for predictor $x_j$, do the following:

1. Regress $x_j$ against all other predictors.  That is, fit a new regression model in which $x_j$ is the response.  Note that the actual response $y$ is not included in this model.
	
2. Calculate $R^2$.
	
3.  The VIF associated with predictor $x_j$ is 
\[
1/\left(1-R^2 \right)
\] 
The usual rule of thumb is that a VIF $\geq$ 10 indicates strong enough collinearity that additional measures should be taken.  As always, don't take the bright-line aspect of this rule too seriously; a VIF of 9.9 is not meaningfully different from a VIF of 10.1.^[Students often wonder why the VIF is calculated as $1/\left(1-R^2 \right)$.  Why not just use the $R^2$ value directly?  This is an understandable question.  The reason for using the VIF is that collinearity really does inflate the variance of the sampling distribution of $\beta_j$, and it inflates it by a factor equal to the VIF.  It just so happens that the VIF can be expressed in terms of the $R^2$ from a regression model in which $x_j$ is the response.  This provides a bit of a computational short-cut, and also helps develop intuition regarding the circumstances that are likely to produce a large VIF.]

There is no magic solution to collinearity.  If two predictors and a response vary in concert, then it is difficult (if not impossible) to tease apart the effect of one predictor on the response from the effect of the other predictor with a statistical model.  Each of the following techniques tries to stabilize the estimated partial regression coefficients at the expense of accepting a (hopefully) small bias.^[Here, we use "bias" in its technical sense, meaning the difference between the average of a sampling distribution of an estimate, and the parameter we are trying to estimate.  All else being equal, we prefer estimators that are unbiased.  However, sometimes a small amount of bias may be acceptable if it leads to big improvements in other properties of the estimator.]

1. Omit predictors that are highly correlated with other predictors in the model.  The justification usually offered here is that highly correlated predictors may just be redundant measures of the same thing.  As an example, if we are studying lakes, the amount of sediment in the water and the clarity of the water may be two different variables that are measuring the same characteristic.  If this is true, there is little to be gained by including both variables as predictors in a regression model.

2. Use principal components analysis (PCA) to reduce the number of predictors, and use principal components as predictors.  PCA is a multivariate statistical method that takes several variables and produces a smaller number of new variables (the "principal components") that contain the majority of the information in the original variables.  The advantage of using a principal component as a predictor is that different principal components are guaranteed to be independent of one another, by virtue of how they are calculated.  The major disadvantage of using principal components is that the newly created predictors (the principal components) are amalgams of the original variables, and thus it is more difficult to assign a scientific interpretation to the partial regression coefficients associated with each.  So, using PCA to find new predictors yields a more robust statistical model, albeit at the expense of reduced interpretability. 

## Variable selection: Choosing the best model

So far, we've learned how to construct and fit regression models that can potentially include many different types of terms, including multiple predictors, transformations of the predictors, indicator variables for categorical predictors, interactions, and polynomial terms.  Even a small set of possible predictors can produce a large array of possible models.  How do we go about choosing the "best" model?

First, we have to define what we mean by a "best" model.  What are the qualities of a good model?

1. *Parsimony*. We seek a model that adequately captures the "signal" in the data, and no more.  There are both philosophical and statistical reasons for seeking a parsimonious model.  The statistical motivation is that a model with too many unnecessary predictors is too flexible, and prone to detect spurious patterns that would not appear in repeated samples from the same population.  We call this phenomenon "overfitting", or "fitting the noise".  In technical terms, fitting a model with too many unnecessary predictors increases the standard errors of the parameter estimates.

2. *Interpretability*.  We often want to use regression models to understand associations between predictors and the response, and to shed light on the data-generating process.  This argues for keeping models simple.  There are occasions where the sole goal of regression modeling is prediction, and in this case interpretability is less important.  This is often the case in so-called "big data" applications, where prediction is the primary goal, and understanding is only secondary.  

3.*Statistical inference*.  As scientists, we are not interested merely in describing patterns in our data set.  Instead, we want to use the data to draw inferences about the population from which the sample was drawn.  Therefore, we want a model that meets the assumptions of regression, so that we can use regression theory to draw statistical inferences.

In statistical jargon, the process of choosing which predictors to include in a regression model and which to leave out is called *variable selection*.  More generally, beyond a regression context, the problem of identifying the best statistical model is called *model selection*.

We will look at several automated routines for choosing the best model.  Helpful as these routines are, they are no substitute for intelligent analysis.  Feel free to use an automated variable selection route to get started, but don't throw your brain out the window in the process.  Also, remember that there is a hierarchy among model terms in regression.  Most automated variable selection routines do not incorporate this hierarchy, so we must impose it ourselves.  In most cases, the following rules should be followed:

1. Models that include an interaction between predictors should also include the predictors individually.
2. Models that include polynomial powers of a predictor should include all lower-order terms of that predictor.

Automated variable selection routines can be grouped into two types: ranking methods and sequential methods. 

### Ranking methods

Ranking methods work best when it is computationally feasible to fit every possible candidate model.  In these situations, we calculate a metric that quantifies the model's adequacy, and select the model that scores the best with respect to that metric.  There are several possible metrics to choose from, and they don't always point to the same best model.  We will look at three different metric that enjoy wide use.

Throughout this section, we will refer to the number of predictors in a model, and denote this quantity by $k$.  To remove ambiguity, what we mean in this case is the number of partial regression coefficients to be estimated.  So, for example, we would say that the model $y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \beta_3 x_1^2 + \beta_4 x_1 x_2 + \varepsilon$ has $k = 4$ predictors.

Before beginning, we should note that $R^2$ is {\em not} a good choice for a ranking metric.  This is because adding a predictor will never decrease $R^2$.  Therefore, $R^2 $ can only be used to compare models that have the same number of predictors.

1. Adjusted $R^2$.  Adjusted $R^2$ is a penalized version of $R^2$ that imposes a penalty for each additional parameter added to the model.  The (rather opaque) formula for adjusted $R^2$ is
	\[
	{\rm Adj-}R^2 =1-\frac{n-1}{n-\left(k+1\right)} \left(1-R^2 \right)
	\] 
	The model with the largest Adj-$R^2 $ is considered best.
	
2. PRESS statistic (PRESS = PRedicted Sum of Squares)
	
	+ Step 1.Remove the first data point.
		
	+ Step 2: Fit the model to the remaining data.
		
  + Step 3. Use the model from step 2 to predict the removed data point, $\hat{y}_1^* $.
		
	+ Step 4.  Add the first data point back to the data set.  
		
	+ Step 5.  Repeat steps 1-4 for each data point in turn.
	
	The PRESS statistic is $\sum_{i=1}^n\left(y_i -\hat{y}_i^* \right)^2$.  The model with the smallest PRESS statistic is considered best.
	
	The PRESS statistic is interesting because it is an example of a more general idea called *cross-validation*.  The idea of cross-validation is to remove one or more points from a data set, fit the model to the remaining data, and use the fitted model to predict the data point(s) that were removed.  A good model should accurately predict the removed data point(s).  There are many variations on the idea of cross-validation, including schemes that remove a subset of the data instead of just a single data point.  The removed data are often called the "testing data", and the data to which the model are fit (i.e., the data that are not removed) are called the "training data". 
	
3. AIC (Akaike's Information Criterion)
	
	AIC is also a penalized goodness-of-fit measure, like adjusted $R^2$.  AIC enjoys a bit more theoretical support than adjusted $R^2$, and is more versatile, although its derivation is a bit more opaque.  (As the name suggests, AIC has its roots in information theory.)  The general form of AIC involves math that is beyond the scope of ST 512, but we can write down the specific formula for regression models, which is
	\[
	AIC=n\ln \left[\frac{SSE}{n} \right]+2\left(k+1\right)
	\] 
	The smallest value of AIC is best. (Smaller here means algebraically smaller --- that is, further to the left on the number line --- not closer to zero.)   Despite its theoretical support, AIC tends to favor models with too many predictors.

<!-- Example of various ranking criteria with the cheese data: -->
<!-- \begin{center} -->
<!-- \begin{tabular}{|p{1.2in}|p{0.7in}|p{0.7in}|p{0.7in}|p{0.6in}|} \hline  -->
<!-- 	predictors & $R^2$ & Adj-$R^2$ & PRESS & AIC \\ \hline  -->
<!-- 	acetic & .302 & .277 & 5140 & 159.5 \\ \hline  -->
<!-- 	H2S & .571 & .556 & 3135 & 144.9 \\ \hline  -->
<!-- 	lactic & .496 & .478 & 3695 & 149.8 \\ \hline  -->
<!-- 	acetic, H2S & .582 & .551 & 3011 & 146.1 \\ \hline  -->
<!-- 	acetic, lactic & .520 & .485 & 3461 & 150.3 \\ \hline  -->
<!-- 	H2S, lactic & .6517 & \textbf{\underbar{.626}} & 2510 & \textbf{\underbar{140.6}} \\ \hline  -->
<!-- 	acetic, H2S, lactic & .6518 & .612 & \textbf{\underbar{2471}} & 142.6 \\ \hline  -->
<!-- \end{tabular}\end{center} -->

### Sequential methods

Sequential methods work best for problems where the set of candidate models is so vast that fitting all the candidate models is not feasible.  Because computers are faster today than they were years ago, it is now feasible to fit a large number of candidate models quickly, and thus sequential methods are less necessary today than they were years ago.  Nevertheless, the ideas are straightforward.

There are three different types of sequential methods, based on the direction in which the model is built.  In *forward selection*, we begin with the simplest possible model (namely, $y = \beta_0 + \varepsilon$), and grow the model by adding predictors to it.  In *backwards elimination*, we start with the most expansive possible model, and shrink it by removing unnecessary predictors.  In *stepwise selection*, we start with the simplest possible model (namely, $y = \beta_0 + \varepsilon$), and then merge forwards and backwards steps, either growing and shrinking the model until converging on one that cannot be improved.  Stepwise selection is used more often than the other two variants.  Each of the three procedures could possibly lead to a different best model.

Here is the algorithm for forward selection:

1. Initiation step: Start with the model $y=\beta_0 +\varepsilon$.  This is the initial "working" model.
2. Iteration step: Fit a set of candidate models, each of which differs from the working model by the inclusion of a single additional model term.  Be aware that the set of candidate models must abide our rules of thumb about hierarchy.  (That is, we wouldn't consider a model like $y = \beta_0 + \beta_1 x_1 x_2 + \varepsilon$.)
3. Ask: Do any of the candidate models improve upon the working model?  
	+ If the answer to this question is "yes", then choose the model that improves upon the working model the most.  Making this model the working model, and begin a new iteration (return to step 2).
	+ (Termination step): If the answer to this question is "no", then stop.  The current working model is the final model. 

The algorithm for backwards elimination is similar:

1. Initiation step: Start with the most expansive possible model.  Usually, this will be the model with all possible predictors, and potentially with all possible first- (and conceivably second-)order interactions.  Note that we can consider a quadratic term to be an interaction of a predictor with itself in this context.  This is the initial "working" model.
2. Iteration step: Fit a set of candidate models, each of which differs from the working model by the elimination of a single model term.  Again, be aware that the set of candidate models must abide our rules of thumb about hierarchy, so (for example) we wouldn't consider a model that removes $x_1$ if the interaction $x_1 x_2$ is still in the model.
3. (Same as forward selection.)  Ask: Do any of the candidate models improve upon the working model?  
	+ If the answer to this question is "yes", then choose the model that improves upon the working model the most.  Making this model the working model, and begin a new iteration (return to step 2).
	+ (Termination step): If the answer to this question is "no", then stop.  The current working model is the final model. 
	
The algorithm for stepwise selection initiates the algorithm with $y=\beta_0 +\varepsilon$, and then forms a set of candidate models that differ from the working model by either the addition or elimination of a single model term.  The algorithm proceeds until the working model cannot be improved either by a single addition or elimination.

So far, we have been silent about how we determine whether a candidate model improves on the working model, and if so, how to find the candidate model that offers the most improvement.  We can use any of our ranking methods at this step.  Historically, $p$-values have often been used to determine whether a candidate model improves on the working model, though this practice has largely been discontinued.  The argument against it is that using $p$-values for variable selection destroys their interpretation in the context of hypothesis testing.  This being said, *any* statistical tests can only be regarded as descriptive if the tests occur in the context of a model that has been identified by model selection.  Statistical tests only have their advertised properties if decisions about which predictors to include are made *before* looking at the data.

The `step' routine in R uses AIC as its default criterion for adding or dropping terms in stepwise selection.  Here is an example of stepwise selection with the cheese data, considering only models without interactions or polynomial terms. 

```r
fm0 <- lm(taste ~ 1, data = cheese)  # the initial model y = b0 + eps
step(fm0, taste ~ Acetic + H2S + Lactic, data = cheese)  
```

```
## Start:  AIC=168.29
## taste ~ 1
## 
##          Df Sum of Sq    RSS    AIC
## + H2S     1    4376.7 3286.1 144.89
## + Lactic  1    3800.4 3862.5 149.74
## + Acetic  1    2314.1 5348.7 159.50
## <none>                7662.9 168.29
## 
## Step:  AIC=144.89
## taste ~ H2S
## 
##          Df Sum of Sq    RSS    AIC
## + Lactic  1     617.2 2669.0 140.65
## <none>                3286.1 144.89
## + Acetic  1      84.4 3201.7 146.11
## - H2S     1    4376.7 7662.9 168.29
## 
## Step:  AIC=140.65
## taste ~ H2S + Lactic
## 
##          Df Sum of Sq    RSS    AIC
## <none>                2669.0 140.65
## + Acetic  1      0.55 2668.4 142.64
## - Lactic  1    617.18 3286.1 144.89
## - H2S     1   1193.52 3862.5 149.74
```

```
## 
## Call:
## lm(formula = taste ~ H2S + Lactic, data = cheese)
## 
## Coefficients:
## (Intercept)          H2S       Lactic  
##     -27.592        3.946       19.887
```

There is no prescription for building models automatically.  (If there were, someone would have written a computer package implementing the procedure and would be filthy rich.)  Here is one cycle of steps for building a regression model, courtesy of Dr. Roger Woodard, formerly of NCSU:

1. Examine univariate (ST 511) summaries of the data (summary statistics, boxplots, etc.).  Identify unusual values or possible problems.  (Don't take it on faith that your data are all correct!)

2. Examine scatterplots with all variables.  Find variables that are closely correlated with the response and with each other.

3. Candidate model selection: Identify a model that includes relevant variables.  Use automated selection procedures if you wish.

4: Assumption checking: Check (standardized) residuals.  Determine if polynomial terms or transformations may be needed.

5: Examine collinearity diagnostics.  Inspect VIFs and pairwise correlations between variables.  Decide if some variables may be removed or added.

6: Revision.  Add or remove terms based on steps 4-5.  Return to step 3.

7: Prediction and testing: Consider validating the model with a sample that was not included in building the model.


## Leverage, influential points, and standardized residuals

Recall that in SLR, a data point can have undue influence on the regression model if the value of the predictor, $x$, is for away from $\bar{x}$.  The same notion applies in MLR: data points can be unduly influential if their combination of predictors lies far away from the "center of mass" of the other predictors.  However, with multiple predictors, it is harder to detect influential points visually.  The *leverage* of a data point is a measure of its distance from the center of mass of the predictors, and hence its influence.  The formula for calculating leverages is complicated, so we'll use a computer to calculate them.  Leverages are usually denoted with the letter $h$, so the leverage for the $i$th data point is $h_i$.  If we looked at the equation for a leverage, however, we would discover that leverages are strictly functions of the predictors, and do not depend on th response.

To calculate leverages in R, first pass the regression model object to the function "influence.lm".  This function returns several diagnostic measures; the leverages are contained in the component called "hat".  To extract the leverages, use code like the following:


```r
fm1 <- lm(BAC ~ weight + beers, data = beer)
fm1.diagnostics <- lm.influence(fm1)
lev <- fm1.diagnostics$hat
```
Here is a look at some of the leverage values for the BAC data:

```r
head(cbind(beer, lev))
```

```
##     BAC weight beers beers.c weight.c       lev
## 1 0.100    132     5  0.1875 -39.5625 0.1118535
## 2 0.030    128     2 -2.8125 -43.5625 0.1948842
## 3 0.190    110     9  4.1875 -61.5625 0.5176556
## 4 0.120    192     8  3.1875  20.4375 0.2029871
## 5 0.040    172     3 -1.8125   0.4375 0.1111125
## 6 0.095    250     7  2.1875  78.4375 0.2588899
```
Not surprisingly, the point with the greatest leverage is the 110-lb person who drank 9 beers.

What qualifies as a large leverage?  Quinn \& Keough's text, p. 95, gives one possible rule of thumb:  "a useful criterion is that any observation with a leverage value greater than $2\left(k+1\right)/n$ should be checked (Hoaglin \& Welsch 1978)."  

{\em Standardized residuals}

Data points associated with large leverages tend to have smaller (raw) residuals.  A better choice than analyzing the raw residuals is to analyze the standardized residuals.  The formula for a standardized residual is:
\[
e_i^{(s)} =\frac{e_i}{s_\varepsilon \sqrt{1-h_i} } 
\]

The benefit of standardized residuals is that if our model assumptions are appropriate, then they should behave like an iid sample from a normal distribution with mean 0 and variance 1.  That is to say, most standardized residuals should be between -2 and +2, and only a few should be $< -3$ or $> +3$.  Some texts call these "studentized residuals" instead of standardized residuals.

R does not have a built-in function for calculating standardized residuals.  However, there is a library of functions called the MASS library that contains the function "stdres".  (MASS is an acronym for \textit{Modern Applied Statistics with S-Plus}, which is one of the standard more advanced texts for using R.  It is written by W.N. Venables and B.D. Ripley.)

```r
library(MASS)
stdres(fm1)
```

```
##          1          2          3          4          5          6          7 
##  0.8307561 -0.3611695  1.4198337 -1.0767727  0.2664003  0.6708175  1.6189097 
##          8          9         10         11         12         13         14 
## -1.6125272 -1.6623992  1.2179975 -0.2650284  0.1241508 -0.8509379 -0.0485307 
##         15         16 
##  1.0854287 -0.6259662
```

*Cook's distance.*  Leverages and standardized residuals can be combined into various quantities that measure the influence each observation has on the fitted regression line.  One of the most popular of these are Cook's distance, $D_i$.  In R, if you pass a regression model to the command "plot", it will produce four (rather sophisticated) diagnostic plots.  The last of these shows Cook's distance.

```r
plot(fm1)
```

<img src="02-MultipleRegression_files/figure-html/unnamed-chunk-27-1.png" width="480" style="display: block; margin: auto;" /><img src="02-MultipleRegression_files/figure-html/unnamed-chunk-27-2.png" width="480" style="display: block; margin: auto;" /><img src="02-MultipleRegression_files/figure-html/unnamed-chunk-27-3.png" width="480" style="display: block; margin: auto;" /><img src="02-MultipleRegression_files/figure-html/unnamed-chunk-27-4.png" width="480" style="display: block; margin: auto;" />
Observations with large values of Cook's distance merit greater scrutiny.

## Appendix: Regression as a linear algebra problem {-}

This section assumes familiarity with linear algebra.  

Ultimately, the linear statistical model (that encompasses regression and ANOVA) is a linear-algebra problem.  In short, the least-squares estimates are found by projecting the data vector (an element of $\mathcal{R}^n$) onto the linear subspace of $\mathcal{R}^n$ spanned by the predictors.  

In matrix notation, the regression equations for a model with $k$ predictors can be written compactly as 
\[
\vecy = \X \vecb + \veceps
\] 
where
\[
\vecy = \left[\begin{array}{c} y_1 \\ y_2 \\ \vdots  \\ y_n \end{array}\right],
\]
\[
\X = \left[\begin{array}{cccc} 1 & x_{11} & \cdots & x_{1k} \\ 1 & x_{21} & \cdots& x_{2k} \\ \vdots & \vdots & \vdots & \vdots  \\ 1 & x_{n1} & \cdots &  x_{nk} \end{array}\right],
\]
\[
\vecb =\left[\begin{array}{c} \beta_0  \\ \beta_1 \\ \vdots \\ \beta_k  \end{array}\right],
\]
\[
\veceps =\left[\begin{array}{c} \varepsilon_1 \\ \varepsilon_2  \\ \vdots \\ \varepsilon_n  \end{array}\right].
\] 
The most important component of the above equation is the $\X$ matrix, also called the design matrix.  Here are the first few rows of the design matrix for the BAC regression that includes beers consumed and weight as the two predictors:
\[
\X=\left[\begin{array}{ccc} {1} & {5} & {132} \\ {1} & {2} & {128} \\ {1} & {9} & {110} \\ {1} & {8} & {192} \\ {\vdots } & {\vdots } & {\vdots } \end{array}\right]
\]

The short of the long is that the vector of least-squares estimates can be found by the matrix equation
\[
\vecbhat = \left(\X'\X \right)^{-1} \X'\vecy
\] 

### Singular, or pathological, design matrices {-}

The power and beauty of the equation $\vecbhat = \left(\X'\X \right)^{-1} \X'\vecy$ is that it works for any regression or ANOVA model, not just SLR.  However, the matrix inverse $\left(\X'\X \right)^{-1}$ does not exist for every possible choice of $\X$ matrices.  Roughly, there are some pathological $\X$ matrices for which trying to find the matrix inverse $\left(\X'\X \right)^{-1}$ is equivalent to dividing by zero.  For example, suppose you are studying the effect of temperature on weight gain in fish, and measure temperature in both degrees Fahrenheit and Centigrade.  (Recall that one can convert between Fahrenheit and Centigrade by the equation F = (9/5) C + 32.)  The design matrix might be
\[
\X=\left[\begin{array}{ccc} {1} & {5} & {41} \\ {1} & {10} & {50} \\ {1} & {15} & {59} \\ {1} & {20} & {68} \end{array}\right]
\] 
where the predictor in the 2$^{nd}$ column is degrees Centigrade and the predictor in the 3$^{rd}$ column is degrees Fahrenheit.  Let's try to fit a regression model in R for some made up values of $y$:

```r
bad.data <- data.frame(y    = c(2.4, 6.1, 4.4, 7.0),
                       degC = c(5, 10, 15, 20),
                       degF = c(41, 50, 59, 68))
summary(lm(y ~ degC + degF, data = bad.data))
```

```
## 
## Call:
## lm(formula = y ~ degC + degF, data = bad.data)
## 
## Residuals:
##     1     2     3     4 
## -0.76  1.73 -1.18  0.21 
## 
## Coefficients: (1 not defined because of singularities)
##             Estimate Std. Error t value Pr(>|t|)
## (Intercept)   1.9500     1.9378   1.006    0.420
## degC          0.2420     0.1415   1.710    0.229
## degF              NA         NA      NA       NA
## 
## Residual standard error: 1.582 on 2 degrees of freedom
## Multiple R-squared:  0.5938,	Adjusted R-squared:  0.3908 
## F-statistic: 2.924 on 1 and 2 DF,  p-value: 0.2294
```

The `NA` values for the partial regression coefficient associated with degrees Fahrenheit tells us that something has gone awry.

While this may seem to be cause for concern, all that is happening here is that the model contains two predictors that are perfectly correlated with one another.  (Actually, $\left(\X'\X \right)^{-1}$ will fail to exist if the regression contains two different weighted sums of predictors that are perfectly correlated with one another.)  When regression models contain perfectly correlated predictors (or perfectly correlated weighted sums of predictors), then there is no way to separate the linear associations between each of the (combinations of) predictors and the response.  Thus, the inability to find least-squares regression estimates in some cases is just the logical consequence of the fact that it is impossible to separate the effects of two perfectly correlated predictors on a single response.

Unfortunately, the computer output doesn't provide a plain-language explanation of the problem.  Each software package behaves a bit differently when faced with a case where the matrix inverse $\left(\X'\X \right)^{-1}$ does not exist.  In R, the program `lm` lops off columns of the design matrix (starting from the right-most column) until it obtains a design matrix where the inverse $\left(\X'\X \right)^{-1}$ does exist, and the computations can proceed.  This is why the output above does not provide values for the regression coefficient associated with degrees Fahrenheit.  There is nothing about degrees Fahrenheit as opposed to degrees Celsius that makes degrees Fahrenheit a problematic predictor.  It is simply that the two predictors are perfectly correlated, so `lm` responds by deleting the last predictor given --- in this case degrees Fahrenheit --- and proceeding.

<!-- What is the common feature of design matrices that allows us to determine whether or not $\left(\X'\X\right)^{-1}$ exists?  In mathematical terms, the matrix inverse $\left(\X'\X \right)^{-1}$ will not exist if the columns of the design matrix contain a *linear dependency*.  Let's write each column of the design matrix as the vector $\vecx$, i.e., for the temperature example above,  -->
<!-- \[ -->
<!-- x_0 =\left[\begin{array}{c} {1} \\ {1} \\ {1} \\ {1} \end{array}\right],x_1 =\left[\begin{array}{c} {5} \\ {10} \\ {15} \\ {20} \end{array}\right],x_2 =\left[\begin{array}{c} {41} \\ {50} \\ {59} \\ {68} \end{array}\right] -->
<!-- \]  -->
<!-- A design matrix with $k+1$ columns has a linear dependency if and only if there exist a set of constants $\lambda_0 ,\lambda_1 ,\lambda_2 ,\ldots ,\lambda_k$ such that  -->
<!-- \[ -->
<!-- \lambda_0 x_0 +\lambda_1 x_1 +...+\lambda_k x_k =\left[\begin{array}{c} {0} \\ {0} \\ {\vdots } \\ {0} \end{array}\right] -->
<!-- \]  -->
<!-- and at least one of the $\lambda$'s is $\ne 0$.  Thus, the temperature example has a linear dependency because -->
<!-- \[ -->
<!-- -32\left[\begin{array}{c} {1} \\ {1} \\ {1} \\ {1} \end{array}\right]-\frac{9}{5} \left[\begin{array}{c} {5} \\ {10} \\ {15} \\ {20} \end{array}\right]+\left[\begin{array}{c} {41} \\ {50} \\ {59} \\ {68} \end{array}\right]=\left[\begin{array}{c} {0} \\ {0} \\ {0} \\ {0} \end{array}\right]. -->
<!-- \]  -->

### Additional results {-}

We can derive additional results by using using the theory of multivariate normal random variables.  The distributional assumptions of the regression model are encapsulated in the assumption that $\veceps$ has a multivariate normal distribution with mean $\mathbf{0}$ (a vector of 0's) and variance matrix $\sigma^2_\varepsilon \mathbf{I}$, where $\mathbf{I}$ is the ($n$-by-$n$) identity matrix.  (Recall that a variance matrix includes variances on the diagonal and covariances in the off-diagonal elements.  Thus, the statement $\bf{\Sigma} = \sigma^2_\varepsilon \mathbf{I}$ says that every component of $\veceps$ has variance $\sigma^2_\varepsilon$, and that the covariance between any two components is 0.)  Or, using $\mathcal{N}$ to denote a (univariate or multivariate) normal distribution, we can write
\[
\veceps \sim \mathcal{N}(\mathbf{0}, \sigma^2_\varepsilon \mathbf{I}).
\]
It then follows that
\[
\vecy \sim \mathcal{N}(\X \vecb, \sigma^2_\varepsilon \mathbf{I}).
\]
Let $\Exp{\cdot}$ and $\Var{\cdot}$ denote the expectation (mean) and variance of a random variable, respectively.  First, we can show that $\vecbhat$ is an ``unbiased'' estimate of $\vecb$, using the linearity of expectations:
\begin{eqnarray*}
\Exp{\vecbhat} & = & \Exp{\left(\X'\X \right)^{-1} \X'\vecy} \\
& = & \left(\X'\X \right)^{-1} \X'\Exp{\vecy} \hspace{0.5in} \mbox{(linearity)}\\
& = & \left(\X'\X \right)^{-1} \X' \X \vecb\\
& = & \vecb.
\end{eqnarray*}
Next, we can find the variance of $\vecbhat$ using a result for the variance of linear combinations of random variables:
\begin{eqnarray*}
	\Var{\vecbhat} & = & \Var{\left(\X'\X \right)^{-1} \X'\vecy} \\
	& = & \left(\X'\X \right)^{-1} \X' \Var{\vecy} \X \left(\X'\X \right)^{-1} \\
	& = & \sigma^2_{\varepsilon} \left(\X'\X \right)^{-1} \X' \X \left(\X'\X \right)^{-1} \\
	& = & \sigma^2_{\varepsilon} \left(\X'\X \right)^{-1}.
\end{eqnarray*}
The second equality above is a quadratic form, and uses the fact that $\left(\X'\X \right)^{-1}$ is symmetric (and thus equal to its transpose).  The final result, $\Var{\vecbhat} = \sigma^2_{\varepsilon} \left(\X'\X \right)^{-1}$, shows that the variances of the least squares estimates (and thus their standard errors) are proportional to the diagonal elements of $\left(\X'\X \right)^{-1}$.  This result will become important in multiple regression when we discuss multicollinearity.  

Finally, let $\vecyhat$ be a vector of fitted values, i.e., $\vecyhat = \left[ \hat{y}_1, \hat{y_2}, \ldots, \hat{y_n} \right]'$.  We can find an experssion for $\vecyhat$ simply as:
\begin{eqnarray*}
\vecyhat & = & \X \vecbhat \\
 & = & \X \left(\X'\X \right)^{-1} \X' \vecy 
\end{eqnarray*}
The matrix $\X \left(\X'\X \right)^{-1} \X'$ (sometimes called the hat matrix, because it maps $\vecy$ to $\vecyhat$) is a projection matrix, and thus the fitted values are the orthogonal projection of $\vecy$ onto the columnspace of $\X$.  The right-triangle that results from the vectors $\vecy$ (the hypotenuse), $\vecyhat$, and $\vece = \vecy - \vecyhat$ gives rise to the sum-of-squares decomposition behind $R^2$.  

There are many good texts that present the linear-algebra formulation of the linear statistical model, including @sen1997regression and @monahan2008primer.
