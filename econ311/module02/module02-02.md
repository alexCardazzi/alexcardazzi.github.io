# One Y Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Dispersion

The middle of some data is an important summary point. However, a second
important measure is: how much does the data vary? Are the observations
in the data close to the mean, or are they far? A measure like this
would start to address the issue with outliers, but also provide
additional information that could be helpful.

This idea is called *dispersion*. A common measure of dispersion is
*variance* or *standard deviation*.

## Building an Equation

Let’s try to build an equation for dispersion that we can use. Remember:
we are interested in quantifying how far away observations are from the
mean ($\mu$ or $\bar{y}$).

Since we cannot observe $\mu$ (because it is a population parameter), we
have to use $\bar{y}$ as our measure of the mean. So, to calculate
distance from the mean, we can simply use subtraction.

$$y_i - \bar{y}$$

Note: I am using a subscript $i$ to denote the $i$th observation. In
other words, if there are $n$ observations, we will calculate:

$$y_1 - \bar{y}, \ y_2 - \bar{y}, \ ..., \ y_i - \bar{y}, \ ..., \ y_n - \bar{y}$$

If we have the distance of each observation from the mean, why don’t we
try taking the mean of this? This would give us, in words, an **average
distance from the mean**. In math:

$$\frac{1}{n} \sum_{i=1}^{n} (y_i - \bar{y})$$

To test our measure, let’s use some example data from the notes on
Central Tendency. Suppose we have two sets of data: one from UNC
Geography Majors in 1985 and 1986 (Michael Jordan’s graduating class).
If our measure makes sense, we should expect the 1985 mean to be less
than the 1986 mean, but the dispersion to be lower as well. Here are the
two datasets:

``` r
salaries85 <- c(32000, 33000, 36000, 33000, 32000, 35000)
salaries86 <- c(31000, 34000, 32000, 34000, 33000, 20000000)
```

Let’s calculate the two sample means and save them.

``` r
mean85 <- mean(salaries85)
mean86 <- mean(salaries86)
cat("Average Salary in 1985:", mean85, "\n")
cat("Average Salary in 1986:", mean86)
```

<details>

<summary>

Output
</summary>

    Average Salary in 1985: 33500 
    Average Salary in 1986: 3360667

</details>

Now, we need to code up our formula for dispersion and test it with both
sets of data.

<div class="columns">

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum(salaries85 - mean85)
denominator <- length(salaries85)
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 0

</details>

</div>

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum(salaries86 - mean86)
denominator <- length(salaries86)
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 0

</details>

</div>

</div>

Not only are the two numbers the same, but they’re zero! There is either
a mistake in the math or in the code. Hint: the mistake is not in the
code. Rather, let’s generate more output as we go this time.

``` r
# Calculate the differences
numerator <- salaries85 - mean85
cat("Numbers to Add:", numerator, "\n")
cat("Sum of Numbers to Add:", sum(numerator))
```

<details>

<summary>

Output
</summary>

    Numbers to Add: -1500 -500 2500 -500 -1500 1500 
    Sum of Numbers to Add: 0

</details>

Since some of the values are above the mean and others are below, the
sum of the positive and negative numbers nets out to zero. Let’s see if
this is true in the algebra.

<div class="aside">

Note: I am dropping the $\frac{1}{n}$ because the issue is clearly in
the numerator, not the denominator

</div>

$$\begin{aligned}\sum_{i=1}^{n} (y_i - \bar{y}) &= (y_1 - \bar{y}) + (y_2 - \bar{y}) + ... + (y_n - \bar{y}) \\
&=(y_1 + ... + y_n) - (\bar{y} + ... + \bar{y})\\
&= \sum{y_i} - (n \times \bar{y})\\
&= \sum{y_i} - (n \times \frac{1}{n}\sum{y_i}) \\
&= \sum{y_i} - (\frac{n}{n}\sum{y_i})\\
&= \sum{y_i} - (\sum{y_i}) \\
&= 0\end{aligned}$$

The conclusion to both the math and the code is that, *no matter what*,
$\sum_{i=1}^n(y_i - \bar{y})$ is always equal to 0.

So, it seems like we need another equation. What if we tried to
transform $y_i - \bar{y}$ so that it was always a positive number? We
can use a square term! So, instead of $y_i - \bar{y}$, we can use
$(y_i - \bar{y})^2$. Then, our formula would look like:

<div class="aside">

You might have thought about absolute values here. This is a good
thought, and I am hoping you arrived at this. Unfortunately, absolute
value is a weird function that doesn’t have nice properties. We won’t
get into it here, but I thought I should note it.

</div>

$$\frac{1}{n} \sum_{i=1}^{n} (y_i - \bar{y})^2$$

Let’s try to build this equation in R and test it on our data.

<div class="columns">

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum((salaries85 - mean85)^2)
denominator <- length(salaries85)
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 2250000

</details>

</div>

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum((salaries86 - mean86)^2)
denominator <- length(salaries86)
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 5.537348e+13

</details>

</div>

</div>

These numbers are huge! These are not interpretable as is, and it’s
because we squared each distance. In words, this is **the average
squared distance from the mean**. This is what statisticians call
**variance**, which is often labeled $\sigma^2$.

To get a more interpretable number, we have to “undo” the squared term.
We can use the square root function to do this. Taking the square root
of variance gives **standard deviation**, often labelled $\sigma$.

$$\sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \bar{y})^2}$$

And in R:

<div class="columns">

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum((salaries85 - mean85)^2)
denominator <- length(salaries85)
sqrt(numerator / denominator)
```

<details>

<summary>

Output
</summary>

    [1] 1500

</details>

</div>

<div class="column" width="50%">

``` r
# add up the differences
numerator <- sum((salaries86 - mean86)^2)
denominator <- length(salaries86)
sqrt(numerator / denominator)
```

<details>

<summary>

Output
</summary>

    [1] 7441336

</details>

</div>

</div>

The interpretation of this number is very similar to “the average
distance from the mean.” In other words, suppose we randomly picked an
element out of a vector. On average, the element we pick will be a
distance of $\sigma$ units from the mean.[^1]

Of course, R has built in functions for variance (`var()`) and standard
deviation (`sd()`).[^2] There is a slight difference in what we coded
and what R will output. The denominator R uses is $n-1$ instead of just
$n$, but the reason for this is beyond the scope of this course.
However, for completeness and comparison:

``` r
numerator <- sum((salaries85 - mean85)^2)
denominator <- length(salaries85)
cat("St. Dev. (with n):", sqrt(numerator / denominator), "\n")
cat("St. Dev. (with n-1):", sqrt(numerator / (denominator-1)), "\n")
cat("St. Dev. (R Default):", sd(salaries85))
```

<details>

<summary>

Output
</summary>

    St. Dev. (with n): 1500 
    St. Dev. (with n-1): 1643.168 
    St. Dev. (R Default): 1643.168

</details>

As a final note, population variance is usually denoted by the Greek
letter *sigma* (squared), or $\sigma^2$. Sample variance is denoted by
$s^2$. Therefore, population and sample standard deviations are
generally $\sigma$ and $s$.

[^1]: In reality, it is the root-mean-squared difference, but you can
    think of it as like how I described.

[^2]: Just like `mean()`, both functions return `NA` if any value is
    missing. To drop the missing values first, add `na.rm = TRUE`, for
    example `sd(y, na.rm = TRUE)`.
