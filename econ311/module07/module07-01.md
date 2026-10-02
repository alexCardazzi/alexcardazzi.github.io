# Fixed Effects and the Limits of Regression
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Fixed Effects

One of the most common uses for dummy variables is for *fixed effects*.
A common use case for fixed effects is when you are working with a
time-by-unit panel. For example: a state-by-year panel.

By including *state* dummies, or fixed effects, we are removing /
controlling for any and all constant (time-invariant) factors. As an
example, if the outcome variable in our state-by-year panel is housing
prices, we can reasonably expect California to have high prices and West
Virginia to have low prices. Why? For a variety of reasons: weather and
other local amenities, economic factors, etc. To make the states more
comparable to one another, we can remove the effect of the
[idiosyncratic](https://en.wikipedia.org/wiki/Idiosyncrasy) differences
intrinsic to each state.

We can also include *year* fixed effects, which control for any and all
factors that impact each state at the same time in the same way. For
example, the 2007 financial crisis reduced housing prices across the
country. When we include dummies for each year (except one, the
reference group), we can remove the influence of these events on our
outcome.

When using fixed effects, we write our equations a bit differently. If
we have a state-by-year panel, you will see equations written like the
following:

$$Y_{st} = \beta_s + \beta_t + \beta_1 X_{st} + \beta_2 Z_{st} + \epsilon_{st}$$

where $\beta_s$ represents state ($s$) fixed effects and $\beta_t$
represents time ($t$) fixed effects. Notice how we do not include
$\beta_0$ (the intercept). This is because the fixed effects take the
place of the intercept in practice.

<div class="aside">

When we estimate fixed effects with `lm()`, R still reports an
intercept. That is because R drops one dummy from each set of fixed
effects (the reference groups, like we saw in Module 6) to avoid perfect
multicollinearity. The intercept is then the expected outcome for the
reference state in the reference year.

</div>

## Income and Housing

To demonstrate fixed effects, we are going to compare [per-capita
personal income](https://fred.stlouisfed.org/series/PCPI51710) and
[housing price
indices](https://fred.stlouisfed.org/series/ATNHPIUS51710A) for
different counties/cities in Hampton Roads over time. Let’s take a look
at the data files we have.

First, we have the [FIPS
codes](https://en.wikipedia.org/wiki/FIPS_county_code) for each of the
areas of interest. FIPS codes are unique identifiers for counties/cities
in the United States. The first two digits denote the state and the
final three numbers denote the county or city within that state.

``` r
hr <- read.csv("https://alexcardazzi.github.io/econ311/data/hr_fips.csv")
hr # hr is "hampton roads"
```

<details>

<summary>

Output
</summary>

                                        name  fips
    1                      Gloucester County 51073
    2                   Isle of Wight County 51093
    3  James City County + Williamsburg City 51931
    4                         Mathews County 51115
    5                            York County 51199
    6                        Chesapeake City 51550
    7                           Hampton City 51650
    8                      Newport News City 51700
    9                           Norfolk City 51710
    10                       Portsmouth City 51740
    11                          Suffolk City 51800
    12                   Virginia Beach City 51810

</details>

The other two datasets we are going to use contain per-capita personal
income and house price index values. Both contain observations by FIPS
code and year.

<div class="columns">

<div class="column" width="50%">

``` r
inc <- read.csv("https://alexcardazzi.github.io/econ311/data/pcpi.csv")
head(inc, 12) # inc is "personal income"
```

<details>

<summary>

Output
</summary>

                        series_id value  fips year
    1  Per Capita Personal Income  3696 51073 1969
    2  Per Capita Personal Income  3916 51073 1970
    3  Per Capita Personal Income  4278 51073 1971
    4  Per Capita Personal Income  4812 51073 1972
    5  Per Capita Personal Income  5115 51073 1973
    6  Per Capita Personal Income  5329 51073 1974
    7  Per Capita Personal Income  5815 51073 1975
    8  Per Capita Personal Income  6493 51073 1976
    9  Per Capita Personal Income  6986 51073 1977
    10 Per Capita Personal Income  7819 51073 1978
    11 Per Capita Personal Income  8297 51073 1979
    12 Per Capita Personal Income  9478 51073 1980

</details>

</div>

<div class="column" width="50%">

``` r
hp <- read.csv("https://alexcardazzi.github.io/econ311/data/athpi.csv")
head(hp, 12) # hp is "housing price"
```

<details>

<summary>

Output
</summary>

                                series_id  value  fips year
    1  All-Transactions House Price Index  83.51 51073 1993
    2  All-Transactions House Price Index  83.21 51073 1994
    3  All-Transactions House Price Index  85.19 51073 1995
    4  All-Transactions House Price Index  86.72 51073 1996
    5  All-Transactions House Price Index  88.11 51073 1997
    6  All-Transactions House Price Index  92.83 51073 1998
    7  All-Transactions House Price Index  95.75 51073 1999
    8  All-Transactions House Price Index 100.00 51073 2000
    9  All-Transactions House Price Index 104.24 51073 2001
    10 All-Transactions House Price Index 109.65 51073 2002
    11 All-Transactions House Price Index 115.22 51073 2003
    12 All-Transactions House Price Index 131.49 51073 2004

</details>

</div>

</div>

We will need to “merge” these three data files in order to run our
regression. In other words, we need to pull information from one file
and place it in another file.

## Merging Datasets

To begin merging, we should check out which data set has the most
“coverage”. For example, the `inc` dataset contains the range of years
{1969, 2021} whereas `hp` contains {1975, 2022}. This can also be seen
graphically:

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 2))
plot(table(inc$year), las = 1,
     main = "Personal Income",
     xlab = "Year", ylab = "Frequency")
plot(table(hp$year), las = 1,
     main = "Housing Price Index",
     xlab = "Year", ylab = "Frequency")
par(mfrow = c(1, 1))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module07_img/07-01-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Distribution plots of year for both personal income and housing price index." />

</details>

These figures suggest we should merge *into* `inc`, or else we would
potentially be dropping data. It’s always a good idea to have too much
than too little.

Next, we are going to merge in the names from `hr` into `inc` so we can
have a better way to decipher which FIPS code is which county/city. To
do this, we are going to match the entries in `fips` in `inc` to the
entries in `fips` in `hr`. We are going to use `match()` to help us with
this.

`match()` accepts two arguments. The first is the values to be matched,
and the second is the values to match against. The resulting vector
tells us the location of each element of the first vector (`inc$fips`)
in the second vector (`hr$fips`).

As a simple example, consider the following. I have a short vector of
colleges/universities (`v1`). We have a second data source (`ex`) that
contains the state these institutions are located. Use the match
function below to pull the state for each institution in the first
vector from the second data source. The result should be
`"VA" "WV" "NJ"`.

``` r
v1 <- c("ODU", "WVU", "RCNJ")
ex <- data.frame(name = c("RCNJ", "RU", "Union", "ODU", "WVU", "WCU"),
                 state = c("NJ", "NJ", "NY", "VA", "WV", "NC"))
the_match <- match() # enter here
the_match; cat("\n")
ex$state[the_match]
```

<details>

<summary>

Solution
</summary>

<details class="code-fold">
<summary>Code</summary>

``` r
v1 <- c("ODU", "WVU", "RCNJ")
ex <- data.frame(name = c("RCNJ", "RU", "Union", "ODU", "WVU", "WCU"),
                 state = c("NJ", "NJ", "NY", "VA", "WV", "NC"))
the_match <- match(v1, ex$name)
the_match; cat("\n")
ex$state[the_match]
```

</details>

<details>

<summary>

Output
</summary>

    [1] 4 5 1

    [1] "VA" "WV" "NJ"

</details>

</details>

## Merging by One Variable

Now let’s use the same code to pull the FIPS names from `hr`.

<details class="code-fold">
<summary>Code</summary>

``` r
m <- match(inc$fips, hr$fips)
inc$name <- hr$name[m]
View(inc[,c("value", "fips", "year", "name")])
```

</details>

<details>

<summary>

Output
</summary>

       value  fips year              name
    1   3696 51073 1969 Gloucester County
    2   3916 51073 1970 Gloucester County
    3   4278 51073 1971 Gloucester County
    4   4812 51073 1972 Gloucester County
    5   5115 51073 1973 Gloucester County
    6   5329 51073 1974 Gloucester County
    7   5815 51073 1975 Gloucester County
    8   6493 51073 1976 Gloucester County
    9   6986 51073 1977 Gloucester County
    10  7819 51073 1978 Gloucester County

</details>

It worked! We have successfully pulled over the county name for each
FIPS code.

Next, we need to pull in the housing price index data from `hp`. We can
try a similar process:

<details class="code-fold">
<summary>Code</summary>

``` r
m <- match(inc$fips, hp$fips)
inc$hp <- hp$value[m]
View(inc[,c("value", "fips", "year", "hp")])
```

</details>

<details>

<summary>

Output
</summary>

       value  fips year    hp
    1   3696 51073 1969 83.51
    2   3916 51073 1970 83.51
    3   4278 51073 1971 83.51
    4   4812 51073 1972 83.51
    5   5115 51073 1973 83.51
    6   5329 51073 1974 83.51
    7   5815 51073 1975 83.51
    8   6493 51073 1976 83.51
    9   6986 51073 1977 83.51
    10  7819 51073 1978 83.51

</details>

While we were able to pull a number from the dataset, the code did not
work as we intended. There should be variation in `hp` across years.
When using `match()`, R will always return the *first* instance of a
match, but that is not necessarily what we want. Since we did not
specify anything about years, R pulled the first value for each FIPS
which is not what we’re looking for.

## Merging by Two Variables

We need to create a variable that contains information about FIPS *and*
year. To do this, we are going to use `paste()`. This function takes two
or more variables and “pastes” them together into one. As an example:
`paste(c("ODU", "WVU"), c("Monarchs", "Mountaineers"))`: ODU Monarchs,
WVU Mountaineers.

``` r
m <- match(paste(inc$fips, inc$year),
           paste(hp$fips, hp$year))
inc$hp <- hp$value[m]
View(inc[,c("value", "fips", "year", "hp")])
```

<details>

<summary>

Output
</summary>

       value  fips year hp
    1   3696 51073 1969 NA
    2   3916 51073 1970 NA
    3   4278 51073 1971 NA
    4   4812 51073 1972 NA
    5   5115 51073 1973 NA
    6   5329 51073 1974 NA
    7   5815 51073 1975 NA
    8   6493 51073 1976 NA
    9   6986 51073 1977 NA
    10  7819 51073 1978 NA

</details>

You might notice that there is quite a bit of missing data in the `hp`
column of `inc`. That is because, if you recall, we have more personal
income data than housing price index data. Now that we have the two
variables lined up in the same data frame, let’s explore their
relationship a bit. First, let’s plot the two variables in a
scatterplot:

``` r
inc$value000 <- inc$value/1000
plot(inc$value000, inc$hp, las = 1,
     xlab = "Personal Income (in Thousands)", pch = 19,
     ylab = "Housing Price Index (100 is 2000 $'s)",
     col = scales::alpha("mediumseagreen", 0.4))
```

<details>

<summary>

Plot
</summary>

<img src="module07_img/07-01-unnamed-chunk-15-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Personal Income vs Housing Price Index" />

</details>

## Estimating Fixed Effects

There appears to be a pretty strong, positive relationship between these
variables. Now let’s take a look at what some regressions tell us about
the strength of the relationships. First, we are going to estimate a
model where housing price index is the outcome and personal income is
the explanatory variable. Next, we are going to include FIPS and year
fixed effects.

``` r
b_pooled <- coef(lm(hp ~ value000, inc))[2]
b_fe <- coef(lm(hp ~ value000 + as.factor(fips) + as.factor(year), inc))[2]
b_pooled; cat("\n")
b_fe
```

<details>

<summary>

Output
</summary>

    value000 
    3.698684 

     value000 
    0.4945878 

</details>

Why are these two coefficients so different?

We have already investigated what happens when we include control
variables into our regressions: their effects get removed from both the
outcome variable and the other explanatory variables. In this case, it
is likely that *time* is an important third variable that drives both
housing prices *and* personal income. Let’s revisit the coefficient bias
table from Module 5.3. Since time likely has a positive effect on both
housing prices and personal income, the bias is likely positive. In
other words, by not accounting for time, the coefficient magnitudes are
being artificially pushed in positive direction.

<div class="aside">

`lm()` can estimate all of these dummies, but with hundreds of counties
or states it gets slow and messy. In ECON 400, you will use `feols()`
from the `fixest` package, which handles fixed effects for you with a
`|` in the formula.

</div>

## FWL Theorem

Let’s take a look at what happens when we partial out the effect of our
“fixed effects”.

We are going to use a trick where we run two regressions with *only*
these fixed effects. One of these regressions will have housing price as
the outcome variable and the other regression will have personal income
as the outcome. After each estimation, we are going to collect the
residuals, which is the variation left over after having removed the
influence of the fixed effects. Then, we are going to plot the two sets
of residuals against each other and run a third regression between them.

``` r
# First two regressions:
r_hp <- lm(hp ~ as.factor(fips) + as.factor(year),
           inc, subset = !is.na(inc$hp))
r_inc <- lm(value000 ~ as.factor(fips) + as.factor(year),
            inc, subset = !is.na(inc$hp))

plot(r_inc$residuals, r_hp$residuals, las = 1)

# Third regression, using only the residuals:
r_resid <- lm(r_hp$residuals ~ r_inc$residuals)
coef(r_resid)[2]
```

<details>

<summary>

Output
</summary>

    r_inc$residuals 
          0.4945878 

</details>

<details>

<summary>

Plot
</summary>

<img src="module07_img/07-01-unnamed-chunk-17-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Residuals of housing price index vs residuals of personal income." />

</details>

The coefficient from the fixed effects model matches the coefficient
from this funky residual model! Taking residuals from two equations like
this is called the
[Frisch–Waugh–Lovell](https://en.wikipedia.org/wiki/Frisch%E2%80%93Waugh%E2%80%93Lovell_theorem)
theorem.

What is left over in those residuals is called *within* variation: how
much a county’s income and housing prices differ from that same county’s
usual levels, in a year compared to other years. Fixed effects throw
away all of the *between* variation (high-income counties versus
low-income counties, expensive years versus cheap years), so the
coefficient only uses the within-unit variation. This is why we say
fixed effects control for anything that is constant within a county or
common to all counties in a year.

The point you should take away is that often times there are unobserved
idiosyncratic factors that need to be controlled for. As an example, our
coefficient estimate shrunk from 3.7 to 0.49 after we included fixed
effects, or by a factor of about 7.5.

## Reporting Coefficients

When reporting regression models, people are usually uninterested in
fixed effects. Rather, people like to include rows in their tables that
indicate which fixed effects are included in which models. As an
example, let’s consider the housing price and personal income
regressions.

<details class="code-fold">
<summary>Code</summary>

``` r
library("modelsummary")
options("modelsummary_factory_default" = "markdown")

fe1 <- lm(hp ~ value000, inc)
fe2 <- lm(hp ~ value000 + as.factor(fips) + as.factor(year), inc)

regz <- list(`Housing Price Index` = fe1,
             `Housing Price Index` = fe2)
coefz <- c("value000" = "Personal Income (in Thousands)")
gofz <- c("nobs", "r.squared")

rowz <- as.data.frame(matrix(c('County Fixed Effects', "No", "Yes",
                               "Year Fixed Effects", "No", "Yes"),
                             byrow = T, nrow = 2)) # 2 rows
attr(rowz, 'position') <- c(3, 4) # rows 3 and 4
# since rows 1 and 2 are coefficient and standard error

modelsummary(regz,
             title = "Effect of Personal Income on Housing Price Index",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz,
             add_rows = rowz)
```

</details>

<details><summary>Output</summary>
&#10;
+--------------------------------+---------------------+----------------------+
|                                | Housing Price Index | Housing Price Index  |
+================================+=====================+======================+
| Personal Income (in Thousands) | 3.699***            | 0.495***             |
+--------------------------------+---------------------+----------------------+
|                                | (0.077)             | (0.130)              |
+--------------------------------+---------------------+----------------------+
| County Fixed Effects           | No                  | Yes                  |
+--------------------------------+---------------------+----------------------+
| Year Fixed Effects             | No                  | Yes                  |
+--------------------------------+---------------------+----------------------+
| Num.Obs.                       | 391                 | 391                  |
+--------------------------------+---------------------+----------------------+
| R2                             | 0.856               | 0.991                |
+--------------------------------+---------------------+----------------------+
&#10;Table: Effect of Personal Income on Housing Price Index
&#10;</details>
