# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## More R

By now, you can probably use R like a sophisticated calculator – adding
and subtracting single numbers, doing logical operations, etc.

> Calling this thing a ‘phone’ is like calling a Lamborghini a
> cupholder. An incredibly elaborate cupholder.
>
> — <span class="blockquote-author">Gary Gulman, referencing an
> iPhone</span>

However, there are a lot of features that make R the Lamborghini of
calculators.

## Variables

In R, you can assign names to values (remember, *objects*). You do this
by using either `<-` or `=`. Online, when Googling, you may find
solutions with both. Despite what you might read, there *are*
differences between the two, but we can ignore those differences for
right now.

Why would you want to assign names to values? This allows your code to
be much more flexible. Consider the following example.

<div class="columns">

<div class="column" width="50%">

``` r
5 + 3
5 / 10
as.character(5)
```

<details>

<summary>

Output
</summary>

    [1] 8
    [1] 0.5
    [1] "5"

</details>

</div>

<div class="column" width="50%">

``` r
x <- 5
x + 3
x / 10
as.character(x)
```

<details>

<summary>

Output
</summary>

    [1] 8
    [1] 0.5
    [1] "5"

</details>

</div>

</div>

Naming `5` as `x` allows us to change `x` only once, and the entire code
will run. This will 1) reduce our effort 2) decrease typos / bugs and 3)
increase readability. Of course, using a variable name such as `x` is
not necessarily informative, but this is just an example.

The following may seem like a simple point, but it is very important. If
you manipulate a variable in any way, but do not re-assign it to a name
(same or different), it does not get updated/saved. Consider the
following example.

<div class="columns">

<div class="column" width="33%">

``` r
x <- 10
x + 5

x
```

<details>

<summary>

Output
</summary>

    [1] 15
    [1] 10

</details>

</div>

<div class="column" width="33%">

``` r
x <- 10
x <- x + 5

x
```

<details>

<summary>

Output
</summary>

    [1] 15

</details>

</div>

<div class="column" width="33%">

``` r
x <- 10
y <- x + 5
x
y
```

<details>

<summary>

Output
</summary>

    [1] 10
    [1] 15

</details>

</div>

</div>

Column 1 computes `x + 5` and prints the result (`15`), but nothing is
assigned. So the last line, `x`, still prints `10`. The calculation
happened, and then evaporated.

Column 2 reassigns the result back to `x` itself: `x <- x + 5`. This
overwrites the old value. Now `x` is `15`. This is the standard way to
“update” a variable in R: you are not editing `x` in place, you are
creating a new value and giving it the same name.

Column 3 assigns the result to a new name, `y`, leaving `x` untouched.
So `x` is still `10`, and `y` is `15`. Both objects now exist side by
side.

The point of putting these three side by side: R never modifies an
object as a side effect of a computation. Something only changes, or
comes into existence, when you use `<-` to assign it a name. Same name
overwrites; new name creates something new.

### Calculate Your Age

In the following WebR chunk, practice by calculating your age in months.
Assign `birth` a value equal to your birth year times twelve plus the
number corresponding to your birth month (e.g., `(2004*12) + 4` for
April 2004). Next, assign `now` the current year times twelve plus the
number corresponding to this month. Finally, subtract these two numbers,
assign the result to `age_in_months`, and print `age_in_months`.

<div class="fragment">

``` r
birth <- 
now <-
age_in_months <- now - birth
print(age_in_months)
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
# Suppose it is January 2027 and you were born in April 2004.
birth <- (2004*12) + 4
now <- (2027*12) + 1
age_in_months <- now - birth
print(age_in_months)
```

<details>

<summary>

Output
</summary>

    [1] 273

</details>

</details>

</div>

<span class="fragment">Now, if you wanted, you could modify this code to
calculate ages in months for your friends and family by simply changing
the value of `birth`! This should hopefully highlight the advantages of
using variables instead of numeric values whenever possible.</span>

## Naming Variables

