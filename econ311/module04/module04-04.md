# One X Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

<style>
.hover_img a { position:relative; }
.hover_img a span { position:absolute; display:none; z-index:99; }
.hover_img a:hover span { display:block; }
</style>

## Statistical Inference

Just like how we tested if our sample mean was statistically different
from some hypothesized value in the last module, we can test if our
regression coefficients are different from hypothesized values as well.

<div class="aside">

In the notes, we use zero, but the hypothesized value could have been
anything.

</div>

<div class="hover_img">

Focusing on $\beta_1$, and <a href="#">skipping the
math<img src="https://media.tenor.com/Oj6i7LwJdlMAAAAC/youre-welcome.gif" alt="image" height="200" /></a>,
we can write the standard error of $\beta_1$ as follows:

</div>

$$se_{\beta_1} = \sqrt{\frac{s^2}{\sum(x_i - \overline{x})^2}}$$

Here, $s^2 = \frac{SSR}{n-2}$ is the variance of the residuals, and $s$
is called the **residual standard error**. You will see it near the
bottom of `summary()` output as “Residual standard error”. In words, it
is the typical size of a miss: how far, in units of $Y$, a point usually
sits from the regression line.

As a reminder, we need the standard error to calculate a confidence
interval and/or perform a hypothesis test. Also, you can think of a
standard error as a measure of how precisely we estimated $\beta$.

To test if your estimate of $\beta_i$ is different from some
hypothesized number, we first need to generate a test statistic:

$$t = \frac{\beta_i - \#}{se_{\beta_i}}$$

Generally, people choose 0 for $\#$, since a coefficient of 0 would
imply that the regressor (e.g., GPA) has **no** relationship with the
outcome variable (e.g., SAT score). This way, the hypothesis test is set
up such that it tests whether $X$ is related to $Y$.

Once we calculate this test statistic, we can calculate a $p$-value and
decide whether to reject the null hypothesis that our regressor is not
related to the outcome variable.

As a final note about inference (i.e., hypothesis tests), we need to
make two important assumptions about our errors ($\hat{\epsilon}_i$) in
order for the above t-statistic to be valid.

1.  Normality: we assume that our errors are independent, random draws
    from a normal distribution.
2.  Homoskedasticity: this big word means that the normal distribution
    we draw our errors from has a constant variance across all points.
    See below for examples of homoskedastic errors and heteroskedastic
    errors.

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-04-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="One graph of homoskedastic errors next to another graph of heteroskedastic errors." />

</details>

## Goodness of Fit

Once we estimate $\beta_0$ and $\beta_1$, we can also begin to talk
about how well the model “fits” the data. In other words, what fraction
of $Y$’s variance is *explained* by $X$.

Before getting too much further with this, I want to be very clear that
**goodness-of-fit measures do not determine whether your model is
good**. You may be able to explain a *lot* of $Y$’s variation, but still
have a bad model. In addition, your model might have poor overall
explanatory power, but still do a good job measuring the effect of $X$
on $Y$, which is what we care about. We will discuss this more as the
course progresses.

Below are three equations for Total Sum of Squares (SST), Explained Sum
of Squares (SSE), and Residual Sum of Squares (SSR). Hover over the
equations for explanations.

<div title="Total Sum of Squares.  This equation measures the total variation in Y.  Note, this is the numerator of the variance equation, meaning that it is a sum rather than an average.">

$$\text{SST} = \sum (Y_i - \overline{Y})^2$$

</div>

<div title="Explained Sum of Squares.  Notice the difference between SSE and SST.  The only change is that in this expression, we use the predicted/fitted Y value instead of the observed value.  If our model is very good, SSE will be very close to SST.  If the model is poor, SSE will be very far away from SST.">

$$\text{SSE} = \sum (\hat{Y}_i - \overline{Y})^2$$

</div>

<div title="Residual Sum of Squares.  This is what is NOT explained by the model.  It is the squared difference between the observed value and the fitted value.  If our model is very good, this number will be very far away from SST, because the errors/residuals would be small.">

$$\text{SSR} = \sum \hat{\epsilon_i}^2 = \sum (Y_i - \hat{Y}_i)^2$$

</div>

Once we have defined these three measures, we can connect all three by
the following equation:

$$SST = SSE + SSR$$

If our model is very good at explaining the variation in $Y$, we would
expect SSE to be very large relative to SSR. For a *perfect* model, we
would see $SST = SSE$. Therefore, we can create a ratio between these
two numbers, which will give us a percentage of the variation that is
explained by the model. We call this ratio $R^2$, and define it as
follows:

$$R^2 = \frac{SSE}{SST} = 1 - \frac{SSR}{SST}$$

Below, I demonstrate what I mean when I say $R^2$ should not be a be-all
and end-all measure of how good a model is. I simulated a dataset of 100
$X$ values. Then, I created two $Y$ variables: $Y_1 = 5X + \epsilon$ and
$Y_2 = 5X + 2.5\epsilon$. Both $Y$ variables have the same relationship
with $X$, but the random errors are magnified in the second. The
estimated coefficients are very similar, but the $R^2$ measure is quite
different. Again, we care more about the coefficients being estimated
accurately rather than the model itself having explanatory power.

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-04-unnamed-chunk-5-1.svg" style="width:90.0%"
data-fig-align="center" />

</details>

## OLS in R

So far, we have derived the OLS estimator, calculated statistics for
hypothesis tests, and looked at measures for goodness of fit. Now, we
are going to talk more about application.

First, to estimate a linear regression model, we will use the `lm()`
function.

`lm()`, which stands for “linear model”, accepts the following
arguments:

- `formula`: Formulas in R take on the following form: `y ~ x1 + x2`. In
  the case for GPAs and SAT scores, we would write: `sat_sum ~ hs_gpa`.
- `data`: This is the data you plan to use. This tells R where to look
  for the variables in your formula.
- `subset`: An optional vector that specifies a subset of observations
  used to fit the line. For example, maybe we want to fit two lines: one
  for men and one for women. We would use `subset = df$sex == 1` to
  estimate the model using only data from female students. Another
  reason to use this would be to eliminate outliers, etc.

We are going to keep examining the GPA data
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv);
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/satgpa.html)).
Let’s read the data into R:

