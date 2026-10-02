# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Binary Outcomes

So far in this module, we have learned about binary *explanatory*
variables. However, what if your *outcome* variable is binary? We can
still use OLS for this!

Let’s consider some data on police treatment of individuals arrested in
Toronto for simple possession of small quantities of marijuana
([data](https://vincentarelbundock.github.io/Rdatasets/csv/carData/Arrests.csv),
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/carData/Arrests.html)).
In these data, we can observe whether the arrestee was released, their
race, age, sex, employment status, citizen status and previous police
interactions.

Let’s build a model to explain whether the arrestee was released given
some of these controls.

<details class="code-fold">
<summary>Code</summary>

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/carData/Arrests.csv")
df$released <- ifelse(df$released == "Yes", 1, 0)
df$black <- ifelse(df$colour == "Black", 1, 0)
df$female <- ifelse(df$sex == "Female", 1, 0)
df$citizen <- ifelse(df$citizen == "Yes", 1, 0)
df$employed <- ifelse(df$employed == "Yes", 1, 0)
summary(r_lpm <- lm(released ~ female + citizen + employed + age + black + checks, df))
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = released ~ female + citizen + employed + age + black + 
        checks, data = df)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -0.98272  0.03631  0.09415  0.19252  0.60548 

    Coefficients:
                  Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  0.7405159  0.0238808  31.009  < 2e-16 ***
    female      -0.0034265  0.0179731  -0.191    0.849    
    citizen      0.0892898  0.0143901   6.205 5.89e-10 ***
    employed     0.1236378  0.0125926   9.818  < 2e-16 ***
    age          0.0004879  0.0006049   0.807    0.420    
    black       -0.0573499  0.0119889  -4.784 1.77e-06 ***
    checks      -0.0499783  0.0033967 -14.714  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.3581 on 5219 degrees of freedom
    Multiple R-squared:  0.09551,   Adjusted R-squared:  0.09447 
    F-statistic: 91.85 on 6 and 5219 DF,  p-value: < 2.2e-16

</details>

Since the outcome is either 0 or 1, the average of the outcome is the
*probability* of being released. This means that a coefficient is the
change in that probability when the explanatory variable increases by
one unit. Because probabilities are already percentages, we describe
these changes in **percentage points**. A model like this is called a
**linear probability model**.

<details>

<summary>

Coefficient Interpretations
</summary>

1.  The coefficient for `female` suggests that females are 0.3
    percentage points less likely than men to be released following an
    arrest for small amounts of marijuana. However, this coefficient is
    not statistically different from zero, so we do not have evidence
    that this difference is meaningful.
2.  The estimate for `citizen` suggests that, relative to non-citizens,
    citizens are 8.9 percentage points more likely to be released.
3.  Similarly, those who are `employed`, relative to those who are
    unemployed, are 12.4 percentage points more likely to be released.
4.  An increase in `age` of one year results in an (insignificant) 0.05
    percentage point increase in the probability of being released.
5.  Individuals who are `black`, controlling for other factors, are less
    likely by 5.7 percentage points to be released following an arrest.
6.  Each additional previous police check (`checks`) reduces the
    probability of being released by 5 percentage points.
7.  The intercept suggests a baseline probability of being released of
    74.1%. Note that this applies when all other variables are equal to
    zero. Specifically, this says that the probability of being released
    for unemployed white male non-citizens with no previous arrest
    history and who are age zero is 74.1%.

</details>

<details>

<summary>

Are these results *causal*?
</summary>

An interesting, albeit unsurprising, result of this regression is that
black Canadians are less likely to be released than white Canadians. Can
we interpret this coefficient as causal? Recall the four reasons why we
might find a correlation:

1.  The causal interpretation: race causes release. To settle on this
    one, we need to rule out the other three.
2.  The reverse causality interpretation: release causes race. This one
    is clearly implausible, and can be promptly ruled out.
3.  The third variable interpretation: is there some third variable that
    is causing both race and release? Unfortunately, given this
    setting/regression, we cannot rule out there being some third factor
    that is correlated with race and whether an individual is released
    following an arrest.
4.  The random chance interpretation: we found this correlation by
    “accident”. This is unlikely given the number of observations we
    have and our statistical tests. We can probably rule this out.

If we could figure out a clever way (e.g. a Randomized Control Trial) to
rule out all possible third variables, we could make causal claims.
Unfortunately, we will not cover that in this course. However, if you
want to stick around and take ECON 400, we’ll get to things like this!
</details>
