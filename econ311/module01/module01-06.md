# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Using R

Now that you have some knowledge about basic operations in R, you may be
itching to try them out on some real data (e.g. spreadsheets, etc.).
However, there is still a bit more background and set-up we need to go
through. We need to first learn to set up an “R Project,” and then talk
about different types of files.

### R Projects

Before R can read (load) a file with data (spreadsheet), it needs to
know where to look for that file. Computers organize files into folders
(directories), and R has to be pointed at the right one.

R has two functions for working with this directly. `getwd()` tells you
where R is currently looking:

``` r
getwd()
```

<details>

<summary>

Output
</summary>

    [1] "C:/Users/alexc/Dropbox/teaching/Spring 2027/econ311/module01"

</details>

`setwd()` changes where R is looking. You can do this by using absolute
paths or relative paths. Suppose my current working directory is
`C:/Users/alexc/Dropbox/econ101/Project`, but I want to be in my folder
for HW 2 in ECON 311: `C:/Users/alexc/Dropbox/econ311/HW02`. Navigating
by absolute path would mean typing out the *entire* path to the new
folder. However, with relative paths, I would move backwards (or “go up
one level”) relative to where R is currently looking with `..`, and then
type the rest of the path. Note that I would need to move backwards
twice: once to get out of econ101’s project file, and then a second time
to move out of the econ101 folder altogether.

``` r
# Absolute path:
setwd("C:/Users/alexc/Dropbox/econ311/HW02")

# Relative path:
setwd("../../econ311/HW02")
```

Now, suppose I write a script that contains the line of code
`setwd("C:/Users/alexc/Dropbox/econ311/HW01")` to tell R where my data
lives. This works fine on *my* computer, but it would break on anyone
else’s machine since that folder does not exist (I am assuming nobody
else has named their user `alexc`). The script breaks immediately, and
it has nothing to do with the R code being wrong.

**R Projects** solve this. An R Project is a folder with a special
`.Rproj` file in it. When you open that `.Rproj` file, RStudio
automatically sets your working directory to that folder — no `setwd()`
required, and no hardcoded path that only works on your machine.

To create one:

1.  In RStudio, go to File \> New Project.
2.  Choose “New Directory” (or “Existing Directory” if you already have
    a folder for this course).
3.  Pick a location and give it a name — this becomes both the folder
    and the project name.
4.  RStudio creates a `.Rproj` file inside that folder and opens the
    project.

<div class="aside">

I highly recommend that you create a folder structure first and use the
“Existing Directory” option when creating the `.Rproj`. I would place a
main folder in my Documents folder, like
`C:/Users/alexc/Documents/econ311`, and then create sub-folders like
“Data,” “Homework,” and “Final Project.” Then, set your `.Rproj` (and
`CLAUDE.md`) here.

</div>

From now on, whenever you want to work on something for this course,
open the `.Rproj` file first. Everything you do (scripts, data, homework
files) should live inside that one project folder. This is also where
your `CLAUDE.md` file should be saved, too. Note that you should only
have one `.Rproj` file.

Once you’re working inside a project, you can read files using
**relative paths**: if your data lives in a subfolder called `data`, you
can just write `read.csv("data/ford_escort.csv")`, and it’ll work on any
computer that has a copy of the project folder — yours, a classmate’s,
or mine.

<div class="aside">

You’ll still run into `setwd()` in older tutorials, StackOverflow
answers, or code you find online — now you’ll recognize what it’s doing.
But relying on it means your code only works on the computer it was
written on, so we won’t use it as the primary approach in this course.

</div>

### Opening a New Script

Now that we have R, RStudio, Claude, and our `.Rproj` set up, we can
begin writing code and working with our data. To open a new file, click
on the top left button underneath “File”. It looks like a white paper
icon with a green +. This will open up a menu of different file
versions. Just select “R Script” for now, but note the ability to select
“Quarto Document,” too. You now have your first `.R` file open.

Typically, when working in R, you will write code in a file that looks
like this. However, in this class, almost all of the code you write will
be inside `.qmd` files. `.qmd` stands for “Quarto Markdown”, and it is a
way of merging R with a text editor (like Word, etc.). You can think of
a `.qmd` file as a text file with mini `.R` scripts within. You will be
able to generate everything (code, text, tables, figures) inside of a
single file. This is advantageous because RStudio then becomes your
one-stop-shop for everything, eliminating the need to manually change
numbers, tables and figures, since your code will automatically update
everything in your assignments.

Moreover, for each assignment you turn in, there will be an accompanying
`.qmd` template you will use. I have provided these to ease the burden
of setting up new `.qmd` files on your own. Again, you can think of a
`.qmd` as a text document with a bunch of mini `.R` files embedded
throughout. Every time you want to switch from text to code, you just
need to make a new “code chunk” where you can type in your code. In
fact, all of the notes for this course have been generated using `.qmd`
files! You should expect all of your output to look similar to the
structure of these notes.

### Reading Data

Finally, we have all of the set up that we need. To get started reading
in and working with data, let’s open our `.Rproj` file. This should set
our working directory to the same folder that the `.Rproj` file is saved
in, and we can check that this is working by typing (or copy-pasting)
`getwd()` into the console, and hitting enter. Another way to check is
to look at the top of the console next to the R logo. It should say the
version of R you are running and then the current working directory.

Now that R knows where to look for data (thanks to our `.Rproj` and/or
`setwd()`), we can import it. Most of the time in this course, we will
use files that end in `.csv`. This stands for “comma separated values”.
`.csv` files are very common and require relatively small amounts of
storage. `.csv` files are also open-able in Excel (you just might get
some warning about how any Excel formulas you write will not be saved).
To create a `.csv` from an `.xlsx` (Excel) file, just use “Save As” in
Excel, and change the file extension to “Comma Separated Values (.csv)”.

