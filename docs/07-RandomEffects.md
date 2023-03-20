# Random effects



## Fixed vs. random effects: the big picture

In statistics, we regard each data point as a single observation from an underlying distribution, or population, of possible values.  A statistical model characterizes the structure of the underlying distributions from which the observed data are drawn.  Loosely, we think of a statistical model as combining two distinct components.  One component of the statistical model describes the mean^[The mean of a distribution is such an important and central concept in statistics that we have many different synonyms for it.  Although the following terms are not perfect synonyms (their technical definitions are all slightly different), all of the following tend to be used as loose synonyms for a mean: average, center of mass, expected value, and expectation.] of the underlying distributions, and how the mean depends on (for example) predictors in a regression model, or on the particular levels of an experimental factor in an ANOVA.  The other component of the model describes how the underlying distributions vary around their respective mean, both with respect to the underlying distribution for a single data point, and the mutual relationship among the underlying distributions for several data points (i.e., their correlations).  In colloquial terms, this distinction is often referred to as the "signal" and the "noise" in a statistical model.  To this point, we have spent nearly all of our effort thinking about how the mean, or "signal", in a statistical model depends on predictors, treatment groups, etc.  We have not yet given much thought to the noise component of the model, beyond asserting that the residual errors were iid draws from a normal distribution with a constant variance.  The concept of "random effects" will give us our first tool for building richer models for the statistical noise.

So far, although we have not used the term, all of the treatment effects in the ANOVA models that we have considered have been  *fixed effects*.  We use fixed effects when we are interested in specific levels of the corresponding treatment factor.  For example, in the hot dog data, were are interested in the three different types of hot dogs included in the experiment.  As a second example, in the reading data, we were interested in drawing inferences about the three different teaching methods.

In contrast to fixed effects, we use *random effects* when the levels of a factor included in an experiment can be regarded as a representative (random) sample from a population of levels, and we are interested in drawing inferences about that population, not just the specific levels included in the experiment.  The residual errors that we have included in all of our models so far is an example of a random effect.  For example, in the reading data, we are not interested in drawing inferences about the particular 66 students included in the study, but instead we regard these students as a random sample from a larger population, and we want to learn about that population.  Similarly, in the pine-resin data, we are not interested in limiting our inferences to the particular 24 trees included in the experiment, but we regard those particular trees as a representative sample from a population of trees to which we wish to extend our inferences.

One (imperfect) way to determine whether a factor should be treated with fixed or random effects is to ask: if the experiment were repeated, would the same levels of the factor be included in the experiment, or would the experiment include a potentially different set of levels?  If the experiment would include the same levels, then this suggests that the experimenter is interested in drawing inferences about these particular levels, and therefore fixed effects should be used.  On the other hand, if the experiment could potentially include different levels, then this suggests that the levels have been drawn from some larger population of levels, and thus random effects should be used.  

Whether we treat an effect as a fixed effect or a random effect determines the parameters that we estimate to characterize that effect.  For fixed effects, we estimate parameters that quantify the differences among the specific levels included in the experiment.  For example, with the reading data, we use parameters that allow us to draw inferences about the differences among the three reading methods in the experiment.  For random effects, we estimate a variance parameter that quantifies the spread in the distribution of the population from which the levels included in the experiment were drawn.  Thus, for the reading data, we also estimated an error variance   that quantifies the variation in responses among students who were taught with the same method.

As a bit of terminology, statistical models can be classified by whether they contain just fixed effects, just random effects, or both fixed and random effects (not counting either the intercept or the residual error term, which all models have).  A model with only fixed effects is called (sensibly enough) a fixed effects model, and a model with only random effects is called a random effects model.  Models with both fixed and random effects are called *mixed-effects models*, or sometimes just ``mixed models'' for short.  

To sum up, we draw inferences about the levels of the fixed effects included in the experiment, and the scope of those inferences extends to the population(s) from which the random effects were selected.

## Random-effects models

Models with no fixed effects (except for the intercept) are called random-effects models.  Pure random-effects models are not frequently encountered in the life sciences, but they are occasionally useful for characterizing hierarchical variability.

Example (from the on-line SAS documentation): ``In the following example from Snedecor \& Cochran (1976), an experiment is conducted to study the variability of calcium concentration in turnip greens. Four plants are selected at random; then three leaves are randomly selected from each plant. Two 100-mg samples are taken from each leaf. The amount of calcium is determined by microchemical methods.''  

<!-- The data are: -->

<!-- \begin{center} -->
<!-- 	\begin{tabular}{c|c|c|c|c|c|c|} -->
<!-- 		& \multicolumn{6}{c}{Leaf} \\ -->
<!-- 		Plant & \multicolumn{2}{c}{1} & \multicolumn{2}{c}{2} & \multicolumn{2}{c}{3} \\ \hline -->
<!-- 		1 &	3.28 & 3.09 &	3.52 &	3.48 &	2.88	 & 2.80 \\  \hline -->
<!-- 		2 &	2.46 & 2.44	 & 1.87	 & 1.92	 & 2.19	  & 2.19 \\  \hline -->
<!-- 		3 &	2.77 & 	2.66 &	3.74 & 	3.44 &	2.55 & 2.55 \\   \hline -->
<!-- 		4 &	3.78 &	3.87 &	4.07 &	4.12 &	3.31 & 	3.31 \\   \hline -->
<!-- 	\end{tabular} -->
<!-- \end{center} -->

