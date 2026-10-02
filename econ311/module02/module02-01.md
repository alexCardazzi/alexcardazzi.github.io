# One Y Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Central Tendency

Now that we have read our data into R, we need ways to summarize it to
communicate it. An intuitive way to summarize data is to report the
*central tendency*, or middle. We care about the middle because,
generally speaking, it’s what is common / what can be expected / what is
typical / is convenient.

As an example, think about GPA. This number summarizes your past
outcomes in the classes you’ve taken.

So, how do we calculate central tendency?

There are three main ways we calculate central tendency:

> [!TIP]
>
> ### Mean
>
> This is the average value. Formally:
> $\frac{1}{n} (y_1 + y_2 + ... + y_n) = \frac{1}{n}\sum_{i=1}^{n} y_i$.
> In R: `mean()`

> [!TIP]
>
> ### Median
>
> This is the middle value. To find the median, you need to put your
> data in numerical order and select the middle number. In R: `median()`

> [!TIP]
>
> ### Mode
>
> This is the most frequently occurring number. To find the mode, count
> the number of times each value appears and select the most popular
> value. There is not a pre-built function to calculate the mode in R.

## An Aside: Missing Data

In the real world, data are hardly ever “clean”. Clean data are data
that are ready for analysis right away.

As an example, suppose I collect income data from people in different
parts of the world. In the US, someone making fifty thousand dollars
would report “\$50,000.00”. In Italy, someone making the same amount
would submit “€50.000,00”. A “clean” version of this would be `50000`.

In addition to formatting quirks, sometimes data is literally missing.
This could be due to someone forgetting to enter their income, not
wanting to disclose their income, or them not having an income to
report. Whatever the reason, we need to come up with a way to handle
this.

Remember, missing data in R is represented by `NA`. `NA` values are
helpful because it allows us to differentiate between “real” data and
missing data. However, `NA` values require special attention.

Let’s consider the example of `mean()`. If you compute the mean of a
vector, say `y <- c(4, 9, 2, 5)`, you could use `mean(y)`. This would
give an answer of 5. What if one of those values were missing? Try
calculating the mean of `c(4, 9, 2, 5)`, and then the mean of
`c(4, 9, NA, 5)` below:

``` r
y <- 
mean(y)
```

<details>

<summary>

Solution
</summary>

Notice how `mean(c(4, 9, NA, 5))` returns an `NA` because R isn’t sure
what to do with the missing value since that missing value could
technically be anything.

To avoid generating `NA`s when using `mean()` (or even other functions,
too), you need to tell R what to do with the missing data. `mean()` has
an argument `na.rm` which accepts a boolean value. If this is equal to
`TRUE`, the `NA` value will be ignored. If it is set to `FALSE`, the
missing value will be considered (and thus the output will be `NA`).

``` r
y <- c(4, 9, NA, 5)
mean(y, na.rm = TRUE)
```

<details>

<summary>

Output
</summary>

    [1] 6

</details>

</details>

## Back to Central Tendency

Every population has a *true* mean, median, mode, etc. However, as we’ve
already discussed, we cannot always observe the entire population for
various reasons.

Population parameters are usually denoted by Greek letters. For example,
the population mean is denoted as *mu*, or $\mu$. The sample mean is
denoted by *y bar*, or $\bar{y}$

Each of the above measures have their own strengths and weaknesses.
Usually, people care the most about the mean, and less about the median
and mode, but each of these remain helpful summary measures.

An important strength of the mean is that it has a concise mathematical
formula:

$$\overline{y} = \frac{1}{n}\sum_{i = 1}^{n} y_i$$

For some people, seeing math written out can be intimidating and scary.
Let me break down some of what is written, and maybe it won’t seem so
foreign.

- $\overline{y}$ is the resulting average.
- $\sum$ is a symbol that means to sum (add) everything to its right. In
  this case, sum across $y_i$.
- $y_i$ is the i<sup>th</sup> element (i.e. 1<sup>st</sup>,
  2<sup>nd</sup>) in the vector $y$, which is the vector of values to be
  added up.
