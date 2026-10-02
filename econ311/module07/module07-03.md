# Fixed Effects and the Limits of Regression
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Dates in `R`

As one last R tool for this course, we will discuss an important package
that does not take much time to learn. Often times, the data we are
working with has some time dimension (e.g., yearly, monthly, quarterly,
daily, etc.) to it, so learning how to work with dates is helpful. The
`lubridate` package in R is outfitted for this exact purpose, so we are
going to learn our way around some of its functions.

<div class="aside">

Yeah, the name *is* hilarious. Mount Rushmore of package names.

</div>

## `lubridate`

Let’s go through some of the important functions / capabilities in
`lubridate`.

**Note**: There is a great
[cheatsheet](https://alexcardazzi.github.io/econ311/data/R_lubridate.pdf)
available online.

`ymd()`, `dmy()`, `mdy()`: when you read data into R and one of the
columns has entries like `"January 2 2014"`, we can use these functions
to help us out. Note that `y` stands for year, `d` stands for day, and
`m` stands for month, so just choose the function that makes sense for
your situation.

``` r
library("lubridate")

mdy("January 2 2014")
ymd("2015/08/10")
dmy("29-10-1978")
ymd("2015-02-30")
```

Did the last one give you a warning? February 30th does not exist, so
`lubridate` returns `NA` instead of a date.

Output from these functions *appear* in year-month-day format, but are
actually numeric under the hood. We can see this by executing
`as.numeric(mdy("January 2 2014"))`: 16072. This is the number of days
since January 1, 1970.

Once your data is converted to well-behaved date objects, we can begin
to manipulate them. We can extract the year, month, day, etc. using
lubridate’s helpfully named `year()`, `month()`, and `day()` functions.
We can also grab the weekday by using `wday(df$date, label = TRUE)`.

Next, we can use `floor_date(df$date, "quarter")` to “round” our dates
*down* to the first day of the quarter, month, year, whatever.

Finally, we can calculate differences in dates by using simple
subtraction. However, often times we want to know the number of, say,
months between two dates. For this, we can use the following code:
`interval(date1, date2) %/% months(1)`. For example, the number of whole
months from January 2, 2014 to March 15, 2015 is 14.
