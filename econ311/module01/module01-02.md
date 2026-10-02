# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## Data, Data, Data

Nowadays, data is (are?) everywhere. Data has become a buzzword for
those in business, policy, government, industry, science, etc.
Understanding how to work with data, therefore, is becoming an
increasingly valuable and marketable skill. Employers want to hire
people who can use data.

However, everyone “knows” Excel. Everyone “knows” what a correlation is.
So, how can **you** differentiate yourself from others? Put differently,
how can you send a credible signal to employers that you *really* know
data? This course will (re-)introduce you to statistics and econometrics
while exposing you to important, marketable tools like R and Claude
Code.

First, let’s review some rudimentary definitions and concepts in
statistics.

## Types of Statistics

A *statistic* is a measurement that comes from some data. Broadly, there
are two ways to classify the purpose of statistics:

> [!TIP]
>
> ### Descriptive Statistics
>
> Descriptive statistics are meant to organize, summarize, and present
> data in ways that helps people understand key facts about the data.

> [!TIP]
>
> ### Inferential Statistics
>
> Inferential statistics are descriptive statistics that are used to
> estimate properties of a population based on a sample.

## Samples

Before taking a measurement, one needs to collect data. Those data can
be collected either as a *population* or as a *sample* of a population.

> [!TIP]
>
> ### Population
>
> The entire possible group of observations. For example, the population
> of students in the college of business.

> [!TIP]
>
> ### Sample
>
> Any subset of students from the population. For example, Economics
> majors or business school juniors.

<div class="aside">

In this example, economics majors are a sample from the population that
is business school students. However, business school students can also
be thought of as a sample of University students.

</div>

Suppose I want to know the average height of ODU students. Asking all
20,000+ students how tall they are would be very expensive (in terms of
time, financial cost, and effort), so I need to do something else. One
thing I could do relatively easily is collect the heights of all student
athletes since they are posted online (most team rosters include
heights). Alternatively, I could ask everyone in this course how tall
they are.

For example, perhaps the average height of the men’s basketball team is
6’5”. This is an interesting *descriptive* statistic, but a poor
*inferential* statistic since basketball players are notoriously taller
than average. On the other hand, the average height of the individuals
in this course would be a much better inferential statistic. However, it
could be that people enrolled in ECON 311 are more likely to be male (or
female), which would once again make the average a poor representation
of the average ODU student.

> [!TIP]
>
> ### A “Truly” Random Sample
>
> To get a legitimately random sample, one needs to either use true
> randomization (picking names from a hat) or use something that is
> sufficiently independent of what one is studying. For example,
> sampling individuals who have a 6 as the third digit from the right in
> their phone number. Phone numbers (and University IDs) are given out
> randomly, or at least not in terms of anything to do with height.

## Qualitative vs Quantitative

Next, one needs to consider whether the statistic they have “measured”
from their sample (or population) is either *qualitative* or
*quantitative*.

> [!TIP]
>
> ### Qualitative Statistics
>
> Statistics that fall under this category are meant to describe
> characteristics or traits of something that are not naturally
> quantifiable. Examples include eye color, nationality, etc.

> [!TIP]
>
> ### Quantitative Statistics
>
> Quantitative statistics are numerical properties of some data.
> Examples include height, income, etc.

## Types of Measurement

These two broad categories of statistics can be broken down a bit
further depending on the scale on which they are measured. These scales
are as follows:

**Nominal**: Nominal data are represented by labels or names. In
addition, there is no natural order to these data. An example would be
“language spoken”, and responses might be “english”, “spanish”,
“japanese”, “italian”, etc. Nominal data are therefore always
qualitative.

**Ordinal**: Ordinal data are recorded in reference to some relative
ranking. These data do indeed contain an order to them, unlike nominal
data, but the numbers representing the order do not have any other
meaning. For example: a list of the
<a href="https://en.wikipedia.org/wiki/100_metres#All-time_top_25_women"
data-preview-link="true">fastest 100 meter dash times</a>. The
difference between \#1 and \#2 is not the same as the difference between
\#8 and \#9.

**Interval**: For interval data, the distance between the numbers is
indeed meaningful, and there must be units that accompany the
measurements. However, zero is usually meaningless insofar that it does
not indicate an absence of the value being measured. For example:
Fahrenheit or dress sizes. In both cases, the difference between any two
numbers is the same and meaningful. At the same time, zero degrees
Fahrenheit or a size zero dress does not mean an absence of temperature
or fabric.

**Ratio**: Ratio measurements are interval measurements, except zero has
a natural meaning. When zero has a natural meaning, the ratio of two
measurements also has meaning. For example, consider income and
Fahrenheit. Someone with \$50 has twice as much as someone with \$25 but
50 degrees Fahrenheit is not twice as hot as 25 degrees. In addition,
having \$0 means you have no money (and therefore negatives mean
something too).

## Discrete vs Continuous

When measurement is quantitative, the measurement can be either discrete
or continuous.

**Discrete**: Discrete measurements do not allow for fractions or
decimals. Only things that can be measured as integers (1, 2, 3, …)
qualify as discrete. For example, the number of followers you have on
social media is discrete because you cannot have half of a follower.

**Continuous**: Continuous data can be sub divided into non-whole
numbers. For example: time is a continuous measurement because you can
have 15.623941004 seconds. Income can be considered continuous even
though dollars only go to two decimal places – it is close enough that
people would consider it continuous.

## Structuring Data

In reality, data sets come with rows and columns. Often, each column
will contain a different type of measurement, whether it be qualitative
or quantitative. In general, there are three main ways to organize data.
Usually, we will have datasets with columns for units (states, firms,
individuals, etc.) and time (year, month, quarter, day).

- Cross Section: data collected for many units at the same (or similar)
  time.
- Time Series: data collected for one unit over many time periods.
- Panel: data collected for many units over many time periods.
  - When all units are observed for each time period (50 states over 20
    years), the panel is said to be *balanced*.
  - When some units are observed more often than others, this is an
    *unbalanced* panel.
  - Sometimes, people observe totally different, unique units each time
    period. For example, observing the students in Econ 311 over time.
    This is called a *pooled cross section*. Essentially, the data
    contains many cross sections all pooled together into something that
    looks like a panel.

Again, most of the time, we will work with panel data. It will be
structured such that each row represents a specific unit at an instance
of time. For example, each row will represent a state (e.g., Virginia,
New York) in a single year (e.g., 2005, 2006).
