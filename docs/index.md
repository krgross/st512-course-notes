--- 
title: "ST 512 course notes"
author: "Kevin Gross"
date: "2022-01-07"
output: bookdown::gitbook
documentclass: book
bibliography: [st512.bib]
biblio-style: apalike
link-citations: yes
description: "Course notes for ST 512, Statistical Methods for Researchers, taught at NCSU in Spring 2022."
---

# Maximum likelihood estimation

## A very simple example with a single observation

Suppose we observe a single observation from a Poisson distribution.  Suppose that observation is $X=2$.  We can use the `dpois` function to evaluate the likelihood for this single observation.  For example, we can evaluate the likelihood at $\lambda = 1.5$:

```r
dpois(x = 2, lambda = 1.5)
```

```
## [1] 0.2510214
```
Or we could evaluate the likelihood at $\lambda = 2$ or $\lambda = 2.5$:

```r
dpois(x = 2, lambda = c(2, 2.5))
```

```
## [1] 0.2706706 0.2565156
```
Now let's evaluate the likelihood at a sequence of $\lambda$ values:

```r
lambda.vals <- seq(from = 0, to = 5, by = 0.01)
my.lhood <- dpois(x = 2, lambda = lambda.vals)
plot(lambda.vals, my.lhood, xlab = expression(lambda), ylab = "Likelihood", type = "l")
```

<img src="index_files/figure-html/unnamed-chunk-3-1.png" width="672" />
