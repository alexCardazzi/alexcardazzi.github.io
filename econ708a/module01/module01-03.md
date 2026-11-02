# Causality
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ708a.html"
target="_blank">alexcardazzi.github.io</a>.

## Terms

Let’s begin this module by defining a few terms. Be on the lookout for
these terms, and more, throughout the notes <u>**bolded and
underlined**</u>.

- Estimand – any quantity we want to estimate
  - Causal Estimand – the change in some outcome $Y$ given some
    treatment $D$
  - Statistical Estimand – the change in the expectation of $Y$ given
    $D$
- Estimate – an approximation of an estimand, using data
- Estimator – function, or series of functions, applied to sample data
  to generate an estimate
  - Estimation – the act of apply*ing* the estimator to data.

Second, here is a flow chart to help you visualize the relationship
between these terms:

<div>

<figure class=''>

<div>

<img src="module01-03_md_files/figure-commonmark/dot-figure-1.png"
style="width:6in;height:3in" />

</div>

</figure>

</div>

## Causality

When you’re first taught econometrics, much focus is placed on
*estimation* and, perhaps to a lesser extent, *inference*. Your
professor, maybe even me, probably tried to hammer home “a one unit
change in $X$ is associated with a $\widehat{\beta}$ units change in
$Y$.” This is estimation. Then, you were probably asked to test if
$\widehat{\beta} = 0$ by considering $\beta$’s standard error. This is
inference.

Once you’re familiar with how to estimate and interpret $\beta$s, which
admittedly is not always easy, you can start thinking more about your
modeling decisions. Estimation and interpretation are the spelling and
grammar of modern econometrics, and you need strong foundations as you
take this next step.

The next step I’m talking about is called *identification*. In this
module, we are going to start thinking about how to estimate a plausibly
*causal* relationship between $X$ and $Y$ rather than a *correlational*
relationship between $X$ and $Y$. Before jumping into identification,
though, we need to lay out some more foundations.

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/J6P4RwWdGBw?si=0VOUDL00dGj6a21I" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Estimands

Economists use econometric models to examine the effects of some policy,
shock, or *treatment* on some outcome. For example, suppose you are in
charge of figuring out if a new pill successfully alleviates headaches.
Perhaps the best way to test the effectiveness of the drug would be to
recruit a number of participants who have headaches. Some of these
participants would be given the new pill while the other half would not
be allowed to take anything. Consider individual $i$ who is part of this
study.

- Let $D$ denote their treatment status. We can represent being given
  the pill with $D=1$. Therefore, $D=0$ will represent being untreated.
- Let $Y$ denote the status of their headache. We can represent no
  headache with $Y=1$. Of course, this means that $Y=0$ represents
  having a headache.

Individual $i$’s outcome, when treated, can be written as $Y_i|_{D = 1}$
(or $Y_i|_{D=0}$ if untreated). However, as shorthand, you’ll see this
written as either $Y_i(1)$ or $Y_{i}^1$. If $Y_i(1) = 1$ and
$Y_i(0) = 0$, then we’d say that the new pill worked. On the other hand,
if $Y_i(1) = 0$ and $Y_i(0) = 0$, we’d probably say that the pill did
not work. Therefore, the <u>**treatment effect**</u> can be written as
the difference between $Y_i(1)$ and $Y_i(0)$: $Y_i(1) - Y_i(0)$.

If the treatment effect for person/unit $i$ is $Y_i(1) - Y_i(0)$, then
we can write the <u>**average treatment effect**</u> (or ATE) as
$E[Y_i(1) - Y_i(0)] = E[Y_i(1)] - E[Y_i(0)]$. Remember, this is
shorthand for $E[Y_i|_{D = 1}] - E[Y_i|_{D = 0}]$. This statement is our
<u>**causal estimand**</u> – the quantity we want to estimate. We need
to find a statistical representation of this so we can take it to data.