It is important to choose informative names for your variables.
Generally, single (or few) character names are easy to type, but can
easily lose meaning. Too-long names aren’t great if you need to type
them over and over. You will figure out a sweet spot for yourself.

There are some names you cannot use for your variable names, and other
names that you simply shouldn’t. For example, you cannot start a
variable name with a number (e.g., `5ever <- 5` will throw an error).
You cannot start names with certain punctuation either. On the other
hand, you should not name things after already-used words that are
native to R. This will just lead to confusing code. For example, do not
name anything `mean`, because that is already a function name that is
native to R.

Learning what is and what is not a good variable name takes time and
practice.

## Collections

So far, we have only worked with single values. Data tends to come in
sets of multiple values, like large spreadsheets with columns and rows.
Let’s built up to the R version of “spreadsheets”, which are called
`data.frame`s. We will touch on each of the following ways to store
multiple values:

- Vectors
- Matrices
- Lists
- `data.frame`

### Vectors

In R, the definition of a vector is a collection of values that are all
of the same type. We use a `c()` to denote vectors. The `c` stands for
*combine*. Once we have our vector, we apply different operations to it
like we did before. For example, we know how to add two values, but what
about a vector and a single value? Or two vectors?

``` r
vec1 <- c(1, 3, 5) # a vector of three elements
vec2 <- c(2, 4, 6)
vec1 + 10 # add a vector and a scaler
vec1 + vec2 # add two vectors
```

Notice how when we added `10` to `vec1`, `10` was added to each element
of `vec1`. However, when we added the two vectors, addition was
element-wise. If two vectors are of different lengths, R will “recycle”
the shorter one to match the longer one.

``` r
vec1 <- c(1, 2, 3)
vec2 <- c(1, 2, 3, 4)
vec1 + vec2
```

<details>

<summary>

Explanation
</summary>

The results of this are `1 + 1`, `2 + 2`, `3 + 3`, and `1 + 4`. Notice
how the first vector has to loop back around to the beginning to match
the length of the second vector. This is effectively just adding
`c(1, 2, 3, 1)` and `c(1, 2, 3, 4)`. Also, again note the warning
generated by R. It’s not often that you will add two vectors of unequal
length, so this should be a flag to you that maybe there’s an issue.
</details>

As a quick aside, R has good help functionality. To access this, you
need to put a `?` in front of whatever you want help with. For example,
suppose you need help with the `mean` function from before.

``` r
?mean
```

Running this line (as a reminder: `ctrl` + `enter` in RStudio) will
bring you to <a
href="https://stat.ethz.ch/R-manual/R-devel/library/base/html/mean.html"
data-preview-link="true">the function’s documentation</a>.

Here, `mean` becomes: `mean(x, trim = 0, na.rm = FALSE, ...)`

- `x`, `trim`, and `na.rm` are the function’s **arguments**. These are
  *inputs*, and the function gives you an *output*.
  - `x` is the vector, `x <- c(1, 4, 8, 7, 2)`, you want the mean of.
  - `trim` is the fraction of observations (elements in the vector) to
    be removed before taking the mean. You might want to remove the top
    and bottom 5% of observations since they might be outliers.
  - `na.rm` is a boolean that will remove `NA` values for you.

R also has different ways (functions) to generate vectors. Explore some
of them below:

``` r
1:4 # This outputs every integer between 1 and 10

rep(1:4, times = 2) # Repeat 1-4 twice
# Function arguments are ordered, so it still works
# even without the "times ="
rep(1:4, 2)
seq(1, 4, by = .5) # Sequence from 1-4 by increments of 0.5

# 4 numbers drawn from a normal distribution
# with mean 0 and sd 1
rnorm(4, mean = 0, sd = 1)
```

What happens if you have a vector of elements that are of different
types?

``` r
c("1", "2", 3)
```

<span class="fragment">Experiment with the chunk above. Does the
resulting vector change depending on the number of character values vs
numeric values?</span> <span class="fragment">Does the resulting vector
change depending on the first entry of the vector?</span>