With these data, we may want to ask: how does sample-to-sample variation within a leaf compare to leaf-to-leaf variation within a plant, and how do each of these sources of variation compare to plant-to-plant variation?

We can answer these questions with a random-effects model.  Importantly, our goal here is to quantify the sources of variation that contribute to variation among the samples.  We are not interested in quantifying the differences among these particular four plants, or among these particular leaves on these particular plants, or among these particular subsamples on these particular leaves.  Instead, each factor in our experiment --- plant, leaf and sample --- contains levels that are regarded as a random sample from a population of levels.  If we were to repeat the experiment, we might choose different plants, or different leaves, or different samples.

Let $y_{ijk}$ be the response for sample $k$ from leaf $j$ of pant $i$.  Let us try the model:
\[
y_{ijk} = \mu + P_i + L_{j(i)} + \varepsilon_{ijk}
\]
where $\mu$ is the reference level, $P_i$ is a random effect associated with plant $i$, $L_{j(i)}$ is a random effect associated with leaf $j$ of plant $i$, and $\varepsilon_{ijk}$ is the residual error .  Because the plants and leaves have been randomly selected from populations of plants and leaves, we assume
\begin{eqnarray*}
P_i & \sim & \mathcal{N}\left(0, \sigma^2_P \right) \\
L_{j(i)} & \sim & \mathcal{N}\left(0, \sigma^2_L \right) \\
\varepsilon_{ijk} & \sim & \mathcal{N}\left(0, \sigma^2_{\varepsilon} \right)
\end{eqnarray*}

In words, each of the random effects are (independently) drawn from normal distributions with distinct variances.  Note that we can think of the residual $\varepsilon_{ijk}$ as the random effect associated with sample $k$ from leaf $j$ of plant $i$.
There's something else going on with this experiment.  Namely, the "leaf" factor and the "plant" factor are not crossed.  That is, leaf "1" does not appear on each of the four plants.  Instead, each leaf is associated with one and only one plant.  Thus, the "leaf" factor is nested within the "plant" factor.  To emphasize this nesting, we have used the convention of writing the subscript for the leaf factor as $j(i)$, to emphasize that level $j$ of the leaf factor represents a different leaf for each level of the plant factor.

You may also observe that the samples are nested within leaves.  Thus, to be consistent, perhaps we should have written the error terms as $\varepsilon_{k(ij)}$.  While this is true, it is true of every experiment that we have considered so far.   (For example, in the hotdog data, replicate 1 of the 'beef' hotdogs was different from replicate "1" of the meat hotdogs.)  Because the replicates are always nested within the individual treatment combinations, the parenthetical notation for the subscripts is superfluous for the error term.

In a random effects model, our goals are not to estimate the individual $P_i$’s or $L_{j(i)}$’s themselves.  Instead, our goal is to estimate the variance parameters (aka variance components) $\sigma^2_P$, $\sigma^2_L$ and $\sigma^2_{\varepsilon}$ .  These parameters quantify the plant-to-plant variability, the leaf-to-leaf variability within each plant, and the sample-to-sample variability within each leaf, respectively.  Here is an analysis within PROC MIXED:

```{}
data Turnip;
  input Plant Leaf Sample Calcium;
  datalines;
  1 1 1 3.28
  1 1 2 3.09
  1 2 1 3.52
  ...
  4 3 2 3.31
; 

proc mixed; 
  class Plant Leaf; 
  model Calcium = ;
  random Plant Leaf(Plant); 
run;

Covariance Parameter
Estimates

Cov Parm        Estimate
Plant             0.3652
Leaf(Plant)       0.1611
Residual        0.006654
```


In PROC MIXED, we only list fixed effects on the right-hand side of the equals sign in the MODEL statement.  In this model, there are no fixed effects, so nothing is placed on the right-hand side of the equals sign.  (The reference level is a fixed effect, but it is included in every model, so it is never specified.)  In the RANDOM statement, we list the random effects.  Here, we request random effects for the individual plants, and for the leaves nested within plants.  Note that SAS uses the parenthetical notation to denote nesting as well, so that we write LEAF(PLANT) to tell SAS that the leaf factor is nested within the plant factor.  We do not have to specify a random effect for the sample error, because the sample error is equivalent to the residual error, and this is always included in the model as well.

Our parameter estimates are $\hat{\sigma}^2_P = 0.3652$, $\hat{\sigma}^2_L = 0.1611$, and $\hat{\sigma}^2_{\varepsilon} = 0.0067$.  Thus, we conclude that plant-to-plant variation is roughly twice as large as leaf-to-leaf variation within plants, and that both of these are much larger than sample-to-sample variation within leaves.

