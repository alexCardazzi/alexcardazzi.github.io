# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

We will keep working with the SAT and GPA data. First, load the
packages, read in the data, and re-create the models from the last set
of notes:

``` r
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
df <- df[df$hs_gpa <= 4,] # remove the one student with a GPA above 4.0
df$female <- ifelse(df$sex == 1, 1, 0)

r2 <- lm(sat_sum ~ hs_gpa, df, subset = df$sex == 1) # female only
r3 <- lm(sat_sum ~ hs_gpa, df, subset = df$sex == 2) # male only
r4 <- lm(sat_sum ~ female + hs_gpa, df) # different intercepts
```

## Different Slopes

Now, what if we wanted to allow the slopes to differ but *not* the
intercept. Well, we can do this too!

To allow for differing slopes by group, we can specify the following
functional form:

$$\text{SAT}_i = \beta_0 + (\beta_1 \times \text{GPA}_i) + (\beta_2 \times \text{GPA}_i \times \text{Female}_i) + \epsilon_i$$

If a *male* student had a GPA of 0, we would expect their SAT to be:

$$\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 0) + (\widehat{\beta}_2 \times 0 \times 0) = \widehat{\beta}_0$$

Similarly, if a *female* student had a GPA of zero, their expected SAT
would be:

$$\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 0) + (\widehat{\beta}_2 \times 0 \times 1) = \widehat{\beta}_0$$

So, in other words, the intercepts would be exactly the same, which
matches our assumption.

What if these two students had 3.0 GPAs? How would their expected SAT
scores change?

For the male student:

$$\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 3) + (\widehat{\beta}_2 \times 3 \times 0) = \widehat{\beta}_0 + 3\widehat{\beta}_1$$

For the female student:

$$\begin{aligned}\text{SAT}_i = \widehat{\beta}_0 + (\widehat{\beta}_1 \times 3) + (\widehat{\beta}_2 \times 3 \times 1) &= \widehat{\beta}_0 + 3\widehat{\beta}_1 + 3\widehat{\beta}_2 \\&= \widehat{\beta}_0 + 3(\widehat{\beta}_1 + \widehat{\beta}_2)\end{aligned}$$

This makes the difference between the two groups equal to
$3\widehat{\beta}_2$, or $\text{GPA} \times \widehat{\beta}_2$. If
$\widehat{\beta}_2$ is positive (negative), the difference between the
groups would widen (shrink) as $\text{GPA}$ increased.

To estimate this regression in R, we will write the equation as follows:
`sat_sum ~ hs_gpa + hs_gpa:female`. The colon (`:`) tells R to multiply
the two variables in the model like we have written above.

<details class="code-fold">
<summary>Code</summary>

