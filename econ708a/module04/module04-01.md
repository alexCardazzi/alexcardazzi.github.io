# Instrumental Variables
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ708a.html"
target="_blank">alexcardazzi.github.io</a>.

## A New Approach

So far, we’ve discussed randomized control trials,
differences-in-differences, and synthetic control, as well as the idea
of controlling for observable characteristics. RCTs are the gold
standard of causal inference due to their ensuring random treatment
assignment. DiD uses changes in a control group to estimate how the
treatment group would have changed in the absence of treatment.
Controlling for observable characteristics tries to balance the two
samples so that treatment appears to be randomly assigned between
groups. Synthetic control, as previously mentioned, tries to split the
difference between this idea and DiD by building a weighted synthetic
comparison unit to estimate counterfactual changes. While these methods
are all theoretically different, they are very similar in spirit.

The next method we’ll introduce is called **Instrumental Variables** (or
IV for short), and it addresses the identification problem in a way that
is different altogether. The idea behind IV is to find something that
randomly, even if only partially, determines treatment status. Then, we
can use this variable (called an *instrument*) to predict treatment
status. By predicting (or *instrumenting for*) treatment using this
variable, we are effectively eliminating the variation from all the
other “stuff” in our setting. Admittedly, this will probably be the most
difficult causal inference approach to wrap your head around, so let’s
dive into an example.

## Military Experience

Let’s ask a simple, albeit big, question: **how does military experience
effect civilian earnings?** Suppose we have individual-level data
containing information about one’s military experience and their
earnings. This question was famously addressed by Joshua Angrist in his
1990 article [“Lifetime Earnings and the Vietnam Era Draft Lottery:
Evidence from Social Security Administrative
Records”](https://www.jstor.org/stable/2006669)

Before addressing IV, let’s think backwards through the causal inference
approaches we’ve already learned.

Right off the bat, we can write off synthetic control since this isn’t a
case study.

Second, let’s think about comparing people who look alike on paper. We
can certainly find two individuals from the same high school, who got
the same grades, were exposed to the same environments, but one opted
for the military and one did not. Would these two individuals be good
counterfactuals for one another? In terms of observable characteristics,
yes! However, could there be something unobservable that is driving the
decision to enlist while also causing future earnings? Almost
definitely. Individuals who join the military, even when observationally
similar to others, often have very different personalities/circumstances
than civilians. For example, maybe these people are more aggressive
and/or have a more intense work ethic. These characteristics are almost
never observable let alone measureable/quantifiable. So, if we only
compare people who look alike on paper, we’d probably be unable to
account for personality/circumstances/etc., making this approach
unsuitable.

Next, we have difference-in-differences. If we’ve already convinced
ourselves that personality is a confounder we can’t control for, we can
probably rule out parallel trends as well. At this point, you might feel
a little stuck or like this question is unanswerable.

Let’s think about what the DAG for this setting would look like. In
reality, this is a very complicated relationship, but what we’re really
concerned about is some backdoor, such as personality, between military
experience and civilian earnings. Drawing a simple DAG for this would
look like the following:

<details class="code-fold">
<summary>Code</summary>

``` r
library("dagitty")
dag <- dagitty("dag {
  Military [exposure]
  Earnings [outcome]
  
  Military -> Earnings
  Military <- Personality -> Earnings
}")
plot(dag)
```

</details>

<details open>

<summary>

Plot
</summary>

<img src="module04_img/04-01-unnamed-chunk-3-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

If we could control for personality, we wouldn’t have a problem. Without
being able to observe/quantify it, though, we don’t have many options.
The only way to identify the causal effect would be through
randomization. Ethically/legally, can we implement an RCT to randomly
assign people to military service? Well… kind of. Military drafts
generate random variation in the probability that someone serves in the
military.

This is not *really* an RCT, though. First off, the military only drafts
people of certain observable characteristics. This is not really a
problem for us since we can control for this by subsetting our sample so
we are only looking at potential draftees. Second, and more importantly,
the draft does not perfectly determine who serves and who doesn’t.

In this setting, there are four “types” of people:

1.  **Compliers**: Individuals whose choice will be determined by their
    draft status. If they’re drafted, they’ll serve. If they’re not
    drafted, they’ll remain a civilian.
2.  **Always Takers**: Individuals who enlist in the military regardless
    of whether they get drafted (e.g. people who want to be in the
    military).
3.  **Never Takers**: Individuals who never enlist regardless of whether
    they get drafted (e.g. conscientious objectors).
4.  **Defiers**: Individuals who refuse to serve if drafted, but enlist
    if not drafted. These people are the opposite of compliers.

<div class="aside">

Of course, these types of people/units exist in all settings, but it’s
especially important to discuss them when talking about IV. We’ll
revisit this later in the module.

</div>

Hopefully, it’s clear that the draft introduces some element of
randomization. However, given these different groups, it may not be as
clear how to use it for identifying the causal link between military
service and civilian earnings. This is where instrumental variables come
in. Let’s redraw the dag to identify the paths in this setting, but also
include draft status as a factor.

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  Military [exposure]
  Earnings [outcome]
  
  Draft -> Military -> Earnings
  Military <- Personality -> Earnings
}")
plot(dag)
paths(dag)
```

</details>

<details>

<summary>

Output
</summary>

    $paths
    [1] "Military -> Earnings"                "Military <- Personality -> Earnings"

    $open
    [1] TRUE TRUE

</details>

<details open>

<summary>

Plot
</summary>

<img src="module04_img/04-01-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

Of course, adding this factor onto the end of the DAG does not modify
the already existing paths. However, we can use the
`instrumentalVariables()` function to give us a list of possible
instruments.

``` r
instrumentalVariables(dag)
```

<details open>

<summary>

Output
</summary>

     Draft

</details>

We are going to predict, or *instrument*, military enlistment using
draft status, which creates a nifty loophole for us. There are two ways
of thinking about this. First, by using the draft to predict military
enlistment, we force the treatment to only vary when draft status
varies. This will shut down the influence personality has on military
enlistment. On the other hand, you can think of this as shifting
treatment from military to draft. Initially, we had the backdoor path
$\text{Military} \leftarrow \text{Personality} \rightarrow \text{Earnings}$.
However, by shifting the treatment to $\text{Draft}$, we create a
collider along this path:
$\text{Draft} \rightarrow \text{Military} \leftarrow \text{Personality} \rightarrow \text{Earnings}$.

To interpret our estimate as the causal effect of military on earnings,
we need to make sure there’s no other paths from our instrument (draft)
to our outcome (earnings). Let’s suppose there were two other factors,
“something” and “else”, that both caused earnings. Further, suppose
“something” also causes draft status but “else” is caused by draft
status. What would this DAG look like?

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  Military [exposure]
  Earnings [outcome]
  
  Draft -> Military -> Earnings
  Military <- Personality -> Earnings
  Draft <- Something -> Earnings
  Draft -> Else -> Earnings
}")
plot(dag)
paths(dag)
```

</details>

<details>

<summary>

Output
</summary>

    $paths
    [1] "Military -> Earnings"                      
    [2] "Military <- Draft -> Else -> Earnings"     
    [3] "Military <- Draft <- Something -> Earnings"
    [4] "Military <- Personality -> Earnings"       

    $open
    [1] TRUE TRUE TRUE TRUE

</details>

<details open>

<summary>

Plot
</summary>

<img src="module04_img/04-01-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Directed Acyclic Graph" />

</details>

What does `dagitty` suggest for us now when we ask for instruments?

``` r
instrumentalVariables(dag)
```

<details open>

<summary>

Output
</summary>

     Draft |  Else, Something

</details>

It still tells us that draft is a candidate for an instrument, but it
also requires us to control for “something” and “else” as well. To see
why this is, let’s keep thinking about this as switching our treatment
from military to draft. The paths in this case would look like the
following:

<details class="code-fold">
<summary>Code</summary>

