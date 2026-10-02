# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Square Footage and Bedrooms

We will keep working with the Ames data. First, load the packages, read
in the data, and rename the columns and create age as before:

``` r
library("scales")
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
ames$age <- 2011 - ames$yr_built
```

Let’s practice with another variable: bedrooms. We’ll estimate a model
of the following form. Think for a minute: what do you think the sign of
each coefficient will be?

$$\text{Price}_i = \gamma_0 + \gamma_1 \times \text{Sq. Ft.}_i + \gamma_2 \times \text{Bedrooms}_i + \epsilon_i$$

<details>

<summary>

Hypotheses
</summary>

1.  We should expect that $\gamma_1>0$, since bigger houses should be
    more valuable.
2.  We should also expect $\gamma_2>0$, since houses with more bedrooms
    should also be more valuable.

</details>

Let’s estimate three models:

<details class="code-fold">
<summary>Code</summary>

``` r
r1 <- lm(price ~ sqft, ames)
r2 <- lm(price ~ bedrooms, ames)
r3 <- lm(price ~ sqft + bedrooms, ames)

regz <- list(`Price` = r1,
             `Price` = r2,
             `Price` = r3)
coefz <- c("sqft" = "Square Footage",
           "bedrooms" = "Bedrooms",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of Sq. Ft. and Bedrooms on Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+----------------+--------------+---------------+---------------+
|                | Price        | Price         | Price         |
+================+==============+===============+===============+
| Square Footage | 111.694***   |               | 136.361***    |
+----------------+--------------+---------------+---------------+
|                | (2.066)      |               | (2.247)       |
+----------------+--------------+---------------+---------------+
| Bedrooms       |              | 13889.495***  | -29149.110*** |
+----------------+--------------+---------------+---------------+
|                |              | (1765.042)    | (1372.135)    |
+----------------+--------------+---------------+---------------+
| Constant       | 13289.634*** | 141151.743*** | 59496.236***  |
+----------------+--------------+---------------+---------------+
|                | (3269.703)   | (5245.395)    | (3741.249)    |
+----------------+--------------+---------------+---------------+
| Num.Obs.       | 2930         | 2930          | 2930          |
+----------------+--------------+---------------+---------------+
| R2             | 0.500        | 0.021         | 0.566         |
+----------------+--------------+---------------+---------------+
&#10;Table: Effect of Sq. Ft. and Bedrooms on Sale Price
&#10;</details>

In the first model, we find that an increase in square footage increases
price. In the second model, we find that an increase in bedrooms
increases price, too. However, in the third model, we find:

1.  Each additional square foot increases price by \$136.36.
2.  Each additional bedroom decreases price by \$29,149.11.

**Wait a minute**… An extra bedroom *decreases* sale price? Initially,
this might be confusing, so let’s think about this carefully. In words,
this coefficient’s interpretation is:

> Increasing the number of bedrooms by one, **holding square footage
> constant**, decreases sale price by \$29,149.11.

What does it mean to increase the number of bedrooms in a property while
holding square footage constant? Suppose a home is 2,000 square feet
with four rooms. Each room is 500 square feet (on average). If we add an
additional room, but do not change the square footage, each room would
now only be 400 square feet (on average). Therefore, adding an extra
bedroom, while holding square footage constant, makes for a bunch of
small rooms. This is not something that is typically sought after in the
housing market, hence the negative coefficient.

We can add more than just two variables to our model. Below is a
progression of models that culminate in a model with three explanatory
variables:

<details class="code-fold">
<summary>Code</summary>

``` r
r1 <- lm(log(price) ~ log(sqft), ames)
r2 <- lm(log(price) ~ log(sqft) + bedrooms, ames)
r3 <- lm(log(price) ~ log(sqft) + bedrooms + age, ames)
regz <- list(`log(Price)` = r1,
             `log(Price)` = r2,
             `log(Price)` = r3)
coefz <- c("log(sqft)" = "log(Square Footage)",
           "bedrooms" = "Bedrooms",
           "age" = "Age")
gofz <- c("nobs", "r.squared")

