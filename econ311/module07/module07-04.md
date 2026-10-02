# Fixed Effects and the Limits of Regression
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## From Estimation to Identification

Over the last seven modules, you have learned how to *estimate*
regressions, *interpret* their coefficients, and *test* whether those
coefficients are different from zero. Estimation, interpretation, and
inference are the spelling and grammar of modern econometrics, and you
now have strong foundations in all three.

The next step is called **identification**: making the case that an
estimate reflects a *causal* relationship between $X$ and $Y$ rather
than just a *correlational* one. This is the last set of notes for ECON
311, so we are going to use it to ask a question that has been lurking
in the background of nearly every module: when does a regression
actually tell us that $X$ *causes* $Y$ (instead of the two just being
associated)?

To keep things simple, let’s use some notation that you will see again
in ECON 400. Let $D$ denote some **treatment**, which is just a variable
whose effect we care about (a basement, a new law, a college degree),
and let $Y$ denote the outcome. Our regression will look like the
following, where $\delta$ is the coefficient on the treatment:

$$Y_i = \alpha + \delta D_i + \epsilon_i$$

## Descriptive vs. Causal

A regression coefficient is always *descriptive*. In other words,
$\widehat{\delta}$ tells us how the average of $Y$ differs between units
that differ by one unit of $D$ (holding any controls constant) in *our
data*. Whether or not $\widehat{\delta}$ is also a *causal* effect
depends on whether the units with different values of $D$ are otherwise
comparable.

Consider the basement example from Module 6. These are two very
different statements:

- **Descriptive**: homes with basements sell for more than similar homes
  without basements.
- **Causal**: adding a basement to a house increases what that house
  sells for.

The first statement is something a regression can tell us. The second
one requires us to compare the *same* house with and without a basement.
Of course, we can never observe both versions of the same house (or
person, or state, or firm). A house either has a basement or it does
not! Instead, we use *other* houses as stand-ins, and this is only a
fair comparison if the houses with and without basements are alike in
every other way. In ECON 400, you will see this called the **fundamental
problem of causal inference**.

## Endogeneity

In Module 4.1, we said that exogenous variables are determined outside
of the system, which means they are as good as random. When the
treatment $D$ is as good as random, the only systematic difference
between the treated and untreated units is the treatment itself, and
$\widehat{\delta}$ can be interpreted as causal.

When $D$ is *not* as good as random, we say that it is **endogenous**.
More specifically, $D$ is endogenous when it is correlated with the
error term, $\epsilon$. Remember, the error term contains everything
that affects $Y$ that is not in our model. So, if something in the error
term moves along with $D$, then $\widehat{\delta}$ will pick up the
effect of that “something” in addition to the effect of $D$.

Back in Module 4.2, we discussed four reasons you might find a
correlation between two variables. The last of these was random chance,
which is why we use confidence intervals and hypothesis tests. The other
three, plus one more from Module 7.2, are the main ways endogeneity
shows up in practice:

1.  **Omitted variables.** A **confounding variable** is something that
    causes both the outcome *and* the treatment. If we leave it out of
    the model, it ends up in $\epsilon$ and biases $\widehat{\delta}$.
    We spent Module 5.3 on this, and we even learned how to use the
    omitted variable bias table to figure out the *direction* of the
    bias. In Module 4.2, we saw that younger people are less likely to
    use sunscreen *and* more likely to get sunburned, so age is a
    confounder. The fix is to control for the confounder, but we can
    only control for things we can measure.
2.  **Reverse causality.** Sometimes $Y$ causes $D$ rather than the
    other way around. In the sunscreen example, if people only apply
    sunscreen after they’ve been burned, it will look like sunscreen
    *causes* sunburns. In this case, $\widehat{\delta}$ is a mix of the
    effect of $D$ on $Y$ and the effect of $Y$ on $D$, and there is no
    variable we can add to the regression to separate the two.
3.  **Selection.** Sometimes the units that end up treated *select* into
    treatment for reasons related to the outcome. For example, suppose
    we compare the SAT scores of students who took a prep course to
    those who did not. The students who sign up for prep courses are
    probably more motivated, and motivated students would likely score
    higher even without the course. Comparing the two groups would
    overstate the effect of the course. Selection is closely related to
    omitted variables (motivation is the confounder here), but it is
    worth naming separately because it is so common whenever people,
    firms, or states *choose* their treatment. A related problem is
    *sample* selection, where we only observe units that made it into
    our data. For example, in the Toronto arrests data, we only observe
    people who were arrested.
4.  **Measurement error in $X$.** In Module 7.2, we saw that random
    measurement error in an explanatory variable pushes its coefficient
    towards zero. This is another way for $\widehat{\delta}$ to differ
    from the true effect.

Here is a summary of these problems:

| Problem | What goes wrong | Example | Do controls fix it? |
|:---|:---|:---|:---|
| Omitted variable | A confounder is in $\epsilon$ and moves with $D$ | Age, sunscreen use, and sunburns | Yes, if we can measure the confounder |
| Reverse causality | $Y$ also affects $D$ | Sunscreen use after a sunburn | No |
| Selection | Units select into $D$ for reasons related to $Y$ | Motivated students and SAT prep | Only for the reasons we can measure |
| Measurement error in $X$ | $D$ is noisy, which biases $\widehat{\delta}$ towards zero | Misreported income | No |

