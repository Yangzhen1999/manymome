# Auxiliary Variables in All-In-One FIML Mediation

## Covariates and auxiliary variables

The `q_*` functions can use `aux` to add auxiliary variables to an
observed-variable FIML mediation model in one call. Variables in `cov`
predict the mediator(s) and/or outcome. Variables in `aux` are added
using the saturated-correlates method implemented by
[`semTools::auxiliary()`](https://rdrr.io/pkg/semTools/man/auxiliary.html);
they are not added as regression predictors. A variable cannot appear in
both roles. A confounder that needs to adjust the substantive equations
should therefore be specified in `cov`, not merely in `aux`.

An auxiliary variable related to missingness or to an incomplete
variable can help make MAR more plausible. Its inclusion does not
establish MAR, correct model misspecification, or establish a causal
indirect effect. Choose auxiliary variables using knowledge of the study
and available data.

## A reproducible illustration

This artificial example generates incomplete mediator values, with
missingness depending on observed `z`. The same `z` also predicts the
mediator. It is an illustration of the interface, not a comparison
study.

``` r

library(manymome)
set.seed(731)
n <- 300
x <- rnorm(n)
c1 <- rnorm(n)
z <- rnorm(n)
m <- 0.5*x + 0.3*c1 + 0.4*z + rnorm(n)
y <- 0.2*x + 0.4*m + 0.2*c1 + rnorm(n)
dat <- data.frame(x, m, y, c1, z)
dat$m[runif(n) < plogis(-1 + 0.5*z)] <- NA

out <- q_simple_mediation(
  x = "x", m = "m", y = "y",
  cov = "c1", aux = "z", data = dat,
  fit_method = "sem", fixed.x = FALSE,
  ci_type = "mc", R = 20000, seed = 123,
  parallel = FALSE, progress = FALSE
)
print(out)
```

    ## 
    ## =============== Simple Mediation Model ===============
    ## 
    ## Call:
    ## 
    ## q_simple_mediation(x = "x", y = "y", m = "m", cov = "c1", data = dat, 
    ##     R = 20000, seed = 123, ci_type = "mc", fit_method = "sem", 
    ##     fixed.x = FALSE, parallel = FALSE, progress = FALSE, aux = "z")
    ## 
    ## ===================================================
    ## |                Basic Information                |
    ## ===================================================
    ## 
    ## Predictor(x): x
    ## Outcome(y): y
    ## Mediator(s)(m): m
    ## Model: Simple Mediation Model
    ## Auxiliary variable(s): z
    ## Auxiliary method: Saturated correlates with FIML
    ## Exogenous variables: Random (fixed.x = FALSE)
    ## Auxiliary variables are not regression covariates.
    ## 
    ## The path model fitted:
    ## 
    ## m ~ x + c1
    ## y ~ m + x + c1
    ##  
    ## 
    ## The original number of cases: 300 
    ## The number of cases in the analysis: 300 
    ## Missing data handling: FIML (full information maximum likelihood) 
    ## 
    ## ===================================================
    ## |             Path Analysis Results               |
    ## ===================================================
    ## 
    ## lavaan 0.7-3 ended normally after 18 iterations
    ## 
    ##   Estimator                                         ML
    ##   Optimization method                           NLMINB
    ##   Number of model parameters                        20
    ## 
    ##   Number of observations                           300
    ##   Number of missing patterns                         2
    ## 
    ## Model Test User Model:
    ##                                                       
    ##   Test statistic                                 0.000
    ##   Degrees of freedom                                 0
    ##                                                       
    ##   Browne's residual (NT model-based) test             
    ##   Test statistic                                 0.000
    ##   Degrees of freedom                                 0
    ## 
    ##  -------------- 
    ##  Predicting m : 
    ##  -------------- 
    ## 
    ## Model:
    ##  m ~ x + c1 
    ##             Estimate   CI.lo   CI.hi   betaS Std. Error z value Pr(>|z|)    
    ## (Intercept)  -0.0805 -0.2210  0.0601    ----     0.0717  -1.122    0.262    
    ## x             0.3852  0.2511  0.5193  0.3244     0.0684   5.630   <2e-16 ***
    ## c1            0.2790  0.1464  0.4117  0.2381     0.0677   4.122   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## R-square:  0.1773 
    ## 
    ## Chi-Squared Difference Test for the R-square
    ## 
    ##      Df    AIC    BIC Chisq Chisq diff Df diff Pr(>Chisq)
    ## fit1  0 4171.7 4245.7     0                              
    ## fit0  2 4167.7 4234.3     0          0       2          1
    ## 
    ## - BetaS are standardized coefficients with only numeric variables
    ##   standardized. Variable(s) standardized is/are: m, x, c1
    ## - CI.lo and CI.hi are the 95.0% confidence levels of 'Estimate'
    ##   computed from the z values and standard errors.
    ## 
    ##  -------------- 
    ##  Predicting y : 
    ##  -------------- 
    ## 
    ## Model:
    ##  y ~ m + x + c1 
    ##             Estimate   CI.lo   CI.hi   betaS Std. Error z value Pr(>|z|)    
    ## (Intercept)   0.0277 -0.0882  0.1436    ----     0.0591   0.469   0.6394    
    ## m             0.3216  0.2077  0.4354  0.3335     0.0581   5.537   <2e-16 ***
    ## x             0.2332  0.1133  0.3530  0.2036     0.0612   3.813   0.0001 ***
    ## c1            0.2184  0.1034  0.3333  0.1932     0.0586   3.723   0.0002 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## R-square:  0.2800 
    ## 
    ## Chi-Squared Difference Test for the R-square
    ## 
    ##      Df    AIC    BIC Chisq Chisq diff Df diff Pr(>Chisq)
    ## fit1  0 4171.7 4245.7     0                              
    ## fit0  3 4165.7 4228.6     0          0       3          1
    ## 
    ## - BetaS are standardized coefficients with only numeric variables
    ##   standardized. Variable(s) standardized is/are: y, m, x, c1
    ## - CI.lo and CI.hi are the 95.0% confidence levels of 'Estimate'
    ##   computed from the z values and standard errors.
    ## 
    ## ===================================================
    ## |             Indirect Effect Results             |
    ## ===================================================
    ## 
    ## ----------------------------------------------------------------
    ## 
    ## == Indirect Effect(s) ==
    ## 
    ##                ind  CI.lo  CI.hi Sig pvalue     SE
    ## x -> m -> y 0.1239 0.0684 0.1898 Sig      0 0.0315
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - The 'ind' column shows the indirect effect(s).
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Indirect Effect(s) (x-variable(s) Standardized) ==
    ## 
    ##                std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> m -> y 0.1282 0.0699 0.1981 Sig      0 0.0330
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The partially standardized indirect effect(s).
    ##  - x-variable(s) standardized.
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Indirect Effect(s) (y-variable(s) Standardized) ==
    ## 
    ##                std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> m -> y 0.1045 0.0583 0.1568 Sig      0 0.0254
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The partially standardized indirect effect(s).
    ##  - y-variable(s) standardized.
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Indirect Effect(s) (Both x-variable(s) and y-variable(s) Standardized) ==
    ## 
    ##                std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> m -> y 0.1082 0.0600 0.1630 Sig      0 0.0265
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The standardized indirect effect(s).
    ##  
    ## ===================================================
    ## |              Direct Effect Results              |
    ## ===================================================
    ## 
    ## ----------------------------------------------------------------
    ## 
    ## == Effect(s) ==
    ## 
    ##           ind  CI.lo  CI.hi Sig pvalue     SE
    ## x -> y 0.2332 0.1118 0.3531 Sig 0.0002 0.0612
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - The 'ind' column shows the effect(s).
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Effect(s) (x-variable(s) Standardized) ==
    ## 
    ##           std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> y 0.2413 0.1149 0.3682 Sig 0.0002 0.0642
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The partially standardized effect(s).
    ##  - x-variable(s) standardized.
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Effect(s) (y-variable(s) Standardized) ==
    ## 
    ##           std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> y 0.1968 0.0953 0.2940 Sig 0.0002 0.0503
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The partially standardized effect(s).
    ##  - y-variable(s) standardized.
    ##  
    ## ----------------------------------------------------------------
    ## 
    ## == Effect(s) (Both x-variable(s) and y-variable(s) Standardized) ==
    ## 
    ##           std  CI.lo  CI.hi Sig pvalue     SE
    ## x -> y 0.2036 0.0981 0.3052 Sig 0.0002 0.0526
    ## 
    ##  - [CI.lo to CI.hi] are 95.0% Monte Carlo confidence intervals with
    ##    20000 replications.
    ##  - [SE] are standard errors.
    ##  - std: The standardized effect(s).
    ##  
    ## ===================================================
    ## |                      Notes                      |
    ## ===================================================
    ## 
    ## - For reference, the Monte Carlo confidence interval (and Monte Carlo
    ##   p-value, if requested) of the (unstandardized) direct effect is also
    ##   reported. The Monte Carlo p-value and the z-statistc p-value can be
    ##   different.
    ## - For the direct effects with either 'x'-variable or 'y'-variable, or
    ##   both, standardized, it is recommended to use the Monte Carlo
    ##   confidence intervals, which take into account the sampling error of
    ##   the sample standard deviations.

Both equations include `c1`. Neither equation includes `z` as a
predictor. The fitted likelihood nevertheless includes `z` through its
mean, variance and covariances. Missing values in an auxiliary variable
are allowed. Use `ci_type = "boot"` for nonparametric bootstrap
intervals; each resample refits the augmented model. Set an adequate
number of resamples for the precision needed in the study.

## Settings and scope

Supplying `aux` treats exogenous variables as random
(`fixed.x = FALSE`). If `fixed.x` is `TRUE`, a message announces this
change. This introduces distributional assumptions for those variables;
it is not just a display option. The printed output identifies the
auxiliaries and their role. The path plot displays the substantive
mediation model. The full augmented fit can be retrieved with
`get_fit(out)`.

This first implementation supports simple, serial, parallel, and custom
observed-variable mediation models fitted by SEM with FIML and ML or
MLR. Auxiliaries must be numeric, nonconstant, and distinct from the
modeled variables. Factor/ordered auxiliary variables, indicators,
moderators, SAM, grouped, clustered, weighted, and MI workflows are not
supported by this argument. Unsupported combinations produce errors
instead of silently ignoring the auxiliary variables. `aux = NULL`
retains the usual workflow.

Auxiliary parameters increase model size. Check convergence,
uncertainty, and the number of usable bootstrap resamples, especially
with small samples or many correlated auxiliaries.