``` r
dag <- dagitty("dag {
  Draft [exposure]
  Earnings [outcome]
  
  Draft -> Military -> Earnings
  Military <- Personality -> Earnings
  Draft <- Something -> Earnings
  Draft -> Else -> Earnings
}")
as.data.frame(paths(dag))
```

</details>

<details open>

<summary>

Output
</summary>

                                             paths  open
    1                    Draft -> Else -> Earnings  TRUE
    2                Draft -> Military -> Earnings  TRUE
    3 Draft -> Military <- Personality -> Earnings FALSE
    4               Draft <- Something -> Earnings  TRUE

</details>

Right off the bat, the last path should set off some alarms. This is an
open backdoor path, so we definitely need to control for “something” to
close it. The third path is already closed because it’s a collider, so
we don’t want to touch this path. With two paths left, we need to think
carefully about whether we need to control for anything more. The second
path is the effect of draft status on earnings, through military
enlistment. This is the path we care about, so we want to keep this path
open. Finally, we’re left with the first path. If we leave this path
open, we would estimate the *total* effect of draft status on earnings.
This would be some combination of “else” and military enlistment. Of
course, we’re not interested in “else”, so by controlling for it, we’re
allowing draft to effect earnings but *only* through the influence of
military enlistment. Essentially, we’re purging military enlistment of
all the “bad” variation we weren’t interested in.

## IV Math

What does instrumental variable estimation look like? The process is
fairly simple in words, but there are a few steps.

1.  Select a candidate instrument.
2.  Model the treatment using the instrument. This is called the **first
    stage**.
3.  Check if the instrument does a “good job” explaining treatment.
4.  Use the model to generate predicted treatment values.
5.  Model the outcome using the predicted treatment values. This is
    called the **second stage**.

This two-step procedure is called <u>**two-stage least squares**</u> (or
2SLS).

For a section labeled “math”, these steps probably seem pretty wordy and
vague. They’re supposed to. Linear regression is just one way of fitting
models and generating predictions. You can use other methods like neural
networks, random forests, etc., but these methods are outside the scope
of this course. Still, you should be aware that even though I am going
to use OLS, and most economists will use OLS, there are other methods of
estimation.

So, what does the regression look like?

Typically, you’ll see four models when people present IV estimates.
First, people present the endogenous regression results for a baseline.
Next, you might see what people call “reduced form” equations, where the
instrumental variable is substituted into the endogenous equation for
the endogenous treatment variable. Finally, you’ll have the first stage
and the second stage regression equations. Below, I have put each of
these regressions in a table where $Y$ represents the outcome, $D$
represents the treatment, $Z$ represents the instrument, and $X$
represents control variables.

| Type | Equation |
|----|:--:|
| Naïve / Endogenous | $Y_i = \alpha_1 + \beta D_i + \omega_1 X_i + e_i$ |
| Reduced Form | $Y_i = \alpha_2 + \gamma Z_i + \omega_2 X_i + e_i$ |
| First-Stage | $D_i = \alpha_3 + \rho Z_i + \omega_3 X_i + e_i$ |
| Second-Stage | $Y_i = \alpha_4 + \delta \widehat{D_i} + \omega_4 X_i + e_i$ |

Types of Equations

We won’t focus too much on the first equation, since we know that this
will produce biased coefficient estimates. Rather, we’ll discuss the
next three.

The second equation in this table is the reduced form equation. This
quantifies the effect of the instrument on the outcome. If the only open
path from instrument to outcome is through the treatment, then this, in
a way, can be thought of as causal. So what’s the issue? Well, $\gamma$
is interpreted in the units of $Z$ instead of $D$, which is not what
we’re interested in. Note, $\gamma = \partial Y / \partial Z$.

So, what we need to do is translate $Z$ into $D$’s units. We can do that
by estimating the first-stage regression. In this case,
$\rho = \partial D / \partial Z$. Once we have this estimated model, we
can use it to predict $\widehat{D_i}$ for each $Z_i$. This is just a
prediction from a regression, like the fitted values you have seen
before. This step allows us to re-estimate the reduced form equation
with our new $\widehat{D}$ instead of $Z$, so we have the proper units.
This will generate $\delta$, our causal effect, which is equal to
$\gamma / \rho = \frac{\partial Y / \partial Z}{\partial D / \partial Z}$.

