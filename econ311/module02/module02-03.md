# One Y Variable
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Distributions

So far, we have characterized certain “moments” of data (i.e., mean and
variance).

While these two numbers are helpful in understanding the data, they do
not tell the full story. As an example, let’s consider the following
data:

``` r
df <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/datasets/anscombe.csv")
cat("Summary Statistics for x1. Mean:", mean(df$x1), "; Variance:", var(df$x1), "\n")
cat("Summary Statistics for x4. Mean:", mean(df$x4), "; Variance:", var(df$x4))
```

<details>

<summary>

Output
</summary>

    Summary Statistics for x1. Mean: 9 ; Variance: 11 
    Summary Statistics for x4. Mean: 9 ; Variance: 11

</details>

Both of these columns have the same average and variance (and thus
standard deviation), so you might think that they must in fact look like
similar. Let’s take a look at the data and see for ourselves:

``` r
df[,c("x1", "x4")]
```

<details>

<summary>

Output
</summary>

       x1 x4
    1  10  8
    2   8  8
    3  13  8
    4   9  8
    5  11  8
    6  14  8
    7   6  8
    8   4 19
    9  12  8
    10  7  8
    11  5  8

</details>

Clearly, even though those data have the same mean and variance, they
have very different *distributions*. The first column had many values
ranging from 4 to 14, but the second column had one 19 and the rest 8s.
Note that this is the inverse of the case of Michael Jordan and UNC.
There, the distributions were presumably very similar with the exception
of Michael Jordan’s single outlier salary.

## Visualizing Distributions

How do we look at the distribution of a variable in our data? We know
how to calculate the mean and variance of a variable, but it’s helpful
to visualize the whole distribution.

If your data is sufficiently discrete, I prefer to use `table()`. Of
course, “sufficiently discrete” is difficult to define. If your data has
many unique values, you may need to transform the data via rounding
before using `table()`. Otherwise, you can use `hist()` to generate a
histogram (which is effectively what `table()` is doing) or `density()`
to create a kernel density plot. You can think of kernel density plots
as smoothed out histograms.

Let’s take a look at some [real estate transaction
data](https://vincentarelbundock.github.io/Rdatasets/doc/AER/HousePrices.html)
as an example.

``` r
# https://vincentarelbundock.github.io/Rdatasets/doc/AER/HousePrices.html
data_url <- "https://vincentarelbundock.github.io/Rdatasets/csv/AER/HousePrices.csv"
df <- read.csv(data_url)
# Keep only columns 2 through 6.
# These columns are the ones that contain numeric information.
# What this step below is doing is sub-setting the columns,
#   then saving over the original.
df <- df[,2:6]
head(df)
```

<details>

<summary>

Output
</summary>

      price lotsize bedrooms bathrooms stories
    1 42000    5850        3         1       2
    2 38500    4000        2         1       1
    3 49500    3060        3         1       1
    4 60500    6650        3         1       2
    5 61000    6360        2         1       1
    6 66000    4160        3         1       1

</details>

Remember the function `table()`? This function returned the number of
times each unique value appeared in the data. This is, definitionally, a
distribution! Let’s try combining `table()` and `plot()`, starting with
bedrooms:

``` r
plot(table(df$bedrooms),
     xlab = "Number of Bedrooms",
     ylab = "Frequency")
```

<details>

<summary>

Plot
</summary>

<img src="module02-03_md_files/figure-commonmark/unnamed-chunk-5-1.svg"
style="width:90.0%" data-fig-align="center"
data-fig-alt="Distribution of number of bedrooms in housing transaction data." />

</details>

From this, we can see that the distribution is somewhat symmetric with
the most density at 3. For bedrooms in a home, this seems reasonable.
Also note that there are no decimal values, because we cannot have 3.5
bedrooms (though you can have 3.5 bathrooms!).

Next, let’s try to plot the distribution of sale price.

``` r
plot(table(df$price), xlab = "Sale Price", ylab = "Frequency")
```

<details>

<summary>

Plot
</summary>

<img src="module02-03_md_files/figure-commonmark/unnamed-chunk-6-1.svg"
style="width:90.0%" data-fig-align="center"
data-fig-alt="Distribution of sale price in housing transaction data." />

</details>

This is not a very informative plot as there’s no discernible pattern.
Rather, let’s divide by 10,000, round the data, and re-multiply by
10,000.

``` r
plot(table(round(df$price/10000)*10000), xlab = "Sale Price", ylab = "Frequency")
```

<details>

<summary>

Plot
</summary>

<img src="module02-03_md_files/figure-commonmark/unnamed-chunk-7-1.svg"
style="width:90.0%" data-fig-align="center"
data-fig-alt="Distribution of sale price in housing transaction data." />

</details>

This is a much better plot. We can see that were are some outliers that
are over 150,000 dollars, and the modal sale is about 60,000 dollars.

Finally, let’s say we want a way to calculate multiple summary
statistics (e.g. mean, median, minimum, maximum) for multiple variables
at once. Luckily for us, we can use R’s `summary()` function to help us.

``` r
summary(df)
```

<details>

<summary>

Output
</summary>

         price           lotsize         bedrooms       bathrooms    
     Min.   : 25000   Min.   : 1650   Min.   :1.000   Min.   :1.000  
     1st Qu.: 49125   1st Qu.: 3600   1st Qu.:2.000   1st Qu.:1.000  
     Median : 62000   Median : 4600   Median :3.000   Median :1.000  
     Mean   : 68122   Mean   : 5150   Mean   :2.965   Mean   :1.286  
     3rd Qu.: 82000   3rd Qu.: 6360   3rd Qu.:3.000   3rd Qu.:2.000  
     Max.   :190000   Max.   :16200   Max.   :6.000   Max.   :4.000  
        stories     
     Min.   :1.000  
     1st Qu.:1.000  
     Median :2.000  
     Mean   :1.808  
     3rd Qu.:2.000  
     Max.   :4.000  

</details>

However, `summary()` isn’t our best option for creating a nice and neat
table. We are going to use an R package for this task. As we saw in
Module 1.6, you only need to **install the package** once, but you need
to **call the package** with `library()` every time you use it.
Installing/loading packages is something students do incorrectly *all*
the time. Take note of this!

Below, I am installing the `modelsummary` package (**which only needs to
be done once**!) and then loading it into our session (**which needs to
be done every time you want to use it!**). You can find [the
documentation for `modelsummary`
here](https://vincentarelbundock.github.io/modelsummary/index.html).

``` r
# only use install.packages() once per package
install.packages("modelsummary")
# use this every time you want to use 'modelsummary'
library("modelsummary")
# add this line when loading 'modelsummary', too
# note that you may need to install 'kableExtra' as well
install.packages("kableExtra")
options("modelsummary_factory_default" = "kableExtra")
```

This package contains a few different functions, but we are going to
focus on two: `datasummary()` and `datasummary_skim()`. You can find
[the documentation for `datasummary`
here](https://vincentarelbundock.github.io/modelsummary/articles/datasummary.html).
To start, let’s see the output of `datasummary_skim()`.

``` r
# Other arguements you might want to use:
#   type = "numeric"
#   fmt = fmt_significant(2)
datasummary_skim(df)
```

<details><summary>Output</summary>
&#10;
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
|           | Unique | Missing Pct. | Mean    | SD      | Min     | Median  | Max      | Histogram                                                                                                                              |
+===========+========+==============+=========+=========+=========+=========+==========+========================================================================================================================================+
| price     | 219    | 0            | 68121.6 | 26702.7 | 25000.0 | 62000.0 | 190000.0 | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module02\tinytable_assets\tinytable_5_idw931znngd50c2eeu1ixs.png){ height=16 } |
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| lotsize   | 284    | 0            | 5150.3  | 2168.2  | 1650.0  | 4600.0  | 16200.0  | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module02\tinytable_assets\tinytable_4_idsbfqvdczi71ulrwiifyt.png){ height=16 } |
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| bedrooms  | 6      | 0            | 3.0     | 0.7     | 1.0     | 3.0     | 6.0      | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module02\tinytable_assets\tinytable_3_id746q8w3w6ob636fb338j.png){ height=16 } |
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| bathrooms | 4      | 0            | 1.3     | 0.5     | 1.0     | 1.0     | 4.0      | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module02\tinytable_assets\tinytable_1_idr5648ova923uyi21n58b.png){ height=16 } |
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
| stories   | 4      | 0            | 1.8     | 0.9     | 1.0     | 2.0     | 4.0      | ![](C:\Users\alexc\Dropbox\teaching\Spring 2027\econ311\module02\tinytable_assets\tinytable_2_idevdtf4ld2flyd5n76f79.png){ height=16 } |
+-----------+--------+--------------+---------+---------+---------+---------+----------+----------------------------------------------------------------------------------------------------------------------------------------+
&#10;</details>

