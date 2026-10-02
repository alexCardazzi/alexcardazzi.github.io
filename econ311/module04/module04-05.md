# One X Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Ames, Iowa

> Location, Location, Location

Real estate agents will tell you that the most important factor in
determining the price of a property is location. Whether or not this is
true, there are obviously other things that contribute to a property’s
value. For example, square footage, number of bedrooms, age, condition,
etc. all play a role in determining a home’s value.

<details>

<summary>

A note about endogeneity.
</summary>

If you’ve taken Transportation Economics, you’ll be familiar with the
Monocentric City Model. In this model, housing characteristics,
specifically square footage, is endogenously chosen. In other words,
distance to the city center will change the size and price of dwellings.
So, if you find a correlation between size and price, there might a
third variable lurking (i.e., distance to a city’s center) that
determines both. Therefore, the correlation or regression between square
footage (or most other housing characteristics) and price is *not*
causal.
</details>

We are going to estimate a bunch of regressions using the `ames` data to
demonstrate economic interpretations of OLS coefficients.

The data come from real estate transactions in [Ames,
Iowa](https://en.wikipedia.org/wiki/Ames,_Iowa)
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv);
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/ames.html)).
There are a lot of columns in the `ames` data. We are only interested in
a few (for now), so I am going to keep only the columns we’ll use. We
will also use `modelsummary` again, so load it first.

``` r
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
```

### Summary Statistics

Let’s rename some columns and take a peek at our data.

``` r
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
View(head(ames, 100)) # View() in RStudio will allow you to see the data in a tab.
```

<details>

<summary>

Output
</summary>

        price sqft bedrooms yr_built condition
    1  215000 1656        3     1960         5
    2  105000  896        2     1961         6
    3  172000 1329        3     1958         6
    4  244000 2110        3     1968         5
    5  189900 1629        3     1997         5
    6  195500 1604        3     1998         6
    7  213500 1338        2     2001         5
    8  191500 1280        2     1992         5
    9  236500 1616        2     1995         5
    10 189000 1804        3     1999         5

</details>

Next, let’s use `modelsummary` to create a summary statistics table.

``` r
datasummary_skim(ames)
```

<details><summary>Output</summary>
&#10;
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
|           | Unique | Missing Pct. | Mean     | SD      | Min     | Median   | Max      | Histogram                                                                                                                              |
+===========+========+==============+==========+=========+=========+==========+==========+========================================================================================================================================+
| price     | 1032   | 0            | 180796.1 | 79886.7 | 12789.0 | 160000.0 | 755000.0 | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module04\tinytable_assets\tinytable_5_iddgwvwl43vy569v5qnihk.png){ height=16 } |
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| sqft      | 1292   | 0            | 1499.7   | 505.5   | 334.0   | 1442.0   | 5642.0   | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module04\tinytable_assets\tinytable_4_id4fr7n23s7w79l62hxl9e.png){ height=16 } |
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| bedrooms  | 8      | 0            | 2.9      | 0.8     | 0.0     | 3.0      | 8.0      | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module04\tinytable_assets\tinytable_1_idmaieof4zsmfzoijqjei8.png){ height=16 } |
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| yr_built  | 118    | 0            | 1971.4   | 30.2    | 1872.0  | 1973.0   | 2010.0   | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module04\tinytable_assets\tinytable_3_id3ghu49nmi3wcnu5btonz.png){ height=16 } |
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| condition | 9      | 0            | 5.6      | 1.1     | 1.0     | 5.0      | 9.0      | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module04\tinytable_assets\tinytable_2_idndrcy9zqxz5ahwhkdxb6.png){ height=16 } |
+-----------+--------+--------------+----------+---------+---------+----------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
&#10;</details>

<!-- Next, let's use the `pairs()` function to make some summary plots. -->

<!-- ```{r} -->

<!-- pairs(ames, col = scales::alpha("tomato", .05), pch = 19) -->

<!-- ``` -->

Interpreting the summary statistics table:

- `price`: This appears to be measured in dollars with a lot of
  variation (e.g. about 1000 unique values out of about 3000
  observations). The average sale price is \$180,000 with a standard
  deviation of \$80,000. The standard deviation is quite high relative
  to the mean, meaning the distribution is very wide. It’s likely there
  are a lot of outliers in the right tail, which is typical of housing
  price data.

- `sqft`: This exhibits very similar characteristics as the `price`
  variable. The mean is 1,500 with a long right tail, which means there
  are a few *very* large homes.

- `bedrooms`: This variable only takes on 8 unique values, meaning there
  is not much variation. The average home has about three bedrooms,
  which matches my prior expectations of average houses. Once again,
  there is likely a long right tail, evidenced by the maximum of eight
  bedrooms.