- $i=1$ indicates to the reader to begin the addition with $i=1$, or the
  first element in $y$.
- $n$ represents the index of the final element to be added. When this
  is equal to $n$, it indicates to end with the final element in $y$.

In simpler terms, we can rewrite the previous as:

$$\overline{y} = \frac{1}{n}\sum_{i = 1}^{n} y_i = \frac{1}{n}(y_1 + y_2 + y_3 + \ ... \ + y_{n-1} + y_n)$$

An important weakness, however, is that the mean is susceptible to
influence from outliers. Consider [this
example](https://www.tigerdroppings.com/rant/o-t-lounge/1986-avg-salary-for-unc-geography-major/101667372/#:~:text=GEOG%20majors%20from%20Chapel%20Hill%20average%20salaries%20over%2010%20years%20from%20graduation%3A%0A%0A1985%3B%20%2433%2C000%0A1986%3B%20%243%2C600%2C000%0A1987%3B%20%2433%2C500%0A%0AAnyone%20know%20the%20reason%3F%20Pretty%20interesting.)
of how the mean can be influenced by extreme values ([part
A](https://en.wikipedia.org/wiki/Michael_Jordan#College_career:~:text=Jordan%20returned%20to%20North%20Carolina%20to%20complete%20his%20degree%20in%201986%2C%5B54%5D%20when%20he%20graduated%20with%20a%20Bachelor%20of%20Arts%20degree%20in%20geography.),
[part
B](https://www.spotrac.com/nba/player/_/id/8739/michael-jordan#:~:text=Michael%20Jordan%20signed%20a%207%20year%20%2C%20%246%2C300%2C000%20contract%20with%20the%20Chicago%20Bulls)).

The *average* salary of geography majors who graduated from the
University of North Carolina Chapel Hill in 1985, 1986, and 1987 were
\$33,000, \$3,600,000, and \$33,500, respectively.

Michael Jordan, the famous NBA star, graduated from UNC in 1986, and the
sum of his salary and endorsements was so high that it drove up the mean
by a factor of 100!

<details class="code-fold">
<summary>Code</summary>

``` r
# This is made up data to highlight the issue
# $20,000,000 is an outlier from the "true" central tendency of ~$33,000
salaries <- c(31000, 34000, 32000, 34000, 33000, 20000000)
mean(salaries)
```

</details>

<details>

<summary>

Output
</summary>

    [1] 3360667

</details>

Depending on the information you’d like to convey, choose your measure
of central tendency carefully!

Of course, R has a `mean` function, but let’s code our own version for
practice (fun?). The first step is to add up every element within a
vector. We can do this with the `sum()` function.

``` r
numerator <- sum(salaries)
```

Next, we need to count up the number of observations we have in the
vector we summed. To do this, we can use `length()`, which will tell how
“long” the vector is.

``` r
# If this was in a data.frame, you could use: nrow(name_of_data.frame)
# You could also use length(name_of_data.frame$name_of_vector)
denominator <- length(salaries)
```

Finally, we need to divide the sum (numerator) by the count
(denominator).

``` r
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 3360667

</details>

## Weighted Average

Another way to calculate the mean of a variable is via weighted average.
Suppose you have the following data:

``` r
# This is the same as typing: c(20, 20, ..., 20, 4, 4, 10, 10, ..., 10)
y_value <- c(rep(20, 6), rep(4, 2), rep(10, 12))
y_value
```

<details>

<summary>

Output
</summary>

     [1] 20 20 20 20 20 20  4  4 10 10 10 10 10 10 10 10 10 10 10 10

</details>

To calculate the mean of this vector, we can simply use `mean()`, which
will perform the algorithm we have already discussed (adding everything
up and dividing by the total number of observations). However, sometimes
you are not given the vector of numbers, but rather a table of values
and how many times they appear. To calculate an average using data like
this, we would need to use a weighted average. The formula for weighted
average is as follows:

$$\frac{\sum_{i = 1}^g w_i \times y_i}{\sum_{i = 1}^g w_i}$$

In this formula, $g$ represents the total number of unique values of
$y$, and $w$ represents the “weight” placed on each $y$. In the case of
the data above, $y_1$ would be 20 and $w_1$ would be 6. $y_2$ and $y_3$
would be 4 and 10, while $w_2$ and $w_3$ would be 2 and 12. Written out,
the formula would be:

$$\frac{\sum_{i = 1}^g w_i \times y_i}{\sum_{i = 1}^g w_i} = \frac{(6\times20) + (2\times4) + (12\times10)}{6 + 2 + 12} = 12.4$$

How can we do this in `R`?

The first thing we need are the unique values of $y$. To gather these,
we can use the `unique()` function. We’ll also want to put these numbers
in ascending order with `sort()`.

<div class="aside">

You can sort things in descending order by using
`sort(x, decreasing = TRUE)`. There is also another function (`order()`)
that returns the *position* of the elements if you were to sort them. To
get our desired effect, we’d need to use:
`unique(y_value)[order(unique(y_value))]`. Ultimately, this is probably
more work than necessary in this case. However, this becomes helpful
when ordering data frames.

</div>

``` r
unique_y <- sort(unique(y_value))
unique_y
```

<details>

<summary>

Output
</summary>

    [1]  4 10 20

</details>

Next, we need to know how many times each value appears in the data. To
get this information, we can use `table()`.

``` r
table_y <- table(y_value)
table_y
```

<details>

<summary>

Output
</summary>

    y_value
     4 10 20 
     2 12  6 

</details>

The `table()` function in `R` is a fundamental command that allows you
to quickly summarize data. The function returns the number of times each
value appears in the data, and we can obtain these counts by using
standard vector operations. For example, `table_y[2]` = 12. We can also
access the values that are being counted by using `names()`. For
example, `names(table_y)[2]` = 10.

Now we have everything we need to calculate the weighted average! We can
use `sort(unique(y_value))` as the vector $y$ and `table(y_value)` as
the vector $w$. Use these two snippets of code to calculate the weighted
average:

``` r
y_value <- c(rep(20, 6), rep(4, 2), rep(10, 12))
y <- sort(unique(y_value))
w <- table(y_value)

numerator <- 
denominator <- 
numerator / denominator
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
y_value <- c(rep(20, 6), rep(4, 2), rep(10, 12))
y <- sort(unique(y_value))
w <- table(y_value)

numerator <- sum(w * y)
denominator <- sum(w)
numerator / denominator
```

<details>

<summary>

Output
</summary>

    [1] 12.4

</details>

</details>

## Weighted Averages in R

Now that you know how a weighted average works under the hood, you do
not need to build one by hand every time. R has a function for it:
`weighted.mean()`. You give it the values first (`x`), then the weights
(`w`). Using the same data as above:

``` r
y_value <- c(rep(20, 6), rep(4, 2), rep(10, 12))
weighted.mean(x = sort(unique(y_value)), w = table(y_value))
```

<details>

<summary>

Output
</summary>

    [1] 12.4

</details>

This is the same answer we got by hand. The function adds up each value
times its weight, then divides by the sum of the weights.

Weighted averages matter most when you are averaging across groups of
different sizes. Suppose three sections of a course have the following
average GPAs and enrollments:

``` r
# This is made up data to highlight the issue
section_gpa  <- c(3.2, 2.9, 3.5)
section_size <- c(10, 40, 25)

# treats every section equally
mean(section_gpa)

# treats every student equally
weighted.mean(section_gpa, w = section_size)
```

<details>

<summary>

Output
</summary>

    [1] 3.2
    [1] 3.14

</details>

The plain `mean()` says the average GPA is 3.2, but that gives the
10-student section the same say as the 40-student section. The weighted
mean, 3.14, is the average GPA across all 75 students. Whenever you
average averages (or average across groups like states with very
different populations), ask whether each group should count equally or
in proportion to its size.

As with `mean()`, `weighted.mean()` has a `na.rm` argument. If
`na.rm = TRUE`, any value of `x` that is `NA` is dropped along with its
weight.