It is possible to generate an ANOVA table for the random-effects model. We could define sums-of-squares for each of the model terms in the following way:
\begin{eqnarray*}
SS(Plant) & = & \sum_{i=1}^4 \sum_{j=1}^3 \sum_{k=1}^2 \left( \bar{y}_{i++} - \bar{y}_{+++} \right)^2 \\
SS(Leaf(Plant)) & = & \sum_{i=1}^4 \sum_{j=1}^3 \sum_{k=1}^2 \left( \bar{y}_{ij+} - \bar{y}_{i++} \right)^2 \\
SS(Error) & = & \sum_{i=1}^4 \sum_{j=1}^3 \sum_{k=1}^2 \left( \bar{y}_{ijk} - \bar{y}_{ij+} \right)^2 \\
\end{eqnarray*}
Each sum-of-squares is associated with a certain number of df:
\begin{center}
	\begin{tabular}{lr} 
		Source & df \\ \hline
		Plant & 3 \ (= 4-1) \\
		Leaf(Plant) & 8 \ (= 12 - 1 - 3) \\
		Residual Error & 12 \ (= 24 - 1 - 3 - 8)\\ \hline
		Total & 23
	\end{tabular}	
\end{center}

Something different has happened here. Because leaf is nested within plant, we have to deduct the df for the SS(Plant)from the df for SS(Leaf(Plant)).  This is the general rule for nested factors.  If factor 'B' is nested in factor 'A', then the df for SS(A) are subtracted from the df for SS(B(A)).  (In fact, this explains why we've been deducting df for factorial effects from the df for error all along.  The replicates are always nested within the factorial treatments, even though we haven't thought of it in this way until now.)

In models in which the residual error is the only random effect, we have learned that the estimate of that error, $\hat{\sigma}^2_{\varepsilon}$, is just equal to the MSE.  Does this mean that we can equate MS(Plant) and MS(Leaf(Plant)) with $\hat{\sigma}^2_P$ and $\hat{\sigma}^2_L$, respectively?  Unfortunately, in this case life isn't that simple.  As it turns out, there is a relationship between each of the mean squares and each of the variance components, but it's complicated.  One way to generate an ANOVA and learn about this relationship in SAS is to use the METHOD = TYPE3 option in PROC MIXED:

```{}
proc mixed data = turnip method = type3;
  class Plant Leaf;
  model y = ;
  random Plant Leaf(Plant);
run;

Type 3 Analysis of Variance

                           Sum of
Source           DF       Squares   Mean Square  Expected Mean Square

Plant             3      7.560346      2.520115  Var(Residual) + 2 Var(Leaf(Plant))
                                                 + 6 Var(Plant)
Leaf(Plant)       8      2.630200      0.328775  Var(Residual) + 2 Var(Leaf(Plant))
Residual         12      0.079850      0.006654  Var(Residual)

Covariance Parameter
Estimates

Cov Parm        Estimate
Plant             0.3652
Leaf(Plant)       0.1611
Residual        0.006654
```

Here, we see the ANOVA table with SS, MS and df for each variance component.  We also see a column labeled "Expected Mean Square". Briefly, the expected mean square is a formula that equates the "expectation" of the mean square for each variance component to each of the model parameters.   (Remember that an "expectation" is the average value that you would observe over many runs of the same experiment.)  The derivation of this formula is beyond the scope of ST512.  With the formula in hand, though, it suggests one method of estimating the variance components.  We can estimate the variance components by equating each MS with its expected mean square, and solving for the variance-component parameters. 

Sometimes, this "method of moments" approach to parameter estimation works well.  Other times, however, it can generate negative estimates of variance parameters.  (To see this, suppose that in the example above we had a data set where MS(Leaf)  <  MS(Residual).  Then we would have to have $\hat{\sigma}^2_L < 0$, which makes no sense.)  Consequently, several other approaches have been developed to estimate variance-component parameters.  The default in PROC MIXED is to use REML, which is an acronym for REstricted Maximum Likelihood.  Although we will not discuss REML, do be aware that REML is a iterative numerical method.  In some cases, the method can fail.  The output to PROC MIXED presents an 'Iteration History' that gives you some information about the success or failure of REML.  It is good practice to review the Iteration History and the Log file to make sure the procedure converged successfully.  (We also saw a numerical method for estimating parameters when we considered non-linear regression.)

For some nice data sets (such as the one above), the method-of-moments estimates of the variance components and the REML estimates are the same.  In other cases, they may differ.  General practice is to use the REML estimates unless a good reason exists to use another method of estimation.

## Subsampling

A second technique for reducing experimental error is subsampling.  Subsampling refers to measuring the same experimental unit multiple times.  This is the first occasion we have encountered in ST512 where the experimental unit (EU) and the measurement unit (MU) differ.  Remember that the EU is the unit to which the experimental factor is applied, and the MU is the unit that is measured.

*Example*:  A former ST512 student studied the effect of 6 different vase solutions on the shelf lives of roses.  She had 30 different vases at her disposal.  The 6 different solutions were randomly assigned to the 30 vases in a balanced CRD with a one-way treatment structure.  Three roses (or stems) were placed in each vase, and the shelf life of each rose was recorded.  Here are some of the data for the cultivar "Freedom":