``` r
r5 <- lm(sat_sum ~ hs_gpa + hs_gpa:female, df)
summary(r5)

plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = scales::alpha(ifelse(df$sex == 1, "tomato", "dodgerblue"), 0.4),
     ylab = "SAT Percentile Sum",
     xlab = "High School GPA")
abline(a = coef(r5)[1], b = coef(r5)[2], col = "dodgerblue", lwd = 3)
abline(a = coef(r5)[1], b = sum(coef(r5)[2:3]), col = "tomato", lwd = 3)
legend("bottomright", bty = "n", lty = c(1, 1),
       col = c("tomato", "dodgerblue"),
       legend = c("Female", "Male"), lwd = 2)
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = sat_sum ~ hs_gpa + hs_gpa:female, data = df)

    Residuals:
        Min      1Q  Median      3Q     Max 
    -43.884  -8.284  -0.255   8.600  34.832 

    Coefficients:
                  Estimate Std. Error t value Pr(>|t|)    
    (Intercept)    63.9518     2.3804  26.866  < 2e-16 ***
    hs_gpa         11.3040     0.7288  15.511  < 2e-16 ***
    hs_gpa:female   2.0289     0.2446   8.294 3.54e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 12.43 on 996 degrees of freedom
    Multiple R-squared:  0.2431,    Adjusted R-squared:  0.2415 
    F-statistic: 159.9 on 2 and 996 DF,  p-value: < 2.2e-16

</details>

<details>

<summary>

Plot
</summary>

<img src="module06_img/06-02-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="GPA and SAT Percentile Sum with two regression lines that start at the same intercept and spread apart." />

</details>

Here, the two lines certainly diverge as `hs_gpa` increases, which
matches up with the regression coefficient (2.03). To interpret this
coefficient: female student SAT scores grow 2 points *more* than male
student SAT scores when increasing GPA by one.

The coefficient for the product (multiplication) of two variables in a
regression is called an **interaction term**.

## Dummies and Differences

Econometricians like to use dummy variables and interaction terms
because they allow us to consider the setting’s inherent
[heterogeneity](https://en.wikipedia.org/wiki/Homogeneity_and_heterogeneity#Sociology)
in our models. However, another convenient thing about them is that they
allow us to estimate *differences*.

For example, what if we wanted to know: do female students perform
better on SAT exams than male students? Before this module, we might
collect SAT scores for female and male students then do a
difference-in-means hypothesis test (from [Module
3](https://alexcardazzi.github.io/econ311/module03/module03-03.html)).
If we found a statistically significant difference, however, we could
not be sure that the difference was in fact due to gender but rather due
to the female students in the sample just being smarter overall. By
using regression, we can partial out the effect of intelligence (via
GPA[^1]) and then re-calculate the difference in means. In fact, our
female dummy variable from `r4` is exactly that: a difference in means
test. The difference we found was 6.9, and it was statistically
significant even after controlling for `hs_gpa`.

## The Full Model

Now that we’ve estimated these two models with three parameters each, we
can combine them into one giant model. This model will look like the
following:

$$\begin{aligned}\text{SAT}_i = \ &\beta_0 + (\beta_1 \times \text{Female}_i) + (\beta_2 \times \text{GPA}_i) \\ &+ (\beta_3 \times \text{GPA}_i \times \text{Female}_i) + \epsilon_i\end{aligned}$$

<details open>

<summary>

Coefficient Interpretations
</summary>

1.  $\beta_0$ is the expected SAT for *male* students with a GPA of 0.
2.  $\beta_1$ is the difference in the expected SAT for *female*
    students relative to male students with a GPA of 0.
3.  $\beta_2$ is the change in expected SAT for *male* students if they
    increase their GPA by 1.0
4.  $\beta_3$ is the difference in the change in expected SAT for
    *female* students relative to male students when they increase their
    GPA by 1.0.

</details>

Let’s take a look at the parameter estimates and compare them to the
previous gender-specific models:

<details class="code-fold">
<summary>Code</summary>

``` r
r6 <- lm(sat_sum ~ female + hs_gpa + hs_gpa:female, df)
# As a note, we could have used "female*hs_gpa" in the model.
#   "*" is a short-hand way to include both variables
#   by themselves in addition to their interaction.
#   However, when learning, I think it's best to write out
#   the full equation.
regz <- list(`Female` = r2,
             `Male` = r3,
             `All Students` = r6)