While this very quickly provides us with a lot of useful information, it
lacks customization. On the other hand, `datasummary()` provides us with
much more versatility though requires a bit more effort. To start, take
a look at the below example. We are summarizing `price`, `lotsize`, and
`bedrooms` from the dataset `df`. For each variable, we are going to
calculate `length` (sample size), `mean`, `sd`, `min`, and `max`.

``` r
datasummary(price + lotsize + bedrooms ~ length + mean + sd + min + max,
            data = df, fmt = fmt_significant(2))
```

<details><summary>Output</summary>
&#10;
+----------+--------+-------+-------+-------+--------+
|          | length | mean  | sd    | min   | max    |
+==========+========+=======+=======+=======+========+
| price    | 546    | 68122 | 26703 | 25000 | 190000 |
+----------+--------+-------+-------+-------+--------+
| lotsize  | 546    | 5150  | 2168  | 1650  | 16200  |
+----------+--------+-------+-------+-------+--------+
| bedrooms | 546    | 3     | 0.74  | 1     | 6      |
+----------+--------+-------+-------+-------+--------+
&#10;</details>

To further complicate this, we can include many other options. However,
for simplicity, we are going to focus on the most important ones. First,
we are going give the table a title. Second, we are going to rename the
variables. Lastly, we are going to format some of the numbers. Take a
look:

``` r
title <- "This is the Table's Caption/Title"
frmla <- (`Price` = price) + (`Lot Size` = lotsize) + (`Bedrooms` = bedrooms) ~
  (`N` = length) + Mean + (`St. Dev.` = sd) + (Min = min) + (Max = max)
datasummary(frmla, data = df, title = title,
            fmt = fmt_significant(2))
```

<details><summary>Output</summary>
&#10;
+----------+-----+-------+----------+-------+--------+
|          | N   | Mean  | St. Dev. | Min   | Max    |
+==========+=====+=======+==========+=======+========+
| Price    | 546 | 68122 | 26703    | 25000 | 190000 |
+----------+-----+-------+----------+-------+--------+
| Lot Size | 546 | 5150  | 2168     | 1650  | 16200  |
+----------+-----+-------+----------+-------+--------+
| Bedrooms | 546 | 3     | 0.74     | 1     | 6      |
+----------+-----+-------+----------+-------+--------+
&#10;Table: This is the Table's Caption/Title
&#10;</details>
