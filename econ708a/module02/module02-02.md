# Difference-in-Differences
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ708a.html"
target="_blank">alexcardazzi.github.io</a>.

## Staggered Treatment Timing

Difference-in-Differences is one of the most widely used research
designs in the social sciences. This is likely due to how (relatively)
easy it is to set up and to understand. While economists tend to prefer
designs like Regression Discontinuity (Module 7) in terms of
credibility, DiD remains a much more popular method. There have been
many econometric innovations in the past decade or so, but none as big
as the DiD one. Certainly, no other method has had a “revolution” in the
way DiD has.

In these notes, we are going to discuss the innovations that have come
to light since ~2020.

When treatment timing is staggered (for example: policies are rolled out
in different places at different times), and treatment effects are
heterogeneous (for example: different across time/units), the DiD
coefficient occasionally fails to represents what we *think* it should
represent. In short, this is due to improper comparisons of groups at
different points in time.

In a TWFE design, OLS[^1] compares the following:

- Newly treated units to never treated units (good!)
- Newly treated units to not-yet treated units (good!)
- Newly treated units to already treated units (bad!)

This last group, where newly treated units are compared to already
treated units, can result in negative estimates of positive treatment
effects and vice versa. This issue puzzled social scientists and
ubiquitously stalled research. For a few years, economists everywhere
were scrambling to figure out what was wrong with two-way fixed effects
models, who Andrew Goodman-Bacon was, and how to do his
difference-in-differences thing. This was such a FAQ that in late 2019,
Goodman-Bacon wrote a paper [*So You’ve Been Told to Do My
Difference-in-Differences Thing: A
Guide*](https://cdn.vanderbilt.edu/vu-my/wp-content/uploads/sites/2318/2019/10/09023516/so_youve_been_told_dd_10_9_2019.pdf).

Let’s spend some time motivating and understanding the problem with TWFE
before moving onto ~~the shortcuts~~ some solutions to the problem.

## TWFE

Consider the following example that we will simulate data for. We have
three groups over ten time periods. The first group is never treated
throughout the sample. The second and third groups are treated at
$t = 4.5$ and $t = 7.5$, but we only observe the units at
$t \in \{1, 2, ..., 10\}$. Put more simply: at $t = 4$, the second group
is 100% untreated and at $t = 5$, the group is 100% treated. The
treatment is either going to change the trajectory of growth for each
group, or simply be a static level shift, depending on the arguments you
choose for the simulation. You should experiment with both, however.

Below are the simulation functions that create the data and then plot
the data. I am providing the code so you can see what it looks like
under the hood.[^2]

``` r
create_data <- function(dynamic = FALSE, rnd = FALSE){
  
  set.seed(757)
  df <- data.frame(id = rep(1:3, times = 10),
                   tt = rep(1:10, each = 3))
  df$col <- ifelse(df$id == 1, "black",
                   ifelse(df$id == 2, "tomato",
                          "dodgerblue"))
  
  # Setting the binary treatment variable
  df$D <- ifelse((df$id == 2 & df$tt >= 5) | (df$id == 3 & df$tt >= 8), 1, 0)
  
  df$btrue <- ifelse(df$D == 1 & df$id == 3, 5,
                     ifelse(df$D == 1 & df$id == 2, 3, 0))
  # Decide if treatment is dynamic or static.
  if(dynamic){
    
    df$rel_time <- df$tt - c(NA, 5, 8)
    df$rel_time <- ifelse(is.na(df$rel_time), -100, df$rel_time)
  } else {
    
    df$rel_time <- 1
  }
  
  # Setting the "true" treatment effect for each time period
  #   This will be nice for later comparison
  df$treatment_effect <- df$btrue*df$D*df$rel_time
  
  # Create outcome variable
  df$y <- df$id + df$tt + df$treatment_effect + if(rnd) rnorm(nrow(df), 0, 0.01) else 0
  
  return(df)
}
plot_data <- function(df){
  
  plot(df$tt, df$y, col = df$col, pch = 19,
       xlab = "Time", ylab = "Y", las = 1)
  for(i in 1:3){
    
    lines(df$tt[df$id == i],
          df$y[df$id == i],
          col = df$col[df$id == i])
  }
  abline(v = c(4.5, 7.5), lty = 2)
}
```

<details class="code-fold">
<summary>Code</summary>

``` r
create_data <- function(dynamic = FALSE, rnd = FALSE){
  
  set.seed(757)
  df <- data.frame(id = rep(1:3, times = 10),
                   tt = rep(1:10, each = 3))
  df$col <- ifelse(df$id == 1, "black",
                   ifelse(df$id == 2, "tomato",
                          "dodgerblue"))
  
  # Setting the binary treatment variable
  df$D <- ifelse((df$id == 2 & df$tt >= 5) | (df$id == 3 & df$tt >= 8), 1, 0)
  
  df$btrue <- ifelse(df$D == 1 & df$id == 3, 5,
                     ifelse(df$D == 1 & df$id == 2, 3, 0))
  # Decide if treatment is dynamic or static.
  if(dynamic){
    
    df$rel_time <- df$tt - c(NA, 5, 8)
    df$rel_time <- ifelse(is.na(df$rel_time), -100, df$rel_time)
  } else {
    
    df$rel_time <- 1
  }
  
  # Setting the "true" treatment effect for each time period
  #   This will be nice for later comparison
  df$treatment_effect <- df$btrue*df$D*df$rel_time
  
  # Create outcome variable
  df$y <- df$id + df$tt + df$treatment_effect + if(rnd) rnorm(nrow(df), 0, 0.01) else 0
  
  return(df)
}
plot_data <- function(df){
  
  plot(df$tt, df$y, col = df$col, pch = 19,
       xlab = "Time", ylab = "Y", las = 1)
  for(i in 1:3){
    
    lines(df$tt[df$id == i],
          df$y[df$id == i],
          col = df$col[df$id == i])
  }
  abline(v = c(4.5, 7.5), lty = 2)
}
```

</details>

Compare the static and dynamic data by switching between `TRUE` and
`FALSE` below:

``` r
df <- create_data(dynamic = FALSE)
plot_data(df)
```

Since we simulated these data, and we *know* the true treatment effect
for each observation (via `treatment_effect`), we should be able to
average them across treated units. Of course, since the treatments are
inherently different for groups 2 and 3, there are many ways we can
summarize these treatment effects. For example, since the two treatment
effects are 3 and 5 in the static case, we can use the average (4) for
our treatment effect. We could also weigh 3 and 5 by how many time
periods they’ve been observed for (6 and 3, respectively). This would
give us an average of 3.67. We could also find an average treatment by
time-since-treatment like in an event study.

Let’s think about what a regression would give us. Suppose we have a
TWFE model of the following form[^3]:

$$Y_{it} = \alpha_i + \lambda_t + \delta^{DD}D_{it} + \epsilon_{it}$$

Intuitively, OLS *should* return an average of all the treatment effects
in the data as $\delta^{DD}$. This would be consistent with a
time-weighted treatment effect of 3.67 in static case (or 6.67 in the
dynamic case).

Let’s compare our time-weighted treatment effect with what TWFE returns.

``` r
df <- create_data(dynamic = TRUE)
mu <- c("Manual Average" = mean(df$treatment_effect[df$D == 1]),
        coef(fixest::feols(y ~ D | id + tt, df)))
print(mu)
```

**Spoiler**: our coefficient is (seemingly) too large in the static case
and too small in the dynamic case.[^4]

## Goodman-Bacon

Wait, so what gives? Well, it’s complicated and we (they: the
econometricians) *just* figured this all out.

As simple as this example is, this was a non-trivial observation to
econometricians. Andrew Goodman-Bacon, the most recent scapegoat for
grumbling economists, demonstrated in an immediately seminal work that
the DiD coefficient in a TWFE framework ($\delta^{DD}$) is a (sometimes
strangely) weighted average of *all possible* 2x2 DiD comparisons.

What does “all possible” 2x2 DiD comparisons mean?

In this three-group case with one untreated group, there are four
possible 2x2 DiD comparisons.

1.  The first two comparisons are the two treated groups each separately
    compared to the never-treated group.
2.  The third comparison is the earlier treated group compared to the
    later treated group **while the later treated group remains
    untreated**.
3.  The final comparison is the later treated group compared to the
    earlier treated group **while the earlier treated group is always
    treated**.

It’s easier to visualize this as the following:

<details class="code-fold">
<summary>Code</summary>

``` r
df <- create_data(TRUE)
par(mfrow = c(2, 2), mar = c(2.1, 2.1, 2.1, 0.6))
## Comp. 1 -- Early vs Never
lim <- df$id %in% c(1, 2)
r1 <- fixest::feols(y ~ D | id + tt, df[lim,])
plot(df$tt[lim], df$y[lim], pch = 19,
     ylim = range(df$y),
     xlim = range(df$tt),
     xlab = "", ylab = "", las = 1,
     main = "I. Early vs Never",
     col = df$col[lim])
lines(df$tt[df$id == 1], df$y[df$id == 1], col = df$col[df$id == 1], lty = 2)
lines(df$tt[df$id == 2], df$y[df$id == 2], col = df$col[df$id == 2])
abline(v = c(4.5, 7.5), lty = 2)
legend("topleft", legend = c("Treatment", "Control"),
       lty = 1:2, bty = "n")

## Comp. 2 -- Late vs Never
lim <- df$id %in% c(1, 3)
r2 <- fixest::feols(y ~ D | id + tt, df[lim,])
plot(df$tt[lim], df$y[lim], pch = 19,
     ylim = range(df$y),
     xlim = range(df$tt),
     xlab = "", ylab = "", las = 1,
     main = "II. Late vs Never",
     col = df$col[lim])
lines(df$tt[df$id == 1], df$y[df$id == 1], col = df$col[df$id == 1], lty = 2)
lines(df$tt[df$id == 3], df$y[df$id == 3], col = df$col[df$id == 3])
abline(v = c(4.5, 7.5), lty = 2)

## Comp. 3 -- Early vs Late
lim <- df$id %in% c(2, 3) & df$tt < min(df$tt[df$id == 3 & df$D == 1])
r3 <- fixest::feols(y ~ D | id + tt, df[lim,])
plot(df$tt[lim], df$y[lim], pch = 19,
     ylim = range(df$y),
     xlim = range(df$tt),
     xlab = "", ylab = "", las = 1,
     main = "III. Early vs Late",
     col = df$col[lim])
lim2 <- lim & df$id == 2
lines(df$tt[lim2], df$y[lim2], col = df$col[lim2])
lim2 <- lim & df$id == 3
lines(df$tt[lim2], df$y[lim2], col = df$col[lim2], lty = 2)
abline(v = c(4.5, 7.5), lty = 2)


## Comp. 4 -- Late vs Early
lim <- df$id %in% c(2, 3) & df$tt >= min(df$tt[df$id == 2 & df$D == 1])
r4 <- fixest::feols(y ~ D | id + tt, df[lim,])
plot(df$tt[lim], df$y[lim], pch = 19,
     ylim = range(df$y),
     xlim = range(df$tt),
     xlab = "", ylab = "", las = 1,
     main = "IV. Late vs Early",
     col = df$col[lim])
lim2 <- lim & df$id == 2
lines(df$tt[lim2], df$y[lim2], col = df$col[lim2], lty = 2)
lim2 <- lim & df$id == 3
lines(df$tt[lim2], df$y[lim2], col = df$col[lim2])
abline(v = c(4.5, 7.5), lty = 2)

par(mfrow = c(1, 1))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-02-unnamed-chunk-7-1.svg" style="width:90.0%"
data-fig-align="center" data-fig-alt="Each 2x2 comparison." />

</details>

Each of these 2x2 comparisons will be given positive weights when making
up $\delta^{DD}$. However, we only necessarily want to consider
comparisons I-III. Comparison IV is flawed because the “control” group
is already tainted by treatment dynamics.

One of the innovations in Goodman-Bacon was the ability to decompose
$\delta^{DD}$ into it’s parts. Using the `bacondecomp` package, we can
apply Goodman-Bacon’s diagnostic methodology to split up $\delta^{DD}$
into its different 2x2 comparisons and their resulting estimates and
weights.

``` r
library("bacondecomp")
bacon(formula = y ~ D, data = df,
      id_var = "id", time_var = "tt",
      quietly = FALSE) -> our_decomp
```

<details>

<summary>

Output
</summary>

                          type  weight  avg_est
    1 Earlier vs Later Treated 0.18182  3.00000
    2 Later vs Earlier Treated 0.13636 -4.00000
    3     Treated vs Untreated 0.68182  6.33333

</details>

This output summarizes the three possible comparisons: treated vs
never-treated (good!), early vs late treated (good!), and late vs early
treated (maybe bad?). What should raise an eyebrow, though, is the late
vs early comparison group with an average estimate of -4! This is, of
course, not representative of what we want OLS to reflect. Looking at
the plot of our dynamic simulation data, it’s crystal clear that our
treatment effects are **positive**.[^5]

<details>

<summary>

An aside about `bacondecomp::bacon()`
</summary>

If we print the result of our decomposition, we can observe each 2x2. We
can also always use this to re-aggregate to the summary `bacon()` output
for us before.

``` r
our_decomp
cat("\n########## Summary ##########\n")
aggregate(list(weight = our_decomp$weight,
               avg_est = our_decomp$estimate * our_decomp$weight),
          list(type = our_decomp$type), sum) -> agg
agg$avg_est <- agg$avg_est / agg$weight
agg
```

<details>

<summary>

Output
</summary>

      treated untreated estimate    weight                     type
    2       5     99999      7.5 0.3636364     Treated vs Untreated
    3       8     99999      5.0 0.3181818     Treated vs Untreated
    6       8         5     -4.0 0.1363636 Later vs Earlier Treated
    8       5         8      3.0 0.1818182 Earlier vs Later Treated

    ########## Summary ##########
                          type    weight   avg_est
    1 Earlier vs Later Treated 0.1818182  3.000000
    2 Later vs Earlier Treated 0.1363636 -4.000000
    3     Treated vs Untreated 0.6818182  6.333333

</details>

</details>

This is no mistake. When I plotted the four comparisons, I also
calculated the TWFE regressions for each. Displaying these coefficients,
we can see how Goodman-Bacon’s decomposition gets it’s estimates.[^6]

<details class="code-fold">
<summary>Code</summary>

``` r
library("modelsummary")
options(modelsummary_factory_default = 'tinytable')
options("modelsummary_format_numeric_latex" = "plain")

modelsummary(list("E vs U" = r1, "L vs U" = r2,
                  "E vs L" = r3, "L vs E" = r4),
             gof_map = "", estimate = "{estimate}",
             title = "All 2x2 DiD Coefficients")
```

</details>

<details><summary>Output</summary>
&#10;
+---+---------+---------+---------+---------+
|   | E vs U  | L vs U  | E vs L  | L vs E  |
+===+=========+=========+=========+=========+
| D | 7.500   | 5.000   | 3.000   | -4.000  |
+---+---------+---------+---------+---------+
|   | (2.864) | (1.725) | (1.449) | (2.082) |
+---+---------+---------+---------+---------+
&#10;Table: All 2x2 DiD Coefficients
&#10;</details>

With this very simple simulation, we’ve found a case where all treatment
effects are positive (visually), but part of the decomposed
$\delta^{DD}$ is negative.[^7] In fact, it’s possible for this
comparison to turn the **entire** treatment effect negative. See below
for an example.[^8]

<details class="code-fold">
<summary>Code</summary>

``` r
baker <- haven::read_dta('https://github.com/scunning1975/mixtape/raw/master/baker.dta')
baker$treated <- ifelse(baker$treat_date > 0, 1, 0)

tmp <- aggregate(list(y = baker$y),
                 list(year = baker$year,
                      group = baker$group),
                 mean)
plot(tmp$year, tmp$y, pch = 19,
     col = scales::alpha(tmp$group, .4),
     xlab = "Year", ylab = "Outcome")
lines(tmp$year[tmp$group == unique(tmp$group)[1]],
      tmp$y[tmp$group == unique(tmp$group)[1]],
      col = tmp$group[tmp$group == unique(tmp$group)[1]])
lines(tmp$year[tmp$group == unique(tmp$group)[2]],
      tmp$y[tmp$group == unique(tmp$group)[2]],
      col = tmp$group[tmp$group == unique(tmp$group)[2]])
lines(tmp$year[tmp$group == unique(tmp$group)[3]],
      tmp$y[tmp$group == unique(tmp$group)[3]],
      col = tmp$group[tmp$group == unique(tmp$group)[3]])
lines(tmp$year[tmp$group == unique(tmp$group)[4]],
      tmp$y[tmp$group == unique(tmp$group)[4]],
      col = tmp$group[tmp$group == unique(tmp$group)[4]])
abline(v = unique(baker$treat_date)-0.5, col = 1:4)
cat("\n##### Goodman-Bacon Decomp #####\n")
bacon(formula = y ~ treat, data = baker,
      id_var = "id", time_var = "year",
      quietly = FALSE) -> this_decomp
cat("\n##### TWFE Results #####\n")
fixest::feols(y ~ treat | id + year, data = baker)
```

</details>

<details>

<summary>

Output
</summary>

    ##### Goodman-Bacon Decomp #####
                          type weight   avg_est
    1 Earlier vs Later Treated    0.5  51.80009
    2 Later vs Earlier Treated    0.5 -65.18046

    ##### TWFE Results #####
    OLS estimation, Dep. Var.: y
    Observations: 30,000
    Fixed-effects: id: 1,000,  year: 30
    Standard-errors: IID 
          Estimate Std. Error  t value  Pr(>|t|)    
    treat -6.69019   0.623867 -10.7237 < 2.2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    RMSE: 29.1     Adj. R2: 0.803436
                 Within R2: 0.003954

</details>

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-02-unnamed-chunk-11-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Time series plot of outcomes by group in the Baker dataset." />

</details>

As a note: unfortunately, the weights are not computed in any intuitive
way. According to [Andrew
Baker](https://andrewcbaker.netlify.app/2019/09/25/difference-in-differences-methodology/)[^9],
“we can think of the TWFE estimator as a weighted average of individual
2x2 DiD estimators, with the weights proportional to group sizes and the
variance of the treatment indicators in each pair, which is highest for
units treated in the middle of the panel.” Therefore, $\delta^{DD}$ is
sensitive to both panel length and which groups appear towards the
center. These are not necessarily things we want to influence our
estimates. If you remain interested in how weights are derived, I
encourage you to read Goodman-Bacon
([2019](https://doi.org/10.1016/j.jeconom.2021.03.014)).

## Solutions

So, now what?

There have been many estimators proposed that solve this problem. In [a
blog
post](https://causalinf.substack.com/p/two-stage-did-and-taming-the-did),
Scott Cunningham notes three categories of these estimators: weighted
group-time ATT, stacking in relative time, and imputation. While this is
a helpful dichotomy to have, these estimators are all quite similar in
reality. Of course, each has its own strengths and weaknesses, so it’s
important to be familiar with more than just one, but the main thread
through all of them is to fix the issues associated with wonky
comparisons.

Some of the proposed estimators, just to name a few, are as follows:

- Gardner ([2022](https://doi.org/10.32614/RJ-2022-048))
- Dube *et al.* ([2023](https://doi.org/10.3386/w31184))
- Callaway and Sant’Anna
  ([2021](https://doi.org/10.1016/j.jeconom.2020.12.001))
- Sun and Abraham
  ([2021](https://doi.org/10.1016/j.jeconom.2020.09.006))
- de Chaisemartin and D’Haultfouille
  ([2020](https://doi.org/10.1257/aer.20181169))
- Borusyak *et al.* ([2023](https://arxiv.org/pdf/2108.12419.pdf))
- Wooldridge
  ([2021](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3906345),
  [2022](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4183726))

\

<center>

<iframe width="560" height="315" src="https://www.youtube.com/embed/hu2nDbnpALA?si=eIy2y8kNNekYT1Wh" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen data-external="1">

</iframe>

</center>

We do not have enough time to cover each method, so instead we are going
to focus on two: Gardner (2022) and Sun and Abraham (2021). Gardner
(2022) proposes something called “Two-Stage Difference-in-Differences”,
while Sun and Abraham (2021) aggregate group-specific effects into a
single quantity. While I would imagine that Callaway & Sant’Anna (2021)
is the most “popular” estimator, I believe Gardner (2022) and Sun and
Abraham (2021) are two of the more intuitive.

## Gardner (2022)

The first solution we will discuss is, in my opinion, the most
intuitive. This is one of those papers that make you think, “I wish I
thought of that!” Gardner (2022) takes a clever approach towards
estimating the TWFE model. The idea starts like this:

If the parallel trends assumption holds (is true), then the potential
outcome for $Y_{it}^0$ is simply a sum of unit $i$’s unit-specific
effect and time period $t$’s common time-specific effect.[^10] Perhaps
we should re-frame the problem and switch our priority to accurately
measure *these* parameters, since they provide information about the
unobservable counterfactual.

<!-- $$ Y_{it} = \alpha_i + \lambda_t + \delta^{DD}D_{it} + \epsilon_{it} $$ -->

Gardner goes through quite a bit of math to effectively (albeit more
elegantly) say the following: since we only have a single, static
$\delta^{DD}$ in our equation, any heterogeneity in treatment effects
gets absorbed by the fixed effects $\alpha_i$ and $\lambda_t$.
Informally, OLS is trying to fit a square peg in a round hole. Forcing
the peg and biasedly estimating $\alpha_i$ and $\lambda_t$ in turn
biases $\widehat{\delta^{DD}}$!

The solution Gardner proposes is rather simple: if estimating $\alpha_i$
and $\lambda_t$ causes us problems, let’s remove them. Wait… how can we
just remove them?! If we can *somehow* get unbiased estimates of our
fixed effects, we can simply subtract them from our outcome before
estimating $\delta^{DD}$!

Before moving on, let’s think about what fixed effects are. Suppose the
data we have contain Virginia from 2010 to 2019. A Virginia-fixed-effect
is a **time-invariant** effect. So, if we estimate a
Virginia-fixed-effect, it shouldn’t matter whether we use data from
2010-2014, 2015-2019, strictly odd years, or any other combination of
years. In theory, all estimations should yield the same (or similar)
result. The same logic holds for time-period-fixed-effects: it shouldn’t
matter which units you use to estimate them since the effect is
invariant across units.

Since we know treatment is messing up our fixed effects, we can estimate
$\alpha_i$ and $\lambda_t$ using *only* data from untreated time
periods. We will call this our **first-stage**. The resulting fixed
effects from this estimating will necessarily be unbiased (assuming
parallel trends) since there is no treatment involved. Then, in a
**second stage**, we can fit the following model using the entire
dataset:

$$ (Y_{it} - \widehat{\alpha}_i - \widehat{\lambda}_t) = \delta^{DD}D_{it} + \epsilon_{it} $$

Let’s take a look at the mechanics of this. We are going to start with
the first stage, and regress our outcome variable on only fixed effects
only using observations from untreated units.

<details class="code-fold">
<summary>Code</summary>

``` r
r1 <- lm(y ~ as.factor(id) + as.factor(tt) - 1, df[df$D == 0,])
coef(r1)
```

</details>

<details>

<summary>

Output
</summary>

     as.factor(id)1  as.factor(id)2  as.factor(id)3  as.factor(tt)2  as.factor(tt)3 
                  2               3               4               1               2 
     as.factor(tt)4  as.factor(tt)5  as.factor(tt)6  as.factor(tt)7  as.factor(tt)8 
                  3               4               5               6               7 
     as.factor(tt)9 as.factor(tt)10 
                  8               9 

</details>

Next, we are going to collect the estimated fixed effects and subtract
them from the outcome variable.

<details class="code-fold">
<summary>Code</summary>

``` r
fe_id <- coef(r1)[grepl("id", names(coef(r1)))]
fe_tt <- coef(r1)[grepl("tt", names(coef(r1)))]
df$fe_id <- fe_id[match(df$id, gsub("as.factor\\(id|\\)", "", names(fe_id)))]
df$fe_tt <- fe_tt[match(df$tt, gsub("as.factor\\(tt|\\)", "", names(fe_tt)))]
df$fe_tt <- ifelse(is.na(df$fe_tt), 0, df$fe_tt)
df$y_2sdid <- df$y - df$fe_id - df$fe_tt
```

</details>

Finally, estimate the second-stage regression *without* fixed effects.

<details class="code-fold">
<summary>Code</summary>

``` r
lm(y_2sdid ~ D, df)
```

</details>

<details>

<summary>

Output
</summary>

    Call:
    lm(formula = y_2sdid ~ D, data = df)

    Coefficients:
    (Intercept)            D  
     -3.243e-16    6.667e+00  

</details>

Notice how this is exactly equivalent to our average treatment effect of
6.67 for the dynamic group! The only issue with doing this two-step
procedure is that the standard errors can get weird. This is true for
all two-stage procedures. There’s an R package called `did2s` that can
help implement two-stage DiD for you. I highly recommend this package.

``` r
library("did2s")
did2s(df, yname = "y",
      first_stage = ~ 0 | id + tt,
      second_stage = ~ D, treatment = "D",
      cluster_var = "id")
```

<details>

<summary>

Output
</summary>

    OLS estimation, Dep. Var.: y
    Observations: 30
    Standard-errors: Corrected Clustered (id) 
      Estimate Std. Error t value  Pr(>|t|)    
    D  6.66667   0.785674 8.48528 2.383e-09 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    RMSE: 2.70801   Adj. R2: 0.544828

</details>

For more information about this package, see [the
vignette](https://kylebutts.github.io/did2s/articles/Two-Stage-Difference-in-Differences.html)
and/or [the R Journal
article](https://journal.r-project.org/articles/RJ-2022-048/).

<!-- https://kylebutts.github.io/did2s/articles/Two-Stage-Difference-in-Differences.html -->

<!-- https://causalinf.substack.com/p/two-stage-did-and-taming-the-did -->

<!-- https://journal.r-project.org/articles/RJ-2022-048/ -->

In addition to estimating $\delta^{DD}$, we can also estimate an event
study using 2SDiD. To do this, simply replace the treatment variable
with the `fixest` notation for an event study. See Module 3.1 for this
information.

As a final note, keep in mind that in order to estimate the unit and/or
time fixed effect, you need to observe *some* untreated units in both.
What I mean is that there cannot be any units that are always-treated
and there cannot be any times that only contain treated units. Their
fixed effects will, of course, not be estimated in the first stage, so
they automatically get removed from the second stage.

## Sun and Abraham (2021)

The standard TWFE event study framework is as follows:

$$Y_{it} = \alpha_i + \lambda_t + \sum_{k\neq-1}\delta_k D_{it}^{k} + u_{it}$$

where $D_{it}^{k}$ is a dummy equal to 1 if unit $i$ is $k$ periods from
treatment at time $t$. \[When treatment effects are heterogeneous across
cohorts, that is, units treated in different periods have different
dynamic paths, $\widehat{\delta}_k$ is not a clean average of
cohort-specific effects at horizon $k$. It gets contaminated by
treatment effects from other relative periods, because already-treated
cohorts end up serving as part of the comparison group for later cohorts
through the two-way fixed effects.

As mentioned, there are three main ways to classify the “solutions” to
the problems introduced by staggered treatment timing. Gardner’s fix was
a two-stage procedure, *imputing* untreated potential outcomes from a
first-stage model, then comparing observed to imputed outcomes in a
second stage. Sun and Abraham (2021) takes a different route entirely,
and stays inside a single regression.

The problem is that $D_{it}^{k}$ pools observations across cohorts $g$
as if one $\delta_k$ could describe every cohort’s effect at horizon
$k$. Sun and Abraham (2021)’s fix is to stop pooling, let each cohort
have its own relative-time path, and then average those paths after the
fact.

Instead of a single dummy per relative period, Sun and Abraham (2021)
interacts the relative-time dummies with cohort indicators
$\mathbb{1}\{G_i = g\}$, where $G_{i}$ is the period unit $i$ is first
treated:

$$Y_{it} = \alpha_i + \lambda_t + \sum_{g}\sum_{k\neq-1}\delta_{g,k} (\mathbb{1}\{G_i = g\} \times D_{it}^{k}) + u_{it}$$

Now, $\delta_{g,k}$ is cohort $g$’s treatment effect at relative time
$k$, estimated only off variation within that cohort’s own path relative
to **never-treated** (or last-treated) units. In this set up, there are
no forbidden comparisons, since the comparison group is fixed and
explicit rather than baked into a shared TWFE.

The issue with $\delta_{g,k}$, though, is that it is specific to each
cohort, whereas what we’d really like is a single estimate or
event-study (like 2S-DiD). To get our target parameters, we can
aggregate the $\delta_{g,k}$ parameters using cohort shares as weights.
Luckily, `fixest` can do this for us with `sunab()`.

``` r
# I need to merge two datasets because
#   one obs per group-time is breaking the SEs. 
df0 <- create_data(TRUE, TRUE)
df1 <- create_data(TRUE, TRUE)
df <- rbind(df0, df1)

# Construct group indicators by treatment time.
agg <- aggregate(df$tt[df$D == 1], list(df$id[df$D == 1]), min)
df$g <- agg$x[match(df$id, agg$Group.1)]
df$g <- ifelse(is.na(df$g), Inf, df$g)
# Untreated units are given 'Inf'

mod_sa <- feols(y ~ sunab(g, tt) | id + tt, data = df)
# or: feols(y ~ sunab(g, tt, att = T) | id + tt, data = df)
summary(mod_sa, agg = "ATT")
```

<details>

<summary>

Output
</summary>

    OLS estimation, Dep. Var.: y
    Observations: 60
    Fixed-effects: id: 3,  tt: 10
    Standard-errors: IID 
        Estimate Std. Error      t value  Pr(>|t|)    
    ATT   6.6636   6.55e-15 1.017208e+15 < 2.2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    RMSE: 5.673e-15     Adj. R2: 1
                      Within R2: 1

</details>

To get the event-study version, we can just use `iplot()`.

## Stacked Difference-in-Differences

The next approach we’ll discuss is stacked difference-in-differences.
There is not a specifically associated paper with this estimator, but
the intuition has been used in multiple studies. One of the most famous
studies that uses a variation of this method is Cengiz *et at.*
([2019](https://doi.org/10.1093/qje/qjz014)), but Wing *et al.*
([2024](https://doi.org/10.3386/w32054)) is another good resource.

First, let’s define some terms:

1.  Event: an event is going to be any time some policy is changed or
    some shock is experienced within a unit (e.g. state, etc.) For
    example, New Jersey changing its minimum wage would constitute an
    event.
2.  Sub-experiment: a sub-experiment is a collection of treatment and
    *clean* control groups. We’ll define “clean” in a moment. An event
    might be New Jersey changing its minimum wage. However, if New
    Jersey and Michigan change their minimum wages at the same time,
    then their events, in addition to their controls, would constitute a
    single sub-experiment. The way to refer to New Jersey’s and
    Michigan’s events would be a *cohort*.
3.  Clean control: a clean control group is made up of units that do not
    experience any treatment, **or residual treatment dynamics**, during
    a sub-experiment’s treatment periods.

Let’s start with a fresh `df` so we can experiment with stacking.

<details class="code-fold">
<summary>Code</summary>

``` r
df <- create_data(TRUE)
# Since this dataset is so small, I need to increase
#   the number of observations for this demo.
#   You would **not** do this in real life.  
df <- rbind(df, df)
df <- df[order(df$id, df$tt),]
```

</details>

Since there are two treated units in this example, we would have two
sub-experiments (one for each event). To focus in on the treatment, we
can create a window of $\pm2$ time period relative to treatment *for
each sub-experiment*.

<details class="code-fold">
<summary>Code</summary>

``` r
par(mfrow = c(1, 2), mar = c(2.1, 2.1, 2.1, 0.6))
lim <- df$tt %in% (5 + (-2:2))
df1 <- df[lim,]
plot(df$tt[lim], df$y[lim],
     main = "Sub-Experiment 1",
     col = df$col[lim], pch = 19,
     xlim = range(df$tt), ylim = range(df$y),
     xlab = "Time", ylab = "Y", las = 1)
for(i in 1:3){
    
  lines(df$tt[df$id == i & lim],
        df$y[df$id == i & lim],
        col = df$col[df$id == i & lim])
}
abline(v = c(4.5, 7.5), lty = 2)

lim <- df$tt %in% (8 + (-2:2)) & df$id != 2
df2 <- df[lim,]
plot(df$tt[lim], df$y[lim],
     main = "Sub-Experiment 2",
     col = df$col[lim], pch = 19,
     xlim = range(df$tt), ylim = range(df$y),
     xlab = "Time", ylab = "Y", las = 1)
for(i in 1:3){
    
  lines(df$tt[df$id == i & lim],
        df$y[df$id == i & lim],
        col = df$col[df$id == i & lim])
}
abline(v = c(4.5, 7.5), lty = 2)
par(mfrow = c(1, 1))
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-02-unnamed-chunk-18-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="2x2 comparisons of each sub-experiment." />

</details>

Now that we have our two separately defined sub-experiments, with clean
control units, we can create variables to enumerate each experiment and
denote relative time *within* each experiment. Finally, we’ll *stack*
the two subsets of data on top of each other **in relative time**. By
“stack”, I mean that we will literally be vertically concatenating the
data. In R, this is done via `rbind()`.

<details class="code-fold">
<summary>Code</summary>

``` r
# Enumerate the sub-experiment
df1$experiment <- 1
df2$experiment <- 2
# Add in relative time
df1$rel_time <- df1$tt - 5
df2$rel_time <- df2$tt - 8
# Stack the data with rbind()
stacks <- rbind(df1, df2)
# Creat a treatment variable
stacks$ever_treat <- ave(stacks$D, paste0(stacks$id, stacks$experiment), FUN = max)
```

</details>

Using these stacked data, we can now estimate a simple 2x2 TWFE
specification. **Note**: the fixed effects should be interacted with the
sub-experiment identifier. With `fixest`, we can do this by using
`FE1^subexperiment`.

``` r
feols(y ~ D | id^experiment + tt^experiment, stacks)
```

<details>

<summary>

Output
</summary>

    OLS estimation, Dep. Var.: y
    Observations: 50
    Fixed-effects: id^experiment: 5,  tt^experiment: 10
    Standard-errors: IID 
      Estimate Std. Error t value  Pr(>|t|)    
    D  3.85714   0.884924 4.35873 0.0001093 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    RMSE: 1.23889     Adj. R2: 0.870041
                    Within R2: 0.351834

</details>

As I see it, there are two main advantages of stacking. First, after
stacking, we’re back to the familiar 2x2 case that we’re already
comfortable with. Moreover, and this is the second benefit, we can also
estimate $\delta^{DD}$ for **each** sub-experiment and summarize/combine
the results however we’d like. See below for an example of how to do
this with `fixest`.

<details class="code-fold">
<summary>Code</summary>

``` r
# Estimate an event study by sub-experiment using split
feols(y ~ i(rel_time, ever_treat, -1)
      | id^experiment + tt^experiment,
      stacks,
      split = ~experiment) -> r2
iplot(r2, grid = F)

# Estimate the aggregate event study
feols(y ~ i(rel_time, ever_treat, -1)
      | id^experiment + tt^experiment, stacks) -> r1
iplot(r1, add = TRUE, col = "dodgerblue")
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-02-unnamed-chunk-21-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Event study plot of each sub-experiment and the OLS weighted outcome." />

</details>

While this is seems easy enough with just three groups, the complexity
increases when the number of sub-experiments and/or the size of the data
becomes large. This becomes even more difficult when treatment is
**non-absorbing**. Treatments that are *absorbing* forever alter the
trajectory of the unit. It is the opposite for non-absorbing treatments,
which “wears off”. In other words, after some time, the effect of the
treatment levels off, a new equilibrium is reached, and the unit
effectively returns back to being untreated.

The type of treatment, absorbing or non-absorbing, does not imply the
magnitude of the effect, but rather whether the treated units can
eventually return to the control group after some time. This is an
assumption that necessarily depends on the institutional setting.

An example of non-absorbing treatment would be a pipe in my apartment
bursting. I would likely have to move out for a few days or so while the
pipe is fixed, but eventually I would be able to return to my normal
life. An example of absorbing treatment would be my landlord evicting me
from my apartment. I would need to find a new place to live and (not to
be dramatic) my life would be changed forever (in a causal inference
sense).

More concrete examples would be minimum wage laws compared to
texting-and-driving laws. Minimum wages are increased every few years to
account for inflation, which necessarily makes the changes
non-absorbing. On the other hand, once texting-and-driving has been made
illegal it will always be illegal.

Unfortunately, there are not any (well developed) R packages that
address stacked difference-in-differences. However, that last sentence
may be out-dated by the time you read it with how quickly theory and
software are developing around this topic. Again, at the time of
writing, I am working on developing a package to do this.

<!-- https://scholarworks.iu.edu/dspace/bitstream/handle/2022/26875/2021-10-22_wim_wing_did_slides.pdf?sequence=1&isAllowed=y -->

## Method Comparison

Finally, let’s revisit that dataset where all the treatment effects were
clearly positive but the TWFE estimate turned out negative. We can
compare the results the 2SDiD, SA and TWFE. Let’s try all of these with
a pooled effect:

<details class="code-fold">
<summary>Code</summary>

``` r
baker <- as.data.frame(baker)
r_twfe <- feols(y ~ treat | id + year, data = baker,
                cluster = ~ state)
r_did2s <- did2s(baker, "y",
                 first_stage = ~ 0 | id + year,
                 second_stage = ~ treat,
                 treatment = "treat",
                 cluster_var = "state")
r_sunab <- feols(y ~ sunab(treat_date, year) | id + year, 
                 baker[baker$year<2004,],
                 cluster = ~state)

cat("TWFE DiD:\n")
r_twfe$coeftable; cat("\n\nTwo-Stage DiD:\n")
r_did2s$coeftable; cat("\n\n\nSun & Abraham:\n")
summary(r_sunab, agg = "ATT")$coeftable
```

</details>

<details>

<summary>

Output
</summary>

    TWFE DiD:
           Estimate Std. Error   t value   Pr(>|t|)
    treat -6.690186   3.391857 -1.972426 0.05568123
    attr(,"vcov_type")
    [1] "Clustered (state)"


    Two-Stage DiD:
          Estimate Std. Error  t value   Pr(>|t|)
    treat 68.32906   5.191276 13.16229 1.9815e-39
    attr(,"vcov_type")
    [1] "Corrected Clustered (state)"



    Sun & Abraham:
        Estimate Std. Error  t value      Pr(>|t|)
    ATT 68.33072 0.01417939 4819.018 3.118791e-114
    attr(,"vcov_type")
    [1] "Clustered (state)"

</details>

Last but not least, let’s visualize the event study versions.

<details class="code-fold">
<summary>Code</summary>

``` r
es_twfe <- feols(y ~ i(time_til, treated, -1) | id + year, data = baker)
es_did2s <- did2s(baker, "y",
                  first_stage = ~ 0 | id + year,
                  second_stage = ~ i(time_til, treated, -1),
                  treatment = "treat",
                  cluster_var = "id")
iplot(es_twfe, grid = F, xlab = "Time until Treatment", main = "")
iplot(es_did2s, add = T, x.shift = 0.1, col = "tomato")
iplot(r_sunab, add = T, x.shift = -0.1, col = "dodgerblue")
legend("topright", c("TWFE", "2SDiD", "SA"),
       col = c("black", "tomato", "dodgerblue"),
       bty = "n", pch = 19, ncol = 2)
```

</details>

<details>

<summary>

Plot
</summary>

<img src="module02_img/02-02-unnamed-chunk-23-1.svg" style="width:90.0%"
data-fig-align="center"
data-fig-alt="Event study estimates of each discussed approach in addition to Sun &amp; Abraham." />

</details>

Before wrapping up, it is important to make the following note. Figures
like the above are *bad* and you should *not* be doing this. Although
each estimator is similar, there are subtle differences. While figures
like the above are tempting, it is more likely to signal a lack of
understanding than robustness. See Roth
([2026](https://doi.org/10.1007/s42973-026-00235-x)).

[^1]: The beautiful, sentient being that it is

[^2]: That said, if you want to change how `create_data` works, you can
    copy, paste, and modify it in one of the following `webr` code
    chunks.

[^3]: Notation taken from Baker et
    al. ([2022](https://doi.org/10.1016/j.jfineco.2022.01.004))

[^4]: Note: it’s not the case that static $\rightarrow$ too small and
    dynamic $\rightarrow$ too large. This is just an artifact of our
    simulated data. My point is that the coefficient estimate can be
    either too large or too small and it’s not necessarily clear which
    will happen.

[^5]: Note: in real life, things are hardly ever as clear!

[^6]: Note: “U” represents never-treated units, “E” represents
    early-treated units, and “L” represents later-treated units.

[^7]: This is not to say that some parts cannot be negative. In real
    life, this will likely be the case. However, in this illustration,
    it’s clear that the negative is unintended.

[^8]: Note: in these data, there is no never-treated group. Therefore,
    the decomposition only contains two groups: early vs late and late
    vs early.

[^9]: This is an excellent blog post, and would be worth a read.

[^10]: I am using $Y^0$ to denote $Y$ as untreated.
