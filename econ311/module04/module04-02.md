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

## Quantifying a Relationship

We continue with the SAT and GPA data from the last lecture
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv),
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/satgpa.html)).

``` r
library("scales")
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
```

Now that we can visually see a relationship, we need to figure out how
to quantify it. We are going to be introduced to something called
**covariance**. This is a measure of how two variables move with one
another. Let’s work up to the formula with some intuition.

We are going to compare each data point to its respective mean. In the
following chunk, subtract off the mean from each variable. Then, plot
the new data, and include vertical and horizontal lines at 0.

``` r
df$gpa_0 <- # subtract off mean here
df$sat_0 <- # subtract off mean here
plot(df$gpa_0, df$sat_0, las = 1, pch = 19,
     col = alpha("ENTER A COLOR HERE", 0),
     xlab = "GPA - mean GPA", ylab = "SAT - mean SAT")
abline() # vertical and horizontal lines here
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
df$gpa_0 <- df$hs_gpa - mean(df$hs_gpa)
df$sat_0 <- df$sat_sum - mean(df$sat_sum)
plot(df$gpa_0, df$sat_0, las = 1, pch = 19,
     col = alpha("dodgerblue", 0.2),
     xlab = "GPA - mean GPA", ylab = "SAT - mean SAT")
abline(h = 0, v = 0)
```

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-02-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Mean of GPA subtracted from GPA vs mean of SAT subtracted from SAT." />

</details>

</details>

If we find that $x_i - \bar{x} > 0$ and $y_i - \bar{y} > 0$ (or
$x_i - \bar{x} < 0$ and $y_i - \bar{y} < 0$) at the same time more often
than not, this is good evidence that the two variables are moving
together. In words, if GPA is above its mean at the same time that SAT
is above its mean (or below/below), these variables are moving together.
See the figure below for an illustration.

``` r
plot(df$gpa_0, df$sat_0, las = 1, pch = 19,
     col = alpha(ifelse(df$gpa_0 * df$sat_0 > 0,
                        "dodgerblue", "black"),
#           Note that the opacity can depend on some value.
                 ifelse(df$gpa_0 * df$sat_0 > 0, 0.2, 0.2)),
     xlab = "GPA - mean GPA", ylab = "SAT - mean SAT")
abline(h = 0, v = 0)
```

For each point, we can tell if both values are positive (meaning both
above their mean) or both values are negative (meaning both below their
mean) by multiplying them together. Two positive numbers (or two
negative numbers), when multiplied together, will produce a positive
number. If one number is negative and the other is positive, then the
product will be negative. To simplify this, we can use the `sign()`
function to output +1, 0, or -1 depending on the sign of the input.

``` r
# Example:
sign(c(-10, 0, 3, -4, 6, 100))
# Application:
# head(sign(df$gpa_0) * sign(df$sat_0))
```

If the points are exactly evenly distributed, we would expect the
average of `sign(df$gpa_0) * sign(df$sat_0)` to be equal to 0 (since the
sum of -1s and +1s cancel out to zero). However, if the average is
positive, this would suggest a positive relationship.

The mean of this outcome is indeed positive (0.304). Using the `table()`
function, we can see that most of the values fall in the (-1, -1) or
(+1, +1) groups:

``` r
table(GPA = sign(df$gpa_0), SAT = sign(df$sat_0))
```

We can use the actual differences instead of using `sign()`, too. Why
might this be better than using `sign()`? First, there are some calculus
reasons for this that we won’t discuss. Second, for intuition, this will
*weigh* observations that are farther from the mean a bit more than ones
close to the mean.

The formula for this average looks like the following:

$$\frac{1}{n}\sum_{i = 1}^n (x_i - \bar{x})\times(y_i - \bar{y})$$

Take a second to stare at this… Doesn’t it look familiar? Perhaps it
would look like the formula for *variance* if we substituted $x_i$ for
$y_i$:

$$\frac{1}{n}\sum_{i = 1}^n (y_i - \bar{y})\times(y_i - \bar{y}) = \frac{1}{n}\sum_{i = 1}^n (y_i - \bar{y})^2$$

This formula (with $x_i$) is called **covariance**. A variable’s
covariance *with itself* is equal to its own variance.

<div class="aside">

In both formulas, I am dividing by $n$ for illustration purposes.
However, the correct formula is $n-1$ as the denominator.

</div>

## Covariance

What is the covariance of these two different variables?

``` r
cat("R's default formula:", cov(df$hs_gpa, df$sat_sum), "\n")
cat("Our custom calculation:", sum(df$gpa_0 * df$sat_0) / (nrow(df) - 1))
```

Unfortunately, there is not a very intuitive interpretation in words for
what this number means. Rather, we mostly interpret the sign of the
number. Positive means that the variables move together and negative
means the variables move in opposite ways.

Let’s see if we can turn this number into something that is
interpretable. Let’s work with the variance formula to start. Suppose we
have data on some distance, and our variance is 36 miles<sup>2</sup>. To
make this number more interpretable, we would take the square root,
which would give us a standard deviation of 6 miles. Instead of taking
the square root, we also could have divided the variance by the standard
deviation which would have given us the same answer. For example:
$\sqrt{\sigma^2} = \frac{\sigma^2}{\sigma} = \sigma$. Let’s do the same
thing for our GPA and SAT covariance.

<div class="hover_img">

Currently, we have 3.3337488 as our covariance, and the units are GPA
units $\times$ SAT units. To scale this, we can divide by the standard
deviation of either variable. But, which variable should we divide by?
<a href="#">Por que no los
dos?<img src="https://media.tenor.com/cFkttZU0au8AAAAC/porque-no-los-dos-gif.gif" alt="image" height="150" /></a>

</div>

Going back to variance, if we divide variance ($\sigma^2$) by its
standard deviation ($\sigma$) twice, we would get
$\frac{\sigma^2}{\sigma \times \sigma} = \frac{\sigma^2}{\sigma^2} = 1$.
Remember, the covariance of any variable with itself is equal to its
variance. There will be no other variable in the world with a stronger
relationship to variable $y$ than $y$ itself! Therefore, a value close
to 1 indicates that two variables are almost identical. On the other
hand, imagine the covariance of $y$ and $-y$. In this case, when $y$ is
above its mean, $-y$ *must* be below its mean. Calculating the
covariance of $y$ and $-y$ will result in the same magnitude as the
variance of $y$, but the sign will be negative. Therefore, a value close
to -1 indicates that two variables are nearly complete opposites.

So, +1 is the maximum value and indicates the *strongest* positive
relationship possible.

Then, -1 is the minimum value and indicates the *strongest* negative
relationship possible.

Let’s scale the covariance of `hs_gpa` and `sat_sum` similarly to how we
scaled the variance of $y$: divide by the standard deviation of both
variables.

Before, we calculated $\frac{cov(y, y)}{\sigma_y \times \sigma_y}$. Now,
we’ll calculate
$\frac{cov(\text{GPA}, \text{SAT})}{\sigma_{\text{GPA}} \times \sigma_{\text{SAT}}}$.

This number is a unitless measurement of the **strength and direction**
of the relationship between two variables. This number is called the
**correlation coefficient**, denoted rho ($\rho$).

$$ \rho_{x,y} = \frac{cov(x, y)}{\sigma_x \sigma_y}$$

## Correlation

Below is a visualization of the previous scenarios ($y$ vs $y$, GPA vs
SAT, $y$ vs $-y$):