``` r
library("scales")

df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
reg_sat_gpa_m <- lm(sat_sum ~ hs_gpa,
                    data = df, subset = df$sex == 2)
reg_sat_gpa_f <- lm(sat_sum ~ hs_gpa,
                    data = df, subset = df$sex == 1)
```

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
```

Let’s estimate the SAT and GPA model from the previous section of notes.

``` r
reg_sat_gpa <- lm(sat_sum ~ hs_gpa, data = df)
reg_sat_gpa
```

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ hs_gpa, data = df)

    Coefficients:
    (Intercept)       hs_gpa  
          66.99        11.36  

</details>

The output of `lm()` returns the call you used to generate the output in
addition to the estimated coefficients. These coefficients are exactly
what we previously estimated with `cov()`, `var()`, `cor()`, etc. Not
only does `lm()` output the coefficients, but it also returns both the
fitted values (`reg_sat_gpa$fitted.values`) and the residuals/errors
(`reg_sat_gpa$residuals`).

``` r
head(data.frame(observed_y = df$sat_sum,
                fitted_y = reg_sat_gpa$fitted.values,
                residual_y = reg_sat_gpa$residuals))
```

<details>

<summary>

Output
</summary>

      observed_y fitted_y residual_y
    1        127 105.6232  21.376776
    2        122 112.4411   9.558875
    3        116 109.6003   6.399667
    4         95 109.6003 -14.600333
    5        107 112.4411  -5.441125
    6        111 112.4411  -1.441125

</details>

We can plot our regression line using the `abline()` function.

``` r
library("scales")
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = alpha("black", 0.2),
     xlab = "GPA", ylab = "SAT")
