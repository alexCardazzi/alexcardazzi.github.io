# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

<style>
.hover_img a { position:relative; }
.hover_img a span { position:absolute; display:none; z-index:99; }
.hover_img a:hover span { display:block; }
</style>

## Housing Price Models

In the last module, we developed and estimated quite a few models to
explain housing prices. However, as you can intuit, there are *many*
factors that influence the price of a house. While an important feature
of economic models is that they are relatively simple, our previous
models were definitely *too* simple. To make our model a bit more
realistic, we can include other factors that might influence price.
First, let’s read in the data, remove some columns, and change the names
of the remaining columns, and then we will see how to add more factors
into our models.

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
```

<div class="aside">

This code has been run in the background for you to facilitate your use
of the following WebR chunks.

</div>

In addition, we are going to create a new variable for age.

``` r
ames$age <- 2011 - ames$yr_built
```

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
ames$age <- 2011 - ames$yr_built
```

## Simple Correlations

We’ll start by estimating a simple model where price is determined by
age. Next, we’ll estimate a model where price is determined by age *and*
square footage. Before we estimate anything, though, let’s first
establish some of the correlations between the three variables in our
data.

``` r
cor(ames[,c("price", "age", "sqft")])
```

1.  The correlation between price and age is -0.558. This suggests that
    newer homes are typically more expensive.
2.  The correlation between price and square footage is 0.707. We saw
    this last module – as square footage increases, so does price. This
    makes sense; bigger houses typically command higher prices.
3.  The correlation between age and square footage is *-0.242*. This
    suggests that newer houses are bigger than older homes. **This is an
    important data artifact!**

Why is this so important? Let’s first estimate a simple model:

$$\text{Price}_i = \alpha_0 + \alpha_1 \times \text{Age}_i + \epsilon_i$$

``` r
reg_alpha <- lm(price ~ age, ames)
cat("Alpha0 (intercept):", coef(reg_alpha)[1],
    "\n", "Alpha1 (slope):", coef(reg_alpha)[2])
```

$\alpha_1$ = -1474.96 suggests that for every one year increase in
property age, housing price decreases by \$1474.96. However, remember,
when we are talking about an older house, we are also talking about a
generally smaller home (as evidenced via the correlation from before).
This means that the regression coefficient of -1474.96 is actually a
“composite” effect of both increased age *and* decreased square footage!
In other words, when we increase age, two things happen: not only does
age increase, but *square footage also decreases*. So which of these two
effects are we really picking up with this coefficient?

Obviously, in reality, houses do not shrink as they age, but the model
doesn’t know that. It only understands the correlations in the data. In
these data, older houses are also smaller, so it’s important we
*control* the size of a home to get a true measure of the effect of age
on prices.

Another way to think about this is that we are trying to compare houses
that are the same in every way *except* for their ages. *Controlling*
for square footage gets us one step closer to keeping everything about
the home constant while we vary only its age.

How, then, do we control for square footage?

## Breaking Down the Composite

To get an estimate of the isolated effect of age on price, we need some
extra information. First, we need the initial regression where price is
a function of age. Next, we need to know how square footage will respond
to changes in age. To get this information, we’ll estimate a regression
where square footage is the outcome and age is the explanatory variable.
Lastly, we need to know how price responds to changes in square footage.

``` r
coef(lm( ~ , ames)) # price vs age
coef(lm( ~ , ames)) # sqft vs age
coef(lm( ~ , ames)) # price vs sqft
```

<details>

<summary>

Solution
</summary>

``` r
coef(lm(price ~ age, ames)); cat("\n")
coef(lm(sqft ~ age, ames)); cat("\n")
coef(lm(price ~ sqft, ames))
```

<details>

<summary>

Output
</summary>

    (Intercept)         age 
     239269.065   -1474.964 

    (Intercept)         age 
    1659.855230   -4.040108 

    (Intercept)        sqft 
      13289.634     111.694 

</details>

</details>

<details open>

<summary>

Explanation of Results
</summary>

The results:

1.  According to the first regression, when we increase age by one year,
    sale price drops by \$-1474.96. Remember, this is a “composite”
    effect.
2.  According to the second regression, when we increase age by one
    year, square footage drops by -4.04 ft<sup>2</sup>.
