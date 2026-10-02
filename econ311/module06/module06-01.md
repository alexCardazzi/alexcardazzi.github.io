# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Guidance Counselors

To start this module, we are going to revisit our beloved SAT - GPA data
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv),
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/satgpa.html)).

One note about the data: the documentation does not say which value of
`sex` is female and which is male. We will treat 1 as female and 2 as
male, as we did in Module 4.

In Module 4, we began by estimating the relationship between HS GPA and
the sum of SAT Percentiles. We then split the sample into males and
females, and estimated two models separately for each group. Let’s re-do
these three models so we can have them as baselines.

<details class="code-fold">
<summary>Code</summary>

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
df <- df[df$hs_gpa <= 4,] # remove the one student with a GPA above 4.0
r1 <- lm(sat_sum ~ hs_gpa, df)
r2 <- lm(sat_sum ~ hs_gpa, df, subset = df$sex == 1)
r3 <- lm(sat_sum ~ hs_gpa, df, subset = df$sex == 2)

library("modelsummary")
options("modelsummary_factory_default" = "markdown")
regz <- list(`All Students` = r1,
             `Female` = r2,
             `Male` = r3)
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
|                 | All Students | Female    | Male      |
+=================+==============+===========+===========+
| High School GPA | 11.538***    | 11.392*** | 13.715*** |
+-----------------+--------------+-----------+-----------+
|                 | (0.753)      | (0.999)   | (1.079)   |
+-----------------+--------------+-----------+-----------+
| Constant        | 66.468***    | 70.196*** | 55.851*** |
+-----------------+--------------+-----------+-----------+
|                 | (2.440)      | (3.167)   | (3.579)   |
+-----------------+--------------+-----------+-----------+
| Num.Obs.        | 999          | 515       | 484       |
+-----------------+--------------+-----------+-----------+
| R2              | 0.191        | 0.202     | 0.251     |
+-----------------+--------------+-----------+-----------+
&#10;Table: Effect of GPA on SAT Scores
&#10;</details>

Next, let’s recreate some visualizations of these regression lines.

<details class="code-fold">
<summary>Code</summary>

``` r
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = scales::alpha(ifelse(df$sex == 1, "tomato", "dodgerblue"), 0.4),
     ylab = "SAT Percentile Sum",
     xlab = "High School GPA")
abline(r2, col = "tomato", lwd = 3)
abline(r3, col = "dodgerblue", lwd = 3)
abline(r1, col = "black", lwd = 2, lty = 2)
legend("bottomright", bty = "n", lty = c(2, 1, 1),
       col = c("black", "tomato", "dodgerblue"),
       legend = c("All", "Female", "Male"), lwd = 2)
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module06_img/06-01-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center" />

</details>

Splitting our sample like this is convenient because it allows for a lot
of flexibility – each group gets their own parameters. However,
remember: the more parameters you need to estimate, the more difficult
inference becomes.[^1]

If we wanted to reduce the number of parameters in our model (which is
currently four: two intercepts and two slopes), we could simply not
differentiate by gender. By doing this, we are sacrificing a bit of bias
for reductions in variance. Then, we’d only have the two parameters
(intercept and slope) to estimate. Is there a compromise we can make,
and maybe estimate just three parameters?

The short answer is: yes! Let’s explore this a bit.

In the two-model situation, we have two intercepts and two slopes. In
order to reduce the number of parameters, we would need to make an
assumption about the setting we’re working with. We could either have
male and female students share a slope or an intercept. If they share a
slope, we would only have to estimate the single slope parameter and two
intercept parameters. After looking at the two regression lines, they
seem to be pretty parallel, which means that the same-slope assumption
seems reasonable.

## Different Intercepts

We are going to estimate the following regression equation:

$$\text{SAT}_i = \beta_0 + \beta_1 \times \text{Female}_i + \beta_2 \times \text{GPA}_i + \epsilon_i$$

where $\text{Female}_i$ is a binary variable equal to 1 if the
observation is for a female student and 0 otherwise, and $\text{GPA}_i$
is the student’s high school GPA.

Before we estimate the model, let’s run through some of the algebra of
this model. Suppose we are working with a **male** student, and they
have a 3.0 GPA. What do we expect their SAT to be?

$$\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 0) + (\widehat{\beta}_2 \times 3) = \widehat{\beta}_0 + 3\widehat{\beta}_2$$

What about a **female** student with the same GPA?

$$\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 1) + (\widehat{\beta}_2 \times 3) = \widehat{\beta}_0 + \widehat{\beta}_1 + 3\widehat{\beta}_2$$

The only difference in expected SAT scores is $\widehat{\beta}_1$, and
this is independent of $\text{GPA}$. In other words, we would interpret
$\beta_1$ as an *intercept shifter/modifier*, or the average
**difference** between female and male scores.

Let’s estimate the model and plot the resulting lines.

``` r
# create binary indicator
df$female <- ifelse(df$sex == 1, 1, 0)
r4 <- lm(sat_sum ~ female + hs_gpa, df)
summary(r4)
```

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ female + hs_gpa, data = df)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -44.642  -8.401  -0.083   8.540  34.198 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  59.9776     2.4687  24.295   <2e-16 ***
    female        6.8978     0.7927   8.702   <2e-16 ***
    hs_gpa       12.4561     0.7335  16.982   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 12.39 on 996 degrees of freedom
    Multiple R-squared:  0.248, Adjusted R-squared:  0.2465 
    F-statistic: 164.2 on 2 and 996 DF,  p-value: < 2.2e-16

</details>

<details open>

<summary>

Coefficient Interpretations
</summary>

1.  The intercept is the expected sum of SAT percentiles when `hs_gpa`
    *and* `female` are equal to zero. Therefore, the interpretation for
    this coefficient is now the expected sum of SAT percentiles for a
    *male* student with a GPA of zero.
2.  Keeping `hs_gpa` at zero, the expected sum of SAT percentiles for
    *female* students is 59.98 + 6.9 = 66.88.
3.  Increasing `hs_gpa` has the same effect for both groups given our
    initial assumption. A one unit increase in `hs_gpa` leads to an
    increase in SAT of 12.46

</details>

Visualizing these results on a set of axes:

<details class="code-fold">
<summary>Code</summary>

``` r
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = scales::alpha(ifelse(df$sex == 1, "tomato", "dodgerblue"), 0.4),
     ylab = "SAT Percentile Sum",
     xlab = "High School GPA")
abline(a = coef(r4)[1], b = coef(r4)[3], col = "dodgerblue", lwd = 3)
abline(a = sum(coef(r4)[1:2]), b = coef(r4)[3], col = "tomato", lwd = 3)
legend("bottomright", bty = "n", lty = c(1, 1),
       col = c("tomato", "dodgerblue"),
       legend = c("Female", "Male"), lwd = 2)
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module06_img/06-01-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="High School GPA vs SAT Percentile Sum, colored by reported sex." />

</details>

Note how these lines are completely parallel. This is a result of our
assuming that the two groups share the same slope. In this case, it does
not seem to be a bad assumption. When we include a binary variable into
a regression, we call these **dummy** variables.

[^1]: Of course, with 1,000 (or $\approx$ 500 per group) observations,
    we don’t need to be too worried, but it’s helpful to have this in
    the back of your mind.
