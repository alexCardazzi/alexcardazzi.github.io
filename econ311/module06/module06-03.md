# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Housing Price Models

While we love pretending to be guidance counselors, we *really* love
pretending to be real estate agents. For (maybe?) the last time, let’s
read in our `ames` housing data
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv);
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/modeldata/ames.html)).
We are going to keep all of the variables we did last time plus one
more: `BsmtFin.Type.1`.

<details>

<summary>

Definition/Details of `BsmtFin.Type.1`
</summary>

    BsmtFin Type 1  (Ordinal): Rating of basement finished area

           GLQ  Good Living Quarters
           ALQ  Average Living Quarters
           BLQ  Below Average Living Quarters
           Rec  Average Rec Room
           LwQ  Low Quality
           Unf  Unfinshed
           NA   No Basement

Source: <https://jse.amstat.org/v19n3/decock/DataDocumentation.txt>
</details>

Let’s load in our data and re-name the columns.

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond", "BsmtFin.Type.1")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition", "bsmnt")
ames$age <- 2011 - ames$yr_built
```

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond", "BsmtFin.Type.1")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition", "bsmnt")
ames$age <- 2011 - ames$yr_built

ames$bsmnt <- ifelse(is.na(ames$bsmnt) | ames$bsmnt == "", "No Basement", ames$bsmnt)
ames$has_bsmnt <- ifelse(ames$bsmnt == "No Basement", 0, 1)
```

## Basements

Let’s take a peek at the `bsmnt` variable to see what all it contains.
To do this, we are going to use `table()`, but add in `useNA = "always"`
just in case there are any missing data elements in the column.

``` r
table()
```