Now, let’s read in a file called
“[ford_escort.csv](https://alexcardazzi.github.io/econ311/data/ford_escort.csv)”.
On my machine, the file lives in a folder called
`C:/Users/alexc/Dropbox/teaching/Spring 2027/econ311/data`. Since these
notes are generated via `.qmd`, and it is saved in
`C:/Users/alexc/Dropbox/teaching/Spring 2027/econ311/module01`,
`ford_escort.csv`’s *relative* filepath is `../data/ford_escort.csv`.
Rather than changing my working directory, I can just use this relative
filepath. To import this data, we will use the `read.csv()` function.

<div class="aside">

When rendering a `.qmd` file (like this one), RStudio automatically sets
the working directory to the folder in which the `.qmd` file is saved.
When using an `.R` file, RStudio uses where your working directory is
set, which is where the `.Rproj` file is saved. This is an important
distinction to note.

</div>

``` r
# Since my working directory is in "econ311/module01",
#   but the file is in "econ311/data",
#   I need to back out of "module01" and navigate to the data folder
ford <- read.csv("../data/ford_escort.csv")
dim(ford); cat("\n") # dim() gives the number of columns and rows
head(ford) # the head() function displays the first 6 rows.

# I could have also done:
# setwd("../data")
# ford <- read.csv("ford_escort.csv")
```

<details>

<summary>

Output
</summary>

    [1] 23  3

      Year Mileage..thousands. Price
    1 1998                  27  9991
    2 1997                  17  9925
    3 1998                  28 10491
    4 1998                   5 10990
    5 1997                  38  9493
    6 1997                  36  9991

</details>

You can also read data straight from a URL if it has been uploaded
correctly. Most of the data for this class’s assignments can be read in
this way. Try using `read.csv()` in the following chunk. Be sure to put
quotes around the URL!

``` r
ford <- read.csv() # Please the url here
dim(ford); cat("\n") # dim() gives the number of columns and rows
head(ford) # the head() function displays the first 6 rows.
```

<details>

<summary>

Solution
</summary>

<hr style="height:4px; visibility:hidden;" />

``` r
ford <- read.csv("https://alexcardazzi.github.io/econ311/data/ford_escort.csv")
```

</details>

### Manipulating Data

Now that we have our data read into R, let’s manipulate it a bit. This
might feel like a lot at once, but try to stay with me. You can
experiment in the previous WebR chunk or in RStudio if you’d like.

1.  If you are in RStudio, use `View(ford)` to open up the dataset in a
    new window. You can also click on `ford` in your environment tab.
2.  Use `colnames(ford)` to print the column names of the dataset to the
    console.
3.  Rename the second column from `Mileage..thousands.` to `mileage`.
4.  Calculate the number of Ford Escorts that were less than \$9000?
5.  Currently, mileage is in thousands of miles. Multiply it by 1000 to
    convert it to just miles, and save over the original variable.
6.  Calculate the price per mile (\$/mi) for each vehicle, and save it
    as a *new* variable.
7.  Calculate the average price per mile.
8.  Use the `range()` function to find the minimum and maximum price per
    mile.

As a note, you can use `cat()` to combine text and code. Put `"\n"` at
the end to make a new line. You can experiment with this on your own.

``` r
# View(ford) # Open the data in a new window.
# Revisit this window to see how/if things have changed

# colnames(ford) # Take a look at the column names.

colnames(ford)[2] <- "mileage" # change the second name

# ford$Price < 9000 # this gives boolean (T/F) values.
# Since R treats TRUE as 1 and FALSE as 0, use sum()
cat("Number of Escorts less than $9,000:", sum(ford$Price < 9000), "\n")

ford$mileage <- ford$mileage * 1000 # Multiply by 1000 and save/overwrite
ford$cost_per_mile <- ford$Price / ford$mileage # Create $/mi

cat("Average Cost per Mile:", mean(ford$cost_per_mile), "\n") # average
cat("Range of Cost per Mile:", range(ford$cost_per_mile)) # min and max
```

<details>

<summary>

Output
</summary>

    Number of Escorts less than $9,000: 5 
    Average Cost per Mile: 0.4315369 
    Range of Cost per Mile: 0.08325 2.198

</details>

### Installing Packages

Base R can do a lot, but one of R’s best features is that anyone can
write and share new tools. These tools are called *packages*. Using a
package is a two-step process, and the easiest way to remember it is to
think about apps on your phone: you *install* an app once, but you
*open* it every time you want to use it.

``` r
# Step 1: download and install the package. Do this once per computer.
install.packages("modelsummary")

# Step 2: load the package. Do this every time you restart R.
library("modelsummary")
```

A few practical tips:

- Run `install.packages()` in the console, *not* inside a `.qmd` file
  that you render. Otherwise, R will try to reinstall the package every
  time you render.
- Put your `library()` calls near the top of your document so that
  anyone reading it can see what the code depends on.
- If you see an error like `there is no package called 'modelsummary'`,
  you have not installed it yet.
- The “Packages” tab in RStudio shows you everything you have installed.

**Try it:** install `modelsummary` now, since we will use it in Module
4. It depends on several other packages, so this can take a few minutes.

> [!WARNING]
>
> ### Use as few packages as you can
>
> Students (and AIs!) love to install and load tons of packages, but
> this is bad programming. Packages get updated, and sometimes those
> updates change how a function works or remove it entirely. Every
> package you rely on is one more thing that can break your code down
> the line, and code that worked perfectly today may fail when you come
> back to it in a year.
>
> Instead, try to master one “style” of R and use as few packages as
> possible. Only use a package when you *need* to, meaning when base R
> cannot do the job or would make it painfully hard. This course will
> tell you which packages to use, and when.