Sources of Endogeneity {.table-striped}

Since this table is condensed, let’s walk through each row. In each
example, the treatment $D$ is something we care about (like sunscreen
use) and the outcome $Y$ is what it might affect (like sunburns).

**Omitted variable: age, sunscreen use, and sunburns.** Younger people
tend to use less sunscreen, and they also tend to spend more time in the
sun, which means more sunburns. If we leave age out of the model, we
will compare sunscreen users (who are older, on average) to non-users
(who are younger, on average), and the older group would have fewer
sunburns even without sunscreen. This makes sunscreen look *more*
protective than it really is. Since we can measure age, we can control
for it, and this solves the problem.

**Reverse causality: sunscreen use after a sunburn.** Sunscreen can
prevent a sunburn, but past sunburns (or someone’s propensity to burn
while out in the sun) can also incentivize people to use sunscreen. In
other words, the relationship runs in *both* directions: people who burn
easily are the ones who buy more sunscreen. In the data, this can make
it look like sunscreen is ineffective, or even that it causes sunburns.
There is no control variable that can untangle which direction is which.

**Selection: motivated students and SAT prep.** Students *choose*
whether or not to take an SAT prep course, and the students who choose
to take one are probably more motivated, more prepared, and more willing
to work hard. Those traits would raise their SAT scores with or without
the course. So, if we compare students who took the course to those who
didn’t, we would give the course credit for what motivation is doing. We
can control for the things we measure (like GPA), but we cannot fully
control for motivation, since we cannot measure it directly.

**Measurement error in $X$: misreported income.** Suppose we want to
know how income affects some outcome, but people round or misreport
their income when they answer a survey. The mistakes in reported income
have nothing to do with the outcome, so they act like noise that hides
the relationship. As we saw in Module 7.2, this noise pulls the
coefficient towards zero, so income will look *less* important than it
really is. Adding control variables does not remove the noise, because
the problem is with the variable itself.

## What Fixed Effects Can (and Can’t) Do

In Module 7.1, we added county and year fixed effects to our model of
housing prices and personal income, and the coefficient fell by a large
amount. Fixed effects help with omitted variables because they control
for *anything* that is constant within a county (amenities, geography,
etc.) and *anything* that hits every county in a given year (the 2007
financial crisis, interest rates, etc.).

However, fixed effects only help with a specific kind of confounder.
They do not control for factors that change over time *within* a county.
For example, suppose a new shipbuilding contract is awarded to Newport
News in one year. This would raise incomes *and* housing prices in
Newport News in that year, but not in the other counties. Neither the
county dummies nor the year dummies can absorb this, so it is still a
confounder. Fixed effects also say nothing about the direction of
causality: higher incomes could raise housing prices, but higher housing
prices could also raise incomes. So, even with fixed effects, our
estimate is probably still better described as *descriptive* than
*causal*, although it is certainly a better description than the model
without fixed effects.

## Making a Causal Claim

To interpret $\widehat{\delta}$ as causal, you need to be able to argue
the following:

- After controlling for what you can, *nothing else* that affects $Y$
  moves with $D$ (no omitted variables and no selection).
- $Y$ does not also cause $D$ (no reverse causality).
- $D$ is measured well enough, and the sample is not distorted, so that
  we are not fooling ourselves.

Your argument for why the variation in $D$ in your data is as good as
random is called your **identification strategy**. Most of these
assumptions cannot be tested using the data, which means you have to
defend them using theory, institutional knowledge, and evidence.

The cleanest identification strategy is to *randomize* the treatment,
which is called a randomized controlled trial (RCT). As we saw in Module
4.2, randomization rules out reverse causality and confounders (and
selection) all at once. Unfortunately, RCTs are often impossible. We
cannot randomly assign minimum wages to states, or randomly give people
college degrees. When we cannot run an RCT, we have to rely on *natural
experiments*, where something in the world creates variation in $D$ that
is plausibly as good as random.

Here are some questions that you can ask of any regression you read (or
write):

1.  What is the treatment, and what is the outcome?
2.  Who gets treated, and why? Could they have selected into treatment
    for reasons related to the outcome?
3.  What else moves with the treatment and also affects the outcome?
    Which of these things are controlled for? Which are not?
4.  Could the outcome cause the treatment?
5.  How well is the treatment measured?

If you can’t answer these, you cannot (yet) interpret your coefficient
as causal. This is exactly the type of thinking that your final homework
asks you to practice.

## Where ECON 400 Picks Up

Everything in this course has been about getting the *estimation* right.
ECON 400 picks up exactly here, and it is entirely about identification.
You will learn how to draw causal diagrams (directed acyclic graphs, or
DAGs) to think through confounders, and you will learn research designs
built to take advantage of natural experiments:
difference-in-differences, matching, synthetic control, instrumental
variables, and regression discontinuity. You will also keep using fixed
effects, but with a faster tool: `feols()` from the `fixest` package,
which handles fixed effects with a `|` in the formula.

Each of these designs is a different answer to the same question we
asked in this module: how can we argue that the variation in our
treatment is as good as random?