coefz <- c("hs_gpa" = "HS GPA",
           "female" = "Female",
           "female:hs_gpa" = "HS GPA x Female",
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
+-----------------+-----------+-----------+--------------+
|                 | Female    | Male      | All Students |
+=================+===========+===========+==============+
| HS GPA          | 11.392*** | 13.715*** | 13.715***    |
+-----------------+-----------+-----------+--------------+
|                 | (0.999)   | (1.079)   | (1.083)      |
+-----------------+-----------+-----------+--------------+
| Female          |           |           | 14.345**     |
+-----------------+-----------+-----------+--------------+
|                 |           |           | (4.782)      |
+-----------------+-----------+-----------+--------------+
| HS GPA x Female |           |           | -2.323       |
+-----------------+-----------+-----------+--------------+
|                 |           |           | (1.471)      |
+-----------------+-----------+-----------+--------------+
| Constant        | 70.196*** | 55.851*** | 55.851***    |
+-----------------+-----------+-----------+--------------+
|                 | (3.167)   | (3.579)   | (3.593)      |
+-----------------+-----------+-----------+--------------+
| Num.Obs.        | 515       | 484       | 999          |
+-----------------+-----------+-----------+--------------+
| R2              | 0.202     | 0.251     | 0.250        |
+-----------------+-----------+-----------+--------------+
&#10;Table: Effect of GPA on SAT Scores
&#10;</details>

Let’s break down the model’s output:

- First, notice how the coefficients for “HS GPA” and “Constant” in the
  third column match the coefficients in the second column. This is
  because those two coefficients in the third column are, in essence,
  male-specific. In effect, we have matched the estimates from the
  male-only model!
- You should also note that even though the coefficients match, the
  standard errors are slightly larger in the third column. This is due
  to having to estimate additional parameters.
- If we replicated the male-specific model, what about the female
  specific model? Well, if we add the constant and the female dummy, we
  get 70.196, which is the constant in the female-specific regression.
  This is similar for HS GPA and HS GPA x Female – the sum of these two
  equals the HS GPA coefficient in the first column.
- The benefit of estimating all of these parameters in a single model is
  that now we can look to see if the *differences* between coefficients
  in the models are statistically different. For example, the difference
  in constants across the models is indeed statistically different
  according to the stars next to the Female parameter in column 3. We
  also find that the two slopes across the models are *not*
  statistically different.
- The results of this model suggest that female students do perform
  better on the SAT than their male counterparts. However, the
  difference between male and female students is relatively constant at
  all levels of intelligence (i.e. GPA) given the insignificant
  interaction term.

<div class="aside">

The Female coefficient in the full model (14.3) is the gap at a GPA of
zero, which is outside of our data (GPAs in this sample run from 1.8 to
4). At any other GPA, the gap is the Female coefficient plus the
interaction coefficient times GPA. For a 3.0 GPA, the gap is 14.3 +
(-2.3 $\times$ 3) = 7.4.

</div>

## Choosing a Reference Group

Initially, we made an arbitrary choice by selecting males to be the
“reference” group in our setting. We could have made the exact opposite
decision and created a dummy variable for males. We can re-run these
models by using a male dummy:

<details class="code-fold">
<summary>Code</summary>

``` r
df$male <- ifelse(df$sex == 2, 1, 0)
r7 <- lm(sat_sum ~ male*hs_gpa, df)
regz <- list(`Female` = r2,
             `Male` = r3,
             `All Students (M ref)` = r6,
             `All Students (F ref)` = r7)
coefz <- c("hs_gpa" = "HS GPA",
           "female" = "Sex",
           "female:hs_gpa" = "HS GPA x Sex",
           "male" = "Sex",
           "male:hs_gpa" = "HS GPA x Sex",
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
+--------------+-----------+-----------+----------------------+----------------------+
|              | Female    | Male      | All Students (M ref) | All Students (F ref) |
+==============+===========+===========+======================+======================+
| HS GPA       | 11.392*** | 13.715*** | 13.715***            | 11.392***            |
+--------------+-----------+-----------+----------------------+----------------------+
|              | (0.999)   | (1.079)   | (1.083)              | (0.996)              |
+--------------+-----------+-----------+----------------------+----------------------+
| Sex          |           |           | 14.345**             | -14.345**            |
+--------------+-----------+-----------+----------------------+----------------------+
|              |           |           | (4.782)              | (4.782)              |
+--------------+-----------+-----------+----------------------+----------------------+
| HS GPA x Sex |           |           | -2.323               | 2.323                |
+--------------+-----------+-----------+----------------------+----------------------+
|              |           |           | (1.471)              | (1.471)              |
+--------------+-----------+-----------+----------------------+----------------------+
| Constant     | 70.196*** | 55.851*** | 55.851***            | 70.196***            |
+--------------+-----------+-----------+----------------------+----------------------+
|              | (3.167)   | (3.579)   | (3.593)              | (3.155)              |
+--------------+-----------+-----------+----------------------+----------------------+
| Num.Obs.     | 515       | 484       | 999                  | 999                  |
+--------------+-----------+-----------+----------------------+----------------------+
| R2           | 0.202     | 0.251     | 0.250                | 0.250                |
+--------------+-----------+-----------+----------------------+----------------------+
&#10;Table: Effect of GPA on SAT Scores
&#10;</details>

Numerically, it doesn’t matter which group is the reference group.
However, it certainly changes the interpretation of your coefficients
which should be carefully considered.

As a final note, it is important to mention that you **cannot** include
both the Female and Male dummies in the same regression. These two
variables are perfectly collinear with the constant, which makes for a
regression that cannot be estimated.

[^1]: Obviously, this is probably a poor measure of intelligence, but
    just go with it for now.