## IV Code

Now that we’ve seen some of the math, let’s code up a quick simulation.

<div class="aside">

Feel free to experiment with these parameters if you’d like.

</div>

``` r
N <- 200
draft <- sample(0:1, N, TRUE)
personality <- sample(0:1, N, TRUE)
# substract draft*personality to remove double counting
military <- draft + personality - draft*personality
earnings <- 3*military - 4*personality
df <- data.frame(earnings = earnings, military = military,
                 draft = draft, personality = personality)
```

- `draft` and `personality` are determined by random number generators.
- `military` is equal to 1 either if `draft == 1` or `personality == 1`.
- `earnings` is equal to `3*military - 4*personality`.

``` r
# Tabulate draft status and military experience:
table(draft = ,
      military = )
```

Notice how everyone who got drafted joined the military, though not all
people who joined the military got drafted. This should be a clear
indicator that draft status is not the only thing causing people to join
the military. In terms of estimation, if we could observe this other
factor (i.e. `personality`), we could control for it, and our estimate
on `military` would be causal. Let’s demonstrate it.

``` r
# Estimate "true" model
lm( ~ , df)
```

We find a treatment effect of 3, which is what we specified in the
simulation, so this works! However, we cannot typically observe/quantify
personality, which presents a problem for us. What if we estimated the
regression without it?

``` r
# Estimate naive model without personality
lm( ~ , df)
```

