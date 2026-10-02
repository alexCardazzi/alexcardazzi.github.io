# Many X Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

We will keep working with the Ames data. First, read in the data, rename
the columns, and create age as before:

``` r
ames <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/ames.csv")
keep_columns <- c("price", "area", "Bedroom.AbvGr", "Year.Built", "Overall.Cond")
ames <- ames[,keep_columns]
colnames(ames) <- c("price", "sqft", "bedrooms", "yr_built", "condition")
ames$age <- 2011 - ames$yr_built
```

## Omitted Variables

Hopefully, after seeing what happened in the bedrooms and square footage
example, you are convinced that omitting variables can bias our
coefficients. In typical (and boring) economist fashion, we call this
**omitted variable bias**.

Unfortunately, sometimes we cannot directly observe every variable that
we’d like to control for in our regression. However, we can **sign** the
bias of our coefficients if we can anticipate how the omitted variable
relates to the variable we included and to the outcome.

## The Bias Formula

Let’s go back to the age and square footage example, because now we can
describe *exactly* what happened. Call the variable we care about $A$
(age) and the variable we are tempted to leave out $B$ (square footage).
We have three regressions:

- The “long” model, which includes both:
  $Y_i = \beta_0 + \beta_1 A_i + \beta_2 B_i + \epsilon_i$
- The “short” model, which omits $B$:
  $Y_i = \alpha_0 + \alpha_1 A_i + \epsilon_i$
- How the two $X$ variables relate to each other:
  $B_i = \delta_0 + \delta_1 A_i + u_i$

It turns out the coefficient on $A$ in the short model is always related
to the coefficient in the long model by:

$$\underbrace{\alpha_1}_{\text{short}} = \underbrace{\beta_1}_{\text{long}} + \underbrace{\delta_1 \times \beta_2}_{\text{bias}}$$

In words: leaving $B$ out means the coefficient on $A$ picks up the
“true” effect of $A$ ($\beta_1$), *plus* the effect of $B$ on $Y$
($\beta_2$) that tags along with $A$ because $B$ changes when $A$
changes ($\delta_1$). Here are the age and square footage numbers:

``` r
alpha1 <- coef(lm(price ~ age, ames))["age"]
reg_long <- lm(price ~ age + sqft, ames)
beta1 <- coef(reg_long)["age"]
beta2 <- coef(reg_long)["sqft"]
delta1 <- coef(lm(sqft ~ age, ames))["age"]

cat("Short coefficient (alpha1):", alpha1, "\n")
cat("Long coefficient (beta1):", beta1, "\n")
cat("Bias (delta1 x beta2):", delta1 * beta2, "\n")
cat("Long coefficient + bias:", beta1 + delta1 * beta2, "\n")
```

<details>

<summary>

Output
</summary>

    Short coefficient (alpha1): -1474.964 
    Long coefficient (beta1): -1087.237 
    Bias (delta1 x beta2): -387.7271 
    Long coefficient + bias: -1474.964 

</details>

The last line matches the first, exactly. This is also why our rough
approximation in Module 5.1 was close, but not perfect: it used the
square footage coefficient from a regression that left age out (111.69)
instead of $\beta_2$ (95.97).

**What I care about most is not that you memorize this formula or
calculate biases by hand.** What I care about is that you can *use* the
table below to think through omitted variable problems: given a variable
that is missing from a model, can you reason about which direction your
coefficient is probably off?

## Using the Omitted Variable Bias Table

The formula says the bias is $\delta_1 \times \beta_2$, so the *sign* of
the bias comes from multiplying two signs. That means we only need to
ask two questions about the omitted variable $B$:

1.  Does $B$ move *with* the variable we included, $A$? (The sign of
    $\delta_1$ is the sign of the correlation between $A$ and $B$.)
2.  Does $B$ affect $Y$ *on its own*, with $A$ held constant? (This is
    the sign of $\beta_2$.)

If you have a model $Y = \alpha_0 + \alpha_1\times A + \epsilon$ and $B$
is your omitted variable, the bias in $\alpha_1$ will be:

|                             | cor(A, B) \> 0 | cor(A, B) \< 0 |
|:---------------------------:|:--------------:|:--------------:|
| B’s effect on Y is positive |      $+$       |      $-$       |
| B’s effect on Y is negative |      $-$       |      $+$       |

Omitted Variable Bias Table

**Remember**: the bias is about the *value* of the coefficient. Positive
(*negative*) bias pushes the estimate *up* (*down*). If the true
coefficient is negative, negative bias makes the estimate even more
negative (a bigger magnitude), while positive bias pulls it toward zero.

Let’s use the table on the examples we have already seen.

**Age and square footage.** Age is $A$, and square footage is the
omitted $B$.

1.  Does square footage move with age? Yes. Newer homes are bigger, so
    the correlation is negative (-0.242).
2.  Does square footage affect price on its own? Yes. Bigger houses are
    worth more, so the effect is positive ($\beta_2$ = 95.97).

A negative times a positive is a **negative** bias. This is what we saw:
when square footage was added, the age coefficient went from -1474.96 to
-1087.24. Said the other way, omitting square footage pushed -1087.24
*down* to -1474.96.

**Square footage and bedrooms.** Now square footage is $A$, and bedrooms
is the omitted $B$.

1.  Do bedrooms move with square footage? Yes. Bigger houses have more
    bedrooms, so the correlation is positive (0.517).
2.  Do bedrooms affect price on their own, *holding square footage
    constant*? Yes, and the effect is negative. In Module 5.2, an extra
    bedroom at a fixed size reduced the price by \$29,149.11.

A positive times a negative is a **negative** bias. Again, this is what
we saw: the square footage coefficient went from 136.36 to 111.69 when
bedrooms was omitted. Both examples ended with negative bias, but for
different reasons!

> [!NOTE]
>
> ### Be careful with question 2
>
> The second question is about $B$’s effect on $Y$ *with $A$ held
> constant*, which is not the same as the raw correlation between $B$
> and $Y$. If you just correlate bedrooms with price, you get 0.144:
> more bedrooms, higher price, because bedrooms are a proxy for how big
> the house is. Once square footage is held constant, though, the effect
> of an additional bedroom is negative. The second number is the one
> that determines the bias.

## Unimportant Variables

Now we know the effect of omitting important variables on our
coefficients. But what is the effect of the opposite: including
unimportant variables?

Including unimportant variables will not bias your other coefficients
because if they’re truly unimportant, we would expect $\beta_i = 0$.
Take a look at the above table – if $B$ does not affect $Y$ whatsoever,
then the bias will be neither positive nor negative *regardless* of
$B$’s correlation with $A$.