modelsummary(regz,
             title = "Determinants of Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+---------------------+------------+-------------+--------------+
|                     | log(Price) | log(Price)  | log(Price)   |
+=====================+============+=============+==============+
| log(Square Footage) | 0.908***   | 1.094***    | 0.875***     |
+---------------------+------------+-------------+--------------+
|                     | (0.016)    | (0.018)     | (0.015)      |
+---------------------+------------+-------------+--------------+
| Bedrooms            |            | -0.138***   | -0.082***    |
+---------------------+------------+-------------+--------------+
|                     |            | (0.007)     | (0.006)      |
+---------------------+------------+-------------+--------------+
| Age                 |            |             | -0.006***    |
+---------------------+------------+-------------+--------------+
|                     |            |             | (0.000)      |
+---------------------+------------+-------------+--------------+
| Num.Obs.            | 2930       | 2930        | 2930         |
+---------------------+------------+-------------+--------------+
| R2                  | 0.523      | 0.580       | 0.731        |
+---------------------+------------+-------------+--------------+
&#10;Table: Determinants of Sale Price
&#10;</details>

Interpreting these coefficients is similar to the case where you have
only two explanatory variables.

<details open>

<summary>

Coefficient Interpretation for Model 3
</summary>

1.  A 1% increase in square footage, holding bedrooms and age constant,
    increases sale price by 0.875%.
2.  An additional bedroom, holding age and square footage constant,
    reduces sale price by 8.2%.
3.  An additional year of age, holding square footage and bedrooms
    constant, reduces sale price by 0.6%.

</details>

## Evaluating $R^2$

Something else to note is how the $R^2$ changes from model to model as
we add explanatory/control variables. Each new control variable adds a
little bit more information, which improves the model’s ability to
explain. As a way to visualize this, we can plot the fitted values
against the true, observed values. Since all three models have
log(price) as the outcome, both axes are in log(price). If the model was
*perfect* at explaining prices, all of the points would fall on the red
45°, $y = x$ line.

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 3))
ylim <- range(r1$fitted.values, r2$fitted.values, r3$fitted.values)
plot(log(ames$price), r1$fitted.values, ylim = ylim,
     ylab = "Fitted log(Price)", xlab = "",
     main = "Controls: sqft",
     col = scales::alpha("black", 0.2), pch = 19)
abline(0, 1, col = "tomato")
legend("bottomright", bty = "n", cex = 2,
       legend = paste0("R2: ", round(summary(r1)$r.squared, 3)))
plot(log(ames$price), r2$fitted.values, ylim = ylim,
     xlab = "Observed log(Price)", ylab = "",
     main = "Controls: sqft + bedrooms",
     col = scales::alpha("black", 0.2), pch = 19)
abline(0, 1, col = "tomato")
legend("bottomright", bty = "n", cex = 2,
       legend = paste0("R2: ", round(summary(r2)$r.squared, 3)))
plot(log(ames$price), r3$fitted.values, ylim = ylim,
     ylab = "", xlab = "",
     main = "Controls: sqft + bedrooms + age",
     col = scales::alpha("black", 0.2), pch = 19)
abline(0, 1, col = "tomato")
legend("bottomright", bty = "n", cex = 2,
       legend = paste0("R2: ", round(summary(r3)$r.squared, 3)))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module05_img/05-02-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Three plots of fitted vs observed values. The correlation between the two increases in each plot from left to right." />

</details>

As control variables are included, the $R^2$ increases and the points
start to get tighter to the diagonal line. This means that the predicted
values are getting closer to the actual values.

<details>

<summary>

A note about $R^2$
</summary>

You should *not* choose which variables are (or are not) important based
on changes in $R^2$. Technically, it is impossible for $R^2$ to decrease
after you add another variable. Of course, variables that add a lot of
explanatory power to your model should be considered, but $R^2$ does not
tell you which model is best. As we will see later, there are trade-offs
faced when including/excluding variables, so you should rely on theory
and intuition to guide your modeling decisions.
</details>

Points that are above the 45° line are expected to have higher sale
prices than what they actually sold for. Points below the line sold for
higher prices than what the model predicted.