```{}
cultivar trt     vase    stem    lifetime
Freedom  1       1       1       13     
Freedom  1       1       2       13     
Freedom  1       1       3       11     
Freedom  1       2       1       14     
Freedom  1       2       2       11     
Freedom  1       2       3       12     
Freedom  1       3       1       14     
...
Freedom  1       5       3       13     
Freedom  2       1       1       16     
Freedom  2       1       2       16     
Freedom  2       1       3       12     
Freedom  2       2       1       13    
...
```

Here, the EU is the vase, and the MU is the stem.  The stem is not the EU!  The different stems are subsamples, in the sense that they are repeated samples of the same EU.  Subsampling does not increase the number of EUs (and hence does not increase the number of df available to estimate the experimental error), but it does reduce the experimental error.

### Equal subsamples per EU
If the number of subsamples (stems) is the same for each EU (vase), we can average the subsample data (stem lifetimes) in each EU to generate 1 data point per EU, and then analyze the EU-averages with the usual one-way ANOVA.  

To calculate an average response for each EU, we can use whatever software package is most convenient (e.g., Excel).  To calculate EU-level averages in SAS, we can use the following procedure.  Suppose we had loaded the data from a file with the following DATA step:

```{}
data freedom;
   infile ".../rose.txt" firstobs=2;
   input cultivar$ trt vase stem lifetime;
run;
```

We can then calculate average lifetimes per vase with PROC MEANS:

```{}
/* This procedure averages the stems in each vase 
   and produced a new data set named 'rosemeans' 
   that contains the newly calculated average lifetimes */

proc means data=freedom noprint;
  by cultivar trt vase;
  var lifetime;
  output out=rosemeans mean=avglife;
run;
```


We can use PROC PRINT to examine the newly created data set:

```{}
proc print data=rosemeans;
run;

Obs  cultivar    trt    vase    _TYPE_    _FREQ_    avglife
1    Freedom      1       1        0         3      12.3333
2    Freedom      1       2        0         3      12.3333
3    Freedom      1       3        0         3      13.3333
4    Freedom      1       4        0         3      12.6667
5    Freedom      1       5        0         3      12.6667
6    Freedom      2       1        0         3      14.6667
7    Freedom      2       2        0         3      13.6667
...
30   Freedom      6       5        0         3      12.0000
```

Note that there is now one observation per vase.  Now we analyze the per-vase averages using a standard one-way ANOVA analysis in PROC GLM:

```{}
proc glm data = rosemeans;
  class trt;
  model avglife = trt;
  means trt / tukey;
run;

Dependent Variable: avglife

                                        Sum of
Source                      DF         Squares     Mean Square    F Value    Pr > F
Model                        5     122.9185185      24.5837037       9.88    <.0001
Error                       24      59.6888889       2.4870370
Corrected Total             29     182.6074074

Source                      DF     Type III SS     Mean Square    F Value    Pr > F
trt                          5     122.9185185      24.5837037       9.88    <.0001

Tukey's Studentized Range (HSD) Test for avglife
Means with the same letter are not significantly different.

Tukey 
Grouping   Mean      N    trt
A       13.8667      5    2
A       13.6000      5    3
A       13.0667      5    6
A       12.6667      5    1
A       11.8667      5    5
B        7.8667      5    4
```

For comparison, suppose that instead only one rose stem had been included in each vase.  The data set ‘rose1’ was formed by extracting the first rose stem listed for each vase from the original data set:

```{}
cultivar trt     vase    stem    lifetime
Freedom  1       1       1       13     
Freedom  1       2       1       14     
Freedom  1       3       1       14     
...
Freedom  2       1       1       16     
Freedom  2       2       1       13    
...
```

Here is the same one-factor ANOVA analysis for this smaller data set:

```{}
proc glm data = freedom1;
  class trt;
  model lifetime = trt;
run;

                                        Sum of
Source                      DF         Squares     Mean Square    F Value    Pr > F
Model                        5      84.5666667      16.9133333       2.30    0.0765
Error                       24     176.4000000       7.3500000
Corrected Total             29     260.9666667

Source                      DF     Type III SS     Mean Square    F Value    Pr > F
trt                          5     84.56666667     16.91333333       2.30    0.0765
```

Now the differences among the vase treatments are no longer significant!  Note that the df's have not changed: this is because both the full and reduced data sets contain the same number of EUs.  What has changed is that the MSE for the reduced data set is much larger than the MSE for the full data set.  This is because the `unexplained variability' in the reduced data set includes both variability among vases assigned to the same treatment, and variability among individual rose stems.  In the full data set, however, the unexplained variability in each data point consists of variability among vases assigned to the same treatment, and variability in the average lifetime of three individual rose stems.  Because averaging reduces variability, the unexplained variability in the full data set is smaller than the unexplained variability in the reduced data set.

Thus, subsampling reduces experimental error by averaging out the variability among individual measurements from the same EU.

The wrong way to analyze these data is to treat the stem as the EU:

```{}
/* This is the WRONG analysis */

proc glm data = freedom;
  class trt;
  model lifetime = trt;
run;
                                        Sum of
Source                      DF         Squares     Mean Square    F Value    Pr > F
Model                        5     368.7555556      73.7511111      10.38    <.0001
Error                       84     597.0666667       7.1079365
Corrected Total             89     965.8222222

Source                      DF     Type III SS     Mean Square    F Value    Pr > F
trt                          5     368.7555556      73.7511111      10.38    <.0001
```


This analysis is wrong because it assumes that vase solutions were randomized to stems and not vases.  This is *pseudoreplication*, because the analysis incorrectly inflates the amount of replication in the experiment.  Pseudoreplication is a common and insidious mistake.

#### A second look: Analysis with a mixed effects model
The method of averaging subsamples works well when each EU has the same number of subsamples.  However, averaging subsamples does not work well when the number of subsamples per EU differs.  This is because EUs with more subsamples will have less variability than EUs with fewer subsamples, and thus the equal variance assumption of ANOVA will be violated with averaged data.  (As always, the seriousness of this violation is a matter of degree.  If, for example, 29 vases had 3 stems and one vase had 2 stems, the unequal variance caused by averaging might be sufficiently mild that one could still justify averaging.)  The best way to analyze data with unequal numbers of subsamples is to use a model that explicitly accounts for both the variation among EUs and the variation among MUs. 

To keep things from getting too complicated, we won't  look here at an example with an unequal number of subsamples per EU, but will instead consider an alternative analysis of the rose data above.  This analysis can be easily modified to accommodate unequal numbers of subsamples per EU.

Here is a model that we can use that models the data for the individual stems explicitly.  This model will work regardless of whether we have the same number of stems in each vase.  Let  be the lifetime of the kth stem from the jth vase assigned to the ith treatment.  Consider the model
\[
y_{ijk} = \mu + \alpha_i + V_{j(i)} + \varepsilon_{ijk}
\]
where 

* $i = 1, 2, \ldots, 6$ is an index for the treatment
* $j = 1, 2, \ldots, 5$ is an index for the vase in each treatment
* $k = 1, 2, 3$ is an index for the stem in each vase
* $\mu$ is a reference level
* $\alpha_i$ is the effect parameter for treatment $i$
* $V_{j(i)}$ is a random effect for vase $j$ from treatment $i$, $V_{j(i)} \sim \mathcal{N}\left(0, \sigma^2_V \right)$
* $\varepsilon_{ijk}$ is subsample (residual) error for stem $k$ from vase $j$ of treatment $i$, $\varepsilon_{ijk} \sim \mathcal{N}\left(0, \sigma^2_{\varepsilon} \right)$

The key feature of the model above is that it includes two random effects: one for the vase-to-vase error within treatments, and a second for the stem-to-stem error within vases.  We assume that the random effects for the vases are drawn from a normal distribution with mean 0 and variance $\sigma^2_V$, and that the random effects for the stems are drawn from a normal distribution with mean 0 and variance $\sigma^2_{\varepsilon}$.  

We can still think about building an ANOVA table for this analysis through a SS decomposition.  The df for each SS will be:
<table>
 <thead>
  <tr>
   <th style="text-align:left;"> source </th>
   <th style="text-align:left;"> df </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Treatment </td>
   <td style="text-align:left;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Vase (EU error) </td>
   <td style="text-align:left;"> 24 (= 30 - 1- 5) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Stems (subsample error) </td>
   <td style="text-align:left;"> 60  (= 90 - 1 - 5 - 24) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Total </td>
   <td style="text-align:left;"> 89 </td>
  </tr>
</tbody>
</table>

The key to this analysis is to realize that the treatment effects should be tested using the mean-squared error for the vases, not the stems.  This is because the vases are the EUs (treatments were randomized to vases).  Thus, any inferences that we draw about the treatments (i.e., $F$-tests, linear contrasts, or multiple comparisons) should be based on the MSE for the vases, not the MSE for the stems.  In particular, we anticipate that the $F$-test for differences among the treatments will be based on an $F$-distribution with 5 ndf and 24 ddf.

Here is PROC MIXED code and output for the Freedom  rose data, using the default REML estimation:

```{}
proc mixed data = freedom;
  class trt vase;
  model lifetime = trt;
  random vase(trt);
run;

Covariance Parameter
Estimates

Cov Parm      Estimate
vase(trt)       0.1648
Residual        6.9667

Type 3 Tests of Fixed Effects

              Num     Den
Effect         DF      DF    F Value    Pr > F
trt             5      24       9.88    <.0001
```