- `yr_built`: This variable has a maximum of 2010, which would be brand
  new construction, but a mean of about 1970. The minimum value is 1870,
  which would be quite an old home.

- `condition`: This variable has a mean and median of about 5. If 5
  means “average condition”, then this would make sense. However, any
  quantitative interpretation of this variable is effectively
  meaningless. What does it mean for a home to improve from a 6 to a 7?
  Is this the same as moving from a 3 to a 4? This is an *ordinal*
  variable, and should not be considered in a linear regression. We will
  explore this variable nonetheless.

It’s always helpful to create distribution plots for your main
variables. Sometimes, this step can help inform you when making modeling
decisions. For example, when variables have long right tails, you’ll
often see people use a `log` transformation.

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 2))
plot(table(round(ames$price/10000)) / nrow(ames),
     xlab = "Price", ylab = "Rel. Freq.")
# plot(table(round(ames$sqft/100)))
plot(table(round(log(ames$price), 1)) / nrow(ames),
     xlab = "log(Price)", ylab = "Rel. Freq.")
par(mfrow = c(1, 1))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-05-unnamed-chunk-7-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Two distributions side-by-side. On the left is the distribution of sale price.  On the right is the distribution of log(sale price)." />

</details>

### Log Models

What would the log transformation do to our models? Let’s write down
four models, visualize the data, and examine estimated parameters:

- Level - Level:
  $\text{Price}_i = \alpha_0 + \alpha_1 \times \text{Sq. Ft.}_i + \epsilon_i$
- Log - Level:
  $log(\text{Price}_i) = \beta_0 + \beta_1 \times \text{Sq. Ft.}_i + \epsilon_i$
- Level - Log:
  $\text{Price}_i = \gamma_0 + \gamma_1 \times log(\text{Sq. Ft.}_i) + \epsilon_i$
- Log - Log:
  $log(\text{Price}_i) = \delta_0 + \delta_1 \times log(\text{Sq. Ft.}_i) + \epsilon_i$

Of course, log() is a non-linear transformation. However, that does not
go against our definition of a linear model. For a model to be linear,
all variables, no matter their transformation, must enter into the model
linearly. Therefore, we can have *anything* of the form
$Y = \alpha + \beta X + \epsilon$, even if $X$ is non-linear. In fact,
we could estimate a model like: $Y = \alpha + \beta^X + \epsilon$
following a (relatively) simple log transformation.

<details open>

<summary>

Plot
</summary>

<img src="module04_img/04-05-unnamed-chunk-8-1.svg" style="width:90.0%"
data-fig-align="center" />

</details>

<!-- ```{r results='hold', fig.show='hold', out.width = '99%', echo=FALSE, fold.plot=FALSE, fig.alt = "Square feet vs log(price)"} -->

<!-- par(mar = c(2.6, 2.6, 2.6, 0.1)) -->

<!-- plot(ames$sqft, log(ames$price), -->

<!--      xlab = "", ylab = "", -->

<!--      pch = 19, col = scales::alpha("black", .2), -->

<!--      xaxt = "n", yaxt = "n") -->

<!-- title(main = "Log - Level", -->

<!--       xlab = "Sqft", ylab = "log(Price)", -->

<!--       line = 1, cex.lab = 2, cex.main = 2) -->

<!-- ``` -->

<!-- ```{r results='hold', fig.show='hold', out.width = '99%', echo=FALSE, fold.plot=FALSE, fig.alt = "log(Square feet) vs price"} -->

<!-- par(mar = c(2.6, 2.6, 2.6, 0.1)) -->

<!-- plot(log(ames$sqft), ames$price, -->

<!--      xlab = "", ylab = "", -->

<!--      pch = 19, col = scales::alpha("black", .2), -->

<!--      xaxt = "n", yaxt = "n") -->

<!-- title(main = "Level - Log", -->

<!--       xlab = "log(Sqft)", ylab = "Price", -->

<!--       line = 1, cex.lab = 2, cex.main = 2) -->

<!-- ``` -->

<!-- ```{r results='hold', fig.show='hold', out.width = '99%', echo=FALSE, fold.plot=FALSE, fig.alt = "log(Square feet) vs log(price)"} -->

<!-- par(mar = c(2.6, 2.6, 2.6, 0.1)) -->

<!-- plot(log(ames$sqft), log(ames$price), -->

<!--      xlab = "", ylab = "", -->

<!--      pch = 19, col = scales::alpha("black", .2), -->

<!--      xaxt = "n", yaxt = "n") -->

<!-- title(main = "Log - Log", -->

<!--       xlab = "log(Sqft)", ylab = "log(Price)", -->

<!--       line = 1, cex.lab = 2, cex.main = 2) -->

<!-- ``` -->