Without controlling for personality, our coefficient is biased
downwards. This is because personality is negatively associated with
earnings but positively associated with military. See [this discussion
about omitted variable
bias](https://alexcardazzi.github.io/econ311/module05/module05-03.html#omitted-variables)
for more information.

Let’s try incorporating our instrument by replace military with draft.

``` r
# Estimate reduced form with draft instead of military
lm( ~ , df)
```

The generated output represents our reduced form estimates. Again, this
just means we’re skipping steps and throwing our instrument into the
regression with our outcome of interest. While you will often see this
equation estimated, it is not *really* part of the IV algorithm.

Now, to estimate the IV model, we have to begin with our first stage.

``` r
# Estimate first-stage where
#   military is explained by draft
lm( ~ , df)
# Save this regression as an object and
#   use summary() to explore the output!
```

Let’s think about this now. We get a coefficient of about 0.5. This
suggests that being drafted increases the probability of enlisting in
the military by 50%. Cool – it seems like our instrument is doing a good
job at predicting our treatment. In just a minute, we’ll formalize
“doing a good job”, but for now, let’s take it at face value.

Our next step is to get the fitted values of military. Then, we will
substitute *this* variable into the regression for our “real” treatment.

Finally, estimate the parameters of the second stage:

``` r
stage1 <- lm(military ~ draft, df)
df$military_fitted <- stage1$fitted.values
# Estimate second stage
lm( ~ , df)
# Save this regression as an object and
#   use summary() to explore the output!
```

Nice! Our slope coefficient is pretty close to 3, which is the true
effect we established in the simulated data.

As a note, here’s why the reduced form estimates are interesting. As
mentioned, if we take the ratio of $\frac{\partial Y}{\partial Z}$ and
$\frac{\partial X}{\partial Z}$, we will get the second stage
coefficient. Let’s test this out:

``` r
rf <- lm(earnings ~ draft, df) # dy/dz
stage1 <- lm(military ~ draft, df) # dx/dz

rf$coefficients[2] / stage1$coefficients[2]
```

## Assumptions

Great! Now we can mechanically estimate IV parameters! Well, wait a
minute… there are a few things I skipped in this discussion. First, we
still have assumptions that we need to be wary of. Second, there are
some further technical considerations that we’ll touch on in the
following sections.

Let’s discuss the assumptions in IV. There are three main ones.

**Relevancy**: The instrument must to be a good predictor of the
treatment. Otherwise, there won’t be enough variation in the fitted
treatment values to explain the outcome. When this happens, we have a
*weak* instrument. Conveniently, this assumption is directly testable!
To test if our instrument is relevant, we can calculate an F-statistic
that indicates the strength of the instrument. As a rule of thumb, the
F-statistic must be greater than 10 for relevancy. Of course, larger
F-stats are preferred! In fact, there has been some discussion in
[recent literature](https://arxiv.org/pdf/2010.05058.pdf) that says this
rule of thumb is too lenient. For this class, we will use this rule of
thumb, but be aware that this might be fairly liberal. In addition, it
always helps to have good theoretical explanations for why your
instrument is relevant.

**Excludability**: The instrument must only impact the outcome through
the influence of the treatment. This is why we needed to control for
“something” and “else” earlier in the notes. Unfortunately, this
assumption is inherently untestable and similar in spirit to that of the
parallel trends assumption in DiD or the assumption that there are no
confounding variables you’ve missed when controlling for observables. If
you are going to use IV, you have to be ready to argue that your
instrument is excludable until you’re blue in the face.

<div class="aside">

In my personal experience, people (i.e. the endogeneity-police) **love**
to attack the excludability assumption when someone uses an IV. While it
is as equally untestable as some DiD or PSM assumptions, it gets much
more scrutiny for whatever reason.

</div>

**Monotonicity**: Basically, this assumes away the existence of defiers
in the sample. In other words, as the value of the instrument grows, it
can only increase the probability (or intensity) of treatment. In terms
of the military draft, we assume that being drafted can *only* increase
(or at least not decrease) the chance that you enlist in the military.

## Revisiting IV Code

In our last code section, we estimated a bunch of different models,
mashed them all together, and our causal effect magically appeared from
the rubble. Luckily, our handy-dandy `fixest` package provides an easy
way to estimate IV models:

<center>

`feols(Y ~ X1 + X2 | FE1 + FE2 | D ~ Z, data)`
</center>

where `Y` represents the outcome, `X1` and `X2` represent any controls
you might have, `FE1` and `FE2` represent fixed effects, `D` represents
the (endogenous) treatment variable, and `Z` represents the instrument.
If you do not have fixed effects or controls, you can use `1` in place
of `X` or `FE`.

By default, this will return the second-stage regression. However, if
you want to access the first-stage (and your regression object is called
`r_iv`), you can use: `r_iv$iv_first_stage`.

To test for relevancy, we must generate an F-statistic. There are many
variations of F-statistics that people tend to use that are robust to
different things, but these are outside the scope of this course.
Rather, we are going to focus on just two: the standard one with no
bells or whistles, and the Wald F-statistic. The differences between
these is that the standard one assumes your errors are homoskedastic.
This may or may not true, and the Wald statistic will adjust depending
on your standard error correction. To generate these, we can use the
following code: `fitstat(r_iv, type = "ivf")` or
`fitstat(r_iv, type = "ivwald")`.

<div class="aside">

A particularly famous test for weak instruments is the Anderson-Rubin
test. This generates an F-statistic *and* coefficient confidence
intervals that are robust to weak instruments. Unfortunately, `fixest`
does not contain a test for this, but the `ivDiag` package (which is
built on top of `fixest`) contains a function for this called `AR_test`.
Using this is straight forward:
`AR_test(df, "earnings", "military", "draft", controls = NULL)`

</div>

Try experimenting with some of this below:

``` r
library("fixest")
r_iv <- feols()
r_iv$iv_first_stage
cat("\n##### ##### #####\n\n")
r_iv
```

You might notice a difference in this regression output compared to our
step-by-step one. You’ll likely notice that the standard errors in the
second stage estimation are a bit larger. This is because the first
stage fitted values themselves come from a regression, so we need to
build in the first model’s uncertainty when calculating the second
model’s uncertainty. Thankfully, `fixest` does this for us, so we don’t
need to get into the nitty gritty, but you should be aware of this. IV
estimation is unbiased but less efficient (larger variance) than OLS
estimation.

## Treatment Effects

Now that we’ve become familiar with a few different causal inference
methods, it’s important to talk about the different types of treatment
effects. There are a few different ones, and you will probably see
different names for some of these when Googling, but these are the basic
ideas. As I see it, There are four main treatment effect types.

Before even getting to the four, we need to think about what we’re even
doing with causal inference. We need to think about *individual
treatment effects*. That is, everyone has some potential outcome when
they’re treated ($Y^1$) and some potential outcome when they’re not
($Y^0$). Therefore, the individual treatment effect can be thought of as
the difference of these two potential outcomes.

If we wanted to summarize the distribution of individual treatment
effects, we could take an average. This would give us our first type of
treatment effect: the **Average Treatment Effect** (ATE).

However, due to the fundamental issue of causal inference, we cannot
observe both potential outcomes for each (or any) individual, making the
average impossible to calculate. Instead, we have to settle for the
**Average Treatment on the Treated** (ATT). The ATT is estimated by
subtracting the average potential outcome for control units from the
average potential outcome for the treated units. Of course, under
randomization, the ATT = the ATE. Without randomization, we need a
combination of controls and assumptions.

Sometimes, we aren’t able to observe the true treatment status for
individuals. Rather, we can only observe assignment to treatment. In
fact, this is probably the most common situation. In the case of
enlistment, earnings, and the draft, being drafted is similar to being
assigned to treatment. Thinking back to the DiD module where the minimum
wage was changed for some restaurants but not others, this law change
represents being assigned to treatment. However, if a restaurant was
already paying above the new minimum wage, the treatment should not
really impact them. The same thing could be said for someone already
planning on enlisting in the military being drafted. The difference in
potential outcomes conditional on treatment assignment, and not
treatment status, is called **Intent to Treat** (ITT).

[Imbens and Angrist (1994)](https://doi.org/10.2307/2951620) discuss a
fourth types of treatment effect they call the **Local Average Treatment
Effect** (LATE). In the paper, the authors write about [Angrist
(1990)](https://www.jstor.org/stable/2006669).

<details>

<summary>

Quote from [Imbens and Angrist (1994)](https://doi.org/10.2307/2951620)
</summary>

> Angrist (1990) uses the Vietnam-era draft lottery to estimate the
> effect of veteran status on earnings. The instrument is the draft
> lottery number, randomly assigned to date of birth and used to
> determine priority for military conscription. The average probability
> of serving in the military falls with lottery number. Condition 1
> \[*excludability*\] requires that potential earnings with and without
> military service be independent of the lottery number. This is a
> standard IV assumption which would be violated if, for example,
> lottery numbers are related to earnings through some variable other
> than veteran status. Condition 2 \[*monotonicity*\] requires that
> someone who would serve in the military with lottery number k would
> also serve in the military with lottery number 1 less than k, which
> seems plausible. … **The average effect of veteran status identified
> under Theorem 1 is for men who would have served with a low lottery
> number, but not with a high lottery number.** \[emphasis my own\]

</details>

The idea is that LATE is the effect of treatment on specifically the
compliers. We can also think of this as the ITT divided by the change in
probability of treatment for a one unit change in the instrument. If
this isn’t ringing any bells, let me put it differently with a bit of
math: the ITT is $\frac{\partial Y}{\partial Z}$, and the change in
probability of treatment is $\frac{\partial X}{\partial Z}$. Look
familiar? This is exactly the IV estimate we found above:
$\frac{\partial Y}{\partial Z} / \frac{\partial X}{\partial Z}$!

## IV in Reality

Alright, so now that we’ve gone through this example ad nauseam, what
are the real findings in [Angrist
(1990)](https://www.jstor.org/stable/2006669)? In short, the study finds
earnings reductions of about 15% for (white) veterans compared to
observationally similar nonveterans. Remember, this is a LATE, not an
ATE. So, what does this tell us? If the government wanted to compensate
drafted individuals who would not have served in the military had they
not been drafted, the LATE is more in line with what they’d be
interested in.

Of course, not every researcher can get social security data and a clean
military draft for an instrument. Good instruments usually feel like
they were pulled out of thin air. In order to satisfy the exclusion
restriction, you often need something a bit off the wall. I encourage
students to read (the short) [Section 7.2.2 in The
Mixtape](https://mixtape.scunning.com/07-instrumental_variables#good-instruments-should-feel-weird)
for a good discussion of “weird” instruments.

What are some examples of weird instruments used by other economists?
There are plenty, but I will only mention a few here in addition to some
canonical, or popular, instrumental variable designs.

[Levitt
(2002)](https://pricetheory.uchicago.edu/levitt/Papers/LevittUsingElectoralCycles2002.pdf)
investigates the casual effect of police on crime. At that point in the
literature, it was a challenge to disentangle the effects of police on
crime. Of course, as crime increases, a city might feel the need to
increase their police presence. So, crime causes police and police cause
crime. Clearly, there’s some weird dynamics going on, so we need a way
to isolate, or identify, the cause of police on crime. Levitt used the
number of fire fighters in a city to instrument for the number of police
officers. These two things should be related due to variation in city
budgets, citizen preferences, etc. However, the number of fire fighters
should not be related to crime in any material way. Levitt found that an
increase in the number of police reduced the amount of crime, which at
the time, was not the consensus in the literature as it is now.

[Dube and Harish
(2020)](http://odube.net/wp-content/uploads/2020/08/Queens_Dube_Harish.pdf)
look at the effects of female leadership on peace. The authors collected
data on European conflicts and monarchies between the years 1480 and
1913, where Queens ruled about 18% of the time. The authors use two
instruments to predict treatment (whether a female was in leadership).
The first instrument is a binary variable equal to 1 if the previous
leader’s first born child was male. The second instrument is a binary
variable equal to 1 if the previous leader had a sister. The findings
suggest that Queens, relative to Kings, had more aggressive war
policies. This is an interesting paper and worth a look.

[Gibson and Shrader (2018)](https://doi.org/10.1162/rest_a_00746)
examine the effect of sleep on earnings. Of course, sleep and earnings
are endogenously determined by personality, etc, which will bias the OLS
coefficient. The authors use time of sunset to instrument for the amount
of sleep individuals get. The findings suggest that an additional hour
of sleep can increase earnings by between 1 and 5%.

Some Canonical IVs:

- **Month of Birth, Relative Age**: [Angrist and Krueger
  (1992)](https://doi.org/10.2307/2290263)
- **Old Maps**: [Duranton and Turner
  (2011)](https://doi.org/10.1257/aer.101.6.2616); [Möller and Zierer
  (2018)](https://doi.org/10.1016/j.jue.2017.10.002)
- **Leniency Designs**: [Dobbie *et al.*
  (2018)](https://doi.org/10.1257/aer.20161503); [Dahl *et al.*
  (2014)](https://doi.org/10.1093/qje/qju019)
- **Bartik, Shift-Share Instruments**: [Breuer
  (2022)](https://doi.org/10.2308/JFR-2021-003)

For some further reading, check out the following two papers.

First, [Angrist and Krueger
(2001)](https://doi.org/10.1257/jep.15.4.69). The journal this paper is
published in, Journal of Economic Perspective, is very reader friendly.
The authors discuss all things IV with only words (no equations!). Note,
if you download this PDF, it will show as being almost 100 pages long.
In reality, it is only 15-ish pages and the other 75 pages contain
citations to articles that cite this one (i.e. it’s famous).

Second, for history fans, check out [Stock and Trebbi
(2003)](https://doi.org/10.1257/089533003769204416). This is probably
the best whodunit I’ve come across in economics. There was much debate
about who first used IV between a father and son. The authors do a great
job of explaining how they uncover who was the founder.

## Obligatory NHK Videos

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/bfQgLnFdLSg?si=mlToQCB0qfU_vn95" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/HL8jR9CDlfA?si=pVouzKr4VrcEpmvU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/DjLXqFTvVWg?si=iyWYT8jy4pCQ7Sui" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/B8MUeudCoWo?si=THo2JpJK7Ks87VnJ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/eoJUPd6104Q?si=31RKvQIzV2L9bwoy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>
