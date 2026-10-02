# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Why use code?

You might be thinking, “why can’t we use Excel?” It’s not that Excel is
bad – it’s that R is better. Using code allows for reproducibility,
customization, and automation.

Perhaps the best argument for writing code over just using Excel is one
about fixed vs marginal cost. Learning how to use Excel requires a much
lower fixed cost than learning how to code. Everyone has seen Excel,
data are nicely formatted into cells, and you can point-and-click to
generate nearly everything. Alternatively, programming is a language,
and learning a language is slow and potentially painful. However, once
you have working code written, you never have to write it again! In
other words, the future marginal cost of coding is much, *much* lower.

### Why use R?

Your next question, however, might be, “why R? My comp-sci friends use
Python, C++, and SQL.”

There are a few arguments I will provide: First, R is becoming one of
the most popular languages for working with *data*. R is uniquely suited
for for statistical analysis and visualization, which you will see as we
go through the course. Second, R is an open-source language. This means
that it is *free*, and it is developed by its users. This allows for
rapid development of cutting edge statistical methods, providing a
significant advantage over other languages. Finally, R is the language
that I know best and the one I am most qualified to help *you* learn

## Some Basics of R

The following might seem abstract at the moment, but hopefully will
become more concrete and clear as time goes on.

- Everything in R is an *object* with a *name*. Your dataset(s) will be
  an object (or objects), and you will assign a name to it (them).
- *Functions* are how you do things (e.g., calculate statistics) to/with
  objects.
- Functions come built-in (called *base* R) or can be loaded from
  *libraries*.
  - You can also write your own functions! In fact, libraries are just
    sets of functions written by other people.
- You can have multiple datasets loaded into R at once
  - The Excel analogue would be having multiple sheets.

Now, let’s download R and RStudio. If you run into trouble, feel free to
jump ahead to the next sub-module. There, you will download Claude Code,
which can help you download R and RStudio as well.

## Download R

