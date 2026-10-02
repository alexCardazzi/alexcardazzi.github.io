# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Loops

Next, we will introduce how to write *loops* in R.

Suppose we are interested in executing the same code over and over and
over. As an example, suppose I wanted to write some code to print every
student name and grade. Of course, I *could* write the following:

``` r
cat("Name:", "Alex", "... Grade:", "B", "\n")
cat("Name:", "Brooke", "... Grade:", "A", "\n")
cat("Name:", "Carlos", "... Grade:", "A", "\n")
cat("Name:", "Dasia", "... Grade:", "B", "\n")
cat("Name:", "Enzo", "... Grade:", "C")
```

<details>

<summary>

Output
</summary>

    Name: Alex ... Grade: B 
    Name: Brooke ... Grade: A 
    Name: Carlos ... Grade: A 
    Name: Dasia ... Grade: B 
    Name: Enzo ... Grade: C

</details>

This code works, but there are some problems. First, writing this out so
many times makes it prone to typos, even if just copying and pasting.
Second, *almost* anything that is repetitive or has an identifiable
pattern is easier for a computer than for a human. Lastly, what if there
were 100 names instead of 5? What if 10,000? This is where loops come
in.

Our first step is going to be creating vectors of names and grades.
Ideally, you’d have these data in a spreadsheet/csv and could easily
read it in using `read.csv()`.

``` r
namez <- c("Alex", "Brooke", "Carlos", "Dasia", "Enzo")
gradez <- c("B", "A", "A", "B", "C")
```

``` r
namez <- c("Alex", "Brooke", "Carlos", "Dasia", "Enzo")
gradez <- c("B", "A", "A", "B", "C")
```

Second, we’re going to write our loop. The loop needs two things:

1.  an *iterator*. Usually, people use `i` but it can be anything, of
    course.
2.  loop *bounds*. This is the only thing that will change during each
    repetition. Since we have 5 names and grades, we’re going to loop
    over elements 1 through 5. We’ll use `1:5`, or `1:length(namez)` to
    be even more flexible.

``` r
# for(iterator in bounds)
# everything between { and } will be looped
for(i in 1:length(namez)){


}
```

The iterator and bounds in `for` loops are similar to the iterator and
bounds in $\sum_{i = 1}^n$

Let’s just fill the loop with a simple printing statement to illustrate
what the loop does.

``` r
# Feel free to change length(namez) to some other number
#   like 20, 100, or 1000
for(i in 1:length(namez)){

  cat(i, "\n") # just printing i
}
```

Remember, to access the first name in `namez`, we would use the
following: `namez[1]`. Similarly, `namez[2]` would return the second
element, and `namez[length(namez)]` would give the last element. Instead
of putting a specific element in the square brackets, we can put the
index variable there. Then, we can put this into our loop:

``` r
for(i in 1:length(namez)){

  cat(namez[i], "\n")
}
```

Finally, we can put this all together and generate our initial output.

``` r
for(i in 1:length(namez)){

  cat("Name:", namez[i], "... Grade:", gradez[i], "\n")
}
```

Now, consider if we had many more names and grades. We could have
thousands of names and grades and our little for loop would remain the
same!

We are not limited to just numeric iterators/bounds. Sometimes, using
non-numeric ones is helpful too:

``` r
for(name in namez){

  cat(name, "\n")
}
```