In statistics, the expectation of $Y$, $E[Y]$, simply represents the
average of $Y$. The expectation of $Y$ given $X$, $E[Y|X]$, is the
conditional average of $Y$. The connection between this and econometrics
is that you can think of OLS as a way to calculate conditional
expectations. This represents our <u>**statistical estimand**</u>.

<div class="aside">

If the **data generating process** for $Y$ is
$Y = \alpha + \delta X + \epsilon$, then
$E[Y] = E[\alpha + \delta X + \epsilon]$. This can be simplified to
$\widehat{\alpha} + \widehat{\delta}E[X]$. Further, consider $E[Y|X]$.
$E[Y | X] = \widehat{\alpha} + \widehat{\delta}E[X|X]$. This can be
simplified further to $\widehat{\alpha} + \widehat{\delta}X$ since
$E[X|X] = X$.

</div>

Back to the ATE – in words, this is equal to the average difference in
the expected outcome of $Y$ minus the average outcome of $Y$ for
untreated units.

Sometimes we only care about the effect for the units that were actually
treated. This is the <u>**average treatment effect on the treated**</u>
(or ATT): $E[Y_i(1) - Y_i(0) | D_i = 1]$. The ATE averages over
everyone, while the ATT averages over only the units that received
treatment. These can differ. For example, if the people who take a job
training program are the ones who benefit the most from it, the ATT will
be larger than the ATE. Many of the methods in this course, like DiD and
synthetic control, are aimed at the ATT.

Okay, cool, but there’s a big issue with all of this… We are talking
about $Y_i(1)$ and $Y_i(0)$. How can we observe *both* $Y_i(1)$ and
$Y_i(0)$? Either individual $i$ is treated or they’re not!

In other words, there are two possible states of the world. In one,
individual $i$ takes the pill, and their outcome is $Y_i(1)$. In the
other version of the world, where they don’t take the pill, their
outcome is $Y_i(0)$. To find the treatment effect like I talked about
above only works if we can simultaneously observe both of these
versions. It’s not even enough for us to believe in parallel universes –
we would need to observe each outcome in each universe. This is the
<u>**fundamental problem**</u> of causal inference, and this issue is
going to motivate the rest of this class.

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/iPBV3BlV7jk?si=2QtfSyykpLVVBcwS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

So, how can we plausibly estimate treatment effects? Since, at the time
of writing, we’re limited to only a single universe, we usually have to
rely on observational data. Like in the new pill example, there might be
a group of treated individuals and a group of untreated individuals. If
the only difference between the groups is their treatment status, then
we can attribute any differences in outcomes to the difference in
treatment status.[^1] In other words, we can use $E[Y|D=1]$ to
approximate $E[Y(1)]$. Think of $E[Y|D=1]$ as the average of $Y$ for the
*subset* of individuals who are treated. This is subtly different from
$E[Y(1)]$, which is is the average of $Y$ for *all* individuals in the
specific universe they are treated. If the treated subset of individuals
are a random sample of all individuals, then $E[Y|D=1] \approx E[Y(1)]$.
This is why **randomized control trials** (RCTs) are considered the gold
standard in causal inference. With randomization, we can be sure that
the treated subset is representative of the rest of the population.

When treatment is *not* randomly assigned, the treated subset is not
representative, and comparing the treated and untreated groups mixes two
things together. The simple difference in group averages is
$E[Y|D=1] - E[Y|D=0]$. If we add and subtract $E[Y(0)|D=1]$ (what the
treated group’s outcome would have been *without* treatment), this
difference splits into two pieces:

$$E[Y|D=1] - E[Y|D=0] = \underbrace{E[Y(1) - Y(0)|D=1]}_{\text{ATT}} + \underbrace{E[Y(0)|D=1] - E[Y(0)|D=0]}_{\text{selection bias}}$$

The first piece is the ATT, the effect we want. The second piece is
<u>**selection bias**</u>: the difference in what the outcomes of the
treated and untreated groups would have been *without* treatment. If the
people who choose to take the pill would have had fewer headaches
anyway, the second term is not zero, and the simple comparison
overstates the effect of the pill. Randomization makes selection bias
zero, since the two groups are alike on average. Every method in this
course is a different strategy for dealing with this second term.