Next, to estimate each equation:

<details class="code-fold">
<summary>Code</summary>

``` r
r_price_sqft <- lm(price ~ sqft, ames)
r_lprice_sqft <- lm(log(price) ~ sqft, ames)
r_price_lsqft <- lm(price ~ log(sqft), ames)
r_lprice_lsqft <- lm(log(price) ~ log(sqft), ames)

regz <- list(`Level - Level` = r_price_sqft,
             `Log - Level` = r_lprice_sqft,
             `Level - Log` = r_price_lsqft,
             `Log - Log` = r_lprice_lsqft)
coefz <- c("sqft" = "Sq. Ft.",
           "log(sqft)" = "log(Sq. Ft.)",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of Sq. Ft. on Sale Price",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

</details>

<details><summary>Output</summary>
&#10;
+--------------+---------------+-------------+-----------------+-----------+
|              | Level - Level | Log - Level | Level - Log     | Log - Log |
+==============+===============+=============+=================+===========+
| Sq. Ft.      | 111.694***    | 0.001***    |                 |           |
+--------------+---------------+-------------+-----------------+-----------+
|              | (2.066)       | (0.000)     |                 |           |
+--------------+---------------+-------------+-----------------+-----------+
| log(Sq. Ft.) |               |             | 171010.918***   | 0.908***  |
+--------------+---------------+-------------+-----------------+-----------+
|              |               |             | (3269.113)      | (0.016)   |
+--------------+---------------+-------------+-----------------+-----------+
| Constant     | 13289.634***  | 11.180***   | -1060765.031*** | 5.430***  |
+--------------+---------------+-------------+-----------------+-----------+
|              | (3269.703)    | (0.017)     | (23757.890)     | (0.116)   |
+--------------+---------------+-------------+-----------------+-----------+
| Num.Obs.     | 2930          | 2930        | 2930            | 2930      |
+--------------+---------------+-------------+-----------------+-----------+
| R2           | 0.500         | 0.484       | 0.483           | 0.523     |
+--------------+---------------+-------------+-----------------+-----------+
&#10;Table: Effect of Sq. Ft. on Sale Price
&#10;</details>

### Interpreting $\beta$

Interpreting the coefficients of the first model (Level - Level) is
similar to how we interpreted the first model of SAT scores and GPAs. If
we increase the size of a property by one square foot, we would expect
the price to increase by \$111.69. Ultimately, this is a very small
amount relative to the average and standard deviation of sale price.
However, an increase of one square foot is also small relative to the
mean and standard deviation of square footage.

When people contemplate additions to their homes, they usually consider
adding whole rooms. For the sake of argument, suppose rooms are about
200 square feet. Then, an additional room’s worth of square footage
would increase sale price by \$22,338, which is a much more intuitive
number.

To interpret the constant, we have to ask ourselves whether it makes
sense for square footage to be equal to zero. In reality, yes, and that
could be interpreted as the value of the land the property is sitting
on. However, since the minimum square footage in the data is 334, we
should avoid interpreting the constant in this case.

Interpreting models with logarithms switches the units from dollars or
square feet to percentages. For example, we would interpret the
coefficient in the second column as follows: if the property’s size
increases by one square foot, we would expect price to increase by
0.056%[^1].

As another way to think about this, we know how a one unit increase in
square footage would impact price – it would increase it by \$111.69.
Relative to the average house price (\$180,796.1), this is 0.062%. These
two models generate very similar output, but put it differently.

To interpret the level-log model, we would say: if square footage
increases by one percent, the sale price would increase by \$1,710.11.
Again, we are going to avoid interpreting the constant in this case.

Finally, to interpret the last model, both variables’ units are changed
to percentages. This model says that if the size of a property increases
by one percent, its sale price is expected to increase by 0.9%.

Importantly, this coefficient is [*an
elasticity*](https://en.wikipedia.org/wiki/Elasticity_(economics))! In
other words, it measures the responsiveness of one variable to another.
In this case, since the elasticity is less than 1%, sale price is
*inelastic* with respect to property size.

### Hypothesis Testing

Think back to hypothesis testing where we tested if a coefficient was
different from zero. To do this, we divided the coefficient (minus 0) by
its standard error. This gave us a t-statistic that we could then
convert into a p-value. In fact, R does *all* of this for us in
`summary()`, and `modelsummary` produces stars to represent p-values.

Now, instead of comparing our coefficient to 0 (which would tell us
whether the independent variable is related to the outcome variable), we
could compare it to 1. This would set up [unit
elasticity](https://corporatefinanceinstitute.com/resources/economics/unit-elastic/)
as the null hypothesis. Why would we do this? If we can reject that the
coefficient is equal to 1, we would have statistical evidence that price
is indeed inelastic with respect to square footage. It’s important to
note that this is much stronger than saying the relationship is
inelastic because the coefficient is less than 1.

What would a hypothesis test look like then?

<div class="aside">

In the following code, I am using `pnorm()`, which assumes a z-score. In
fact, we should be using a t-statistic for this. However, with the
number of observations we have, z and t will not differ much.

</div>

``` r
coefz <- coef(summary(r_lprice_lsqft))
coefz; cat("\n")
test_stat <- (coefz[2,1] - 1) / coefz[2,2]
cat("p-value:", format(2 * pnorm(abs(test_stat), lower.tail = F), scientific = F, digits = 3))
```

<details>

<summary>

Output
</summary>

                 Estimate Std. Error  t value Pr(>|t|)
    (Intercept) 5.4301860 0.11644484 46.63312        0
    log(sqft)   0.9078053 0.01602294 56.65659        0

    p-value: 0.00000000872

</details>

Given this p-value, we can reject the null hypotheses that $\beta_1 = 1$
(in addition to previously rejecting that $\beta_1 = 0$). Therefore, the
data seem to support the idea that price is inelastic, or less than
proportionally responsive, to square footage.

So what? Who cares?

Suppose a contractor tells you that it will cost \$$x$ dollars to
increase the size of your house by 25%. Assuming you want to sell this
home, and the current expected sale price is \$200,000, for what values
of $x$ should you expand your home? According to the model, a 25%
increase in the size of a home is worth an increase in sale price of 25%
$\times$ 0.9. On the open market, this addition would increase your sale
value by: \$200,000 $\times$ 0.25 $\times$ 0.9 = \$45,000. Therefore,
investing in the addition would only be worthwhile if X \< 45,000.

### Goodness of Fit

As a final note, examine the R$^2$ for each of these estimations. The
fourth model fits the data the best followed by the first model. This
might not be too surprising after taking a look at the initial
scatterplots. The visual relationships in the level-level and log-log
plots appear to be the most “linear”. Compare these with the other two
plots which appear to be [convex or
concave](https://i.stack.imgur.com/GNBZ4.png).

Remember, R$^2$ is **not** the be-all end-all for determining which
model is best. However, this does provide us with some idea of which
functional form (log-log) we should be partial to.

### Log Interpretation Table

Below is a table to help you remember how to interpret each type of
log-model.

| Model | Equation | Interpretation |
|----|----|----|
| Level-Level | $Y = \beta_0 + \beta_1 X$ | One unit change in $X$ leads to a $\beta$ unit change in $Y$. |
| Log-Linear | $\text{log}(Y) = \beta_0 + \beta_1 X$ | One unit change in $X$ leads to a $\beta \times 100$ percent change in $Y$.[^2] |
| Linear-Log | $Y = \beta_0 + \beta_1 \text{log}(X)$ | One percent change in $X$ leads to a $\beta \div 100$ unit change in $Y$. |
| Log-Log | $\text{log}(Y) = \beta_0 + \beta_1 \text{log}(X)$ | One percent change in $X$ leads to a $\beta$ percent change in $Y$. |

Coefficient Interpretation

<!-- ## Stability of $\beta_1$ {.smaller} -->

<!-- ```{r} -->

