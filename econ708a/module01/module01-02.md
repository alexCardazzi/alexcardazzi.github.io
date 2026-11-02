# Introduction
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ708a.html"
target="_blank">alexcardazzi.github.io</a>.

## Econometrics

Just like how the first half of the module was a quick (re-)primer on
programming in R, this will be a short refresher on econometrics. To
start, a simple model that uses some variable $X$ to explain some
outcome $Y$ can be written as follows:

$$Y = \beta_0 + \beta_1 X + \epsilon$$ If there are multiple variables
that explain variation in $Y$, we can rewrite this equation as follows.
Econometrics is concerned with estimating, interpreting, and testing the
$\beta$’s in this equation:

$$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \epsilon$$

In this model, we would say that a one unit change in $X_1$ is
associated with a $\beta_1$ unit change in $Y$, holding $X_2$ constant.
For this interpretation to make sense, a one unit change in $X_1$ must
be tangible and concrete. For example, a one dollar increase in income
would make sense, since increasing income from \$50,000 to \$50,001 is
the same as increasing from \$60,000 to \$60,001. On the other hand,
increasing by one unit on a likert scale of happiness, or some ranking
system, does not make sense in the context of linear regression. Is
increasing happiness from a 6 to a 7 the same as increasing from 7 to 8?
Do 7s even mean the same thing for everyone? Clearly, interpreting the
coefficient if this were the variable would become difficult.

$\beta_0$ represents the expected value of $Y$ when $X_1$ and $X_2$ are
equal to zero. For this interpretation to hold weight, zero must be
realistic values that $X_1$ and $X_2$ can take on. For example, if $X_1$
is someone’s height in inches, and $Y$ is someone’s weight, then
$\beta_0$ would represent someone’s expected weight were they to be zero
inches tall. Obviously, this is ridiculous, and would make $\beta_0$
lose its interpretation. However, if $X_1$ were credit card debt, and
$Y$ was their credit score, $\beta_0$ would represent the expected
credit score for a person with \$0 in debt. In this case, the intercept,
$\beta_0$, would have a very intuitive interpretation.

### Log Models

There are times where its more natural to think about changes in
variables in terms of percentages rather than units. To do this, we have
to apply a logarithmic transformation to the variable we want to think
about in percent changes. If we wanted to think about $X_1$ like this,
we would rewrite our model as:

$$Y = \beta_0 + \beta_1 \text{log}(X_1) + \beta_2 X_2 + \epsilon$$

