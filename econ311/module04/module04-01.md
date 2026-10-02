# One X Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Economic Relationships

Thus far, we have mostly described single variables. We have examined a
variable’s distribution (central tendency, dispersion), made claims
about the population it originates from (confidence intervals), and
tested hypotheses we’ve developed. However, we are not usually
interested in single variables by themselves. More often, economists are
interested in relationships between two (or more) variables.

When two variables are in fact related, learning about one of them
reveals something about the other one. For example, consider SAT and ACT
scores. If you found out what your friend scored on the SAT, you could
probably guess their ACT score within a few points.

The point of this module is to learn how to quantify and exploit these
types of relationships.

## Economic Models

Economists often create simplified *theoretical* models of the world to
study how it works. A famous example of an economic model is the law of
demand.

> [!NOTE]
>
> ### Law of Demand
>
> The quantity demanded of a product or service is a function of, or
> determined by, its price. As price increases, quantity demand will
> decrease.[^1]

Of course, there are a lot of factors that impact the quantity demanded
of a product or service. However, creating models that account for every
little factor would be paralyzing. So, economists focus on what they
believe to be the most important factors.

What are some general characteristics of economic models?

> [!TIP]
>
> ### Explanatory
>
> Economic models identify **causal** forces most relevant to a
> decision-maker’s behavior. It then follows that these models are
> therefore **predictive**. In the case of demand, price **causes**
> quantity demanded.

> [!TIP]
>
> ### Representative
>
> Economic models are intended to characterize important determinants of
> **average** behavior, not capture every little nuance.

> [!TIP]
>
> ### Qualitative
>
> Economic models generally describe **direction** of change, but not
> necessarily **magnitude** of change. In the case of demand, increases
> in price lead to decreases in quantity demanded.

<div class="aside">

One of the main points of this course is to turn qualitative economic
models into quantitative statistical models.

</div>

When creating an economic model, one needs to consider two kinds of
variables:

> [!TIP]
>
> ### Exogenous Variables
>
> A variable that is determined *outside* of the system. In other words,
> these variables are as good as random. In the case of demand, this
> would be the price.

> [!TIP]
>
> ### Endogenous Variables
>
> A variable that is determined *inside* of the system. These variables
> can be explained by, of functions of, the exogenous variables. In the
> case, of demand, this would be quantity demanded.

As a shortcut, you can think of “endogenous” variables as $Y$ variables,
and “exogenous” variables are $X$ variables insofar that $X$ *causes*
$Y$.

Once a theory is developed, it becomes important to *test* the theory.
This is where econometrics comes in.

Before we get too far into the weeds:

- There are different names for exogenous variables. If you see any of
  the following terms, they are all referring to exogenous variables:
  “control variables”, “independent variables”, $x$-variables,
  regressors, covariates, explanatory variables.
- Generally, endogenous variables will be called $y$-variables, outcome
  variables, or dependent variables.

Before jumping to data, economists come up with mathematical expressions
to explore their theories. As a simple example, consider the law of
demand. In words, we have two conditions:

1.  Quantity demanded is determined by price.
2.  Quantity demanded decreases when price increases.

To “mathematize” the first condition, we can write $Q_d$ as a function
of $P$: $Q_d = f(P)$. The second condition implies something about
$f(P)$. Specifically, it suggests that the first derivative should be
negative. Mathematically: $\frac{\partial Q_d}{\partial P} < 0$.

There are many (infinite) functions one could choose from that would
satisfy the two important conditions. However, to reduce a model’s
complexity, economists will often “guess” particular functional forms
for $f()$. Perhaps the most simple function that relates $Q_d$ and $P$
in a way that satisfies the conditions is a **linear** relationship.
Below is an example of a linear relationship between quantity demanded
and price:

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-01-unnamed-chunk-3-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Plot of the law of demand." />

</details>

This line satisfies all of the theory of our law of demand. Here, price
and quantity demanded are linked via this function, and the function has
a negative slope.

<div class="aside">