Remarks:
* The MODEL statement in PROC MIXED only contains the fixed effects.  In this model, the only fixed effects are the different treatments (vase solutions).
* The RANDOM statement specifies the random effects to be included in the model.  The residual (= the error term for the subsample) is always included by default, so a separate term for ``stem'' did not need to be specified.
* We use the parenthetical syntax VASE(TRT) to tell SAS that VASE is nested within TRT.   
* The portion of the output labeled ``Covariance Parameter Estimates'' provides the estimates for $\sigma^2_V$  and  $\sigma^2_{\varepsilon}$.  In this case, they are $\sigma^2_V  = 0.165$ and  $\sigma^2_{\varepsilon} = 6.97$.  Although these estimates are not the primary focus of our analysis, they are still informative.  In this case, they tell us that the stem-to-stem variability among stems in the same vase is much greater than the vase-to-vase variability among vases receiving the same treatment.
* The portion of the output labeled ``Type 3 Tests of Fixed Effects'' provides F-tests of the fixed effects.  With REML estimation, PROC MIXED does not present the full ANOVA table.  Note that the F-test for the treatment effect is identical to the F-test that we obtained by averaging subsamples within EUs.  These two tests are identical because in this case we have the same number of stems per vase.
* Note that the F-test for the treatment effects has the correct ndf and ddf.  Unless we specify otherwise, PROC MIXED uses the "containment" method for determining the appropriate error terms.  In this case, the fixed-effects for TRT are contained within the random effect for VASE(TRT) (this is another way of saying that the vases are nested within the treatement), and so PROC MIXED uses the MSE for VASE(TRT) to test for the TRT fixed effects.   The df for the F-tests can be used as a check to make sure that you've coded the model correctly.

Here is an example of using linear contrasts and multiple comparisons to explore differences among the treatments.  Note that throughout the df used for inference are the df that correspond to the vase error, not to the stem error.

Linear contrasts:

```{}
proc mixed data = freedom;
  class trt vase;
  model lifetime = trt;
  random vase(trt);
  estimate 'Difference b/w solutions 1 and 2' trt 1 -1 0 0 0 0;
run;
                                                Standard
Label                               Estimate       Error      DF    t Value    Pr > |t|
Difference b/w solutions 1 and 2     -1.2000      0.9974      24      -1.20      0.2407
```

Multiple comparisons (there is no MEANS statement available in PROC MIXED, so we use LSMEANS instead):

```{}
proc mixed data = freedom;
  class trt vase;
  model lifetime = trt;
  random vase(trt);
  lsmeans trt / pdiff adj=tukey;
run;

Least Squares Means

                             Standard
Effect    trt    Estimate       Error      DF    t Value    Pr > |t|
trt       1       12.6667      0.7053      24      17.96      <.0001
trt       2       13.8667      0.7053      24      19.66      <.0001
trt       3       13.6000      0.7053      24      19.28      <.0001
trt       4        7.8667      0.7053      24      11.15      <.0001
trt       5       11.8667      0.7053      24      16.83      <.0001
trt       6       13.0667      0.7053      24      18.53      <.0001

Differences of Least Squares Means

                                 Standard
Effect   trt   _trt   Estimate      Error     DF   t Value   Pr > |t|   Adjustment      Adj P

trt      1     2       -1.2000     0.9974     24     -1.20     0.2407   Tukey          0.8310
trt      1     3       -0.9333     0.9974     24     -0.94     0.3587   Tukey          0.9331
trt      1     4        4.8000     0.9974     24      4.81     <.0001   Tukey          0.0008
trt      1     5        0.8000     0.9974     24      0.80     0.4304   Tukey          0.9644
trt      1     6       -0.4000     0.9974     24     -0.40     0.6919   Tukey          0.9985
trt      2     3        0.2667     0.9974     24      0.27     0.7915   Tukey          0.9998
trt      2     4        6.0000     0.9974     24      6.02     <.0001   Tukey          <.0001
trt      2     5        2.0000     0.9974     24      2.01     0.0563   Tukey          0.3685
trt      2     6        0.8000     0.9974     24      0.80     0.4304   Tukey          0.9644
trt      3     4        5.7333     0.9974     24      5.75     <.0001   Tukey          <.0001
trt      3     5        1.7333     0.9974     24      1.74     0.0951   Tukey          0.5217
trt      3     6        0.5333     0.9974     24      0.53     0.5978   Tukey          0.9941
trt      4     5       -4.0000     0.9974     24     -4.01     0.0005   Tukey          0.0060
trt      4     6       -5.2000     0.9974     24     -5.21     <.0001   Tukey          0.0003
trt      5     6       -1.2000     0.9974     24     -1.20     0.2407   Tukey          0.8310
```

If we want to generate an ANOVA table, we can use the METHOD=TYPE3 option for method-of-moments estimation:

```{}
proc mixed data = freedom method = type3;
  class trt vase;
  model lifetime = trt;
  random vase(trt);
run;

Type 3 Analysis of Variance

                         Sum of
Source         DF       Squares   Mean Square  Expected Mean Square                 Error Term

trt             5    368.755556     73.751111  Var(Residual) + 3 Var(vase(trt))     MS(vase(trt))
                                               + Q(trt)
vase(trt)      24    179.066667      7.461111  Var(Residual) + 3 Var(vase(trt))     MS(Residual)
Residual       60    418.000000      6.966667  Var(Residual)                        .


Type 3 Analysis of Variance

            Error
Source         DF    F Value    Pr > F
trt            24       9.88    <.0001
vase(trt)      60       1.07    0.4015

Covariance Parameter
Estimates

Cov Parm      Estimate
vase(trt)       0.1648
Residual        6.9667
```

