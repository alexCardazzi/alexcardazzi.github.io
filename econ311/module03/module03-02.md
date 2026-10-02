# One Y Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Sampling

Now that we’ve discussed central tendency, dispersion, and probability,
we need to talk about *sampling*. As mentioned earlier, we cannot often
observe an entire population for various reasons, and we need to rely on
(hopefully random and representative) samples. So, when we sample a
population, what can we say?

1.  The sample mean ($\overline{y}$) is our **best** guess for the
    population mean ($\mu$)
2.  The sample st. dev. ($s$) is our **best** guess for the population
    st. dev. ($\sigma$)

Suppose we are interested in sampling [School Expenditures and Test
Scores for the 50
States](https://vincentarelbundock.github.io/Rdatasets/doc/stevedata/Guber99.html)
from school year 1994-95. Specifically, we are interested in average
teacher salary. However, for one reason or another, we cannot observe
all 50 states at once though we can look at a sample of 10 states.

``` r
usa <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/stevedata/Guber99.csv")
mu <- mean(usa$tsalary) # calculate "true" mean
```

``` r
usa <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/stevedata/Guber99.csv")
mu <- mean(usa$tsalary) # calculate "true" mean
```

``` r
# set.seed(757) # this is a way to "replicate" randomness.
# 10 random draws from 1:50 *with* replacement.
smpl <- sample(1:50, 10, TRUE)
mydata <- usa$tsalary[smpl]
cat(mydata, "\n")
cat("Mean of 10 Random Draws:", mean(mydata))
```

## Central Limit Theorem

At this point, you have pulled at least one combination of 10 random
states out of 50 total states. There are 62.8 *billion* different
combinations of 10 states that we can pull out of the 50, and each
sample we draw is going to be a little bit different. Well, if each
sample is different, then what can we say about the *distribution* of
these sample means?

This might seem like an odd question, but it is an important one.

Due to the **Central Limit Theorem**, we know that the distribution of
sample means is approximately normal. In other words, if we take 10,000
random samples of 10, and calculate the mean of each sample (call it
$\overline{y}_i$), the distribution of the $\overline{y}_i$s will be
normally distributed.

Moreover, the mean of the distribution of $\overline{y}_i$s has **the
same mean as the original population distribution** ($\mu$). Also, the
standard deviation of the $\overline{y}_i$s is equal to
$\frac{\sigma}{\sqrt{n}}$. We call $\frac{\sigma}{\sqrt{n}}$ the
**standard error** of the sample mean.

Below is a numerical demonstration of the Central Limit Theorem.

This code samples 10 data points from the total of 50, and then
calculates an average. However, since it is inside of a loop, the
sampling and averaging is repeated over and over. The code keeps track
of the calculated means in the vector `meanz` so we can examine the
distribution.

``` r
BIG_N <- 10000 # number of iterations
meanz <- rep(NA, BIG_N) # setting up storage for the sample means
for(i in 1:BIG_N){
  
  new_smpl <- sample(1:50, 10, TRUE)
  meanz[i] <- mean(usa$tsalary[new_smpl])
}
plot(table(round(meanz, 1)), ylab = "Frequency", las = 1)
abline(v = mu, col = "tomato") # true mean
```

The mean of this distribution should be approximately the same as the
population mean.

``` r
cat("Population Mean:", mu, "\n")
cat("Sample Distribution Mean:", mean(meanz))
```

In addition, the standard deviation of this distribution should be
approximately equal to $\frac{\sigma}{\sqrt{n}}$.

``` r
cat("Population St. Dev. divided by square root of 10:", sd(usa$tsalary)/sqrt(10), "\n")
cat("Sample Distribution St. Dev.:", sd(meanz))
```

It’s important to note that we do not know *anything* about the
distribution of teacher salaries in the data. The distribution can be
any arbitrary shape, and the CLT will still apply. In other words, it’s
true **for any distribution** that the mean of the sample means equals
the population mean, and the standard deviation of the sample means is
equal to the standard deviation of the original distribution divided by
the square root of the sample size.

What happens if we change the size of the samples? Below, the same loop
is repeated for three different sample sizes (5, 10, and 15), and the
sample means for each size are saved in one column of a matrix. Try
tweaking some of the following code in the WebR chunk (for example,
change the sample sizes or the number of samples).

``` r
BIG_N <- 10000 # number of iterations
sizes <- c(5, 10, 15) # the sample sizes to compare
meanz_by_size <- matrix(NA, nrow = BIG_N, ncol = length(sizes)) # one column per sample size
for(j in 1:length(sizes)){
  for(i in 1:BIG_N){
    
    new_smpl <- sample(1:50, sizes[j], TRUE)
    meanz_by_size[i, j] <- mean(usa$tsalary[new_smpl])
  }
}

# We mentioned "density()" in Module 2.3.
# This is a "smoothed" version of a histogram.
meanz05 <- density(meanz_by_size[, 1])
meanz10 <- density(meanz_by_size[, 2])
meanz15 <- density(meanz_by_size[, 3])

plot(0, 0, xlab = "Sample Means", ylab = "", type = "n", las = 1,
     xlim = range(meanz05$x, meanz10$x, meanz15$x),
     ylim = range(meanz05$y, meanz10$y, meanz15$y))
abline(v = mu, lwd = 2, lty = 2) # create a vertical line at "mu"
lines(meanz05$x, meanz05$y, col = "tomato", lwd = 3)
lines(meanz10$x, meanz10$y, col = "orchid", lwd = 3)
lines(meanz15$x, meanz15$y, col = "dodgerblue", lwd = 3)
legend("topright", c("N =  5", "N = 10", "N = 15"), lwd = 2,
       col = c("tomato", "orchid", "dodgerblue"), bty = "n")
```

Clearly, the distribution gets *tighter* (taller and skinnier) around
the true mean as $n$ increases. In other words, the sample average will
be more accurate as $n$ increases. **This is why large samples can be
helpful!**

### 3Blue1Brown Video on the CLT

For more information regarding the CLT, check out this video from
[3Blue1Brown](https://www.3blue1brown.com/).

<details>

<summary>

But what is the Central Limit Theorem?
</summary>

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/zeJD6dqJ5lo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

</details>

## Teacher Salaries

Now, let’s return to the initial idea – we want to calculate out the
average salary of teachers in the US. Below, I will do one last sampling
that we will use for the remainder of the notes.

``` r
set.seed(123)
smpl <- sample(1:50, 10, TRUE) # 10 random draws from 1:50 *with* replacement.
mydata <- usa$tsalary[smpl]
cat(mydata, "\n")
cat("Mean of 10 Random Draws:", mean(mydata))
```

<details>

<summary>

Output
</summary>

    28.493 31.511 36.785 32.175 32.477 31.285 31.223 38.555 36.785 31.189 
    Mean of 10 Random Draws: 33.0478

</details>

As mentioned before, we were only able to sample 10 states out of 50,
and we said that our sample average ($\overline{y}$) is our best guess
of the population average ($\mu$). However, *best* $\neq$ good. It is
not believable that $\overline{y} = \mu$, but rather
$\overline{y} \approx \mu$.

To adjust for the uncertainty associated with sampling, we need to
produce a range of values instead of just one. For example, maybe we
want to give a range like $33.0478 \pm 5$. However, this is not all that
satisfying as we’re just pulling $5$ out of thin air. To justify the
size of our range, we are going to lean on the CLT. The range we create
is going to be called a **confidence interval**.

To start, we need to come up with a middle point for our interval. Since
$\overline{y}$, or 33.0478 in this case, is our best guess, it is
natural to use it as the center.

Next, we need to come up with how wide to make our interval. The width
of our confidence interval is going to depend on two things:

1.  The standard error of the sample mean ($\frac{s}{\sqrt{n}}$).
2.  The level of confidence you select, represented by $z$.

The first part is easy. Once you gather your data, calculate the
standard deviation and divide by the square root of the sample size. In
this case, our standard deviation is 3.2033078 and sample size is 10.
Therefore, the standard error is 1.0129749.

How do we select our level of confidence, and what does it do to the
width of our interval? First, larger levels of confidence require larger
intervals. Think of it this way: if you want to be 100% confident that
$\mu$ is within your interval, your interval better be $-\infty$ to
$+\infty$! Otherwise, you *could* be wrong, in theory. However, if
you’re willing to be wrong just 1% of the time, and you lower your
confidence to 99%, you can shrink that interval a bit. So, as confidence
intervals get smaller, so does your level of confidence that $\mu$
exists within your interval.

Since sample means come from a normal distribution (thanks to the CLT),
we can use values from the *standard* normal distribution to determine
confidence levels (or probabilities). For example, we know that 95.45%
of draws will be between -2 and +2, or $\pm 2$. To get exactly 95%, we
would use $\pm 1.96$.

$z$-values for other confidence levels can be found via the following R
code: `qnorm((1 - 0.95)/2)`, where you’d replace `0.95` with `0.90`,
`0.99`, or whatever confidence level you’d like. However, 90%, 95%, and
99% are the most frequently used.

At this point, we have everything we need to create our confidence
interval! The formula is as follows:

$$\overline{y} \pm (z\times\frac{s}{\sqrt{n}})$$

For teacher salaries, our confidence interval, numerically, is:

$$\begin{aligned}33.0478 \pm 1.96\times\frac{3.2033078}{\sqrt{10}} &= 33.0478 \pm 1.9854307\\
&= [31.06, 35.03]
\end{aligned}$$

Of course, you might ask: does this work? Since we know $\mu$ (because
it is in our data), we can compare this interval with $\mu$. We
calculated $\mu$ as `mean(usa$tsalary)`: 34.82892, and this is indeed
within our interval.

We’ve now seen some of the power of the CLT – without observing the
entire dataset, we can still get precise estimates of population
parameters. The CLT is incredibly important in the field of statistics,
and basically makes the rest of this course possible.

Next, we are going to use the CLT to help us perform **hypothesis
tests**.