First, we have to download R via
[cran.r-project.org](https://cran.r-project.org/). This is the actual
language, and what you should think of as the “brain” of R.

- This website *should* look like it was built in the 1990s or early
  2000s. This sometimes makes students skeptical, but fear not.
- Make sure to choose the correct operating system and latest version of
  R.

## Downloading RStudio

Second, we need to download RStudio via
[posit.co](https://posit.co/download/rstudio-desktop/). If the previous
download is the brain, you should think of RStudio as the body. We will
only ever interact with R through RStudio in this course. Once again,
make sure you select the correct operating system for your machine.

<div class="aside">

Posit used to be known as RStudio, but has since changed names to
generalize themselves. RStudio (the product) continues to be developed
and maintained by Posit. Think of this like Meta or Anthropic (the
company) running Facebook or Claude (the product).

</div>

## Exploring RStudio

<img src="module01_img/rstudio_sc.png" class="r-stretch"
data-fig-align="center"
data-fig-alt="Screenshot of RStudio (on Windows)."
alt="Screenshot of RStudio (on Windows)" />

When you open RStudio for the first time, you will see four panels like
in the above image. It is likely that your version of RStudio has a
white background with blue or black text. If you would like to change
this, go to “Tools \> Global Options… \> Appearance \> Editor theme”. I
like a darker theme to make it easier on my eyes if I am looking at the
screen for long periods of time.

The four panels shown above are as follows:

- Top Left: **Source** – This is where you will write the R code you
  want to save. In other words, this is where you write and save your
  work, usually called R scripts or Quarto Markdown files (.R or .qmd).
- Bottom Left: **Console** – When you execute (or *run*) code, you will
  usually see output here. This is also a place you can write code you
  do not want to be part of your final script. If you were a painter,
  the **Source** panel would be your canvas and the **Console** would be
  your <a
  href="https://upload.wikimedia.org/wikipedia/commons/0/04/Oil_painting_palette.jpg"
  data-preview-link="true">palette</a>. Also note the *Terminal* tab
  here. You can interact with Claude Code here once it is set up.
- Top Right: **Environment** – Here is where we will be able to see all
  the objects (data, etc.) that we are working with in the moment. To
  clear your environment, paste the code `rm(list = ls())` into your
  Console, and hit Enter.
- Bottom Right: **Output** – This is mostly where you will see plots you
  have generated or files you have rendered, but can also see files on
  your computer, packages you have installed, and “Help” for certain
  functions.

<!-- https://docs.posit.co/ide/user/ide/guide/ui/images/rstudio-panes-labeled.jpeg -->

<img src="module01_img/rstudio-panes-labeled.jpeg" class="r-stretch"
data-fig-align="center"
data-fig-alt="Screenshot of RStudio with panel labels."
alt="Screenshot of RStudio with panel labels." />

## Code Tips

Before we start writing code, here are some important tips and tricks
that will make your life (our lives) easier.

- The hardest part about coding is learning how to Google and/or prompt
  your AI. You read that correctly. The best programmers are the best
  Googlers/prompters. There is a wealth of knowledge online, and knowing
  how to sift through it all is truly a skill.
- Write yourself comments. You can do this by writing `#` before you
  type something. This will help you remember what your code does after
  you have been away from it for a long time. Sometimes, re-reading
  (decyphering) uncommented code is harder than re-writing it from
  scratch.
- Your code probably won’t work the first time. Your code probably won’t
  work the first few times. However, when trying to fix something, only
  change one thing at a time.
- Give objects informative names. It is easier to understand code when
  things are named “country_gdp” or “yearly_unemployment” rather than
  “gdp2” or “x”.

**As a final tip, and this one is important, we are going to change some
default settings to RStudio.**

- Click on “Tools \> Global Options… \> General”
- Uncheck “Restore .RData into workspace at startup”
- Change “Save workspace to .RData in exit:” to Never

It might seem like these auto-saving features are a good idea, but trust
me: you will be *much* better off without it. <mark>Do not skip this
part!</mark>

## R’s Data Types

R has a few different data types:

- Numbers: You can type a number into R and R will know its value.[^1]
- Boolean: This data type is made up of `TRUE` and `FALSE` values. Think
  of this like binary values (`0` and `1`). Here is a picture of <a
  href="https://upload.wikimedia.org/wikipedia/commons/thumb/c/ce/George_Boole_color.jpg/330px-George_Boole_color.jpg"
  data-preview-link="true">George Boole</a>.
- Characters: This datatype is reserved for text. Sometimes characters
  are called *strings*, but they are always found inside quotation
  marks. In R, you can use `"` or `'`.
- Factors: Factors are a weird mix of characters and numbers. Perhaps
  the best way to think of them is as a categorical variable. In this
  course, we will generally avoid the use of Factors.

## Evaluation

To execute / evaluate / run code in R, there a few different ways to do
it. The easiest way is to highlight whatever you are interested in
running, and typing `ctrl` (`Cmd` on Mac) + `enter`. You can also place
your cursor on the line of code you’d like to run, and use the same keys
to run that specific line. There is also a button on the top right of
the Source panel that says “Run”, which will do the same thing.

## WebR

Before moving on to explore basic operations, I want to mention
something you’ll see embedded throughout this course. I will be
exhibiting code in each module in static code blocks. Many times, these
code blocks, sometimes called code chunks, might generate output, plots,
both, or nothing. Unfortunately, these code blocks are, for all intents
and purposes, set in stone. In other words, besides collapsing/expanding
them, you cannot really interact or experiment with them. This probably
stifles student curiosity, since you’ll probably want to tweak things as
you’re going through the notes.

To address this, I have included `WebR` chunks into each module’s notes.
These chunks will look a bit different from the static chunks, and I
encourage you to interact with them! You can write, alter, and execute
code inside each chunk, and each `WebR` chunk will “remember” what
you’ve run in other chunks. Go ahead and explore a bit with the chunks
below:

<div class="aside">

While the `WebR` chunks can “talk” to one another, and the static chunks
can talk to one another, there is no communication between the two types
of chunks.

</div>

``` r
# This is a static chunk
# Notice how you cannot modify what's written here.
```

``` r
# This is an interactive WebR chunk.
# Try writing and executing some code here.
# Or, simply remove the hashtag from the line below, and click run:
# print("hello world!")
```

## Basic Operations

Once we have a handle on data types, we can begin to perform operations
on data. For numeric values, we can use simple arithmetic operations
such as addition (`+`), subtraction (`-`), multiplication (`*`), and
division (`/`).

<div class="aside">

Most, if not all, of the code blocks (and output) in this course will be
collapsable. Click on them to hide/display the code (or output).

</div>

``` r
# Example Comment.  Get ready to math.
5 + 5
10 / 3
4 + 3 * 100 # Another comment. Something about PEMDAS.
(4 + 3) * 100 # Something else about PEMDAS.
```

<details>

<summary>

Output
</summary>

    [1] 10
    [1] 3.333333
    [1] 304
    [1] 700

</details>

Next, play around with some of this in WebR:

``` r
# Example Comment.  Get ready to math.
5 + 5
10 / 3
4 + 3 * 100 # Another comment. Something about PEMDAS.
(4 + 3) * 100 # Something else about PEMDAS.
```

When we have boolean values instead of numbers, we need to use logical
operators: And (`&`), Or (`|`), Not (`!`)

- “And” and “Or” take two boolean values and combine them to into a
  single boolean.
  - “And” returns `TRUE` only when both values are `TRUE`.
    - Example: The sky is blue (`TRUE`) `&` the grass is green (`TRUE`)
      results in `TRUE`
    - Example: The sky is green (`FALSE`) `&` the grass is green
      (`TRUE`) results in `FALSE`
  - “Or” returns `TRUE` only when both values are **not** `FALSE` (or at
    least one is `TRUE`).
    - Example: The sky is blue (`TRUE`) `|` the grass is green (`TRUE`)
      results in `TRUE`
    - Example: The sky is green (`FALSE`) `|` the grass is green
      (`TRUE`) results in `TRUE`
- “Not” negates a single boolean value.
- It may feel a bit clunky, but logical operators can be thought of a
  lot like English.

<details class="code-fold">
<summary>Code</summary>

``` r
TRUE & FALSE
TRUE | TRUE
TRUE & !FALSE
```

</details>

<details>

<summary>

Output
</summary>

    [1] FALSE
    [1] TRUE
    [1] TRUE

</details>

Try some of these with WebR. Un-comment ones you want to try by deleting
the hashtag. Re-comment them by adding the hashtag. You can also try
various other options.

``` r
TRUE & FALSE
# TRUE | TRUE
# TRUE & !FALSE
# TRUE & (TRUE | FALSE)
# TRUE & (TRUE & FALSE)
# !(TRUE | FALSE)
```

Some other logical operations to note are `==`, `<`, `>`, `>=`, and
`<=`. These are logical operations applied to numerical values, and you
will likely be much more familiar with these.

``` r
5 < 3
5 > 3
5 > 3 & 4 > 3
5 > 3 | 4 > 5
```

<details>

<summary>

Output
</summary>

    [1] FALSE
    [1] TRUE
    [1] TRUE
    [1] TRUE

</details>

These logical operations are incredibly important, because we will use
these to subset (or filter) our data. For example, you may want to
subset your data to look at rows of only Males under the age of 25. This
will look something like `Gender == "Male" & Age < 25`.

We will not discuss operations for characters and factors until later in
the course, but there may be times where you will want to convert data
from one type to another. To convert from a number or boolean to
character, you can use `as.character()`. To go from text to numeric, you
can use `as.numeric()`.

<details class="code-fold">
<summary>Code</summary>

``` r
as.character(5)
as.numeric("5")
as.logical("FALSE")
as.numeric("Five")
```

</details>

<details>

<summary>

Output
</summary>

    [1] "5"
    [1] 5
    [1] FALSE
    [1] NA

</details>

Notice how the final line produces an `NA` value. Seeing an `NA` value
is the same as seeing R shrug its shoulders. It is not smart enough to
know that `"Five"` is `5`, so it returns a “missing” value. `NA` values
can mess up a lot of things in R. For example, what is the average of
this collection of numbers: `2, 4, NA, 8`? R will return `NA` when
asked, because it isn’t sure how to think about the `NA` here. Should
you remove the missing value? Replace it with zero? It is not always
clear. A helpful function, therefore, is `is.na()`. This returns a
boolean equal to `TRUE` when the input is `NA`.

<details class="code-fold">
<summary>Code</summary>

``` r
is.na(as.numeric("5"))
is.na(as.numeric("Five"))
```

</details>

<details>

<summary>

Output
</summary>

    [1] FALSE
    [1] TRUE

</details>

As a final note, if you run the above in RStudio, you might get output
saying `Warning: NAs introduced by coercion`. This is R giving you a
heads up about what I just mentioned. Sometimes, this warning is
expected, but other times it’s a good signal to check your data/code!

[^1]: <span class="fragment">This is trivial, I know.</span>