As a note, 99% of the time, the variable that determines another is
placed along the $x$-axis. Of course, it then follows that the dependent
variable is then placed along the $y$-axis. However, Alfred Marshall
popularized the convention of price being on the $y$-axis even though
this is usually thought of as the variable that determines quantity
demanded. See [stackexchange](https://hsm.stackexchange.com/a/5260) for
a more detailed explanation if interested. This is one of the few times
you will see the $x$ variable on the $y$ axis.

</div>

It is important to note:

> “All models are wrong, but some are useful.”

All models are going to be wrong because they cannot account for every
possible scenario. For example: 2020’s pandemic, 2008’s financial
crisis, and 2000’s dot com bubble are all examples of generally
unforeseeable circumstances that escaped economic models. However, some
models provide valuable insight into how the world operates. We will use
math and statistics to help us decides which are at least useful.

Let’s suppose my theory is that students with higher GPAs in high school
will score higher on their SAT. Now, we need a way to test this theory.

Let’s pick out our exogenous and endogenous variables. I contend that
SAT scores are a function of, or are determined by, high school GPA. In
reality, **both** of these outcomes are likely to be determined by
things like natural ability, study habits, family income (i.e.,
tutoring, etc.), educational environment, etc. However, for now, let’s
think about GPA as a proxy for all of these things, and SAT scores
follow as a direct result of GPAs.

Another (and more realistic) way to think about this model is as a tool
for guidance counselors to help their students understand what they can
expect for their SAT scores given their GPA.

In this dataset, we observe SAT and GPA data for 1000 students
([data](https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv),
[documentation](https://vincentarelbundock.github.io/Rdatasets/doc/openintro/satgpa.html))
to examine the potential relationship.

As a first step, we should read in the data and take a look at it.

<div class="aside">

Note that the `sat_sum` variable is a sum of the students Math and
Verbal percentiles. Therefore, the best student in the world would be
100<sup>th</sup> percentile for both, meaning `sat_sum` would be 200. A
median student in each would have a value of 50 and 50, making `sat_sum`
equal to 100.

</div>

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
```

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/openintro/satgpa.csv")
head(df)
```

Next, let’s visualize the relationship between `sat_sum` and `hs_gpa`
using R’s `plot()` function.

``` r
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     xlab = "HS GPA", ylab = "SAT Percentile Sum")
```

## An Aside

Indeed, this plot is pretty messy… The points are covering each other,
everything is one solid color, and there’s hardly a discernible pattern.
A first step you can take is to make the points more transparent to
identify density.

To do this, we are going to use the `scales` package. As we saw in
Module 1.6, you install a package once (`install.packages("scales")`)
and load it with `library("scales")` every time you use it. `scales`
contains a function `alpha()` that accepts vectors of colors and
opacities as arguments. Try playing around with colors and opacities
below.

``` r
library("scales")
plot(df$hs_gpa, df$sat_sum, las = 1, pch = 19,
     col = alpha("tomato", .2), # .2 is 20%
     xlab = "HS GPA", ylab = "SAT Percentile Sum")
```

<div class="aside">

You do not need to use `install.packages("scales")` in this WebR chunk,
but you do need to use it on your personal machine. Remember: you only
need to *install* the package **once**!

</div>

This is a good first step, as our plots now show a bit of that density I
mentioned. However, there are still so many points (1,000 to be exact)
that the overall relationship might be a bit obscured.

Notice how there are many points in these vertical lines. This is
because GPA is a relatively discrete measure. In other words, there are
35 unique values[^2] for 1000 observations. We can take advantage of
this by collapsing the data down into these 35 points. To do this, we
are going to calculate an average SAT for each unique GPA value.

One way we can do this is with a loop!

First, we’ll extract the unique GPA values. Next, we’ll create an empty
vector where we will put our SAT averages for each GPA value. Finally,
we’ll calculate the mean and store it.

``` r
# gather unique GPAs
gpaz <- unique(df$hs_gpa)
# Create empty vector
satz <- rep(NA, length(gpaz))
for(gpa in gpaz){
  
  # Take the average of the subsetted SATs.
  temp <- mean(df$sat_sum[df$hs_gpa == gpa])
  # Store "temp" into satz where gpaz == gpa
  satz[gpaz == gpa] <- temp
}
# Try making the new plot:
plot()
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
# gather unique GPAs
gpaz <- unique(df$hs_gpa)
# Create empty vector
satz <- rep(NA, length(gpaz))
for(gpa in gpaz){
  
  # Take the average of the subsetted SATs.
  temp <- mean(df$sat_sum[df$hs_gpa == gpa])
  # Store "temp" into satz where gpaz == gpa
  satz[gpaz == gpa] <- temp
}
plot(gpaz, satz, las = 1, pch = 19,
     xlab = "HS GPA", ylab = "SAT Percentile Sum")
```

<details>

<summary>

Plot
</summary>

<img src="module04_img/04-01-unnamed-chunk-10-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="SAT Percentile Sum vs High School GPA" />

</details>

</details>

With this plot, one can start to pick out the relationship between GPA
and SAT even if only visually. However, having to write a loop to
generate these data is unnecessarily difficult. Luckily, R has a
function called `aggregate()` that will do exactly what we wanted, but
with much less typing.

`aggregate()` accepts a few main arguments:

- `x`: The data you want to aggregate/collapse. In our case, `sat_sum`.
- `by`: The level you want to aggregate/collapse to. In our case,
  `hs_gpa`.
- `FUN`: The function to be applied to `x` for each unique `by`. In our
  case, `mean()`.
  <!-- - `drop`: Should observations with unused combinations be dropped? We will revisit this momentarily. -->

Below is an example of how to use aggregate for the *exact* reason we
used the previous loop.

``` r
# If you enter elements as lists, you can name them.
# These names will be passed to the data.frame output.
#         x is a list of what to collapse
aggregate(x = list(sat = df$sat_sum),
#         by is a list of what to collapse by
          by = list(gpa = df$hs_gpa),
#         FUN is the function to use
          FUN = mean) -> agg_grades
head(agg_grades) # note: the output is a data.frame!
plot(agg_grades$gpa, agg_grades$sat, las = 1, pch = 19,
     xlab = "HS GPA", ylab = "SAT Percentile Sum")
```

What makes `aggregate()` special is the ability to aggregate multiple
variables *and/or* by multiple variables. As an example, let’s find the
average `sat_sum`, `sat_m`, and `sat_v` by both `hs_gpa` and `sex`.

<div class="aside">

I am going to make an assumption about the `sex` variable. If `sex` is
`1`, it denotes a female.

</div>

<!-- We can also aggregate by multiple variables. The `aggregate()` function is something you will use all the time to either help you create data or simply summarize it. -->

``` r
aggregate(x = list(sat = df$sat_sum, # collapsing multiple variables
                   math = df$sat_m,
                   verbal = df$sat_v),
          by = list(gpa = df$hs_gpa, # collapsing by multiple variables
                    female = ifelse(df$sex == 1, 1, 0)),
          FUN = mean) -> agg_grades
head(agg_grades)
plot(agg_grades$gpa, agg_grades$sat, las = 1, pch = 19,
     col = alpha(ifelse(agg_grades$female == 1, "tomato", "dodgerblue"), .7),
     xlab = "HS GPA", ylab = "SAT Percentile Sum", cex = 1.5)
legend("bottomright", c("Female", "Male"), pch = 19,
       col = c("tomato", "dodgerblue"), bty = "n", cex = 1.5)
```

With `aggregate()`, you can also use other functions like `length()` to
calculate the number of observations that fall within each bucket. Or,
you can use `sd()` to calculate standard deviations. If we calculate
`mean()`, `length()`, and `sd()`, we can create confidence intervals.
The following code will create confidence intervals for each GPA.

<!-- ```{r, fig.alt="SAT Percentile Sum vs High School GPA plus Confidence Intervals"} -->

``` r
meanz <- aggregate(x = list(sat = df$sat_sum),
                   by = list(gpa = df$hs_gpa),
                   FUN = mean)
sdz <- aggregate(x = list(sat = df$sat_sum),
                 by = list(gpa = df$hs_gpa),
                 FUN = sd)
nz <- aggregate(x = list(sat = df$sat_sum),
                by = list(gpa = df$hs_gpa),
                FUN = length)

# Note, we cannot calculate a standard deviation when n < 2
# We'll have to drop observations where this is the case.
meanz <- meanz[nz$sat > 1,]
sdz <- sdz[nz$sat > 1,]
nz <- nz[nz$sat > 1,]

ci_upper <- meanz$sat + (1.96 * sdz$sat / sqrt(nz$sat))
ci_lower <- meanz$sat - (1.96 * sdz$sat / sqrt(nz$sat))
plot(meanz$gpa, meanz$sat, las = 1, pch = 19,
     xlab = "HS GPA", ylab = "SAT Percentile Sum",
     ylim = range(ci_upper, ci_lower))
# segments() is a new function for you, so take a look at what it does.
segments(x0 = meanz$gpa, y0 = ci_lower, y1 = ci_upper)
```

[^1]: This is not to say that increases in quantity demanded can
    influence price.

[^2]: I calculated this via: `length(unique(df$hs_gpa))`
