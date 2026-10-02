# One X Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Bedrooms

We will keep working with the Ames data. First, load the packages, read
in the data, and rename the columns as before:

``` r
library("scales")
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
```

Next, let’s estimate models with different explanatory variables. First,
we’ll estimate the relationship between sale price and number of
bedrooms.

<details class="code-fold">
<summary>Code</summary>

``` r
r_price_bedrooms <- lm(price ~ bedrooms, ames)
r_lprice_bedrooms <- lm(log(price) ~ bedrooms, ames)

regz <- list(`Price` = r_price_bedrooms,
             `log(Price)` = r_lprice_bedrooms)
coefz <- c("bedrooms" = "# of Bedrooms",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of Bedrooms on Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+---------------+---------------+------------+
|               | Price         | log(Price) |
+===============+===============+============+
| # of Bedrooms | 13889.495***  | 0.089***   |
+---------------+---------------+------------+
|               | (1765.042)    | (0.009)    |
+---------------+---------------+------------+
| Constant      | 141151.743*** | 11.767***  |
+---------------+---------------+------------+
|               | (5245.395)    | (0.027)    |
+---------------+---------------+------------+
| Num.Obs.      | 2930          | 2930       |
+---------------+---------------+------------+
| R2            | 0.021         | 0.033      |
+---------------+---------------+------------+
&#10;Table: Effect of Bedrooms on Sale Price
&#10;</details>

Breaking down the output:

1.  I did not estimate models where log(bedrooms) was the independent
    variable. This is because thinking about marginal changes in
    bedrooms as a percentage doesn’t make much sense. For example, what
    does it mean for a home to have a 1% increase in the number of
    bedrooms?
2.  All coefficients are statistically significant (or, different from
    zero) at the 0.001 level (`***`).
3.  The constant/intercept is not interpretable because a property with
    zero bedrooms cannot not exist.
4.  The slope coefficient in the first model suggests that an additional
    bedroom will increase price by about \$14,000.
5.  The slope coefficient in the second model suggests that an
    additional bedroom will increase price by about 9%.

## Home Condition

Next, we can estimate models where a home’s condition is the explanatory
variable.

<details class="code-fold">
<summary>Code</summary>

``` r
r_price_condition <- lm(price ~ condition, ames)
r_lprice_condition <- lm(log(price) ~ condition, ames)

regz <- list(`Price` = r_price_condition,
             `log(Price)` = r_lprice_condition)
