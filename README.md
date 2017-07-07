
<!-- README.md is generated from README.Rmd. Please edit that file -->
R/`survtmle`
============

[![Travis-CI Build Status](https://travis-ci.org/benkeser/survtmle.svg?branch=master)](https://travis-ci.org/benkeser/survtmle) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/benkeser/survtmle?branch=master&svg=true)](https://ci.appveyor.com/project/benkeser/survtmle) [![Coverage Status](https://img.shields.io/codecov/c/github/benkeser/survtmle/master.svg)](https://codecov.io/github/benkeser/survtmle?branch=master) [![CRAN](http://www.r-pkg.org/badges/version/survtmle)](http://www.r-pkg.org/pkg/survtmle) [![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](http://www.repostatus.org/badges/latest/active.svg)](http://www.repostatus.org/#active) [![MIT license](http://img.shields.io/badge/license-MIT-brightgreen.svg)](http://opensource.org/licenses/MIT)

> Targeted Minimum Loss-Based Estimation (TMLE) for Survival Analysis with Competing Risks

**Authors:** [David Benkeser](https://www.benkeserstatistics.com/) and [Nima Hejazi](http://nimahejazi.org)

------------------------------------------------------------------------

Description
-----------

`survtmle` is an R package designed to use Targeted Minimum Loss-Based Estimation (TMLE) to compute marginal cumulative incidence estimates in right-censored survival settings with and without competing risks, including vaccine sieve analysis. This tool also provides facilities for computing and obtaining inference for the use of data adaptive target parameters in such settings.

------------------------------------------------------------------------

Installation
------------

You can install a stable release of `survtmle` from GitHub via [`devtools`](https://www.rstudio.com/products/rpackages/devtools/) with:

``` r
devtools::install_github("benkeser/survtmle")
```

For standard use, we recommend installing the package from [CRAN](https://cran.r-project.org/) (an initial release will be coming soon) via

``` r
install.packages("survtmle")
```

------------------------------------------------------------------------

Issues
------

If you encounter any bugs or have any specific feature requests, please [file an issue](https://github.com/benkeser/survtmle/issues).

------------------------------------------------------------------------

Example
-------

This minimal example shows how to use `survtmle` to obtain cumulative incidence estimates with a very simple, simulated data set.

``` r
library(survtmle)
#> survtmle: Targeted Learning for Survival Analysis
#> Version: 0.1.2
set.seed(341796)

# simulate data
n <- 100
t_0 <- 10
W <- data.frame(W1 = runif(n), W2 = rbinom(n, 1, 0.5))
A <- rbinom(n, 1, 0.5)
T <- rgeom(n,plogis(-4 + W$W1 * W$W2 - A)) + 1
C <- rgeom(n, plogis(-6 + W$W1)) + 1
ftime <- pmin(T, C)
ftype <- as.numeric(ftime == T)

# apply survtmle for estimation
fit <- survtmle(ftime = ftime, ftype = ftype,
                adjustVars = W, glm.ftime = "I(W1*W2) + trt + t",
                trt = A, glm.ctime = "W1 + t", method = "hazard",
                verbose = TRUE,  t0 = t_0, maxIter = 2)
#> Warning in checkInputs(ftime = ftime, ftype = ftype, trt = trt, t0 = t0, :
#> glm.trt and SL.trt not specified. Proceeding with glm.trt = '1'
#> TMLE Iteration  1   :  -9e-04 1e-04

# extract cumulative incidence at each time point
tpfit <- timepoints(fit, times = seq_len(t_0))
#> TMLE Iteration  1   :  0 0 
#> TMLE Iteration  1   :  -3e-04 0 
#> TMLE Iteration  1   :  -6e-04 0 
#> TMLE Iteration  1   :  -3e-04 0 
#> TMLE Iteration  1   :  -0.0017 0 
#> TMLE Iteration  1   :  -9e-04 -2e-04 
#> TMLE Iteration  1   :  -5e-04 -2e-04 
#> TMLE Iteration  1   :  -0.0017 -1e-04
print(tpfit)
#> $est
#>              t1         t2         t3         t4         t5         t6
#> 0 1 0.032997470 0.02429619 0.03646452 0.04867155 0.04076233 0.11147399
#> 1 1 0.008014555 0.01727758 0.02571549 0.03401515 0.05722310 0.05131788
#>             t7         t8         t9        t10
#> 0 1 0.10375280 0.11522782 0.18040243 0.26644929
#> 1 1 0.07233118 0.08266406 0.07688998 0.07278289
#> 
#> $var
#>               t1           t2           t3           t4           t5
#> 0 1 4.565496e-04 0.0004508117 0.0004457730 0.0004420009 0.0004461814
#> 1 1 2.111604e-06 0.0003335335 0.0003191353 0.0003058917 0.0006114673
#>               t6           t7           t8           t9          t10
#> 0 1 0.0012230239 0.0012205672 0.0011840986 0.0019516376 0.0031229308
#> 1 1 0.0006198923 0.0009695247 0.0009425824 0.0009515562 0.0009578375

# examine plot of cumulative incidences
plot(tpfit, type = "smooth")
```

<img src="README-example-1.png" style="display: block; margin: auto;" />

------------------------------------------------------------------------

License
-------

© 2016-2017 [David C. Benkeser](http://www.benkeserstatistics.com)

The contents of this repository are distributed under the MIT license. See below for details:

    The MIT License (MIT)

    Copyright (c) 2016-2017 David C. Benkeser

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.