Again, things work out nicely here because the data are balanced, but it is not guaranteed that the method-of-moments estimation and REML estimation will match up exactly.

Variations with unbalanced data: Let's briefly consider how the df accounting might change with unbalanced data.  Consider an experiment identical to the one above, except that now a single stem was lost from each of 7 vases, so that there are 83 total stems and 30 vases.  The df accounting is now:
<table>
 <thead>
  <tr>
   <th style="text-align:left;"> source </th>
   <th style="text-align:left;"> df </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Treatment </td>
   <td style="text-align:left;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Vase (EU error) </td>
   <td style="text-align:left;"> 24 (= 30 - 1- 5) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Stems (subsample error) </td>
   <td style="text-align:left;"> 53  (= 83 - 1 - 5 - 24) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Total </td>
   <td style="text-align:left;"> 82 </td>
  </tr>
</tbody>
</table>

Now, suppose that two entire vases were lost, and that there are 3 stems in each of the remaining 28 vases.  Now the df accounting would become:
<table>
 <thead>
  <tr>
   <th style="text-align:left;"> source </th>
   <th style="text-align:left;"> df </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Treatment </td>
   <td style="text-align:left;"> 5 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Vase (EU error) </td>
   <td style="text-align:left;"> 22 (= 28 - 1- 5) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Stems (subsample error) </td>
   <td style="text-align:left;"> 56  (= 84 - 1 - 5 - 24) </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Total </td>
   <td style="text-align:left;"> 83 </td>
  </tr>
</tbody>
</table>

In all other respects, the analysis of unbalanced data would proceed identically to the analysis of balanced data.

A final comment on subsampling: Often it is more expensive to have more EUs and is easier to have more subsamples.  The extent to which increasing the number of subsamples will increase statistical power depends on the relative magnitudes of the variability among EUs and the variability among subsamples.   If the variability among EUs is large relative to the variability among subsamples (as might be the case with the grip strength example, for instance), then adding subsamples does little to increase statistical precision.  But if the variability among subsamples is of the same or greater magnitude than the variability among EUs (as in the rose example), then adding subsamples can increase precision and statistical power.

## Mathematical foundations

In this section, we introduce formal mathematical arguments for how random effects induce correlations among grouped responses.  This section makes heavy uses of the mathematics of probability.  Before proceeding, we will need to review several key probability concepts

### Probability refresher
Let $X$ denote a random variable.  Recall that the *expectation*, or *expected value*, of $X$ is one measure of the center of the probability distribution of $X$.  The expectation can also be thought of as the mean or average of $X$.  We denote the expectation of $X$ as $\Exp{X}$, or sometimes $\mu_X$.  (The subscript on $\mu$ is only used when we need it to clarify to which random variable the expectation refers.)  

The *variance* of $X$, often denoted $\Var{X}$ or $\sigma^2_X$, is defined as the expectation of $(X - \mu_X)^2$.  In other words,
\[
\Var{X} = \Exp{(X - \mu_X)^2}.
\] 
The variance of a random variable is a measure of its dispersion, or spread.

Consider two random variables, $X$ and $Y$. The *covariance* of $X$ and $Y$, written as $\Cov{X}{Y}$, is defined as the expectation of the product $(X - \mu_X) (Y - \mu_Y)$.  In other words,
\[
\Cov{X}{Y} = \Exp{(X - \mu_X)(Y - \mu_Y)}.
\] 
Note that the covariance of a random variable with itself is just its variance, that is, $\Cov{X}{X} = \Var{X}$.  The covariance of two random variables is a measure of their association.  If $X$ and $Y$ are positively associated, then values of $X$ larger (smaller) than $\mu_X$ will tend to co-occur with values of $Y$ larger (smaller) than $\mu_Y$, thus the product $(X - \mu_X) (Y - \mu_Y)$ will usually be positive, and so the expectation of that product --- the covariance --- will be positive as well.  On the other hand, if the reverse is true, and values of $X$ larger (smaller) than $\mu_X$ tend to co-occur with values of $Y$ smaller (larger) than $\mu_Y$, then the product $(X - \mu_X) (Y - \mu_Y)$ will usually be negative, and so the covariance will be negative.

When $X$ and $Y$ are independent, then their covariance is zero, i.e., $\Cov{X}{Y} = 0$.  In general, the converse is not true: just because two random variables have a covariance of 0 does not necessarily imply that they are independent.  However, if $X$ and $Y$ are two normally distributed random variables, then $\Cov{X}{Y} = 0$ does imply that $X$ and $Y$ are independent.