abline(reg_sat_gpa, lty = 1, col = "gold", lwd = 4)
```

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-04-unnamed-chunk-11-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Scatter plot of GPA and SAT with the OLS line." />

</details>

Using the `summary()` function generates some important information
about our regression and the individual coefficients.

``` r
summary(reg_sat_gpa)
```

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ hs_gpa, data = df)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -42.055  -8.669  -0.351   8.803  34.240 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  66.9885     2.4441   27.41   <2e-16 ***
    hs_gpa       11.3632     0.7535   15.08   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 12.9 on 998 degrees of freedom
    Multiple R-squared:  0.1856,    Adjusted R-squared:  0.1848 
    F-statistic: 227.4 on 1 and 998 DF,  p-value: < 2.2e-16

</details>

In the table portion, each coefficient has an `Estimate`, `Std. Errors`,
`t value`, and `Pr(>|t|)` In addition, you can find the
`Multiple R-Squared` value, which is the measure for goodness of fit.

<!-- :::: {.columns} -->

<!-- :::: -->

<div class="columns">

<div class="column" width="49.9%">

Regression for Male Students:

<details class="code-fold">
<summary>Code</summary>

``` r
reg_sat_gpa_m <- lm(sat_sum ~ hs_gpa,
                    data = df, subset = df$sex == 2)
summary(reg_sat_gpa_m)
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ hs_gpa, data = df, subset = df$sex == 
        2)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -31.997  -8.305  -0.455   8.574  33.287 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)   55.851      3.579   15.61   <2e-16 ***
    hs_gpa        13.715      1.079   12.71   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 12.33 on 482 degrees of freedom
    Multiple R-squared:  0.2512,    Adjusted R-squared:  0.2496 
    F-statistic: 161.7 on 1 and 482 DF,  p-value: < 2.2e-16

</details>

</div>

<div class="column" width="49.9%">

Regression for Female Students:

<details class="code-fold">
<summary>Code</summary>

``` r
reg_sat_gpa_f <- lm(sat_sum ~ hs_gpa,
                    data = df, subset = df$sex == 1)
summary(reg_sat_gpa_f)
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ hs_gpa, data = df, subset = df$sex == 
        1)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -45.497  -8.323   0.153   8.877  31.153 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)   71.280      3.183   22.39   <2e-16 ***
    hs_gpa        11.019      1.003   10.98   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 12.55 on 514 degrees of freedom
    Multiple R-squared:  0.1901,    Adjusted R-squared:  0.1885 
    F-statistic: 120.6 on 1 and 514 DF,  p-value: < 2.2e-16

</details>

</div>

</div>

Take a look at the `Pr(>|t|)` column. This contains a p-value
corresponding to the hypothesis test that $\beta_i \neq 0$. The value
`<2e-16` means that the p-value is smaller than `0.0000000000000002`. R
doesn’t report p-values that are smaller than this for brevity. In
addition, R includes asterisks next to p-values that are statistically
significant.

- Nothing next to the p-value indicates $0.1 < p \leq 1$.
- `.` next to the p-value indicates $0.05 < p \leq 0.1$
- `*` next to the p-value indicates $0.01 < p \leq 0.05$
- `**` next to the p-value indicates $0.001 < p \leq 0.01$
- `***` next to the p-value indicates $0 < p \leq 0.001$

This is helpful when looking to see if your coefficients are significant
at different levels.

How can we interpret these results? Let’s start with $\beta_0$. For a
male student with a GPA of 0, the expected SAT score is 55.85. For a
female student with a GPA of 0, the expected SAT score is 71.28. In
addition, for any GPA, we can compute the expected SAT score by plugging
it into the equation.

``` r
cat("Expected SAT for a male with a GPA of 3.0:", reg_sat_gpa_m$coefficients[1] + (reg_sat_gpa_m$coefficients[2] * 3.0), "\n")
cat("Expected SAT for a female with a GPA of 3.0:", reg_sat_gpa_f$coefficients[1] + (reg_sat_gpa_f$coefficients[2] * 3.0))
```

Thankfully, R has a pre-defined function called `predict()`. This
function accepts two arguments: a `lm` object and `newdata`. `newdata`
must have the same column names as variables in the `lm` object.

``` r
# Create a new data.frame of GPAs from 2 to 4 by 0.25 increments.
pred_df <- data.frame(hs_gpa = seq(2, 4, by = 0.25))
pred_df$sat_m <- predict(reg_sat_gpa_m, newdata = pred_df)
pred_df$sat_f <- predict(reg_sat_gpa_f, newdata = pred_df)
pred_df
```

<details>

<summary>

Output
</summary>

      hs_gpa     sat_m     sat_f
    1   2.00  83.28204  93.31803
    2   2.25  86.71088  96.07282
    3   2.50  90.13971  98.82762
    4   2.75  93.56855 101.58242
    5   3.00  96.99738 104.33721
    6   3.25 100.42622 107.09201
    7   3.50 103.85505 109.84680
    8   3.75 107.28389 112.60160
    9   4.00 110.71272 115.35639

</details>

It seems that female students consistently outperform male students on
the SAT, according to these data.

Another helpful way to look at this would be to plot both lines together
with the underlying data.

``` r
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = alpha("black", 0.2),
     xlab = "GPA", ylab = "SAT")
