# Difference-in-Differences
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ708a.html"
target="_blank">alexcardazzi.github.io</a>.

## What is DiD?

<!-- https://matheusfacure.github.io/python-causality-handbook/01-Introduction-To-Causality.html -->

Difference-in-differences (DiD or diff-in-diff) is a quasi-experiential
research design that isolates casual treatment effects when treatment
and control groups can be observed before and after some treatment. DiD
is likely the most widely used causal inference design in economics and
other social science disciplines. This is due to its accessibility and
(relative) parsimony.

Rather than belabor some clunky, abstract definition, let’s jump right
into an example.

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ggYnrOGG97o?si=8dUZ_bRaHhsexLn4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Minimum Wage

One of the most famous topics in economics is the minimum wage. In Micro
101, we talk about how a price floor set above an equilibrium price will
cause an oversupply relative to demand. In the case of labor markets,
the price floor is the minimum wage and the oversupply is called
unemployment. In 1994, David Card & Alan Krueger published an infamous
paper in the *American Economic Review* that studied the minimum wage as
if it were an experiment. Read <a
href="https://mixtape.scunning.com/09-difference_in_differences#dd-and-the-minimum-wage"
data-preview-link="true">Section 9.2.3 in The Mixtape</a> for a great
discussion of [Card and Krueger
(1994)](https://davidcard.berkeley.edu/papers/njmin-aer.pdf).

In early 1992, Card and Krueger decided to survey fast food
establishments in New Jersey and Pennsylvania before and after a minimum
wage change impacted New Jersey in April of 1992. They surveyed these
stores in February and then again in November. They collected a lot of
data, but most importantly they asked about each store’s wage rate and
level of employment. The February survey was meant to serve as a
baseline and the November survey an update after the minimum wage.

The first time Card and Krueger surveyed these fast food establishments,
the distribution of wages seemed to be fairly similar. This makes sense
– these are bordering states likely catering to, and employing, a rather
homogenous population. The minimum wage in both states was also the same
(\$4.25) at the time. In April 1992, New Jersey increased its minimum
wage to \$5.05, which amounts to an increase of almost 20%. This forced
every store in NJ whose wages were previously below \$5.05 to
immediately jump up to this new threshold. Stores in neighboring PA
could remain at whatever wage they wanted so long as it was above the
original minimum wage of \$4.25.

Here is a visualization of the wage distributions in NJ and PA before
and after the minimum wage change:

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-3-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Distribution of minimum wage in both states before and after the policy." />

</details>

I’m guessing that with this figure alone, Card and Krueger have
convinced you that the minimum wage policy had some “bite” to it, or
more simply, it **mattered** for a lot of stores. This is important: if
the minimum wage did not really impact any stores, then the rest of the
study would be moot.[^1]

So – stores were forced to increase their wages – but what happened to
employment? In words, Card and Krueger’s theory is that if the minimum
wage change *hadn’t* happened, New Jersey’s employment *would have*
followed a trajectory similar to Pennsylvania. They used the change in
PA’s employment to predict, or infer, the (unobservable) counterfactual
NJ’s employment. Then, they could compare the “real” NJ to its
counterfactual. This is causal inference in a nutshell – creating a
counterfactual treatment group for comparison.

<!-- ([data](https://alexcardazzi.github.io/econ708a/data/fast-food-data.csv)).  -->

Let’s visualize Card and Krueger’s analysis before approaching it
econometrically. We’ll start by taking a look at the Feb. 1992 average
employment in both PA and NJ.

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-4-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Pre-period average employment for both states." />

</details>

For whatever reason, PA seems to have higher levels of employment. This
could be due to a multitude of reasons that aren’t all that important.
Card and Krueger are either going to **assume** that none of these
factors change from Feb. to Nov., or control for them explicitly in
their regression.

Next, let’s examine how PA’s employment evolved in the absence of
treatment. We are able to observe this since PA did not have a minimum
wage change.

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-5-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Evolution of PA&#39;s employment following the NJ law change." />

</details>

Employment fell in PA from Feb. to Nov. Once again, this could be for a
variety of reasons, but Card and Krueger are either going to **assume**
that these reasons can either be controlled for in a regression or they
impacted both PA and NJ simultaneously.

Card and Krueger use this change in PA to fill in the blanks for a
counterfactual New Jersey, which they cannot observe.

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-6-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Imputed counterfactual of NJ given the change in PA." />

</details>

This is another assumption that Card and Krueger are going to make,
which is called the **parallel trends** assumption. Card and Kreuger
assume that, in the absence of treatment, NJ would have followed the
exact path as PA.

What actually happened, though?

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-7-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Counterfactual vs actual outcome for NJ." />

</details>

Interestingly, the real NJ saw an increase in employment! Now, I want to
be very clear about the increase I’m talking about. Of course, NJ in
Nov. had higher employment than NJ in Feb. This is **not** the increase
I’m referring to. The increase I’m mentioning is the increase relative
to the counterfactual NJ, which is determined by the change in PA. In
other words, the difference in employment in NJ is *more positive* than
the difference in employment in PA. This is where the name
“difference-in-differences” comes from. So, even though NJ increased
from 20.7 to 21.1 ($\Delta = 0.4$), it was *supposed* to decrease by 1.9
($\Delta$ in PA). Therefore, NJ made up the drop **and more** for a
total effect of 2.3.

The treatment effect would therefore still be positive even if New
Jersey’s employment hadn’t increased from the previous level. See below
for an example.

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-8-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Difference in counterfactual and hypothetical outcome." />

</details>

This is still a positive, albeit smaller, treatment effect. Again, the
point is that the difference between the change in NJ and change in PA
is positive. In other words, NJ’s employment did not fall as much as
PA’s employment even though NJ was the one with the minimum wage change.

It’s important to note that we cannot just look at what happened in New
Jersey. Since this is observational data, there may have been things
that happened at the same time as our policy change that could have
influenced the employment measures. This is why we are using the changes
in PA – we are assuming that these changes reflect general fluctuations
in employment that are generalizable to New Jersey.

Similarly, we cannot just look at differences between NJ and PA after
the minimum wage hike. As we saw, PA had higher employment than NJ in
both periods. We don’t want to accidentally attribute the effect of the
minimum wage to pre-existing differences. This is why we need multiple
periods to remove time-invariant effects of being in PA or NJ.

## Assumptions

Before moving on to the econometrics, what assumptions are Card and
Krueger making, either implicitly or explicitly?

1.  First and foremost, they assume that NJ and PA would have moved in
    parallel if there were no minimum wage change. This assumption is
    crucial, because we use PA’s trajectory to inform us about NJ’s
    counterfactual. This is the **parallel trends** assumption. In
    potential outcomes notation, this says
    $E[Y_{post}(0) - Y_{pre}(0) | D=1] = E[Y_{post}(0) - Y_{pre}(0) | D=0]$:
    without treatment, the treated group’s average change in $Y$ would
    have equaled the control group’s.
2.  Second, Card and Krueger are assuming that NJ was unable to
    anticipate the upcoming minimum wage change. Or, perhaps a little
    more flexibly, NJ was able to anticipate the change but not act on
    it until the policy took effect. Is this a reasonable assumption?
    According to [this document from
    NJ](https://repo.njstatelib.org/bitstream/handle/10929.1/10162/L1990c18.pdf?sequence=1&isAllowed=y),
    the minimum wage increases were scheduled in 1990. So, since Card
    and Krueger interviewing these stores only a month or so before the
    law went into effect, the stores may have already adjusted their
    employment levels in anticipation of the change.
3.  Third, they are making a SUTVA (Stable Unit Treatment Values
    Assumption) assumption[^2]. The main point of SUTVA is that the
    outcomes of Unit $j$ are not impacted by the treatment status of
    Unit $i$. This assumption is also a bit tricky. If the wage of a
    nearby store has increased, it only makes sense for a) workers to
    prefer working at the higher paying store and b) the untreated
    stores increase their own wages to stay competitive.
4.  Finally, they are assuming that the treatment is not endogenous, or
    being caused by prior outcomes. What does this mean? In plain
    English, if law makers in NJ increased the minimum wage because of
    the trajectory of employment, this would violate the parallel trends
    assumption because the counterfactual group would have diverged
    anyway.

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/NcHT8pl57HY?si=_tfujicPwsbKEHNA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Estimation

To estimate a modification of the regression in Card and Krueger, we are
going to use a simplified version of their data. These data can be
downloaded
[here](https://alexcardazzi.github.io/econ708a/data/fast-food-data.csv).
Read the data into a data frame called `ff`.

``` r
## Read in data here:
ff <- 
```

Difference-in-differences equations typically control for differences in
treatment/control groups, differences in pre/post time periods, and the
combination of both. The equation we will estimate will look like the
following:

$$\text{FTE}_{ist} = \alpha + \gamma \text{Treat}_s + \lambda \text{Post}_t + \delta(\text{Treat}_s \times \text{Post}_t) + \epsilon_{ist}$$

<div class="columns">

<div class="column" width="50%">

<center>

PA Pre: $\alpha$

PA Post: $\alpha$ + $\lambda$

</center>

</div>

<div class="column" width="50%">

<center>

NJ Pre: $\alpha + \gamma$

NJ Post: $\alpha + \lambda + \lambda + \delta$

</center>

</div>

</div>

This implies:

- $\gamma$ is a measure of how the treatment group differs from the
  control group before treatment.
- $\lambda$ is a measure of how the control group changes following
  treatment.
- $\delta$ is the *difference* in the change in the treatment group
  relative to the control group.

In R, this equation can be translated into `treat + post + treat:post`
or simply `treat*post`. In `ff`, the variable for $\text{Treat}$ is `nj`
and the variable for $\text{Post}$ is `post`. The outcome variable is
`fte` for full-time employment. Estimate the model and display its
estimates with `summary()`.

``` r
did <- lm()
summary(did)
```

Your coefficients should look like the following:

<details>

<summary>

Output
</summary>

    α: 23.7         γ: -3.03        λ: -1.88        δ: 2.28

</details>

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/95HxUdn-BJg?si=nC9KD3Ayqod_jymj" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## MLB

Let’s switch gears and think about another application of
difference-in-differences. Unfortunately (or maybe fortunately), I
consider myself to be $\approx$ 50% sports economist. Sports are
convenient to think about because the rules are clear and well known,
employee (player) performance is nearly perfectly observable, labor
demand is fixed, etc. So, let me tell you a little bit about baseball.

Major League Baseball is organized in two leagues: the American League
(AL) and the National League (NL). This distinction is nearly entirely
for organizational purposes. For example, the New York Yankees are in
the AL and the New York Mets are in the NL. The Chicago White Sox are in
the AL and the Chicago Cubs are in the NL. Not all cities have two
teams, but the point is that it has nothing to do with geography, or
anything else, really. The only material difference between the two
leagues is a single rule difference.

Historically, pitchers are the worst hitters on their teams by a *lot*.
In 1973, the American League decided that enough was enough, and they
created the Designated Hitter (DH) position. This player stands in for
the pitcher when it is the pitcher’s time to bat. The stereotypical DH
is a big, burly player who hits a lot of home runs. DHs are often one of
the *best* hitters on their teams. So, the AL replaced their worst
hitter with possibily their best hitter. Of course, this instantly
increased offense in the AL. <a
href="https://en.wikipedia.org/wiki/Designated_hitter#Background_and_history"
data-preview-link="true">For whatever reason</a>, the NL voted against
the DH position and forced those poor pitchers to keep hitting.

Fast forward to 2021, and the MLB has started toying with some ideas
about how to improve the experience of watching baseball games.
Generally speaking, viewership numbers and fan engagement metrics have
been heading south for some time. One idea they had to reverse this
trend was to increase offensive production. Their hypothesis (and hope)
was that additional scoring would cause an increase in interest in
games.

In 2021, the MLB finally decided to allow the DH position in NL
games.[^3] Before we go any further, let’s think about what we have:
there are two groups of teams, AL and NL. One of them had a DH for the
entire time and the other one adopted the position at the start of the
2021 season. It sounds like we have treatment and control groups!

Usually, control groups in DiD settings are **never treated**. In this
setting, the control group (AL teams) are **always treated**. The
important part is that the treatment status of the control group **does
not change**.

Let’s approach this problem like Card and Krueger. Game-level
information for games during the 2013-2019 and 2021-2022 MLB seasons can
be downloaded
[here](https://alexcardazzi.github.io/econ708a/data/mlb_dh.csv). These
data have already been cleaned to remove games with no attendance
(i.e. the 2020 season and a few other outlier games), and we are left
with 22,011 observations. Some important variables are as follows:

- `tm` (`tm_o`): This is a variable identifying the home (away) team in
  the game.
- `nl`: This is a variable identifying if the home team is in the
  National League.
- `yr`: This variables denotes the season.
- `post`: This variable denotes the 2022 season.
- `score_home` (`score_away`): This variable tells the runs scored for
  the home (away) team.
- `ATT`: This variable contains the attendance at the game.

<!-- fixest::feols(c(ATT, log(ATT), att_cap, att_cap1, -->

<!--                 score_home, ou, I(score_away + score_home)) ~ nl:post -->

<!--               + wins + winpct -->

<!--               + wins_o + winpct_o -->

<!--               + pre_ou + pre_ou_o -->

<!--               + odds3 + odds_o3 -->

<!--               + I(odds3^2) + I(odds_o3^2) -->

<!--               + ml_home3 + I(ml_home3^2) -->

<!--               + rating_h + rating_a -->

<!--               + elo_h + elo_a -->

<!--               | yr + tm + tm^stadium_num + tm^tm_o + tm^month(date)^wday(date),# + tm[winpct], -->

<!--               data = all[lim,], -->

<!--               se = "twoway") -> r -->

Read the data into R and calculate the average home-team runs scored by
league before and after the DH rule was implemented. Use the
`aggregate()` function to do this.

``` r
mlb <- read.csv("https://alexcardazzi.github.io/econ708a/data/mlb_dh.csv")
# Use aggregate() here
```

Your output should match the following:

<details>

<summary>

Output
</summary>

      Period League Home_Score
    1   Post     AL   4.179487
    2    Pre     AL   4.566034
    3   Post     NL   4.453871
    4    Pre     NL   4.413892

</details>

Using `lm()` and `summary()`, estimate the following 2x2 DiD model:

$$\text{Runs} = \beta_0 + \beta_1\times\text{NL} + \beta_2\times\text{Post} + \beta_3\times(\text{NL}\times\text{Post}) + \epsilon$$

``` r
# Use lm() here
# Use summary() here
```

Your coefficients should match the following:

<details>

<summary>

Output
</summary>

    β.0: 4.57       β.1: -0.15      β.2: -0.39      β.3: 0.43

</details>

In this model, $\widehat{\beta}_3$ is our parameter of interest, or
*treatment effect*. This suggests that home-team offense increased in
the National League by 0.43 runs relative to expectation. We are relying
on our parallel trends assumption to say that teams in the National
League *would have* otherwise followed a trend similar to that of teams
in the American League if they had not been treated with Designated
Hitters. More concretely, in the American League, we estimate an average
change in runs of -0.39. We are assuming that, in the absence of the DH
rule change, offense in the National League would have also decreased by
0.39 runs.

Now that we have established an increase in offense, which was the point
of the DH rule change, what happened to attendance? Estimate the
following 2x2 DiD models:

$$\text{Attendance} = \alpha_0 + \alpha_1\text{NL} + \alpha_2\text{Post} + \alpha_3(\text{NL}\times\text{Post}) + \epsilon$$
$$log(\text{Attendance}) = \beta_0 + \beta_1\text{NL} + \beta_2\text{Post} + \beta_3(\text{NL}\times\text{Post}) + \epsilon$$

``` r
reg_level <- lm()
reg_log <- lm()
summary(reg_level)
summary(reg_log)
```

Interpreting $\widehat{\alpha}_3$, the DH rule increased attendance by
about 1,200 fans. $\widehat{\beta}_3$ suggests the DH rule increased
attendance by about 5.3%.

Perhaps there are some oddities in the data that are not representative
of the average MLB team. For example, the Houston Astros (`hou`) were
found guilty of cheating. This may have impacted their attendance
numbers due to fan distastes of cheating (or tastes for boo-ing). Also,
in the 2021 MLB season, the Toronto Blue Jays (`tor`) were not allowed
to play in their home stadium due to COVID restrictions in Canada that
did not exist in the US.

Re-estimate the regressions, but exclude games played by the Houston
Astros (home or away) and the 2021 Blue Jays from the sample.

``` r
exclude_logic <- 
# There are two ways to remove observations:
reg_level <- lm( , subset = exclude_logic)
reg_log <- lm( , data = mlb[exlude_logic,])
summary(reg_level)
summary(reg_log)
```

## Two-Way Fixed Effects

We can grant our model some flexibility by using fixed effects. For
example, the New York Yankees and Los Angeles Dodgers likely attract
larger crowds than, say, the Milwaukee Brewers, since the first two
teams play in the largest cities in the United States. To control for
this, we can use team fixed effects. This will remove all **time
invariant** factors associated with each individual team. Second, it
might just be that certain seasons had higher levels of attendance (or
offense) overall. For example, the MLB has been accused of using
[“juiced” baseballs](https://en.wikipedia.org/wiki/Juiced_ball_theory)
in the past. To address this, we can use year fixed effects. This will
control for all factors that are common to all teams within a season.

<!-- # lim <- mlb$tm != "hou" & mlb$tm_o != "hou" & !(mlb$yr %in% 2021 & mlb$tm == "tor") -->

<!-- # mlb$score_total <- mlb$score_home + mlb$score_away -->

<!-- # lm(score_total ~ nl*post, mlb[lim,]) -->

<!-- # # fixest::feols(c(ATT, log(ATT), score_home, I(score_away + score_home)) ~  -->

<!-- # #                 nl*post, data = all[lim,]) -->

In a simple 2x2 DiD, the standard framework
(`treat + post + treat:post`) and TWFE will generate the same treatment
effect. However, in the case where there are multiple time periods and
multiple units, this is not the case. Using `feols()` from the `fixest`
package, estimate one model using the standard framework and another
using TWFE.

``` r
library("fixest")
reg_did <- feols( ~ ,
                  mlb[exclude_logic,], se = "iid")
reg_twfe <- feols( ~
                  | tm + yr,
                  data = mlb[exclude_logic,],
                  se = "iid")
```

### Staggered Treatment Timing

It’s not often that some policy changes for only half of some population
and not for others like in the case of the DH. Rather, policies are
typically rolled out at different times across geography. For example,
same-sex marriage laws were adopted in different states at different
times. The frequency at which fuel taxes and minimum wages update varies
by state. Facebook, Uber, Walmart, and your other favorite stores are
also rolled out over time and space.

Before $\approx$ 2018, TWFE and/or event studies were seen as totally
acceptable methods for analyzing these types of staggered shocks.
However, the econometrics literature is in the midst of a
difference-in-differences revolution, and these sorts of settings need
to be handled very carefully. We will come back to this problem in
Module 2.2.

## Event Study

In both the 2x2 and TWFE models, we are implicitly assuming that once
the treatment is applied, its effect is constant over time. However,
this might not be the case in reality. For example, maybe the effect
starts off strong but then tapers off. To model this, researchers use
event studies.

To estimate an event study, the researcher will create a variable that
measures time to treatment. Then, this variable gets interacted with
`treat` (instead of `post`).

Estimating event studies also allows us to observe how the treatment
group was trending relative to the control group before treatment.
Significant trends leading up to treatment suggests a violation of the
parallel trend assumption.

To demonstrate this, we can continue using the MLB data.

1.  We can create a “time to treatment” variable by substracting 2022
    from the `yr` variable. This is going to put 2021 and -1, 2019 as
    -3, etc.
2.  Make this variable a “factor” variable using `as.factor()`. This
    turns the values into indicator variables rather than simply
    numeric.
3.  Estimate the model of the form `time_to_dh:nl | tm + yr`

``` r
mlb$time_to_dh <- as.factor(mlb$yr - 2022)
mlb$score_total <- mlb$score_home + mlb$score_away
feols(score_total ~ time_to_dh:nl
      | tm + yr,
      mlb, se = "iid")
```

    The variable 'time_to_dh0:nl' has been removed because of collinearity (see
    $collin.var).

<details>

<summary>

Output
</summary>

    OLS estimation, Dep. Var.: score_total
    Observations: 22,011
    Fixed-effects: tm: 30,  yr: 9
    Standard-errors: IID 
                     Estimate Std. Error  t value  Pr(>|t|)    
    time_to_dh-9:nl -0.995903   0.252187 -3.94907 0.0000787 ***
    time_to_dh-8:nl -0.656511   0.251618 -2.60916 0.0090826 ** 
    time_to_dh-7:nl -0.665590   0.251440 -2.64711 0.0081240 ** 
    time_to_dh-6:nl -0.452071   0.250835 -1.80227 0.0715170 .  
    time_to_dh-5:nl -0.504603   0.251363 -2.00747 0.0447120 *  
    time_to_dh-4:nl -0.762460   0.250611 -3.04241 0.0023497 ** 
    time_to_dh-3:nl -0.748276   0.252159 -2.96747 0.0030058 ** 
    time_to_dh-1:nl -0.518359   0.252055 -2.05653 0.0397435 *  
    ... 1 variable was removed because of collinearity (time_to_dh0:nl)
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    RMSE: 4.38426     Adj. R2: 0.035415
                    Within R2: 8.735e-4

</details>

The estimated coefficients represent the effect of being in the NL on
total scoring *relative* to being in the NL in the 2022 season. In
short, scoring had been statistically significantly lower in each season
relative to the 2022 season. Flipping the logic on its head, this
suggests that the 2022 season had higher scoring than previous seasons.
This gets us the answer we want, but not in a particularly aesthetic
way. Moreover, to remain consistent with our previous results, the
coefficient should represent the effect of the DH relative to previous
seasons, not relative to the DH season.

To fix this, we can use the `i()` function from `fixest`. With this
function, you can tell R which factor level(s) you’d like to be the
reference. The norm for event study models is to make the time period
immediately before treatment, which is generally -1, the reference.

``` r
mlb$time_to_dh <- mlb$yr - 2022
feols(score_total ~ i(factor_var = time_to_dh,
                      var = nl,
                      ref = -1)
      | tm + yr,
      mlb, se = "iid") -> es
iplot(es)
```

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-01-unnamed-chunk-22-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Event study plot of the effect on total score." />

</details>

Now, we can see the effect of the DH before and after it was put into
place. The effect of the policy before it happens *should* be zero. If
it were not zero, then this may be evidence of anticipatory effects. If
we had more years of data, we’d be able to examine the effect of the DH
over time. Unfortunately, since the rule change is so recent, those data
are not available yet.

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/_nknBbw-HFQ?si=399tkBdX3W0jKxr9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

## Revisiting Assumptions

Before finishing up, let’s revisit some of the assumptions of DiD.

1.  Parallel Trends: There is very little reason why we would not expect
    offensive output and attendance levels for a random half of MLB
    teams to proxy for the other half.
2.  Anticipation: the rule change occurred before the 2022 MLB season
    but following the free agency period where teams make the most
    changes to their rosters. This rules out anticipation as a
    significant concern.
3.  SUTVA: In this setting, each team’s offensive output is independent
    of other teams offensive output. While it could be argued that
    attendance is a function of the offense of both teams, NL teams
    always used DHs when playing at AL stadiums. So, the increase in
    offense should be limited to only NL stadiums. This rules out
    violations of SUTVA.
4.  Endogenous Treatment: This is likely the most difficult thing to
    argue. The MLB made this rule change in order to increase fan
    interest. However, the MLB did not make this change in the 2022
    season because NL offense had been dipping in the previous years. In
    other words, this change was forward looking and not reactionary.

## 2023 MLB Rule Changes

In the 2023 season, Major League Baseball implemented even more rule
changes. For example, they began using a pitch clock to make sure the
game is moving at a fast enough pace. Unfortunately, these rule changes
cannot be studied using DiD because the change occurred to every teams
at the same time. Therefore, there’s no control group to inform the
counterfactual. The effect of the pitch-clock would simply be absorbed
by the season fixed effects.

## Quirks

As always, it’s important to be able to visualize your data. While event
studies are helpful for this, the raw data can also be helpful. For
example, in Card and Krueger, we were able to see how NJ and PA changed
from pre-treatment to post treatment. Sometimes, it can be the case that
changes in the control group could be driving your estimated effect.
This is fine if you have strong reasons to believe in the parallel
trends assumption, but otherwise you should look at your analysis
sideways.

For example, suppose you have the following data:

``` r
set.seed(757)
y_ctrl <- c(rep(1, 3), rep(0, 3)) + rnorm(6, 0, 0.1)
y_treat <- c(rep(2, 6)) + rnorm(6, 0, 0.1)
plot(c(-3:2), y_ctrl, ylim = c(-1, 3),
     col = "dodgerblue", pch = 19,
     type = "b", cex = 2, yaxt = "n",
     ylab = "", xlab = "Time")
points(c(-3:2), y_treat, col = "tomato",
       pch = 19, type = "b", cex = 2)
abline(v = -0.5, lty = 2)
legend("bottomleft", cex = 1.3,
       col = c("tomato", "dodgerblue"),
       bty = "n", pch = 19, lty = 1,
       c("Treatment", "Control"))
```

Estimate the treatment effect:

``` r
data.frame(y = c(y_ctrl, y_treat),
           time = rep(-3:2, 2),
           treat = rep(c(0, 1), each = 6)) -> df
df$post <- ifelse(df$time >= 0, 1, 0)
reg <- lm()
summary()
```

OLS will estimate a positive treatment effect for this setting because
DiD assumes that the red line should have also dropped in the treatment
period, but didn’t. Therefore, the treatment should have had a positive
effect! Sometimes, this can be valid, but you ought to have a good
reason because this can look suspicious.

[^1]: For the sake of completeness, 92.4% of stores in NJ increased
    their wages compared to only 25% in PA.

[^2]: I know, this is like saying ATM machine but I can’t help it.

[^3]: The use of a DH was determined by the league of the home team.
    There was never a time where an NL team and an AL team would play
    with only one of them using a DH.
