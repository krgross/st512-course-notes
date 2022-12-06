# One-factor ANOVA




## One-factor ANOVA: The basics

*Example.*  The data below provide calorie content for 20 randomly selected beef hotdogs, 17 randomly selected poultry hotdogs, and 17 randomly selected meat (!) hotdogs.
<div class="figure" style="text-align: center">
<img src="03-OneFactorLayout_files/figure-html/unnamed-chunk-2-1.png" alt="Strip chart of calorie content for hot dog data." width="480" />
<p class="caption">(\#fig:unnamed-chunk-2)Strip chart of calorie content for hot dog data.</p>
</div>
(Note: The above plot is an example of a strip chart.  Only left-to-right variation is meaningful.  Vertical variation within male or female groups is just random scatter added to make sure the data points do not lie directly on top of one another.)  

Let $\mu_B$, $\mu_M$ and $\mu_P$ denote the average calorie content for beef, meat and poultry hotdogs, respectively.  Suppose we are interested in asking if the data above contain evidence that $\mu_B$, $\mu_M$ and $\mu_P$ differ.  The strip plot above certainly seems to suggest that these means differ, but we seek a formal statistical procedure for evaluating the strength of evidence.  In particular, we seek a statistical test of the null hypothesis $H_0$: $\mu_B = \mu_M = \mu_P$ vs.\ the alternative hypothesis that at least two of these means differ.  The statistical procedure that will allow us to do so is a one-factor analysis of variance (ANOVA).

Before we begin, we need to develop some notation.  ANOVA notation differs somewhat from regression notation.  The first type of ANOVA that we will consider is a one-factor ANOVA, so called because there is one factor that distinguishes the groups that we are comparing.  In a one-factor ANOVA, $g$ different populations are compared.  In the hot dog example, $g = 3$.  Let $i = 1,\ldots,g$ be an index that distinguishes the different populations.  The size of the random sample drawn from population $i$ is written $n_i$. When the sample sizes are the same in every group, we say that the data are balanced, and sometimes replace the individual sample sizes $n_i$  by a common sample size $n$.  Let $n_T = \sum_{i = 1}^g n_i$  denote the total number of data points in the data set.  Let $j=1, \ldots,n_i$ be an index that distinguishes the different data points within each sample; that is, $y_{ij}$  is observation $j$ from population $i$.  Finally, let $\mu_i$ denote the population mean for group $i$.

We will also use the subscript ``+'' to indicate summation over the values of an index.  (Oelhert uses large dots instead of plus signs.)  For example, if we wanted to add together all the data points from the sample from group $i$, we could write
\[
y_{i+} = \sum_{j=1}^{n_{ij}} y_{ij}
\]
Or, if we wanted to add up all the data points in the data set, we could write
\[
y_{++} = \sum_{i=1}^g \sum_{j=1}^{n_{ij}} y_{ij}
\]
Lastly, we use bars to denote sample averages.  The sample mean from population $i$ is written
\[
\bar{y}_{i+} = \frac{1}{n_i}\sum_{j=1}^{n_{ij}} y_{ij}
\]
and the “grand mean” is written
\[
\bar{y}_{++} = \frac{1}{n_T}\sum_{i=1}^g \sum_{j=1}^{n_{ij}} y_{ij}.
\]

A word about subscripting: As we progress, the subscripting that we use will become increasingly complicated.  Remember that the basic rule for selecting a subscripting scheme is that each unique combination of subscripts must identify a unique data point.  In regression, one subscript was sufficient (i.e., $i = 1, \ldots, n$), because the value of $i$ was sufficient to specify a unique data point.  In ANOVA, we need one subscript to distinguish the different groups, and a second subscript to distinguish the individual observations within each group.  

The basic hypothesis test of interest in a one-factor ANOVA is a test of $H_0$: $\mu_1 = \mu_2 = \ldots = \mu_g$  vs.\ the alternative that at least two group means differ.  As we will see below, this is identical to a model utility test in regression with indicators, and so we shouldn't be surprised that this is an $F$-test.  In the context of ANOVA, however, it is traditional to represent this test via a sums-of-squares decomposition.  To be fluent in the language of ANOVA, it is important to understand this representation also.  We begin by partitioning the variation in the data into two pieces: one quantifying the variation among populations and a second quantifying variation within populations.  
\[
\mbox{Total variation: } SS_{Total} = \sum_{i=1}^g \sum_{j=1}^{n_{ij}} \left( y_{ij} - \bar{y}_{++} \right)^2
\]
\[
\mbox{Variation among groups: } SS_{Groups} = \sum_{i=1}^g \sum_{j=1}^{n_{ij}} \left( \bar{y}_{i+} - \bar{y}_{++} \right)^2  = \sum_{i=1}^g n_i \left( \bar{y}_{i+} - \bar{y}_{++} \right)^2
\]
\[
\mbox{Variation within groups: } SS_{Error} = \sum_{i=1}^g \sum_{j=1}^{n_{ij}} \left( y_{ij} - \bar{y}_{i+} \right)^2  
\]
Although it is not obvious, the $SS_{Groups}$ and $SS_{Error}$ add together to give $SS_{Total}$, that is,
\[
SS_{Total}= SS_{Groups} + SS_{Error}
\]
Heuristically, we want to compare the variation among groups to the variation within groups.  However, we cannot compare the $SS_{Groups}$ to the $SS_{Error}$ directly, because these sums of squares are based on a different number of free differences, or degrees of freedom.  Thus, we must standardize each sum of squares by dividing through by the number of free differences on which the sum of squares is based.

Source | df 
-------|---
Groups | $g - 1$ 
Error | $n_T - g$ 
-------|---
Total | $n_T - 1$

We divide both $SS_{Groups}$ and $SS_{Error}$ by their respective df to obtain the corresponding mean squares.  Mean squares can be directly compared, and the ratio of the $MS_{Groups}$ to the $MS_{Error}$ yields our $F$-statistic:
\[
F = \frac{MS_{Groups}}{MS_{Error}} = \frac{SS_{Groups} / (g - 1)}{SS_{Error} / (n_T - g)}.
\]
Mathematically, it can be shown that if the null hypothesis is true, then $MS_{Groups} \approx MS_{Error}$, and so $F \approx 1$.  If the null is false and the alternative is true, then $MS_{Groups} > MS_{Error}$, and so $F > 1$.  Of course, both mean squares have some inherent randomness, so we measure the degree of evidence against the null by comparing the $F$ statistic to the appropriate reference distribution.  If the null is true, then the $F$ ratio has an $F$-distribution with numerator df equal to $g - 1$ and denominator df equal to $n_T - g$.  Large values of the $F$ statistic provide evidence against the null and in favor of the alternative, and so we compute the $p$-value with a one-tailed test.

It is customary to arrange all the information that goes into an $F$-test into an ANOVA table.  Although the layout of this table may differ slightly from one text to the next, the basic pieces are all the same.  One possible arrangement of an ANOVA table is: