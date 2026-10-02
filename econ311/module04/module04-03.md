# One X Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

<style>
.hover_img a { position:relative; }
.hover_img a span { position:absolute; display:none; z-index:99; }
.hover_img a:hover span { display:block; }
</style>

<div class="hover_img">

<a href="#">Warning: Math
Ahead<img src="https://media.tenor.com/yvQm6ieAtnAAAAAC/prepare-yourself-goliath.gif" alt="" height="400" class="center"/></a>

</div>

## Correlation

In the last part of the module, we were building/estimating a
correlation between GPAs and SAT scores so we would have a tool for
guidance counselors to predict SAT scores given a student’s GPA. Or,
maybe we were hired by real estate agents to create a tool to predict a
home’s sale price given some features about the property. Or, maybe we
want to estimate the impact some government program like
[SNAP](https://www.fns.usda.gov/snap/supplemental-nutrition-assistance-program)
has on employment outcomes for citizens. How can we use correlations to
inform us?

## Conditional Mean

<!-- BBall Height, NBA vs WNBA -->

Let’s suppose today is your first day as a guidance counselor and your
first student comes into your office and says, “what do you think I’ll
get on my SAT?” Unfortunately, you do not know anything about them, so
the *best* guess you can come up with is… the average.

However, if the student told you that they have a below average GPA, you
would likely adjust your guess downwards. By adjusting your guess
downwards, you are implicitly calculating a *conditional* mean. When we
know nothing, the best guess we can come up with is the mean of the
entire distribution. We talked about this in Module 2. If you are to
gain some additional information (e.g., GPA) before making your guess,
your guess can only improve. We can demonstrate this with a simple
simulation.

Let’s take a look at [data on the heights of all-star basketball
players](https://raw.githubusercontent.com/alexCardazzi/alexcardazzi.github.io/main/econ311/data/bball_allstars.csv).

Suppose you were playing a (not so fun) game with a friend where they
picked a player in this data at random and asked you to guess the
player’s height. What height should you guess? Spoiler: the average
height. Let me try to convince you.

Let’s define the quality of a guess as the squared difference of the
height of the randomly the selected player ($h_i$) and the guessed
height ($G$). Therefore, if the guess is spot on, the guess’s quality
would be $(h_i - G)^2 = (0)^2 = 0$. If the guess is too high or too low,
the resulting measure of quality would increase. In other words, this
score is like golf where lower scores are better scores. We can then
calculate the average guess quality by averaging $(h_i - G)^2$ across
all heights $h_1, h_2, ..., h_n$. Then, if we have two guesses $G_1$ and
$G_2$, we could say one guess is better than the other if its average
guess quality measure is smaller than the other’s.

In the following code, I am going to calculate the quality of a sequence
of different guesses ranging from six inches below the sample average to
six inches above the sample average. Then, I will plot the guess on
$x$-axis and its corresponding quality on the $y$-axis. Remember: lower
means a better guess!

<details class="code-fold">
<summary>Code</summary>

``` r
library("scales")
bball <- read.csv("https://raw.githubusercontent.com/alexCardazzi/alexcardazzi.github.io/main/econ311/data/bball_allstars.csv")

# Guesses range from 6 inches below the mean
#   and increase by a tenth of an inch until
#   6 inches above the mean is reached.
the_guesses <- seq(mean(bball$HEIGHT) - 6,
                   mean(bball$HEIGHT) + 6,
                   by = 0.1)
avg_sqr_diffs <- c()
for(guess in the_guesses){
  
  sqr_diff <- (bball$HEIGHT - guess)^2
  ans <- mean(sqr_diff)
  avg_sqr_diffs[length(avg_sqr_diffs) + 1] <- ans
}

plot(the_guesses, avg_sqr_diffs, las = 1,
     xlab = "Guess for Height", pch = 19,
     ylab = "Average Squared Error",
     col = alpha("dodgerblue", 0.33))
abline(v = mean(bball$HEIGHT), col = "tomato")
legend("bottomright", "Average Height", lty = 1, col = "tomato", bty = "n")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-03-unnamed-chunk-3-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Average error by guess, which is a U-shape with the bottom occurring around the average height of all players." />

</details>

What does this picture tell us? Perhaps unsurprising, it seems that the
guess with the smallest error is simply the sample’s average. Now, what
if your friend tweaked the game a bit and decided to only pull names
from the WNBA portion of the data? Let’s see how the optimal guess might
change.

<details class="code-fold">
<summary>Code</summary>

``` r
the_guesses <- seq(mean(bball$HEIGHT) - 6,
                   mean(bball$HEIGHT) + 6,
                   by = 0.1)
avg_sqr_diffs <- c()
for(guess in the_guesses){
  
  sqr_diff <- (bball$HEIGHT[bball$LEAGUE == "WNBA"] - guess)^2
  ans <- mean(sqr_diff)
  avg_sqr_diffs[length(avg_sqr_diffs) + 1] <- ans
}

plot(the_guesses, avg_sqr_diffs, las = 1,
     xlab = "Guess for Height", pch = 19,
     ylab = "Average Squared Error",
     col = alpha("dodgerblue", 0.33))
abline(v = mean(bball$HEIGHT), col = "tomato")
abline(v = mean(bball$HEIGHT[bball$LEAGUE == "WNBA"]),
       col = "mediumseagreen", lty = 2)
legend("bottomright", c("Average Height", "Average WNBA Height"),
       lty = 1:2, col = c("tomato", "mediumseagreen"), bty = "n")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-03-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Average error by guess, which is a U-shape with the bottom occurring around the average height of WNBA players." />

</details>

Now, the best guess is the average of the WNBA players instead of the
average of the entire sample. This is probably obvious to you, and you
might even be wondering what the point of this is. The idea is that
without any information, the most accurate we can be is a simple
average. However, once we gain additional relevant information, we can
revise our guess and increase accuracy.

<!-- In both cases, you can see that the points reach a (near) minimum error when the "guess" is near the mean of the respective distribution. -->

Keep in mind that our goal here was to minimize the sum (mean) of
squared errors. We will revisit this soon, but first, we are going to
jump back to guessing SAT scores given GPAs.

<div class="aside">

The $y$-axis above is in *squared* inches. If you take the square root
of the average squared error, you get back to inches, just like taking
the square root of variance gives the standard deviation (Module 2.2).
At the best guess (the mean), the average squared error is the variance
(dividing by $n$) and its square root is the standard deviation. Taking
the square root would change the height of the curve but not the
location of its bottom, so the best guess would still be the mean.

</div>

Our goal as guidance counselors is to give students our best guess for
what they’ll score on their SAT. In essence, we want to create a
*function* that converts GPA (as an argument) into an SAT score. We want
this function to have a few properties:

1.  SAT score must be dependent on GPA.
2.  Given an average GPA, the model should output an average SAT.
3.  SAT score should increase as GPA increases or decrease as GPA
    decreases (since they’re positively correlated).
4.  The model should be correct on average.

Like we did with the law of demand, we are going to guess a functional
form for the relationship between GPA and SAT:

$$\text{SAT}_i = \beta_0 + \beta_1 \times \text{GPA}_i$$

This functional form satisfies our first two conditions right off the
bat. However, we still need to select values for $\beta_0$ and $\beta_1$
to satisfy the third and fourth conditions.

This equation of a line gives us the conditional expectation of SAT
given GPA. In other words, a predicted SAT score for any given GPA. This
is most often written as $E[ \ \text{SAT} \ | \ \text{GPA} \ ]$. You
might say, “that’s great, but what values should we use for $\beta_0$
and $\beta_1$?” In the chunk below, experiment with a few different
values.

``` r
library("scales")
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = alpha("black", 0.2),
     xlab = "GPA", ylab = "SAT")
# a is "beta_0" and b is "beta_1"
abline(a = 10, b = 30, lty = 1, col = "tomato", lwd = 2)
abline(a = , b = , lty = 2, col = "orchid", lwd = 2)
abline(a = , b = , lty = 3, col = "dodgerblue", lwd = 2)
```

Unfortunately, no matter what values we choose for $\beta_0$ and
$\beta_1$, there’s never going to be a single line that runs through
every one of the points. To fix this, let’s write the equation as
follows:

$$\text{SAT}_i = \beta_0 + \beta_1\text{GPA}_i + \epsilon_i$$ where
$\epsilon_i$ (epsilon) is our *error term*. This will make up the
difference between the point and the line we draw through the points.

## Line of Best Fit

Ideally, we wouldn’t need $\epsilon$ at all and our model would
perfectly fit the data. However, since we *do* need $\epsilon$, we want
to minimize it. Specifically, like we did when guessing heights of
basketball players, we are going to minimize the *sum of squared
errors*, or $\sum \epsilon_i^2$. Note that we can calculate the errors
associated with choosing $\beta_0$ and $\beta_1$ by the following:
$\epsilon_i = \text{SAT}_i - \beta_0 - \beta_1 \text{GPA}_i$.

Since the errors will be minimized, the line given by $\beta_0$ and
$\beta_1$ will be colloquially known as the “line of best fit”.

<!-- 1. On average, the model should be correct. In other words, the average difference between $Y$ and $\hat{Y}$ should be zero.  Mathematically, we want $E[Y - \hat{Y}] = 0$.  Since $\hat{Y} = \beta_0 + \beta_1X$, this can also be written as: $E[Y - \hat{Y}] = E[Y - \beta_0 - \beta_1X] = 0$. -->

<!-- 1. Like we did with heights of basketball players, we want $\beta_0$ and $\beta_1$ to *minimize the sum of squared errors*. Mathematically: $\text{arg}\,\min\limits_{\beta_0, \beta_1}\, \sum \epsilon_i^2$. -->

## Ordinary Least Squares

The algorithm we are going to use to solve for $\beta_0$ and $\beta_1$
is called **Ordinary Least Squares**, or OLS. To begin, let’s start with
writing an expression for the sum of squared errors.

$$S = \sum \epsilon^2= \sum (Y - \beta_0 - \beta_1X)^2 $$ When we want
to minimize an expression, we must take the derivative and set it equal
to zero. Since $\beta_0$ and $\beta_1$ are unknown, these are what we
need to take the derivative with respect to. First, we will focus on
$\beta_0$.

The following equations have text if you hover over them:

<div title="To start, we need to take the derivative of S with respect to beta 0.  The 2 in front of the expression comes from the exponent, and the -1 comes from the negative sign in front of beta 0 via the chain rule.">

$$\frac{\partial S}{\partial \hat{\beta}_0} = \sum 2\times (Y - \hat{\beta}_0 - \beta_1X) \times (-1) = 0$$

</div>

<div title="We can divide out the 2 and -1 at the same time because the other side of the equal sign is zero.  This is convenient for division purposes.">

$$\frac{1}{-2} \times \sum 2\times (Y - \hat{\beta}_0 - \beta_1X) \times (-1) = \frac{1}{-2} \times 0$$

</div>

<div title="Since the summation sign, sigma, is just addition, we can 'distribute' the sigma across each factor.">

$$\sum Y - \sum \hat{\beta}_0 - \sum \beta_1X = 0$$

</div>

<div title="Here, I am reorganizing the terms by moving sigma beta 0 over to the left, and then factoring out beta 0.  Notice how I am left with a sum of 1s.  Effectively, this is adding n 1's, which will sum to n.">

$$\sum Y - \beta_1\sum X = \hat{\beta}_0 \sum 1$$

</div>

<div title="Since I have n on the right side via the sum of 1's, I can now get ready divide both sides by n.">

$$\sum Y - \beta_1\sum X = n \times \hat{\beta}_0 $$

</div>

<div title="Now I am left with 1 over n times the sum of Y's.  This is the same as the average of the Y's!  Same for the X's.">

$$\frac{1}{n}\sum Y - \beta_1\frac{1}{n}\sum X = \hat{\beta}_0 $$

</div>

<div title="Finally, I am left with a numeric solution for an estimate of beta 0. Unfortunately, I still need to solve for beta 1, but I am half way there.">

$$\overline{Y} - \beta_1 \overline{X} = \hat{\beta}_0$$

</div>

Now, how do we solve for $\hat{\beta}_1$? Use the same approach:

<div title="Begin by taking the derivative using the chain rule the same way as before.">

$$\frac{\partial S}{\partial \hat{\beta}_1} = \sum 2\times (Y - \hat{\beta}_0 - \hat{\beta}_1X) \times (-X) = 0$$

</div>

<div title="Divide out the -2 at this step as well.">

$$\sum(Y - \hat{\beta}_0 - \hat{\beta}_1X) \times (X) = 0$$

</div>

<div title="Distribute both the X and the sigma.">

$$\sum XY - \hat{\beta}_0\sum X - \hat{\beta}_1 \sum X^2 = 0$$

</div>

<div title="Since we have already solved for beta 0, we can plug in our previous solution for this.  Now, our equation is a function of data and only one unknown.">

$$\sum XY - \Big[\overline{Y} - \hat{\beta}_1 \overline{X}\Big]\sum X - \hat{\beta}_1 \sum X^2 = 0$$

</div>

<div title="Again, distribute the sigma X into the formula for beta 0.">

$$\sum XY - \overline{Y}\sum X + \hat{\beta}_1 \overline{X}\sum X - \hat{\beta}_1 \sum X^2 = 0$$

</div>

<div title="Move all of the terms with beta 1 in it to the right side of the equal sign.">

$$\sum XY - \overline{Y}\sum X = \hat{\beta}_1 \sum X^2 - \hat{\beta}_1 \overline{X}\sum X$$

</div>

<div title="Factor out the beta 1 from everything on the right side.  Next, I am going to divide both sides by what I factored beta 1 out of.  Following this division, I will be left with an expression for beta 1 that is only a function of data.">

$$\sum XY - \overline{Y}\sum X = \hat{\beta}_1 \Big[\sum X^2 - \overline{X}\sum X\Big]$$

</div>

<div title="Following this step of division, notice how similar both top and bottom appear.  Imagine if Y was equal to X. These would be exactly the same expression! Moreover, the numerator is, with some modification, the expression for covariance. This makes the denominator the expression for variance.">

$$\frac{\sum XY - \overline{Y}\sum X}{\sum X^2 - \overline{X}\sum X} = \hat{\beta}_1$$

</div>

<div title="Formula for beta 1.">

$$\frac{cov(X,Y)}{var(X)} = \hat{\beta}_1$$

</div>

<div title="formula for beta 0.">

$$\overline{Y} - \frac{cov(X,Y)}{var(X)} \overline{X} = \hat{\beta}_0$$

</div>

As a note, we can also express $\beta_1$ in terms of the correlation
coefficient $\rho$:

$$\frac{cov(X,Y)}{var(X)} = \frac{cov(X,Y)}{\sigma_X\sigma_Y}\times\frac{\sigma_Y}{\sigma_X} = \rho\times\frac{\sigma_Y}{\sigma_X}$$

Okay, that was a *lot* of math. In fact, this is the most math you’ll
see in this course. So, take a breath and grab a water.

<div class="aside">

If you’re interested in more of this, you can find some [derivations on
stackexchange](https://stats.stackexchange.com/questions/133554/least-squares-calculus-to-find-residual-minimizers).

</div>

<!-- df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv") -->

Now, let’s use some of this math to figure out $\beta_0$ and $\beta_1$
for our GPA and SAT example:

``` r
cov_xy <- cov(df$hs_gpa, df$sat_sum)
var_x <- var(df$hs_gpa)
beta1 <- cov_xy / var_x
beta0 <- mean(df$sat_sum) - (beta1 * mean(df$hs_gpa))
cat("SAT =", beta0, "+", beta1, "x GPA")
```

This expression gives us the expectation of a student’s SAT score given
a student’s GPA. We know for sure that this minimizes the sum of squared
errors (because all of that math), but is the model correct on average?
To test it, we can calculate the average error. Being correct would
yield an average of zero.

``` r
sat_expectation <- beta0 + beta1*df$hs_gpa
sat_real <- df$sat_sum
round(mean(sat_real - sat_expectation), 5)
```

<!-- It appears that $E[SAT_i - E[SAT_i]] = 0$, meaning the model is correct on average. -->

What does the model look like relative to the data? Using the chunk
below, try entering in some of your favorite lines from before.

``` r
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = alpha("black", 0.2),
     xlab = "GPA", ylab = "SAT")
abline(a = 10, b = 30, lty = 1, col = "tomato", lwd = 2)
abline(a = , b = , lty = 2, col = "orchid", lwd = 2)
abline(a = , b = , lty = 3, col = "dodgerblue", lwd = 2)
abline(a = beta0, b = beta1, lty = 1, col = "gold", lwd = 4)
legend("bottomright", c("OLS"), bty = "n",
       "gold", lty = 1, lwd = 4)
```

What do you think? How does OLS compare to your preferred line?

In terms of interpretation, what does this equation mean in words?

- $\beta_1$ (11.36): If we increase GPA by one unit (e.g., from 2.2 to
  3.2), we expect the sum of our SAT percentiles to increase by 11.36.
  In more abstract terms, “a one unit increase in $X$ will change $Y$ by
  $\beta_1$.”
- $\beta_0$ (66.99): If someone has a GPA of 0, we expect their sum of
  SAT percentiles to be equal to 66.99. In more abstract terms, “the
  value of $Y$ when $X=0$.”

As long as “a one unit change” makes sense for $X$, $\beta_1$ will be
interpretable. To bring this back to an earlier module, the variables
must be either [*interval* or
*ratio*](https://alexcardazzi.github.io/econ311/module01/module01-02.html#types-of-measurement)
measures.

For $\beta_0$ to be interpretable, $X$ must be able to take on a
reasonable value of zero. Since a GPA of zero is nearly impossible,
$\beta_0$ does not have much of an interpretation in this case.

<!-- Before moving onto other topics, check out this [interactive OLS example](https://setosa.io/ev/ordinary-least-squares-regression/). -->