3.  How much would the price decrease because of the associated
    reduction in square footage? From the third regression, \$111.69 is
    the change in price for a one square foot increase, but we have a
    square footage change of -4.04 ft<sup>2</sup> since age increased by
    one year. Therefore, multiplying these two numbers together gives us
    \$-451.26. You can think of this as the “indirect” effect of
    decreasing property size due to increasing age.
4.  The effect of age alone would then be the composite effect
    (\$-1474.96) minus the effect attributable to the change in square
    footage (\$-451.26). This comes out to be: **\$-1023.7**, which is
    certainly smaller in magnitude compared to what the first regression
    tells us.

</details>

The above is a rough approximation because all of those regressions are
inherently biased for the same reason that the first one is. So, rather
than estimating three regressions and doing some major algebratics[^1],
we can do this all in one step. We are now going to estimate the
following model:

$$\text{Price}_i = \beta_0 + \beta_1 \times \text{Age}_i + \beta_2 \times \text{Sq. Ft.}_i + \epsilon_i$$

<div class="hover_img">

<a href="#">Without going through all the
math<img src="https://media.tenor.com/UZzuVZSzn6MAAAAM/whew-out-of-breath.gif" alt="SpongeBob Meme" height="200" class="center"/></a>,
OLS will simultaneously find *two* lines of best fit. You can even think
of this as a surface, or a “plane”, since there are two dimensions
instead of just one. $\beta_0, \beta_1, \text{and} \ \beta_2$ will still
minimize the sum of squared residuals and provide an average residual of
zero.

</div>

Before estimating this specific model, however, let’s visualize the data
in three dimensions. On the $Z$-axis (the one going up and down), we’ll
plot the home’s sale price. Then, on the $X$- and $Y$-axes, we are going
to plot age and square footage. Feel free to click on and spin the
following visualization:

*(Interactive 3D plot omitted from the markdown version.)*

The following figures are 2D representations of what you would see if
you were to spin the 3D figure in certain directions. The first panel
shows us the relationship between price and square footage. The second
panel depicts the relationship between age and price. The final panel
shows the relationship between square footage and age.

<details>

<summary>

Plot
</summary>

<img src="module05_img/05-01-unnamed-chunk-13-1.svg" style="width:90.0%"
data-fig-align="center" />

</details>

## Estimation with Multiple Variables

Now let’s estimate the model I mentioned before. To include another
variable in `lm()`, simply add it to the right side of the formula like
you would a math equation.

``` r
# use age and square footage here
reg_beta <- lm(price ~ , ames)
coef(reg_beta)
```

<details class="code-fold">
<summary>Code</summary>

``` r
reg_beta <- lm(price ~ age + sqft, ames)
coef(reg_beta)
```

</details>

<details>

<summary>

Output
</summary>

    (Intercept)         age        sqft 
    79973.60078 -1087.23673    95.96949 

</details>

<details>

<summary>

Coefficient Interpretation
</summary>

1.  $\beta_0$’s interpretation is now the price of a property where
    *all* $X$ variables are equal to zero. In this case, the price of a
    house that has an age of zero and zero square footage. Of course, a
    house like this does not exist, so $\beta_0$ is not very meaningful.
2.  $\beta_1$ is the effect of one additional year of age on price,
    *holding square footage constant*. The model has removed the effect
    of square footage from this coefficient since we have “controlled”
    for it by adding square footage into the model.[^2]
3.  $\beta_2$ is the effect of an additional square foot of size on
    final sale price, *holding age constant*.

</details>

This model also makes some intuitive sense: it says that older houses
are worth less and larger houses are worth more.

Recall from earlier when I said a regression with two $X$ variables
creates a plane rather than a single line. See the below visualization
of this regression in action. Given any combination of age and square
footage, we can find a point on the *plane* that gives us the predicted
price.

Because this model has an intercept, the plane does *not* go through the
origin: where age and square footage are both zero, the predicted price
is the intercept (\$79,974). The plane is drawn all the way back to zero
so that you can see this, even though no house in the data is that small
(or that new).

*(Interactive 3D plot omitted from the markdown version.)*

[^1]: I’m inventing a new word for “Algebra” + “Acrobatics”. I’m doing
    my best to make econometrics fun, so you have to cut me some slack.

[^2]: Notice how close this number is to -1023.7, from before. It isn’t
    *exactly* the same because our rough approximation used the square
    footage coefficient from a regression that left age out. We will see
    the exact relationship in Module 5.3.