coefz <- c("condition" = "# of Condition",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of Condition on Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+----------------+---------------+------------+
|                | Price         | log(Price) |
+================+===============+============+
| # of Condition | -7309.010***  | -0.018**   |
+----------------+---------------+------------+
|                | (1321.319)    | (0.007)    |
+----------------+---------------+------------+
| Constant       | 221457.104*** | 12.119***  |
+----------------+---------------+------------+
|                | (7495.923)    | (0.038)    |
+----------------+---------------+------------+
| Num.Obs.       | 2930          | 2930       |
+----------------+---------------+------------+
| R2             | 0.010         | 0.002      |
+----------------+---------------+------------+
&#10;Table: Effect of Condition on Sale Price
&#10;</details>

First of all, using the condition variable in the regression does not
lead to interpretable coefficients. Condition is an *ordinal* (and
subjective) variable, which makes a one unit change meaningless.

That said, the results of these models are suspicious. Since higher
numbers mean better condition, the coefficient estimates should still be
positive even if it’s an ordinal variable. Remember the four reasons you
might find a correlation between two variables. In this case, there is
likely a third variable that is driving both condition and sale price.

Let’s try to investigate and figure out why this might be.

First, let’s check out the distribution of the condition variable.

<details class="code-fold">
<summary>Code</summary>

``` r
plot(table(ames$condition), las = 1,
     main = "Distribution of Condition",
     xlab = "Condition", ylab = "Frequency")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-06-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Distribution plot of condition." />

</details>

Clearly, there are many homes with a condition of 5. In fact, 56% of
homes are rated to be a 5.

Next, let’s examine the average sale price for each possible value for
condition.

<details class="code-fold">
<summary>Code</summary>

``` r
agg <- aggregate(list(price = ames$price), list(condition = ames$condition), mean)
plot(agg$condition, agg$price/1000, las = 1, pch = 19,
     xlab = "Condition", ylab = "Price (in Thousands)")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-06-unnamed-chunk-7-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Average price by condition." />

</details>

There appears to be an upward trend in price as condition increases.
However, there is an outlier value when condition equals 5. There must
be a bunch of high price homes that have been given a condition of 5.

Theoretically, the condition of a home should be related to its age (or
the year it was built). Let’s examine the relationship between year
built and condition by finding the average condition for each possible
year built.

<details class="code-fold">
<summary>Code</summary>

``` r
agg <- aggregate(list(condition = ames$condition), list(yr = ames$yr_built), mean)
plot(agg$yr, agg$condition, las = 1, pch = 19,
     col = alpha("mediumseagreen", 0.6),
     xlab = "Year Built", ylab = "Average Condition")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-06-unnamed-chunk-8-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Average condition by year built" />

</details>

There seems to be a negative relationship between year built and
condition. However, the correlation between year built and price is
positive. It’s likely that year built is driving both price **and**
condition simultaneously. The negative coefficient between condition and
price is due to the negative relationship between year built and
condition.

## Home Age

What do models look like where year built is the main explanatory
variable? Using the year a home was built does not make as much
intuitive sense as thinking about the age of a home. Since all of these
homes were sold between 2006 and 2010, according to the data
description, we can calculate age of a property by subtracting the year
built from 2011. Let’s run some models with age as the explanatory
variable.

<details class="code-fold">
<summary>Code</summary>

``` r
ames$age <- 2011 - ames$yr_built
r_price_age <- lm(price ~ age, ames)
r_lprice_age <- lm(log(price) ~ age, ames)
r_price_lage <- lm(price ~ log(age), ames)
r_lprice_lage <- lm(log(price) ~ log(age), ames)

regz <- list(`Level - Level` = r_price_age,
             `Log - Level` = r_lprice_age,
             `Level - Log` = r_price_lage,
             `Log - Log` = r_lprice_lage)
coefz <- c("age" = "Age",
           "log(age)" = "log(Age)",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of Age on Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+----------+---------------+-------------+---------------+-----------+
|          | Level - Level | Log - Level | Level - Log   | Log - Log |
+==========+===============+=============+===============+===========+
| Age      | -1474.964***  | -0.008***   |               |           |
+----------+---------------+-------------+---------------+-----------+
|          | (40.493)      | (0.000)     |               |           |
+----------+---------------+-------------+---------------+-----------+
| log(Age) |               |             | -47030.296*** | -0.251*** |
+----------+---------------+-------------+---------------+-----------+
|          |               |             | (1098.021)    | (0.005)   |
+----------+---------------+-------------+---------------+-----------+
| Constant | 239269.065*** | 12.350***   | 333731.719*** | 12.838*** |
+----------+---------------+-------------+---------------+-----------+
|          | (2018.987)    | (0.010)     | (3753.501)    | (0.019)   |
+----------+---------------+-------------+---------------+-----------+
| Num.Obs. | 2930          | 2930        | 2930          | 2930      |
+----------+---------------+-------------+---------------+-----------+
| R2       | 0.312         | 0.379       | 0.385         | 0.423     |
+----------+---------------+-------------+---------------+-----------+
&#10;Table: Effect of Age on Sale Price
&#10;</details>

Interpreting these models:

1.  Increasing the age of a home by one year reduces the sale price by
    \$1,475.
2.  Increasing the age of a home by one year reduces the sale price by
    0.8%.
3.  Increasing the age of a home by one percent reduces the sale price
    by \$470.30.
4.  Increasing the age of a home by one percent reduces the sale price
    by 0.25%.

## Other Manipulations

<!-- {style="font-size:58%;"} -->

A final point for this module is what happens to coefficient estimates
when apply different transformations before estimation. So far, we’ve
shown the effect of using a log() transformation. Now we’ll look at the
effect of addition/subtraction and multiplication/division.

<details class="code-fold">
<summary>Code</summary>

``` r
r_price_sqft <- lm(price ~ sqft, ames)
r_price_sqftdiv <- lm(price ~ I(sqft/1000), ames)
r_pricediv_sqft <- lm((price/1000) ~ sqft, ames)
r_price_sqftsub <- lm(price ~ I(sqft - 1500), ames)
r_pricesub_sqft <- lm(I(price - 180000) ~ sqft, ames)

regz <- list(`Original` = r_price_sqft,
             `Sq Ft. / 1000` = r_price_sqftdiv,
             `Price / 1000` = r_pricediv_sqft,
             `Sq Ft. - 1500` = r_price_sqftsub,
             `Price - 180K` = r_pricesub_sqft)
coefz <- c("sqft" = "Sq Ft.",
           "I(sqft/1000)" = "Sq Ft.",
           "I(sqft - 1500)" = "Sq Ft.")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Variable Manipulation",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+----------+------------+---------------+--------------+---------------+--------------+
|          | Original   | Sq Ft. / 1000 | Price / 1000 | Sq Ft. - 1500 | Price - 180K |
+==========+============+===============+==============+===============+==============+
| Sq Ft.   | 111.694*** | 111694.001*** | 0.112***     | 111.694***    | 111.694***   |
+----------+------------+---------------+--------------+---------------+--------------+
|          | (2.066)    | (2066.073)    | (0.002)      | (2.066)       | (2.066)      |
+----------+------------+---------------+--------------+---------------+--------------+
| Num.Obs. | 2930       | 2930          | 2930         | 2930          | 2930         |
+----------+------------+---------------+--------------+---------------+--------------+
| R2       | 0.500      | 0.500         | 0.500        | 0.500         | 0.500        |
+----------+------------+---------------+--------------+---------------+--------------+
&#10;Table: Variable Manipulation
&#10;</details>

1.  If you divide (multiply) your explanatory variable by $x$, the
    coefficient will be multiplied (divided) by $x$.
2.  If you divide (multiply) your outcome variable by $x$, the
    coefficient will be divided (multiplied) by $x$.
3.  Adding or subtracting from either variable does not change the slope
    parameter.