We can also use loops to create or modify data. As an example, we’re
going to build up the [Fibonacci
Sequence](https://www.mathsisfun.com/numbers/fibonacci-sequence.html)
one element at a time.

Element $n$ in the Fibonacci Sequence is simply a sum of the previous
two elements. Explicitly, $F_n = F_{n-1} + F_{n-2}$. Usually, people
start the sequence with 0 and 1, which makes the third element equal to
1 (1 + 0), the fourth element equal to 2 (1 + 1), the fifth element
equal to 3 (2 + 1), and so on. Our goal is to generate the first $n$
elements of the sequence.

Again, the formula for any element $n$ is just
$F_n = F_{n-1} + F_{n-2}$. So, to calculate $F_n$, we need these other
two numbers. However, to get $F_{n-2}$, for example, we need $F_{n-3}$
and $F_{n-4}$. Obviously, this continues back until we arrive at $F_1$
and $F_2$. This suggests that we’ll need to calculate each element in
the sequence until we arrive at $F_{n}$.

Let’s start with the third element. Since we have `v <- c(0, 1)`
already, we can write `v[3] <- v[2] + v[1]`. This can also be written
as: `v[3] <- v[3-1] + v[3-2]`. Once we have `v[3]` established, we can
calculate `v[4]` as `v[3] + v[2]`, or `v[4] <- v[4-1] + v[4-2]`. We
would repeat this for 5, 6, 7, and all the way until $n$. Hopefully, you
can see that we could also generalize this code to look like
`v[i] <- v[i-1] + v[i-2]` inside of a for loop. Our loop bounds would
start at 3 (since elements 1 and 2 are established as 0 and 1 already),
and we would continue the loop until $n$.

``` r
n <- 10
v <- c(0, 1)
# fill in bounds of the loop:
for(i in ){
  # fill in code to be looped over here...
}
print(v)
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
n <- 10
v <- c(0, 1)
for(i in 3:n){

  v[i] <- v[i-1] + v[i-2]
}
print(v)
```

<details>

<summary>

Output
</summary>

     [1]  0  1  1  2  3  5  8 13 21 34

</details>

</details>

## Conditionals

We can build “logic” into our loops by adding in `if` statements, too.
Take a look at the following example using names and grades again. Here,
I will change what gets printed based on the grade the individual
obtained.

``` r
for(i in 1:length(namez)){

  if(gradez[i] == "A"){

    cat("Name:", namez[i], "... crushin' it!", "\n")
  } else if(gradez[i] == "B"){

    cat("Name:", namez[i], "... you're doing a good job!", "\n")
  } else {

    cat("Name:", namez[i], "... keep studying!", "\n")
  }
}
```

<details>

<summary>

Output
</summary>

    Name: Alex ... you're doing a good job! 
    Name: Brooke ... crushin' it! 
    Name: Carlos ... crushin' it! 
    Name: Dasia ... you're doing a good job! 
    Name: Enzo ... keep studying! 

</details>

If you remember, in Module 1.7 we colored points based on certain
characteristics. We did this by first setting all colors to be the same
value, and then changed the color of certain observations based on their
values. See below.

``` r
v <- c(2, 4, 7, 4, 6, 1, 3, 6)
colorz <- rep("black", length(v))
colorz[v > 5] <- "tomato"
```

We can use loops to achieve a similar outcome. You can run the code
snippets above and below to see for yourself that the results are the
same.

``` r
colorz2 <- rep("black", length(v))
for(i in 1:length(v)){

  if(v[i] > 5){

    colorz2[i] <- "tomato"
  }
}
```

## `ifelse()`

While both of these solutions arrive at the same answer, there is a
better option.

First, in general, loops are relatively slow in R. It might not seem so
when dealing with small samples, but it becomes noticeable as the data
grow larger.

Second, as people often say, “lazy” programming is good programming! We
should be writing as little as possible[^1], without sacrificing
coherence/readability, to minimize mistakes, bugs, etc.

Introducing: `ifelse()`. This is a function that accepts three
arguments:

- `test`: an object which can be coerced to logical mode. In other
  words, some logical vector like `v > 5`
- `yes`: return values for true elements of `test`. In other words, what
  should be the output when `v > 5` is `TRUE`?
- `no`: return values for false elements of `test`. In other words, what
  should be the output when `v > 5` is `FALSE`?

``` r
v <- c(2, 4, 7, 4, 6, 1, 3, 6)
colorz3 <- ifelse(v > 5, "tomato", "black")
colorz3
```

You can think of `ifelse()` as creating a `for` loop with `if()`
statements inside of it. In fact, we can even nest `ifelse` statements.
For example, consider these data on [U.S. Senate vote on the use of
force against Iraq in
2002](https://vincentarelbundock.github.io/Rdatasets/doc/pscl/iraqVote.html).
For each observation, we want to assign some value (here, I chose to
assign some text) by party and by vote.

``` r
votez <- read.csv("https://vincentarelbundock.github.io/Rdatasets/csv/pscl/iraqVote.csv")
votez$vote_and_party <- ifelse(
  votez$y == 1,
  ifelse(votez$rep == TRUE,
         "Vote Yes and Republican",
         "Vote Yes and Democrat"),
  ifelse(votez$rep == TRUE,
         "Vote No and Republican",
         "Vote No and Democrat"))
table(votez$vote_and_party)
```

## A Note on Writing Your Own Functions

Throughout this lesson (and the last several), we have been *using*
functions that someone else wrote, like `cat()`, `length()`, and
`ifelse()`. R also lets you write your own functions with `function()`.
For example:

``` r
double_it <- function(x){

  return(x * 2)
}

double_it(4)
```

Writing your own functions is a powerful skill, and you will see it in
other R tutorials and code online. However, we are not going to cover it
in this course. Everything we need can be done with the tools we have
already covered.

[^1]: Except for code comments! Always comment your code.