If this is the case, why don’t we throw every single variable we can
think of into the model?! Well, [there’s no such thing as a free
lunch](https://en.wikipedia.org/wiki/No_such_thing_as_a_free_lunch).
Each time we include a new variable, especially when the new variable is
highly correlated with one or more other variables, we lose a little bit
of our precision. In other words, **including new variables generally
increases the standard errors of the coefficients for the other
variables in the model**. This makes it more difficult to effectively
test our hypotheses.

## Multicollinearity

Intuitively, adding two variables into a model that are highly
correlated supplies the model with overlapping and redundant
information. This makes it difficult for the model to parse out the
effect of each variable individually. The problem of redundant
information in a model is called
[**multicollinearity**](https://www.youtube.com/watch?v=BvjYUr0cmug). If
two variables contain *exactly* the same information, the regression
cannot differentiate between the two variables and therefore cannot be
estimated without removing one of the variables. This is called
*perfect* multicollinearity.

Multicollinearity occurs for one of two reasons:

1.  Structural: Sometimes we create variables based on already existing
    variables. For example, we created an “age” variable from the “year
    built” variable. Year built and age are highly correlated. In fact,
    since one is a linear function of the other[^1], including both into
    a regression will create perfect multicollinearity. Put differently,
    since the correlation between the two is -1, they contain exactly
    the same information.

<details>

<summary>

Age and Year Built in a Regression
</summary>

``` r
lm(price ~ age + yr_built, ames)
```

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = price ~ age + yr_built, data = ames)

    Coefficients:
    (Intercept)          age     yr_built  
         239269        -1475           NA  

</details>

*Notice how the resulting “yr_built” coefficient is `NA`. This is
because R could not estimate it and drops it from the model.*
</details>

2.  Data-Based: Other times, variables are correlated simply because we
    are working with observational data. For example, in reality, the
    number of hours of SAT tutoring for a student is highly correlated
    with their family income. If we had an RCT that randomized the
    amount of tutoring students got, we wouldn’t have to worry about
    parental income. However, in an observational setting, this is just
    a fact of the matter.

To demonstrate the effects of multicollinearity on model output, we are
going to simulate some data. Basically, we are going to simulate the
following data generating process:

$$Y = \beta_0 + \beta_1 X_1 + \beta_2 X_2 + \epsilon$$

where $\beta_0 = 0$ and $\beta_1=\beta_2=1$.

The first time we run the model, $X_1$ and $X_2$ will be completely
uncorrelated. However, we are going to use a `for` loop to re-run the
model over and over, increasing the correlation between the two
variables after each iteration. The code is not necessarily important,
but you can still hopefully follow along.

<details class="code-fold">
<summary>Code</summary>

``` r
set.seed(757)
N <- 100 # sample size
x1 <- rnorm(N, 0, 1) # create X1
e <-  rnorm(N, 0, 3) # create unobserved error

a_vals <- seq(0, 0.99, 0.01)
# empty vectors to store the results of each iteration
rho <- rep(NA, length(a_vals)) # correlation between X1 and X2
b1 <- rep(NA, length(a_vals)) # estimated coefficient on X1
se1 <- rep(NA, length(a_vals)) # standard error of that coefficient

for(i in 1:length(a_vals)){

  # X2 is a combination of randomness and X1
  # When a = 0, X2 and X1 are completely uncorrelated
  a <- a_vals[i]
  x2 <- ((1-a)*rnorm(N, 0, 1)) + (a*x1)
  y <- x1 + x2 + e # Data Generating Process
  reg <- lm(y ~ x1 + x2)

  rho[i] <- cor(x1, x2)
  b1[i] <- coef(reg)["x1"]
  se1[i] <- coef(summary(reg))["x1", "Std. Error"]
}

# once the correlation is essentially 1, the numbers get so extreme that they
# squash everything else, so we only plot correlations up to 0.99
keep <- rho <= 0.99
lower <- b1 - 1.96*se1
upper <- b1 + 1.96*se1

par(mfrow = c(1, 2), mar = c(4.1, 4.1, 1.1, 1.1))
plot(rho[keep], b1[keep], type = "n", las = 1, main = "",
     ylim = range(lower[keep], upper[keep]),
     xlab = "Correlation Between X1 and X2",
     ylab = "Coefficient Estimate (95% CI)")
segments(rho[keep], lower[keep], rho[keep], upper[keep],
         col = scales::alpha("dodgerblue", 0.4))
points(rho[keep], b1[keep], pch = 19,
       col = scales::alpha("black", 0.6))
abline(h = 1, col = "tomato", lty = 2, lwd = 2) # the true value
plot(rho[keep], se1[keep], las = 1, pch = 19, main = "",
     col = scales::alpha("black", 0.6),
     xlab = "Correlation Between X1 and X2",
     ylab = "Standard Error")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module05_img/05-03-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Two plots. The left shows coefficient estimates with confidence intervals fanning out as the correlation between X1 and X2 increases. The right shows the standard error rising with the correlation." />

</details>

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 1))
```

</details>

<details open>

<summary>

Explanation of these figures
</summary>

1.  The red dashed line is the true value of $\beta_1$, which is 1. The
    estimates (black dots) are not systematically pushed away from it as
    the correlation grows. They just get noisier.
2.  The standard error (right panel) only goes up. When $X_1$ and $X_2$
    are uncorrelated, it is about 0.33. It is 0.38 at a correlation of
    0.5, 0.73 at 0.9, and 2.31 at 0.99, which is about 7 times larger
    than where we started.
3.  Because the standard errors grow, the confidence intervals (blue
    lines) fan out. At a correlation of 0.9, the estimate is 2.17 – not
    very close to 1 – but the interval (0.74 to 3.6) still contains the
    truth. The model is simply less sure. At a correlation of 0.99, the
    interval runs from -6.21 to 2.86, which is so wide that it cannot
    rule out zero.

</details>

Notice that the true value of 1 was inside the confidence interval in 88
of the 88 iterations, even at very high correlations. All of these plots
provide evidence that multicollinearity *does not* bias coefficients,
but *does* hinder the precision of the estimates. Since a $t$-statistic
is the estimate divided by its standard error, bigger standard errors
also make it harder to reject a null hypothesis of zero.

## Choosing Variables

To summarize the effect of including (or not including) additional
regressors:

1.  Omitting important variables biases coefficient estimates (and
    therefore model predictions, too).
2.  Including unimportant variables reduces the precision of the
    coefficient estimates.

The way you should think about adding new variables to your model is
through a **bias-variance tradeoff**. We know that coefficients can be
biased if we omit important variables, but our standard errors get
larger as the number of parameters we have to estimate increases.

It’s important to consider only the most important variables in your
model to minimize both bias and variance. You might ask: *how can I tell
which variables are most important?* To that I would say: good question,
and the best answer is **economic theory**.

Economic theory should guide your choices about which variables to
include/exclude and which functional forms you should use (`log()`,
`x + x^2`, etc.). Unfortunately, this might seem like an unsatisfying
answer, but econometrics is as much an art as it is a science. As you
become more comfortable with econometrics, selecting variables and
functional forms will become more natural.

In the next module, we will discuss how to incorporate
binary/categorical variables into our models.

[^1]: Age is a linear function of year built:
    `ames$age <- 2011 - ames$yr_built`