abline(reg_sat_gpa_m, col = "dodgerblue", lwd = 4)
abline(reg_sat_gpa_f, col = "tomato", lwd = 4)
legend("bottomright", horiz = TRUE, bty = "n",
       legend = c("Male", "Female"),
       col = c("dodgerblue", "tomato"), lwd = 4)
```

In this plot, it’s very clear that the biggest difference between male
and female students is when GPAs are relatively low. The gap then
decreases as GPAs get larger.

While `summary()` provides helpful information about our regressions,
the output is not very aesthetically pleasing. We are going to revisit
the `modelsummary` package, and use the titular function
`modelsummary()`. This function takes many arguments, but for
simplicity, we will just discuss a few:

- `models`: A named list of models we want to include in the table.
- `title`: The title of our table.
- `estimate`: This is a bit complicated, but we are going to set this
  equal to `"{estimate}{stars}"`. This way, the table will display
  coefficients, standard errors, and stars to denote statistical
  significance. This is the norm. If you do not like the stars, you can
  remove them by not including `estimate` in the function call.
- `coef_map`: A *named* vector variable names. This will rename the
  variable names from what they are labeled as in the data to something
  more human readable.
- `gof_map`: A vector of desired goodness of fit measures for each
  model. We are going to stick with `"nobs"` and `"r.squared"` for the
  time being.

<details class="code-fold">
<summary>Code</summary>

``` r
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

regz <- list(`All Students` = reg_sat_gpa,
             `Male` = reg_sat_gpa_m,
             `Female` = reg_sat_gpa_f)
coefz <- c("hs_gpa" = "High School GPA",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of GPA on SAT Scores",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+-----------------+--------------+-----------+-----------+
|                 | All Students | Male      | Female    |
+=================+==============+===========+===========+
| High School GPA | 11.363***    | 13.715*** | 11.019*** |
+-----------------+--------------+-----------+-----------+
|                 | (0.754)      | (1.079)   | (1.003)   |
+-----------------+--------------+-----------+-----------+
| Constant        | 66.988***    | 55.851*** | 71.280*** |
+-----------------+--------------+-----------+-----------+
|                 | (2.444)      | (3.579)   | (3.183)   |
+-----------------+--------------+-----------+-----------+
| Num.Obs.        | 1000         | 484       | 516       |
+-----------------+--------------+-----------+-----------+
| R2              | 0.186        | 0.251     | 0.190     |
+-----------------+--------------+-----------+-----------+
&#10;Table: Effect of GPA on SAT Scores
&#10;</details>