Covariance is not just a measure of the association between two random variables.  A covariance is also a measure of the dispersion or spread of those variables.  Sometimes, we wish to factor out the dispersion component of covariance, and thus isolate the portion of covariance that measures the strength of association.  We can do so by considering the correlation between $X$ and $Y$, written $\Cor{X}{Y}$ or $\rho_{X,Y}$, which scales the covariance by the dispersion of both $X$ and $Y$:
\[
\Cor{X}{Y} = \dfrac{\Cov{X}{Y}}{\sqrt{\Var{X}\Var{Y}}}.
\]
Or, if we recognize that $\sqrt{\Var{X}} = \SD{X}$ (the standard deviation of $X$), then we can write
\[
\Cor{X}{Y} = \dfrac{\Cov{X}{Y}}{\SD{X}\SD{Y}}.
\]

Now here are some useful rules about the expectation and variance of a sum of random variables.  First, the expectation of $X+Y$ is simply
\[
\Exp{X+Y} = \Exp{X} + \Exp{Y}.
\]
The variance of $X+Y$ is more complicated:
\[
\Var{X+Y} = \Var{X} + \Var{Y} + 2 \Cov{X}{Y}.
\]
Thus, if $X$ and $Y$ are independent, then $\Var{X+Y} = \Var{X} + \Var{Y}$.  But, if $X$ and $Y$ are not independent, then we have to factor in the covariance.

### Application to models with random effects

Here is how this applies to models with random effects.  Consider our model for the turnip-green data, but consider a simplified data set where there is only one sample from each leaf.  Let's write the model for these data as
\[
y_{ij} = \mu + P_i + \varepsilon_{ij}
\]
where $P_i \sim \mathcal{N}\left(0, \sigma^2_P \right)$ represents the plant random effects, and $\varepsilon_{ijk} \sim  \mathcal{N}\left(0, \sigma^2_{\varepsilon} \right)$ represents the leaf random effects (the residual error in this case).  Let's first find an expression for the variance of $y_{ij}$:
\begin{eqnarray*}
\Var{y_{ij}} & = &  \Var{\mu + P_i + \varepsilon_{ij}} \\
       & = &  \Var{P_i} + \Var{\varepsilon_{ij}} + 2 \Cov{P_i}{\varepsilon_{ij}}\\
       & = &  \sigma^2_P + \sigma^2_{\varepsilon}. 
\end{eqnarray*}
Note that $\mu$ is a constant, and not a random variable, so its variance is 0.  Note also that because the plant random effects and the leaf random effects are independent, then $\Cov{P_i}{\varepsilon_{ij}} = 0$.

Now, let's try to find an expression for the correlation between two data points.  First, we'll find the covariance between two data points.  To do so, we'll need to use a rule for the covariance between two sums of random variables.  That rule is 
\[
\Cov{X+Y}{W+Z} = \Cov{X}{W} + \Cov{X}{Z} + \Cov{Y}{W} + \Cov{Y}{Z}.
\]
(Note that we can apply this rule to obtain $\Var{X+Y} = \Cov{X+Y}{X+Y} = \Var{X} + \Var{Y} + 2 \Cov{X}{Y}$.)

Now, write two different data points as $y_{ij}$ and $y_{kl}$.  Then,
\begin{eqnarray*}
	\Cov{y_{ij}}{y_{kl}} & = &  \Cov{\mu + P_i + \varepsilon_{ij}}{\mu + P_k + \varepsilon_{kl}} \\
	& = &  \Cov{P_i}{P_k} + \Cov{P_i}{\varepsilon_{kl}} + \Cov{\varepsilon_{ij}}{P_k} + \Cov{\varepsilon_{ij}}{\varepsilon_{kl}}. 
\end{eqnarray*}
Again, the $\mu$'s drop out because they are constants and have no variance.  Next, because the plant random effects are independent of the leaf random effects, $\Cov{P_i}{\varepsilon_{kl}} = \Cov{\varepsilon_{ij}}{P_k} = 0$.  Next, assuming that are two data points are from different leaves, then $\Cov{\varepsilon_{ij}}{\varepsilon_{kl}} = 0$ (because the leaf-level random effects are independent of one another).  

What about $\Cov{P_i}{P_k}$?  If the leaves come from the same plant, that is, if $i=k$, then we have $\Cov{P_i}{P_k} = \Cov{P_i}{P_i} = \Var{P_i} = \sigma^2_P$.  However, if the leaves come from different plants ($i \neq k$), then the two plant random effects are independent, and thus $\Cov{P_i}{P_k} = 0$.  Putting it all together, we have
\[
\Cov{y_{ij}}{y_{kl}} = \begin{cases} \sigma^2_P & i = k \\ 0 & i \neq k \end{cases}.
\]

Finally, to convert a covariance to a correlation, we just have to divide the covariance by $\sqrt{\Var{y_{ij}} \Var{y_{kl}}} = \sigma^2_P + \sigma^2_{\varepsilon}$, giving
\[
\Cor{y_{ij}}{y_{kl}} = \begin{cases} \dfrac{\sigma^2_P}{\sigma^2_P + \sigma^2_{\varepsilon}} & i = k \\ 0 & i \neq k \end{cases}.
\]

Thus, we see that including the plant-level random effect induces a positive correlation between leaves from the same plant.  (Note that correlation must be positive because $\sigma^2_P$ and $\sigma^2_{\varepsilon}$ must both be positive.)