When our model is written this way, we would say, “a one **percent**
increase in $X_1$ is associated with a $\beta_1 \div 100$ unit change in
$Y$. If this were swapped, where $Y$ was transformed and $X_1$ was not,
we would interpret the model as,”a one unit increase in $X$ is
associated with a $\beta_1 \times 100$ percent increase in $Y$.” If
*both* variables were transformed, $\beta_1$ would represent the
elasticity of $Y$ with respect to $X_1$. In other words, a one percent
increase in $X_1$ would be associated with a $\beta_1$ percent increase
in $Y$. See [this
table](https://alexcardazzi.github.io/econ311/module04/module04-05.html#log-interpretation-table)
for a reminder on how to interpret log models.

### Binary Variables

In this course, we’ll often work with binary variables. These variables
are binary in the sense that they can take on values of either one or
zero. These are used to represent factors that are dichotomous such as
sex, whether some is insured, or if they’ve attended some training. Back
to our original model, if $X_1$ was binary, we would interpret $\beta_1$
as the difference in expected value of $Y$ for when $X_1 = 1$ compared
to when $X_1 = 0$, holding other factors constant. This, if you think
about it, still represents a one unit increase in $X_1$. You can also
think of binary variables as on/off switches. For example, $X_1$ could
represent whether some policy has been enacted since this would
otherwise be difficult to quantify.

Binary variables are also often used as outcome variables. In these
cases, the things we are trying to explain would be yes/no outcomes. If
$Y$ is binary, and $X_1$ is continuous, then we would say, “a one unit
increase in $X_1$ is associated with a $\beta_1$ percent increase in the
probability of $Y$ being equal to 1. These types of models are called
linear probability models (LPM).

LPMs are used because they are intuitive to think about, but they do
have some drawbacks. Most notably, predicted probabilities can be
greater than 1 (and less than 0) in LPMs. To address this issue, we can
also estimate logistic regressions, which are built to handle
dichotomous outcome variables. The coefficients in logistic regressions
are difficult to interpret on their own, so we will avoid their
discussion for now. That said, you should be aware of their existence.

### Interactions

Another nuance we can add to models are interactions. By using
interactions, we allow the effects of some variable $X_1$ to vary by
different values of $X_2$. Let’s suppose $X_1$ is continuous and $X_2$
is binary. We could estimate the following model:

$$Y = \beta_0 + \beta_1 X_1 + \beta_2 (X_1 \times X_2) + \epsilon$$

When $X_2 = 0$, a one unit increase in $X_1$ leads to a $\beta_1$ unit
change in $Y$. To be complete, we can write out the algebra as follows:

$$\begin{aligned}\Delta Y =& \ \beta_0 + \beta_1(X_1 + 1) + \beta_2((X_1 + 1) \times 0) \\ 
&-[\beta_0 + \beta_1 X_1 + \beta_2(X_1 \times 0)]\\
=& \ \beta_1(X_1 + 1) - [\beta_1X_1]\\
=& \ \beta_1 + \beta_1X_1 - \beta_1X_1 \\
=& \ \beta_1\end{aligned}$$

When $X_2 = 1$, however, the math changes a bit:

$$\begin{aligned}\Delta Y =& \ \beta_0 + \beta_1(X_1 + 1) + \beta_2((X_1 + 1) \times 1) \\ 
&-[\beta_0 + \beta_1 X_1 + \beta_2(X_1 \times 1)]\\
=& \ \beta_1(X_1 + 1) + \beta_2(X_1 + 1) - [\beta_1X_1 + \beta_2X_1]\\
=& \ \beta_1 + \beta_2 + \beta_1X_1 + \beta_2X_1 - \beta_1X_1 - \beta_2X_1 \\
=& \ \beta_1 + \beta_2\end{aligned}$$

If both $\beta_1$ and $\beta_2$ are positive, then an increase in $X_1$
has a larger effect on Y when $X_2 = 1$ compared to when $X_2 = 0$. We
would interpret $\beta_1$ the same as we did before, and we would
interpret $\beta_2$ as the *difference* in the effect of $X_1$ on $Y$
for observations when $X_2$ is equal to 1 relative to 0.

For a real life example of this, consider the gender wage gap. Suppose
$X_1$ measured someone’s years of experience, $X_2$ measured their sex
(1 for men, 0 for women), and $Y$ was wages. We would interpret
$\beta_1$ as the additional wages earned for one more year of experience
for women. To calculate the additional wages earned for one more year of
experience for men, we would need to add $\beta_1$ and $\beta_2$.
$\beta_2$ would then represent the additional wages earned for men
relative to women for an additional year of experience. If this were
zero, then the two groups would get similar returns for their
experience. If this were positive, it would signal to us that men tend
to get larger raises than women.

## Estimation

So, how do we estimate parameters ($\beta$s) in linear models? The most
famous algorithm is called Ordinary Least Squares (OLS). How does it
work? Intuitively, OLS finds the line of best fit for some set of data.
However, this analogy only works with a single explanatory variable. If
there are two variables, OLS finds the [plane of best
fit](https://i.stack.imgur.com/oi7dw.gif). If there are three variables,
the analogy breaks down a bit, but hopefully you get the point.
Intuitively, this means that OLS can determine the [*ceteris
paribus*](https://en.wikipedia.org/wiki/Ceteris_paribus#Economics)
relationship between each $X$ variable and the outcome $Y$. This is
appealing to economists since we are always interested in how some thing
$X_1$ will effect $Y$, holding everything else constant. This is
*precisely* how OLS works.

Another argument for why we should care about OLS is that [most
statistical tests can be easily represented via
regression](https://lindeloev.github.io/tests-as-linear/linear_tests_cheat_sheet.pdf).

<!-- <details><summary>Statistics is just Regression</summary> -->

<!-- ![](https://lindeloev.github.io/tests-as-linear/linear_tests_cheat_sheet.pdf){width=100% height=500} -->

<!-- ![](https://i.imgflip.com/88bk1z.jpg) -->

<!-- </details> -->

<!-- <center> -->

<!-- <blockquote class="twitter-tweet" data-media-max-width="560"><p lang="en" dir="ltr">When you ask an economist to model something. <a href="https://t.co/O3a6CJXeoG">pic.twitter.com/O3a6CJXeoG</a></p>&mdash; Edmund Helmer (@theotheredmund) <a href="https://twitter.com/theotheredmund/status/1349453230762196992?ref_src=twsrc%5Etfw">January 13, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> -->

<!-- </center> -->

<center>

<img src="../ezgif.com-video-to-gif-converter.gif"
data-fig-align="center" />
</center>

### Estimation With Math

We will not go into the rigorous mathematical details about how OLS
works. For a more thorough discussion of the underlying mathematics,
visit [this
link](https://alexcardazzi.github.io/econ311/module04/module04-03.html#line-of-best-fit).

### Omitted Variable Bias

Above, I claimed that OLS can pick out the relationship between $X_1$
and $Y$, while holding everything else constant. This statement needs a
small disclaimer: OLS can only hold constant the factors that are
*included* in the estimation. In other words, if OLS doesn’t know about
some factor $X_2$, it’s unable to partial out its effects. This is fine
when $X_1$ and $X_2$ are uncorrelated or independent, but it becomes an
issue as the relationship between $X_1$ and $X_2$ becomes stronger. The
example that I think does a good job of demonstrating this uses housing
prices, bedrooms, and square footage. We will talk through the example
here and then code it up below.

According to
[rockethomes.com](https://www.rockethomes.com/real-estate-trends/va/norfolk),
there were about 1,200 homes for sale in November 2023. The median sale
price for homes was about \$320,000. The median sale price, by number of
bedrooms, was as follows:

|             | Oct. 2023 | Nov. 2023 | Change |
|-------------|----------:|----------:|-------:|
| 1 Bedroom   |  \$180.0K |  \$180.0K |  0.00% |
| 2 Bedrooms  |  \$228.2K |  \$234.9K |  3.00% |
| 3 Bedrooms  |  \$300.0K |  \$299.9K |  0.00% |
| 4 Bedrooms  |  \$370.0K |  \$368.0K | -0.50% |
| 5+ Bedrooms |  \$419.9K |  \$424.9K |  1.20% |

What can we gather from this information? It seems like people like
homes that have more bedrooms! We don’t even have to run a regression to
have that come through in the data. Choose either October or November,
and plot the number of bedrooms on the x-axis and median sale price on
the y-axis.

``` r
plot(
     xlab = "# of Bedrooms",
     ylab = "Median Sale Price")
```

Homes with more bedrooms selling for higher prices is an intuitive
result. However, there are two ways to add an additional bedroom to a
home. We could either add an extension to the home, or we could
rearrange the walls inside the property to get an extra bedroom. One of
these things is in the spirit of *ceteris paribus* and one is not…

In the second option, where we rearrange the interior, the house doesn’t
really change other than the additional bedroom. This is the choice that
many landlords in college towns elect for. Essentially, landlords will
manipulate the sizes of their properties’ rooms to squeeze as many
people into their apartments as possible. In practice, a landlord might
build a wall down the middle of the property’s biggest bedroom, or
convert an office into a bedroom by adding a window. On the other hand,
when a landlord adds an addition to a property, they not only add a
bedroom, but also square footage.

What’s missing from this table is the size of each house. Houses with
$n+1$ bedrooms are (generally) bigger than houses with $n$ bedrooms. So,
is the relationship between bedrooms and price actually driven by
bedrooms, or is it driven by the property’s square footage? In other
words, what happens when we “control for”, “condition on”, or “partial
out” square footage? Let’s write down our empirical model of sale price
for property $i$ below:

$$P_i = \beta_0 + \beta_1 SF_i + \beta_2 BR_i + \epsilon$$ where $P$
represents price, $SF$ represents square footage, and $BR$ represents
bedrooms. How can we interpret each parameter?

- $\beta_0$ is difficult to interpret since not only do properties with
  zero square feet and zero bedrooms not typically transact on the
  market, they do not really exist. For this reason, we would avoid
  thinking too hard about how to interpret the intercept.
- $\beta_1$ is the effect of increasing the square footage of a home,
  holding the number of bedrooms constant. In words, if we increase the
  size of a property by one square foot, we expect the property to sell
  for an additional \$$\beta_1$.
- $\beta_2$ is the effect of increasing the number of bedrooms in the
  home, holding the size constant.

Using this regression, we would be able to tease apart the price effects
of increasing the number of bedrooms and increasing the square footage.
Of course, there still may be other factors correlated with bedrooms
and/or square footage that we might be worried about. For example,
larger homes are more likely to be on larger plots of land, and
therefore more likely to have other amenities like garages, pools, and
driveways. If we find that an increase in square footage leads to an
increase in price, but haven’t controlled for plot size, garages, etc.,
can we *really* attribute the increase in price to the increase in
square footage?

Omitted Variable Bias is defined as when some variable not in the
regression is driving, or contributing to, an estimated relationship. We
need to be *very* careful to consider omitted variable bias when we
interpret our coefficients. Unless we can be sure that we’ve controlled
for all potential *confounding* variables (i.e. variables that would
bias our results if they were omitted from the model), we must hesitate
before we can claim causality. Much of this course can be boiled down to
different ways of addressing concerns about omitted variable bias. You
should keep this in the back of your mind as you move through the
course.

<img src="../ovb.jpg" data-fig-align="center"
data-fig-alt="Econometrics meme about omitted variable bias." />

<!-- - FWL? -->

### Estimation With R

To test the above model for housing prices, we are going to use data
collected from property transactions in Ames, Iowa
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv);
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/ames.html)).
Read the data into the `WebR` chunk and keep only the columns for price,
square footage, and bedrooms. You may need to access the documentation
for some help.

``` r
# read in the data
ames <- read.csv()
# list columns to keep here
keep_columns <- c()
# subset and save
ames <- ames[,keep_columns]
# change column names to something easier to work with:
colnames(ames) <- c("price", "sqft", "bedrooms")
```

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms")
```

First, let’s generate some scatterplots for our data.

``` r
# Generate plots here:
plot( , ames$price,
     las = 1, pch = 19,
     col = ,
     ylab = "Price")
```

Since bedrooms is so discrete (i.e. recorded in whole numbers rather
than with decimals), it’s a bit difficult to pick out a pattern in the
data. However, this is not the case when plotting square footage and
price – the relationship between the two becomes quite clear. Let’s use
these data to estimate the following housing price models:

$$\begin{alignat}{3}
P_i &= \gamma_0  + \gamma_1 SF_i && && + \epsilon_i \ \ \ \ \ \ \ \ \ \ (1)\\
P_i &= \alpha_0 && + \alpha_1 BR_i && + \epsilon_i \ \ \ \ \ \ \ \ \ \ (2) \\
P_i &= \beta_0 + \beta_1 SF_i && + \beta_2 BR_i && + \epsilon_i \ \ \ \ \ \ \ \ \ \ (3)
\end{alignat}$$

The first function we’ll use to estimate regressions in R is `lm()`,
which stands for “linear model”. The syntax for this function’s
arguments are as follows:

- `formula`: Formulas in R take on the following form: `y ~ x1 + x2`.
- `data`: This is the data you plan to use. This tells R where to look
  for the variables in your formula.
- `subset`: An optional vector that specifies a subset of observations
  used to fit the line. This can be either a logical vector or a vector
  of indices.

In the code chunk below, try estimating each of the models above and
examine the output with `summary()`.

``` r
r <- lm(, data = ames)
summary(r)
```

<details>

<summary>

Housing Price Model Estimates
</summary>

<details><summary>Output</summary>
&#10;
+----------------+--------------+---------------+---------------+
|                | (1)          | (2)           | (3)           |
+================+==============+===============+===============+
| Square Footage | 111.694***   |               | 136.361***    |
+----------------+--------------+---------------+---------------+
|                | (2.066)      |               | (2.247)       |
+----------------+--------------+---------------+---------------+
| # of Bedrooms  |              | 13889.495***  | -29149.110*** |
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
&#10;Table: Housing Price Models
&#10;</details>

</details>

Coefficient estimates and standard errors from these three models can be
seen in the table above. In the first column, we find a coefficient of
111.694 on square footage. This suggests that for every square foot
increase in the size of a property, the price increases by \$111.69. In
the second column, our estimate for bedrooms suggests that every
additional bedroom is “worth” \$13889.5. Let’s think hard about this for
a second – that’s not *really* what this coefficient means. This
regression isn’t controlling for anything, so this coefficient is
picking up everything associated with extra bedrooms. Remember, this
usually means larger homes, and larger homes mean garages and backyards
and pools. The third column is trying to address this by holding
property size constant. Now, we find a negative effect of additional
bedrooms. Adding an extra bedroom, while holding square footage
constant, either makes every other bedroom smaller or takes away from
other rooms in the home.

Hopefully, this demonstration has illustrated the potential perils of
omitted variable bias. It should also be noted that you can “sign” the
bias associated with omitted variables if you can make certain
assumptions about the relationship between the omitted variable, the
explanatory variable and the outcome variable. If you have a model
$Y = \delta_0 + \delta_1\times A + \epsilon$ and $B$ is your omitted
variable, the bias in $\delta_1$ will be:

|                             | cor(A, B) \> 0 | cor(A, B) \< 0 |
|:---------------------------:|:--------------:|:--------------:|
| B’s effect on Y is positive |      $+$       |      $-$       |
| B’s effect on Y is negative |      $-$       |      $+$       |

Omitted Variable Bias Table

Positive bias would indicate that the magnitude of the coefficient is
pushed upwards, in the positive direction, artificially. To think
through this, we can look at column (2) in the table above. We find a
coefficient of 13889.5, though we believe this to be effected by omitted
variable bias. Since we think square footage and number of bedrooms are
positively correlated, and that the effect of square footage on price is
positive, we would say that this coefficient is biased upwards. Once we
“correct” for this bias by including square footage into the model in
column (3), we see the coefficient for bedrooms plummet. Therefore, as
we predicted, the initial, unconditional coefficient estimate was too
positive, or biased upwards, due to square footage being omitted from
the model.

### Estimation With `fixest`

Another function we will use to estimate regressions in R is called
`feols()`. This function comes from the package `fixest`, which provides
a lot of neat tools for econometrics. The main arguments for `feols()`
are very similar to `lm()`: `formula`, `data`, and `subset`. Let’s
re-estimate our regressions using `feols()`:

<div class="aside">

To use `feols()` on your computer, you will first need to *install* and
*load* `fixest`. See Module 1.1 for more information.

</div>

``` r
r <- feols(, data = ames)
summary(r)
```

So, what are the differences? Actually, there are none! The output is
very similar and the coefficients are identical. So what’s the catch?
Let me introduce some additional capabilities, arguments, and functions.

- Multiple Outcomes: There are many cases where you might want to
  estimate the same model for multiple outcomes. For example, maybe one
  model where you use `price` as an outcome, and another where you use
  `log(price)`. To do this, put `c(Y1, Y2)` as the left side of your
  formula. Specifically, `c(price, log(price)) ~ sqft + bedrooms`.
- Stepwise Estimation (`sw()`, `sw0()`): Similarly, you might want to
  estimate different specifications for each outcome. Putting control
  variables into the `sw()` function will estimate the model multiple
  times with different sets of controls. For example, we can estimate
  the three models before in a single `feols()` call by using
  `sw(sqft, bedrooms, sqft + bedrooms)` on the right side of the
  formula. `sw0()` will do the same thing, except it is a shortcut for
  `sw(0, sqft, bedrooms, sqft + bedrooms)`. Keep whatever variables
  you’d like in every version of the model outside of the `sw()`
  function.
- Subsampling: (`split`, `fsplit`): This argument allows one to estimate
  the model(s) on different subsets of data. For example, suppose these
  housing sales data were for multiple cities. If we wanted a unique
  regression for each city, we could add `split = ~city` to the function
  call. This will estimate a model using only data from each unique
  city. The difference between `split` and `fsplit` is that `fsplit`
  will estimate the model on the full sample in addition to each
  subsample.

``` r
# Try using some of the above tricks below
feols( ~ ,
      data = ames)
```

Another major advantage of using `fixest` is the convenience it provides
when estimating *fixed effects*. Fixed effects control for unobservable
factors that cause differences across groups. For example, let’s suppose
we had housing data for Manhattan, New York and Ames, Iowa. Homes in
Manhattan are very expensive and very small. Homes in Iowa are
relatively bigger and cheaper. Therefore, if you try to estimate a
relationship between price and square footage, you might end up finding
that smaller homes sell for higher prices. This is, in some ways,
another manifestation of omitted variable bias! We would then be able to
control for the differences in price levels and property sizes by
estimating New York and Iowa fixed effects. See below for a
demonstration.

``` r
# Create data... feel free to modify:
N <- 100
ny <- data.frame(price = NA, city = "ny", sqft = rnorm(N, 750, 100))
ia <- data.frame(price = NA, city = "ia", sqft = rnorm(N, 1400, 200))

ny$price <- 200000 + 100*ny$sqft + rnorm(N, 0, 10000)
ia$price <- 20000 + 100*ia$sqft + rnorm(N, 0, 10000)
homes <- as.data.frame(rbind(ny, ia))
head(homes)
```

``` r
r <- feols(price ~ sqft, homes)
summary(r)
```

Estimating a model using square footage alone to explain prices gives us
a negative coefficient! This is strange since we simulated data and set
the price effect of an additional square foot to be 100. Let’s generate
a scatterplot to see what’s happening:

``` r
plot(homes$sqft, homes$price, pch = 19,
     col = ifelse(homes$city == "ny", "dodgerblue", "tomato"))
# use abline() to plot the regression line!
```

Finally, estimate the model with fixed effects. To do this, we are going
to modify the formula from `y ~ x1 + x2` to `y ~ x1 + x2 | FE1 + FE2`.
In this case we would use `price ~ sqft | city`:

``` r
r_fe <- feols(price ~ sqft | city, homes)
summary(r_fe)
```

Boom! We get the coefficient we were expecting. We could have estimated
this model with `lm()`, but `fixest` cleans up the fixed effect
coefficients for us. Generally, nobody cares about the coefficients of
the fixed effects, just whether they are included in your model.

## Presenting Estimates

The presentation of your coefficients is also an important skill to
have. Of course, we can always use `print()` or `summary()`, but these
options are clumsy and ugly. If you love `summary()`, I apologize, but
it’s time to face the truth. In this course, we are going to use
functions from the package `modelsummary` to both make summary
statistics and regressions estimate tables.

The titular function, `modelsummary()`, can accept a lot of different
arguments, but for simplicity, we will just discuss a few:

- `models`: A named list of models we want to include in the table.
- `title`: The title of our table.
- `estimate`: This is a bit complicated, but we are going to set this
  equal to `"{estimate}{stars}"`. This way, the table will display
  coefficients, standard errors, and stars to denote statistical
  significance. This is the norm in economics. If you do not like the
  stars, you can remove them by omitting the `estimate` argument in the
  `modelsummary` function call.
- `coef_map`: A named vector of variable names. This will rename the
  variable names from what they are labeled as in the data to something
  more human readable.
- `gof_map`: A vector of desired goodness of fit measures for each
  model. We are going to stick with `"nobs"` and `"r.squared"` for the
  time being.

Here is a template for displaying regression output with
`modelsummary()`:

``` r
library("modelsummary")
options(modelsummary_factory_default = 'tinytable')
options("modelsummary_format_numeric_latex" = "plain")
# These names could also be the name of the outcome variable.
regz <- list(`Name of Column (1)` = reg1,
             `Column (2)` = reg2,
             `Column (3)` = reg3)
coefz <- c("var_name_in_data" = "Human-Readable Variable Name",
           "(Intercept)" = "Constant")
gofz <- c("nobs", "r.squared")
modelsummary(regz,
             title = "Effect of X on Y",
             estimate = "{estimate}{stars}",
             coef_map = coefz,
             gof_map = gofz)
```

The `modelsummary` package also comes with handy functions for
generating summary statistics such as `datasummary()` and
`datasummary_skim()`. You will be expected to generate summary
statistics tables using these functions. Visit [this
link](https://alexcardazzi.github.io/econ311/module02/module02-03.html)
for more information.

As a final note on this topic, it does not seem like `modelsummary`
functions work inside `WebR` chunks.

## Inference

The last topic we will discuss in these notes is statistical inference.
Of course, we are interested in estimating models to generate and
interpret $\beta$s, but we are also interested in testing hypotheses.
You might have noticed that both `summary()` and `modelsummary()`
generate standard errors. Standard errors are a measure of uncertainty
for coefficients. Smaller standard errors indicate higher levels of
precision, which is obviously desired. However, sometimes standard
errors can be misleadingly small (or large), so we need to be sure we
take care in addressing this. We will first talk about what we do with
standard errors, and then we will discuss how we might want to adjust
our standard errors.

### Using Standand Errors

Often times, we want to know if changes in some variable $X_1$ is
correlated with (or causes) changes in our outcome variable $Y$. If
there is no correlation/causation, then $\beta_1 = 0$. In
statistics/econometrics, our default, often called the *null*
hypothesis, is that $\beta_1 = 0$. From here, our evidence will either
reject this hypothesis or fail to reject. This language is admittedly
esoteric, but important.

Standard errors provide us evidence by creating confidence intervals
around our coefficient estimates. Our confidence interval gives us a
range of possible values that contains the true parameter. Remember,
there is some “true” $\beta$, and we are using OLS to estimate it. Now,
if 0 is inside of our estimate’s confidence interval, then there is some
non-trivial probability that 0 is the true parameter value. In language
that is more closely aligned to the statistics: if 0 is inside the
confidence interval, we cannot rule out that $\beta = 0$.

So, how do we create confidence intervals? First, we will build the
interval around $\widehat{\beta}$, our estimate of $\beta$, minus some
hypothesized value. When the hypothesized value is 0, which is typically
is, then we would use $\widehat{\beta} - 0$. In theory, however, this
could be any value. Then, we add a little bit and subtract a little bit
from this difference. This creates a range from
$(\widehat{\beta} - 0) - \text{something}$ to
$(\widehat{\beta} - 0) + \text{something}$.

So, what is $\text{something}$? This is made up of some $t$-statistic
multiplied by the standard error of the estimate. For a 95% confidence
interval, and a large sample size, the $t$-statistic would be equal to
1.96. Therefore, the larger the standard error, the wider the confidence
interval, and the more likely that zero is inside of it.

Instead of testing whether zero is inside or outside of a set confidence
interval, we can also calculate $p$-values. These measure the
probability of observing as large of a $\widehat{\beta}$ given the true
$\beta = 0$, or some other hypothesized value. Once your sample size is
over a couple hundred, and you’re testing $\widehat{\beta}$ against 0,
you can simply divide $\widehat{\beta}$ by its standard error and check
out the result. If this result is equal to 1.96 in absolute value, then
0 is lying right on the border of the 95% confidence interval. If the
result is larger, then you can reject the hypothesis that $\beta = 0$.
$t$-statistics of 1.96 correspond to 95% confidence intervals which then
correspond to $p$-values of 0.05. As the $t$-statistic increases, the
$p$-value will decrease. R will calculate $p$-values for you, and a
typical threshold is 0.05.

For a more thorough discussion of hypothesis testing, visit [this
link](https://alexcardazzi.github.io/econ311/module03/module03-03.html).

### Correcting Standand Errors

When R calculates the standard errors in a regression, it makes some
assumptions on your behalf. One of these assumptions is that the
regression’s errors are *homoskedastic*, which means the errors have a
constant variance. If this is true, which is rare, then we have nothing
to worry about! However, if there is *heteroskedasticty*, meaning
non-constant variance, R’s assumption is violated. This could lead to
incorrect standard errors, and therefore incorrect conclusions drawn
from the analysis.

Correcting for heteroskedasticity using `lm()` is difficult compared to
doing it with `fixest`. In `feols()`, there is an argument called `vcov`
which allows us to adjust our standard errors. To generate standard
errors that are robust to heteroskedasticity, we can use
`vcov = "hetero"`.

When we use `feols()` without fixed effects, the default is `"iid"`
standard errors, which are also called analytical standard errors.
Again, heteroskedasticity robust standard errors are preferred to
analytical standard errors. However, when you use `feols()` with fixed
effects, the default is clustered standard errors at the level of the
first fixed effect. Clustering is outside of the scope of the course,
but the idea is that standard errors should be clustered at the level of
treatment. For example, if we had some policy changing at the state
level, and we could observe multiple individuals within each state
across time, we would cluster our standard errors at the state level. In
other words, we would *not* use `vcov = "hetero"`, we would just make
sure that the state fixed effect comes first in our set of fixed
effects, or we could use `cluster = ~state` in `feols()`.