However, it’s usually impossible to run RCTs in real life. For example,
if I want to know the effect of speed limits on crash rates, I cannot go
around randomly changing the speed limits of different highways. Rather,
I have to rely on *natural experiments* that occur out in the wild.
Usually, these natural experiments are comprised of one group that
experiences some change and another very similar group that does not.
For example, maybe a few states change their speed limits but others do
not, or the law change affects interstates by not state routes. Natural
experiments can also occur when some institutional quirk allows us to
isolate and leverage random variation in treatment. As an example,
lotteries of all shapes and sizes (e.g. [military
drafts](https://en.wikipedia.org/wiki/Draft_lottery_(1969)), [health
insurance
lotteries](https://www.nber.org/programs-projects/projects-and-centers/oregon-health-insurance-experiment/oregon-health-insurance-experiment-background),
[housing voucher
lotteries](https://sf.gov/step-by-step/how-affordable-housing-lottery-works),
etc.) can mimic the randomness of an RCT. Using data in a way that
allows us to pick out the variation we care about is called
<u>**identification**</u>. We’ll discuss different *identification
strategies* to estimate causal treatment effects in observational data
as we progress throughout this course.

## Identification

Before working with natural experiments, let’s first consider some toy
models to demonstrate what identification really means. Suppose I have
the following hypothesis:

> Sleeping with shoes on leads to headaches in the morning.

How could I test this hypothesis?

The first option would be to run an RCT. In this RCT, I would sneak into
homes at random as some Nike-Santa and put shoes onto people who are
asleep. Then, I would record information on whether the participants had
headaches the next morning. Of course, this is next to impossible, but
let’s think through this. If I were to estimate a treatment effect, I
would be able to interpret it as causal since the only thing differing
between the groups is their treatment status. In this setting, the
randomization allows me to ensure that I am *identifying* the treatment
effect and not some other factor.

In the absence of an RCT or natural experiment, I need to rely on
observational data. In this case, I would have to find some people who
slept with their shoes on and some people who did not. Suppose the data
I collect are as follows.

| Individual $(i)$ | Treatment $(D)$ | Outcome $(Y)$ |
|:----------------:|:---------------:|:-------------:|
|        1         |        1        |       1       |
|        2         |        1        |       1       |
|        3         |        1        |       0       |
|        4         |        1        |       1       |
|        5         |        0        |       0       |
|        6         |        0        |       1       |
|        7         |        0        |       0       |
|        8         |        0        |       0       |

Table of Treatment Status and Observed Outcomes {.table-striped
.table-hover}

<div class="aside">

Reminder: $D=1$ for individuals who slept with their shoes one, and
$Y = 1$ for individuals who woke up with a headache.

</div>

Remember, we can only observe a single universe, so this table is really
a condensed version of the following:

| Individual $(i)$ | Treatment $(D)$ | Outcome $(Y(0))$ | Outcome $(Y(1))$ |
|:----------------:|:---------------:|:----------------:|:----------------:|
|        1         |        1        |        ?         |        1         |
|        2         |        1        |        ?         |        1         |
|        3         |        1        |        ?         |        0         |
|        4         |        1        |        ?         |        1         |
|        5         |        0        |        0         |        ?         |
|        6         |        0        |        1         |        ?         |
|        7         |        0        |        0         |        ?         |
|        8         |        0        |        0         |        ?         |

Table of Treatment Status and Potential Outcomes {.table-striped
.table-hover}

Here we observe most of the treated group have an outcome of 1 while
most of the control group have an outcome of 0. Using these data, we can
calculate $E[Y|D = 0]$ and $E[Y|D = 1]$ (the *statistical* estimands)
with the parts of the data that we can observe as stand-ins for
$E[Y(0)]$ and $E[Y(1)]$ (the *causal* estimands). Since
$E[Y | D = 1] = 0.75$ and $E[Y | D = 0] = 0.25$, it seems that wearing
shoes when you sleep does lead to more headaches! If we do this, our
strategy to identify the treatment effect of shoes on headaches is
simply throwing our hands up and assuming (hoping?) that treatment was
randomly assigned.

Before we run to tell people not to wear shoes when they sleep, let’s
think about this a bit deeper. Is assuming random assignment of
treatment a good assumption? Put differently, might there be something
causing both sleeping with shoes on *and* waking up with a headache? Is
there something about the treated individuals that makes them different
from the untreated individuals? Since this is observational, there could
be many reasons!

One possible reason would be drinking the night before. Drunk people are
much more likely to fall asleep with their shoes on *and* wake up with a
headache, or so I’ve been told. This is an example of a **confounding**
variable: something that causes both the outcome *and* the treatment.
When we have confounding variables, we need to control for them. In
other words, we want to account for the factor that differs between the
treatment and control groups. This is what we call our **identification
strategy**. When we had our RCT, that randomization was our
identifications strategy. In this case, our identifications strategy is
controlling for this confounding variable.

How do we implement identification strategies? In short: econometrics.
Using OLS, we can account for, or control for, confounding variables by
including them in our regressions. For example, we can write the initial
regression as the following:

$$\text{Headache}_i = \alpha + \delta\text{Shoes}_i + \epsilon_i$$

Here, $\widehat{\delta}$ will be biased. In other econometrics courses,
you might have heard of this as *omitted variable bias*. To address
omitted variable bias, we need to control for the omitted, or
confounding, variable. We can modify the regression like the following:

<div class="aside">

Note: drinking is positively correlated with both wearing shoes to bed
and waking up with a headache. Therefore, $\widehat{\delta}$ would be
exhibit positive biased. In other words, we would probably fine
$\widehat{\delta} > 0$ because wearing shoes to bed is proxying for
drinking the night before.

</div>

$$\text{Headache}_i = \alpha + \delta\text{Shoes}_i + \beta\text{Drink}_i + \epsilon_i$$

In this regression, we will get a plausibly causal estimate of $\delta$
since we’ve controlled for the influence of drinking. In reality, it’s
very likely that $\delta \approx 0$, but now we can think of this as
causal rather than correlational.[^2]

## Directed Acyclic Graphs

In the examples above, it was fairly easy to pick out confounding
variables and think through the logic of how to control for them.
However, as models gets more complicated, we need a systematic way to
think through them. Many economists have begun to use <u>**Directed
Acyclic Graphs**</u>, which are more commonly known as DAGs, to
illustrate or graphically represent causality. Let’s break down the
meaning of DAGs:

- Directed: One thing causes one (or many) other thing(s).
- Acyclic: No feedback in causality!
- Graph: a visual.

Every DAG is be made up of nodes, which are causal factors or variables.
Each node will be connected to at least one other node by an edge. Edges
establish the flow of causality between nodes via arrows.

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/aMNGfodzKO8?si=VNZgysK58tt_oR8K" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

To do this in R, we are going to install and load the `dagitty` package.

``` r
library(dagitty)
```

To start, let’s re-imagine our RCT where we randomly put shoes on people
as they sleep. We can illustrate this setting with a DAG. First, let’s
label our variables. Our treatment, wearing shoes, will be denoted by
$D$. Our outcome, headaches, will be labeled $Y$, and drinking the night
before as $X$. Second, we are going to use the `dagitty()` function. In
this function, we need to do the following:

- We need to tell `dagitty` that we are trying to make a DAG. We do this
  by typing `dag` at the very beginning of our string.
- Next, we need to tell `dagitty` what nodes we’ll be using for our
  outcome and exposure (treatment).
- Finally, tell `dagitty` how each node is connected.

``` r
dag <- dagitty("dag {
  D [exposure]
  Y [outcome]
  
  D -> Y
  X -> Y
}")

plot(dag)
```

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-5-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

Here we have two factors that cause Y. Since there’s only one path from
$D$ to $Y$, we are free to estimate a regression of the following form:

$$Y_i = \alpha + \delta D_i + \epsilon_i$$

In this DAG, there is not much use in controlling for $X$ since it is
independent of $D$. If we were to control for $X$, though, we’d be
removing something from the error term. This should increase the
precision of our estimate (i.e. shrink our standard errors), but
ultimately leave $\widehat{\delta}$ unchanged.

## Confounders

What would the DAG look like if we did not have an RCT to assign
treatment? Now, drinking would cause both shoes and headaches. We’d have
to re-write the DAG as the following:

``` r
dag <- dagitty("dag {
  D [exposure]
  Y [outcome]

  D -> Y
  X -> Y
  X -> D
}")

plot(dag)
```

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

Note that there are now *two* paths from $D$ to $Y$:

1.  $D \rightarrow Y$
2.  $D \leftarrow X \rightarrow Y$

I know this is a bit weird with the way the arrows are drawn, but this
is the norm. This second path, where you see two arrows pointing away
from one another $(\leftarrow X \rightarrow)$, is called a
<u>**backdoor**</u> path. Any time you see an arrow pointing back at the
treatment variable, you have a backdoor path. For every backdoor we see,
we need to control for something along that path (that isn’t the
treatment or the outcome, of course).

<div class="aside">

In fact, `dagitty` has a function called `paths()` that will spit out
the paths from exposure (what we call treatment) to the outcome. You’ll
notice that the paths are written the same way as I’ve written them, and
the output also tells us that both paths are `open`.

</div>

<!-- ```{r} -->

<!-- paths(dag) -->

<!-- ``` -->

In this case, since $X$ is the only variable on this backdoor path, we
need to control for it. This will block/close the backdoor path from D
to Y, which is not the path we’re interested in. Once $X$ is controlled
for, and assuming we’re satisfied with this DAG, we can interpret
$\widehat{\delta}$ as the causal effect of wearing shoes while asleep on
waking up with a headache.

Translating from DAG to regression, we would write
$Y_i = \alpha + \delta D_i + \beta X_i + \epsilon_i$. Without
$\beta X_i$ in the regression, our analysis would suffer from
confounding or omitted variable bias.

We can also use a function from `dagitty`, `adjustmentSets()`, to help
us identify which variables we need to control to identify a causal
effect of the treatment on the outcome.

<details class="code-fold">
<summary>Code</summary>

``` r
cat("Adjustment Set for DAG:\n")
print(adjustmentSets(dag, effect = "direct"))
```

</details>

<details>

<summary>

Output
</summary>

    Adjustment Set for DAG:
    { X }

</details>

<div class="aside">

In this function call, I am specifying `effect = "direct"`. There are
two options you can use: `"total"` or `"direct"`. Which one you pick
simply depends on your research question, and we can discuss this at
another time.

</div>

<!-- Not to state the obvious, but DAGs should be written in ways that are consistent with the institutional setting.  DAGs are theoretical and ca -->

Of course, estimating this regression is only possible if we can observe
**and** measure $X$. If $X$ was either unobservable or unmeasurable, we
would not be able to control for it, and thus be unable to interpret
$\delta$ as causal. Let’s modify our DAG such that $X$ is unobservable
and see what the output of `adjustmentSets()` gives us.

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  D [exposure]
  Y [outcome]
  X [unobserved]
  
  D -> Y
  D <- X -> Y
}")
plot(dag)
cat("Adjustment Set for DAG:\n")
print(adjustmentSets(dag, effect = "direct"))
```

</details>

<details>

<summary>

Output
</summary>

    Adjustment Set for DAG:

</details>

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-8-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

When $X$ was observed, `dagitty` told us to control for it. However, now
that $X$ is unobserved, it no longer tells us to control for it. This is
not because we don’t have to, but rather `dagitty` won’t tell us to
control for something that we physically can’t. Unfortunately, given the
current DAG, there’s no way to isolate the effect of $D$ on $Y$.

However, if $X$ causes another observable thing, $W$, before causing
$D$, then we could just condition on $W$ instead of $X$ because $W$ is
on the same backdoor path.

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  D [exposure]
  Y [outcome]
  X [unobserved]
  
  D -> Y
  D <- W <- X -> Y
}")
plot(dag)
cat("Paths in DAG:\n")
print(paths(dag))
cat("Adjustment Set for DAG:\n")
print(adjustmentSets(dag, effect = "direct"))
```

</details>

<details>

<summary>

Output
</summary>

    Paths in DAG:
    $paths
    [1] "D -> Y"           "D <- W <- X -> Y"

    $open
    [1] TRUE TRUE

    Adjustment Set for DAG:
    { W }

</details>

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-9-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

These are the basics of how to handle confounding variables and
sketching DAGs. Of course, there’s more to it, but in the interest of
not being *too* verbose, I will let Nick Huntington-Klein fill in some
of those gaps with the following two (optional) videos:

- [Drawing
  DAGs](https://www.youtube.com/embed/NOslfkYr_II?si=9vKTpy3iJ9yjqQLY)
- [Simplifying
  DAGs](https://www.youtube.com/embed/h5C2iMoHFag?si=HZS7SWHGqmB2cWeC)

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZTSgNR_PiXo?si=99sYYejw0gOBHfjb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Mediators

Not every path from the treatment to the outcome is a problem. A
<u>**mediator**</u> is a variable that sits *on* the causal path between
the treatment and the outcome: $D \rightarrow M \rightarrow Y$. It is
part of the *how* of the effect. For example, suppose you want to know
the effect of a job training program ($D$) on earnings ($Y$), and the
program works partly by raising workers’ skills ($M$). Skills are caused
by the training and, in turn, cause earnings.

Unlike a confounder, you do **not** want to control for a mediator when
you are after the total effect of $D$ on $Y$. Controlling for $M$ blocks
part of the very effect you are trying to measure, so your estimate
would only capture the part of the effect that does *not* run through
$M$. (Researchers sometimes control for a mediator on purpose to learn
*how* an effect works, but that is a different question from the one we
are asking here.)

``` r
dag <- dagitty("dag{
               Y[outcome]
               D[exposure]
               
               D -> Y
               D -> M
               M -> Y
}")
plot(dag)
paths(dag)
adjustmentSets(dag, effect = "total")
```

<details>

<summary>

Output
</summary>

    $paths
    [1] "D -> M -> Y" "D -> Y"     

    $open
    [1] TRUE TRUE

     {}

</details>

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-10-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

Both paths from $D$ to $Y$ are causal paths, and there are no backdoor
paths, so `dagitty` tells us there is nothing to adjust for. To
summarize: control for confounders, do not control for mediators (when
you want the total effect), and do not control for colliders.

## Colliders

The opposite of backdoor paths are <u>**collider**</u> paths. To
identify collider paths, somewhere along the path you’ll see something
like this: $\rightarrow X \leftarrow$. These paths are *already closed*.
When we have backdoor paths, we need to control for at least one node
along the path to close the backdoor. If we condition on a node on a
collider path, we are actually opening a new backdoor path that had been
already closed to start.

This might be a bit confusing to start, but think through this example.
Suppose you’re interested in the effect of someone’s IQ
(intelligence[^3]) on the IQ of their significant other. In other words,
do intelligent people tend to match with people with similar
intelligence? To investigate this, we could collect data on the IQ of
each subject $(D)$, their significant other $(Y)$, and their first-born
child $(X)$. Let’s draw the DAG.

``` r
dag <- dagitty("dag{
               Y[outcome]
               D[exposure]
               
               D -> Y
               Y -> X
               D -> X
}")
plot(dag)
```

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-11-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

In this DAG, both the subject and their significant other cause the IQ
of their child. The edge connecting $D$ and $Y$ is then the relationship
we’d like to test. This should make some intuitive sense as IQ is likely
genetic.

Since the relationship between $D$ and $Y$ is what we’re testing, this
variable will appear in our regression:
$Y_i = \alpha + \delta D_i + \epsilon_i$. There are two reasons why $X$,
the IQ of the child, should *not* appear in the regression:

1.  Since we are trying to model the data generating process for $Y$,
    the right side of this equation should only contain factors that
    cause $Y$. Since our DAG shows $Y\rightarrow X$, we can safely omit
    $X$ from the equation.
2.  Suppose in reality, there is no relationship between $D$ and $Y$.
    Further, suppose $X$, the child’s IQ, is an average of $D$ and $Y$.
    If we know that a child’s IQ is 100, then at least one of their
    parent’s IQs must also be high. If we know the IQ of the high-IQ
    parent is 120, then we know the IQ of the other parent must be 80.
    If the IQ of the high-IQ parent increases to 130, then we know the
    IQ of the other parents must be 70. Controlling for the child’s IQ
    is creating a negative correlation between the two parents’ IQs out
    of thin air!

Let’s explore a simple example simulation of this setting.

``` r
N <- 100
# randomly generate IQs between 50 and 150:
D <- sample(50:150, N, T)
Y <- sample(50:150, N, T)
# Child's IQ is the average parents IQ
X <- (D + Y)/2

# Note: Y and D are random
#   there is no correlation between them!
# Run the code and then try including X
r <- lm(Y ~ D)
summary(r)
```

For another example, take a look at this discussion about [Beauty and
Talent from The
Mixtape](https://mixtape.scunning.com/03-directed_acyclical_graphs#sample-selection-and-collider-bias).

<!-- In summary, conditioning on a variable along a collider path will open a backdoor and bias your coefficient! -->

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/PJMVtpogJnQ?si=cK3hEJze2LmeUFOT" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Headaches

For practice, let’s build a DAG to explain headaches and to study the
effects of taking medicine. The variables and paths we’ll consider are
as follows:

- We are testing the effects of medicine on headaches.
- Being sick causes headaches.
- Being sick also causes taking medicine.
- Rain causes sickness.
- Drinking the night before causes headaches.
- Being sick causes doctor visits.
- Doctor visits cause taking medicine.

Drawing this setting out in a DAG would look like the following:

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  Headache[outcome]
  Medicine [exposure]
  
  Medicine -> Headache
  Sick -> Headache
  Sick -> Medicine
  Rain -> Sick
  Drinking -> Headache
  Headache -> Doctor
  Sick -> Doctor
  Sick -> Medicine
}")
plot(dag)
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module01_img/01-03-unnamed-chunk-13-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

Let’s check on the paths of this DAG.

<details class="code-fold">
<summary>Code</summary>

``` r
p <- dagitty::paths(dag)
as.data.frame(p)
```

</details>

<details>

<summary>

Output
</summary>

                                       paths  open
    1                   Medicine -> Headache  TRUE
    2 Medicine <- Sick -> Doctor <- Headache FALSE
    3           Medicine <- Sick -> Headache  TRUE

</details>

1.  The first path is the one we’re interested in.
2.  Second, we have a backdoor path that is in fact closed, due to
    `Doctor` being a collider.
3.  The final path is an open backdoor that we would need to close. We
    would do this by controlling for `Sick` in a regression.

An equation to *identify* the impact of medicine on headaches would be
the following:

$$\text{Headache}_{i} = \alpha + \delta \times \text{Medicine}_{i} + \beta\times \text{Sick}_i + \epsilon_i$$

Here, our estimate of $\delta$, $\widehat{\delta}$, can be interpreted
as causal.

## Future Modules

In upcoming modules, we will be exposed to different research designs
(like RCTs) that leverage variation in observational data to identify
causal effects. Each of these designs rely on their own sets of
assumptions, data, etc. Causal inference is the umbrella term for these
designs, and it’s become a rapidly growing field.

[^1]: Of course, differences could also be due to random chance, but
    this is why we use hypothesis tests and/or confidence intervals

[^2]: Is drinking before bed the only confounder? Maybe. Maybe not.
    Ultimately, this is something you would have to argue in your
    writing.

[^3]: In economics, this is usually called *ability* or something
    similar. I am using IQ since it’s a bit more quantifiable albeit
    imprecise at best and pseudo-science at worst.