<!-- ames$id <- 1:nrow(ames) -->

<!-- all1 <- list() -->

<!-- set.seed(757) -->

<!-- n <- 30; N <- nrow(ames) -->

<!-- ames <- ames[sample(1:N, N, FALSE),] -->

<!-- for(i in n:N){ -->

<!--   all1[[length(all1) + 1]] <- summary(lm(log(price) ~ log(sqft), data = ames[1:i,]))$coefficients -->

<!-- } -->

<!-- all <- do.call(rbind, all1) -->

<!-- x <- c(n:N, rev(n:N)) -->

<!-- y <- c(all[c(F,T),1] + 1.96*all[c(F,T),2], rev(all[c(F,T),1] - 1.96*all[c(F,T),2])) -->

<!-- plot(n:N, all[c(F,T),1], type = "l", ylim = range(y), las = 1, -->

<!--      xlab = "Number of Observations", -->

<!--      ylab = "Coefficient Estimate") -->

<!-- polygon(x, y, col = scales::alpha("black", 0.2), border = NA) -->

<!-- abline(h = 1) -->

<!-- ``` -->

[^1]: The coefficient in the table does not match because it is rounded
    from 0.00056 to 0.001

[^2]: If $\beta = 0.03$, this means a one unit change in $X$ leads to 3%
    change in $Y$.