Let’s suppose you only want a part of a vector. You can select elements
from vectors by *index* (position within the vector) or by boolean
values. You do this by typing the vectors name, followed by a square
bracket, followed by another vector that containing indices or boolean
values. Experiment with the following examples.

``` r
this_vec <- c(0, 8, 3, 6, 1, 2, 2, 7, 6)

# Select the 3rd, 4th, and 5th observations
this_vec[c(3, 4, 5)]

# Select every other observation
# Note: there are 9 elements, by R cycles through c(TRUE, FALSE)
# until it gets through all 9.
this_vec[c(TRUE, FALSE)]

# Select observations less than 5.
this_vec[this_vec < 5]

# Select observations less than 5 or greater than 7
this_vec[this_vec < 5 | this_vec > 7]

# Select observations less than 5 and greater than 7
# Notice the output here since the logic is impossible
# Something cannot be less than 5 AND greater than 7
this_vec[this_vec < 5 & this_vec > 7]
```

Another important way to subset vectors is with the `%in%` operator.
Suppose you have a vector of years as follows:
`c(2006, 2006, 2003, 2005, 2012, 2002, 2016, 2006, 2008)`. If you were
to subset the vector where you only kept elements where years were equal
to 2006, 2007, or 2008, you would have to write the following:

``` r
v <- c(2006, 2006, 2003, 2005, 2012, 2002, 2016, 2006, 2008)
v[v == 2006 | v == 2007 | v == 2008]
```

<div class="fragment">

This can be very tedious, is prone to error/typo, and infeasible if the
list were much longer (i.e., not just three years). As a shortcut, R has
the following:

``` r
v <- c(2006, 2006, 2003, 2005, 2012, 2002, 2016, 2006, 2008)
v[v %in% c(2006, 2007, 2008)]

# You could also do the following:
# want <- c(2006, 2007, 2008)
# v[v %in% want]
```

</div>

### Matrices

A collection of vectors (of similar *type* and *length*) is called a
matrix. Matrices have two dimensions: rows and columns. Matrices look
like: `example_mat[rows,cols]`. To create matrices from vectors, you can
use `rbind()` (to stack row-wise) or `cbind()` (column-wise). Let’s
start by assuming you have a few vectors to work with.

PS: do not be concerned when you see me using `; cat("\n")` below. This
is simply to break up the output so things are easier to see. This is
purely for aesthetics and should be ignored.

``` r
v1 <- rnorm(4) # 4 random numbers
v2 <- 1:4 # 1 - 4
v3 <- 9:6 # 9 - 6
cbind(v1, v2, v3); cat("\n") # column-wise
rbind(v1, v2, v3) # row-wise
```

Another way to generate matrices would be to put one giant vector into
the `matrix` function. Of course, you will need to give `matrix()` a bit
of help. You need to tell it *something* about the dimensions you’d
like. This could be `ncol` for number of columns or `nrow` for number of
rows. In addition, you should specify whether the vector is “by row” or
not (i.e. “by column”).

If `r1` denotes an element belonging on the first row, etc.:  
A “by row” vector would be `c(r1, r1, r1, r2, r2, r2, r3, r3, r3)`

A “by column” vector would be `c(r1, r2, r3, r1, r2, r3, r1, r2, r3)`

<div class="fragment">

``` r
v_mat <- c(v1, v2, v3) # combine the three vectors
matrix(v_mat, ncol = 3); cat("\n")
matrix(v_mat, nrow = 3); cat("\n")
matrix(v_mat, nrow = 3, byrow = TRUE)
```

</div>

Once you have the matrix of your dreams, you may need to access certain
columns or rows. Remember: `example_mat[rows, columns]`. For vectors, if
you want the first element, you would use `example_vec[1]`. For a
matrix, `example_mat[1,]` will give you the first row, `example_mat[,1]`
will give you the first column, and `example_mat[i,j]` will give you the
i$^{th}$ row and j$^{th}$ column. To select multiple rows, you can use
logic or indices, much like vectors. Explore the code below:

``` r
matrix(v_mat, ncol = 3) -> mat
mat; cat("\n") # whole matrix
mat[1,]; cat("\n") # first row
mat[,2]; cat("\n") # second column
mat[1,2]; cat("\n") # first row, second column
mat[c(1, 3),]; cat("\n") # first and third row
# Select all rows where the elements in the first column are positive.
# mat[,1] > 0 will return boolean values, and mat[,] will return the rows with TRUE values
mat[ mat[,1] > 0 ,]
```

### Lists

Lists are similar to vectors in that they allow for the collection of
elements. However, with lists, each element can be of a different type.
In fact, each element of a list can be an entire vector! Lists, for this
reason, are incredibly flexible. In fact, this flexibility can actually
make it difficult to work with lists. Try exploring the lists below.

``` r
list(1, 2, "3"); cat("\n")
# list(1, 2, c("3", "4", "5")); cat("\n")
# list(1, 2, list(3, 4, c("5", "6")))
```

An interesting feature of lists, is that you can name the elements
within the list. This is possible with vectors as well, but not as
useful. Here are some examples of naming and using the names within
lists.

<div class="fragment">

``` r
list(person1 = c("alex", "cardazzi"),
     person2 = c("jalen", "brunson"),
     person3 = c("thom", "yorke")) -> list_people
list_people[1]   # single square bracket...
list_people[[1]] # double square bracket...
list_people$person1 # or, you can use $ for double square bracket
```

</div>

Changing the format of the list a little bit:

``` r
list(first = c("alex", "jalen", "thom"),
     last = c("cardazzi", "brunson", "yorke")) -> namez
namez$first
namez$last[2:3]
```

<span class="fragment">This is a special list because both vectors of
the list have the same number of elements, or observations. When this
happens, we have something called a `data.frame`. Really, this is just
how `R` represents spreadsheets – a collection of columns all with the
same number of rows!</span>

### `data.frame`

So, what do `data.frame`’s look like?

``` r
list(first = c("alex", "jalen", "thom"),
     last = c("cardazzi", "brunson", "yorke")) -> namez
as.data.frame(namez) -> namez_df
namez_df
```

Observations can be accessed in `data.frame`s via the `$` or `[`. These
objects combine lists and matrices to make a more realistic view of the
types of data that are most common in the real world.

``` r
data.frame(first = c("alex", "jalen", "thom"),
           last = c("cardazzi", "brunson", "yorke"),
           num_of_albums = c(0, 0, 10),
           nba_seasons = c(0, 5, 0),
           phds = c(1, 0, 0),
           birth_country = c("us", "us", "uk")) -> df
df
```

<details>

<summary>

Output
</summary>

      first     last num_of_albums nba_seasons phds birth_country
    1  alex cardazzi             0           0    1            us
    2 jalen  brunson             0           5    0            us
    3  thom    yorke            10           0    0            uk

</details>

Suppose you want to subset the `df` object that you’ve created. Again,
there are different ways to do this. Like matrices, to get some rows and
all columns, you would use `df[lim,]` where `lim` is a vector of boolean
values or indices. Leaving nothing following the comma indicates to R
that you want everything in that dimension. To get columns, you can
reverse this (`df[,3:4]`) or use names
(`df[,c("nba_seasons", "phds")]`). If you only want a single column, of
course, you can use `df$phds`.

<span class="fragment">**Please re-read this last part.**</span>
<span class="fragment">Subsetting data is one of the most important and
most used operations you will learn throughout this course. If I had a
dollar every time a student asked me to remind them how to do this, I
would be able to retire tomorrow.</span>

As a final note about `data.frame`s, here are a few important functions:

- `nrow()`: Returns the number of rows in a `data.frame`.
- `ncol()`: Returns the number of columns in a `data.frame`.
- `colnames()`: Returns the names of columns in a `data.frame`.