According to this tabulation, there are 80 observations with a blank
entry, and 0 observations that R reads as missing (`NA`). According to
[this
resource](https://jse.amstat.org/v19n3/decock/DataDocumentation.txt#:~:text=BsmtFin%20Type%201%09(Ordinal)%3A%20Rating%20of%20basement%20finished%20area%0A%0A%20%20%20%20%20%20%20GLQ%09Good%20Living%20Quarters%0A%20%20%20%20%20%20%20ALQ%09Average%20Living%20Quarters%0A%20%20%20%20%20%20%20BLQ%09Below%20Average%20Living%20Quarters%09%0A%20%20%20%20%20%20%20Rec%09Average%20Rec%20Room%0A%20%20%20%20%20%20%20LwQ%09Low%20Quality%0A%20%20%20%20%20%20%20Unf%09Unfinshed%0A%20%20%20%20%20%20%20NA%09No%20Basement),
`NA` values in the original data (here, they are blank) indicate no
basement. Therefore, we can convert these blank values to say
`"No Basement"`.

``` r
ames$bsmnt <- ifelse(ames$bsmnt == "", "No Basement", ames$bsmnt)
```

If we were real estate agents, it would be really nice to have some way
to quantify this basement variable so we could use it in our model. To
start, let’s just create a dummy variable equal to 1 if the property has
a basement and a zero otherwise.

``` r
ames$has_bsmnt <- ifelse(ames$bsmnt == "No Basement", 0, 1)
```

Similar to how we controlled for gender differences, which is a
categorical variable, we can include this variable into our housing
price model. For simplicity, we are going to just use square footage as
our other explanatory variable. Students are encouraged to explore the
inclusion of other variables by themselves.

Estimating our model:

``` r
# Use log(price) as the outcome variable
# Use log(sqft) and has_bsmnt as explanatory variables
reg1 <- lm(log(price) ~ , ames)
reg1
```

This model suggests that a 1% increase in square footage increases
expected sale price by 0.89%, controlling for whether the property has a
basement. Second, this model says that having a basement (controlling
for square footage) increases price by 38.8% relative to no basement.

<div class="aside">

**A note on percentages.** The rule of thumb from Module 4.5 is to
multiply a coefficient from a log model by 100 to get a percent change,
which is what we just did. The exact formula is
$(e^{\beta} - 1) \times 100$, which gives 47.4% here. The rule of thumb
works well for small coefficients, but the two drift apart for larger
ones like this.

</div>

Let’s think about this for a second. A home that is 2,000 square feet
*including a basement* is worth more than a 2,000 square foot home
without a basement? Put differently, a 1,600 square foot home with a 400
square foot basement is worth more than a 2,000 square foot home? If
this sounds fishy to you, check out [the definition of the `area`
column](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/ames.html#:~:text=area,area%20square%20feet.).

It seems that `area` only measures the non-basement square footage.
Therefore, this coefficient represents a composite effect of both adding
a basement as well as adding the square footage that comes with the
basement. In other words, this regression is *really* saying that a
2,000 square foot home with a 400 square foot basement (for a total of
2,400 ft<sup>2</sup>) is worth more than a 2,000 square foot home
without a basement. This should sound awfully similar to the same
omitted variable bias we had in the bedroom situation! However, let’s
turn a blind eye to this for now.

## Basement Quality

Let’s return to the original model that said adding a basement increases
prices by 38.8%. This is helpful, but of course there are many different
“ratings” of basement quality reported in the `bsmnt` variable that we
are ignoring. The lowest quality is “Unf”, or unfinished. Let’s make a
dummy variable for this type of basement.

``` r
ames$unfinished <- ifelse(ames$bsmnt == "Unf", 1, 0)
```

Now, let’s re-estimate the model with this variable included:

<details class="code-fold">
<summary>Code</summary>

``` r
reg2 <- lm(log(price) ~ log(sqft) + has_bsmnt + unfinished, ames)
reg2
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = log(price) ~ log(sqft) + has_bsmnt + unfinished, 
        data = ames)

    Coefficients:
    (Intercept)    log(sqft)    has_bsmnt   unfinished  
         5.0663       0.9062       0.4363      -0.1679  

</details>

We find that having a basement, relative to no basement, increases the
expected price by 43.6%. Now, what if that basement was unfinished? The
coefficient suggests -16.8%, but this is not the end of the story.
Notice that `has_bsmnt` is *always* equal to one whenever `unfinished`
is equal to one. Therefore, the overall effect is 43.6% - 16.8% = 26.8%.
In other words, an unfinished basement is worth more than no basement,
but a finished basement is worth more than an unfinished basement
($0 < 26.8 < 43.6$).

This interpretation is a bit clunky because it requires us to sum two
coefficients to get our total estimate. To make the interpretation
easier, we can redefine `has_bsmnt` to exclude unfinished basements:

<details class="code-fold">
<summary>Code</summary>

``` r
ames$has_bsmnt <- ifelse(ames$bsmnt %in% c("No Basement", "Unf"), 0, 1)
reg2 <- lm(log(price) ~ log(sqft) + has_bsmnt + unfinished, ames)
reg2
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = log(price) ~ log(sqft) + has_bsmnt + unfinished, 
        data = ames)

    Coefficients:
    (Intercept)    log(sqft)    has_bsmnt   unfinished  
         5.0663       0.9062       0.4363       0.2683  

</details>

This interpretation is much easier. An unfinished basement, relative to
no basement, is worth a 26.8% increase. A non-unfinished basement,
relative to no basement, is worth a 43.6% increase. If we wanted to
estimate the effect of some other basement quality level, we would have
to again redefine `has_bsmnt` to exclude that basement type, and create
a new dummy variable for it. If we take this logic to the finish line,
we’d end up $K-1$ dummy variables where $K$ is the number of possible
categories. We subtract one because one group must be the reference
group (no basement in this case).

I’m sure you’ve heard this one before: *work smarter, not harder*. In
programming, they say: *A good programmer is a lazy programmer*. While
one of these is earnest and the other sarcastic, they mean similar
things. In our case, making a bunch of dummy variables can be a lot of
work! Luckily, R has a ~~lazy~~ **efficient** solution for us.

## Factors

To create many dummies at once, we can use the `factor()` command.
Moreover, we can even select which factor becomes our reference group!
To do this in R, we would do the following:

``` r
# I am putting a "_" to differentiate between the original variable.
ames$bsmnt_ <- as.factor(ames$bsmnt)
head(ames$bsmnt_)
cat("\n")
ames$bsmnt_ <- relevel(ames$bsmnt_, ref = "No Basement")
head(ames$bsmnt_)
```

<details>

<summary>

Output
</summary>

    [1] BLQ Rec ALQ ALQ GLQ GLQ
    Levels: ALQ BLQ GLQ LwQ No Basement Rec Unf

    [1] BLQ Rec ALQ ALQ GLQ GLQ
    Levels: No Basement ALQ BLQ GLQ LwQ Rec Unf

</details>

Then, if we throw this into a regression, R will create all of the
dummies for us. I will also include bedrooms and age into this model for
completeness.

``` r
reg3 <- lm(log(price) ~ log(sqft) + bsmnt_ + bedrooms + age, ames)
summary(reg3)
```

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = log(price) ~ log(sqft) + bsmnt_ + bedrooms + age, 
        data = ames)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -1.86737 -0.11004  0.00482  0.12182  0.77719 

    Coefficients:
                  Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  5.8752614  0.1011122   58.11   <2e-16 ***
    log(sqft)    0.8568861  0.0146948   58.31   <2e-16 ***
    bsmnt_ALQ    0.3529548  0.0242718   14.54   <2e-16 ***
    bsmnt_BLQ    0.3285093  0.0253126   12.98   <2e-16 ***
    bsmnt_GLQ    0.3932176  0.0240384   16.36   <2e-16 ***
    bsmnt_LwQ    0.2831736  0.0274313   10.32   <2e-16 ***
    bsmnt_Rec    0.2922540  0.0251362   11.63   <2e-16 ***
    bsmnt_Unf    0.2459743  0.0233557   10.53   <2e-16 ***
    bedrooms    -0.0694749  0.0055325  -12.56   <2e-16 ***
    age         -0.0047742  0.0001461  -32.69   <2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.1987 on 2920 degrees of freedom
    Multiple R-squared:  0.763, Adjusted R-squared:  0.7623 
    F-statistic:  1045 on 9 and 2920 DF,  p-value: < 2.2e-16

</details>

The model spits out a bunch of numbers for `bsmnt_`, but it’s important
to remember that they are all interpreted as having that “type” of
basement relative to not having a basement, controlling for the
property’s age, size and number of bedrooms. Moreover, we can plot the
coefficients and show that they’re generally increasing in the order
that we’d expect.

<details>

<summary>

Plot
</summary>

<img src="module06_img/06-03-unnamed-chunk-15-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Coefficient estimates for the type of basement and the effect on housing prices." />

</details>

<details>

<summary>

Basement Rating Reminder
</summary>

    BsmtFin Type 1  (Ordinal): Rating of basement finished area

           GLQ  Good Living Quarters
           ALQ  Average Living Quarters
           BLQ  Below Average Living Quarters
           Rec  Average Rec Room
           LwQ  Low Quality
           Unf  Unfinshed
           NA   No Basement

Source: <https://jse.amstat.org/v19n3/decock/DataDocumentation.txt>
</details>

It is important to point out the assumptions of this model. We are
assuming that changing the square footage (or age or bedrooms) of a home
impacts sale price independently of the type of basement. Of course, we
could estimate a fully interacted model, but the output would be huge
and uninterpretable.