<!-- ```{webr-r} -->

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 3))
plot(df$hs_gpa, df$hs_gpa, xlab = "GPA", ylab = "GPA",
     main = c("Correlation:", round(cor(df$hs_gpa, df$hs_gpa), 3)),
     col = alpha("dodgerblue", 0.2), pch = 19)
abline(v = mean(df$hs_gpa), h = mean(df$hs_gpa))
plot(df$hs_gpa, df$sat_sum, xlab = "GPA", ylab = "SAT",
     main = c("Correlation:", round(cor(df$hs_gpa, df$sat_sum), 3)),
     col = alpha("mediumseagreen", 0.2), pch = 19)
abline(v = mean(df$hs_gpa), h = mean(df$sat_sum))
plot(df$hs_gpa, -df$hs_gpa, xlab = "GPA", ylab = "-GPA",
     main = c("Correlation:", round(cor(df$hs_gpa, -df$hs_gpa), 3)),
     col = alpha("tomato", 0.2), pch = 19)
abline(v = mean(df$hs_gpa), h = mean(-df$hs_gpa))
par(mfrow = c(1, 1))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-02-unnamed-chunk-11-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Three images. From left to right, a perfect positive correlation, a correlation of 0.431, and then a perfect negative correlation." />

</details>

The word “correlation” is one of the few words that have been able to
sneak out of statistics textbooks and find its way into ordinary, every
day vocabulary. I’m sure you’ve heard or said “this is correlated with
that”, or “correlation does not mean causation”, etc. Correlation is a
very specifically defined term, and hopefully, you have a deeper
understanding of what it really means now.

If two variables ($A$ and $B$) are correlated, this is because:

1.  $A \rightarrow B$ ($A$ causes $B$)
2.  $B \rightarrow A$
3.  $C \rightarrow A$ and $C \rightarrow B$ (some third factor causes
    both)
4.  Sometimes, things are correlated by random chance.

Each time you find a correlation between two variables, you should think
deeply about all four of these possible reasons.

In our example about GPAs and SAT scores, it is likely that there is a
third factor, or quite a few other factors, that determine both
someone’s GPA and SAT score. Therefore, the correlation we identified
should not be interpreted as causal.

Take a look at this [website of spurious
correlations](https://www.tylervigen.com/spurious-correlations) for
examples of things that are correlated due to chance.

<!-- To further complicate things, **not** finding a correlation when you expect to find one could be due to three reasons: -->

<!-- 1. There is no causal relationship between $A$ and $B$. -->

<!-- 1. Some third factor $C$ is masking/obscuring the relationship between $A$ and $B$. -->

<!-- 1. Sometimes, things are *not* correlated by chance. -->

If we are after causation, what is the best way to rule out all of the
ways we could find a correlation between $A$ and $B$? Randomization!

Suppose we wanted to know if a new sunscreen prevented sunburn. The best
way to answer this question would be to take a group of people and split
them into two groups. The first group gets the sunscreen and the other
group gets lotion.

Randomization would:

- Remove the possibility of reverse causality (Story 2). In other words,
  without randomization, maybe people only use sunscreen once they get a
  sun burn. This would make it look like sunscreen is causing sunburns,
  even though it’s the other way around.
- Imply the treatment and control group differ *only* by treatment,
  which rules out the story of a third factor causing the correlation
  (Story 3). Without randomization, since younger people are generally
  more risky, they are less likely to use sunscreen. Additionally,
  younger people are also more likely to spend time outside which leads
  to more sunburns. Therefore, we would find that sunscreen leads to
  less sunburn, but this would be due to age influencing both variables.
- Ensure any correlation is not driven by statistical chance once the
  sample size is large enough (Story 4).

Calculating correlations in R can be done with the `cor()` function.
This outputs a correlation coefficient when you enter two vectors.
Moreover, you can feed `cor()` a `data.frame`, and it will calculate a
correlation matrix. The matrix contains the correlation coefficients
calculated from every combination of variables.

``` r
cor(df$sat_sum, df$hs_gpa)
cat("\n")
cor(df[,c("sat_v", "sat_m", "sat_sum", "hs_gpa", "fy_gpa")])
```
