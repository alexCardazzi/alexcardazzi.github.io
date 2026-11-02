# Rubric
Alex Cardazzi

Each graded assignment after the Onboarding HW (the IE and the
Implementation for each of difference-in-differences, synthetic control,
and IV or RDD) is worth 60 points: a 20-point written report, a 20-point
recorded presentation, and a 20-point Q&A. The rubric applies to the
**whole report**, not to individual sections. The Onboarding HW is
graded question by question against an answer key (with partial credit)
and is not scored with this rubric.

## Written report (20 points)

Each of the four dimensions is scored 5, 3, 1, or 0. Scores are
holistic: find the band that best describes the report as a whole. The
bands are guides, not checklists. Scores of 4 and 2 are possible for
work between bands.

Presentation and Reflection are the same for both kinds of assignment.
The other two dimensions depend on whether the report is an IE (a
design) or an Implementation (results).

### Presentation (both, 5)

Does the rendered `.html` read like a report a stranger could follow?

- **5**: Renders without errors. Clear order. Every figure and table is
  labeled, referred to in the text, and earns its place. Code is
  organized, commented, and folded neatly, and each output can be tied
  to the chunk that made it.
- **3**: Renders and is mostly clear. A few figures or tables are
  unlabeled or unreferenced, or some code does not fold cleanly.
- **1**: Renders but is hard to follow: output dumped without
  explanation, default-looking figures, disorganized code.
- **0**: Does not render, or is not a report.

### Reflection (both, 5)

Does the Process Reflection show real, specific engagement with Claude?

- **5**: In the student’s own words. Specific about what they asked
  Claude, where its first attempt fell short, and what they changed or
  rejected and why. Shows the student checking Claude against the course
  material.
- **3**: Specific in places, generic elsewhere. Describes what happened
  more than why, or shows little pushback.
- **1**: Vague or generic (“Claude was helpful”), with few or no
  concrete examples.
- **0**: Missing, or reads as though the AI wrote it.

### IE: Design (5)

Is the method the right one for this setting, and are its assumptions
stated and honestly assessed?

- **5**: The question is clear and the setting is specific (who or what
  is treated, when, compared with whom). The method fits the setting,
  and the student says why this method over the alternatives from the
  course. The identifying assumptions (parallel trends, no anticipation,
  exclusion, no manipulation at the cutoff, and so on) are stated in
  this setting’s own terms and honestly assessed, including the ways
  they might fail. A DAG or equation shows the causal parameter of
  interest.
- **3**: The method fits, but the assumptions are stated generically
  (copied from the notes) or the threats are thin. The causal parameter
  is present but vaguely defined.
- **1**: The method is a poor fit for the setting, or the assumptions
  are named but not applied to the setting, or the causal parameter is
  unclear.
- **0**: No real design, or a design for a different method than the one
  assigned.

### IE: Plan (5)

The IE works like a pre-registered analysis plan: the student commits to
a study they will carry out themselves the following week. The data do
not have to be downloaded or loaded for the IE, but the student is
responsible for being able to execute the plan, and should have
pressure-tested it with Claude for whether it is doable given the time,
the data access, and the scope. Every IE must be motivated by a
published paper that the student finds; this dimension also scores that
link. If a paper the student starts from has a replication package, they
must not use it, and must say so.

- **5**: The plan is concrete enough that someone else could tell
  afterward whether the student did what they said: the named data
  source, the unit of observation, the years, the key variables
  (outcome, treatment, and any controls or instrument), the
  specification or estimator, and the diagnostics the student will run.
  The scope is realistic for one week of work, and the student has
  thought about access and what to do if something does not pan out. The
  design is motivated by a published paper that the student found: it is
  summarized accurately, and the proposed study clearly builds on it (it
  may extend the paper, complement it, or apply it in a new setting; it
  need not be novel, and it may be simpler).
- **3**: The plan names the data and method but is vague in places (a
  variable that is not pinned down, no stated diagnostics, or no sense
  of what could go wrong), or the scope is ambitious. The link to the
  motivating paper is present but loose.
- **1**: The data are assumed to exist (“I would use data on…”) with no
  source, the plan is too vague to be checked later, or the scope is
  clearly unmanageable in a week. The paper is only summarized, with no
  connection to the design.
- **0**: No plan. No motivating paper.

Out-of-scope methods should be flagged and explained. Anything that is
not in the course notes is fair game in the Q&A.

### Implementation: Execution (5)

Was the method carried out correctly on real data, with the diagnostics
the method calls for?

- **5**: The method is implemented correctly and follows the student’s
  IE plan. Changes from the plan are fine within reason (a different
  year range, a dropped control, a swapped data source), but the student
  explains clearly what changed and why. The data work is documented
  (what was dropped, recoded, merged, and why). The method’s own
  diagnostics are shown and discussed (pre-trends or an event study for
  DiD, pre-treatment fit and placebo tests for SCM, first stage and
  instrument strength for IV, the running-variable plot, bandwidth
  sensitivity, and a manipulation check for RDD). The analysis is as
  simple as it can be while still answering the question.

- **3**: The method is implemented correctly, but a diagnostic is
  missing or only shown, not discussed. Some data choices are
  undocumented. Changes from the IE are not explained.

- **1**: The method has a substantive error or does not match the IE, or
  the diagnostics are absent.

- **0**: No real implementation, or the analysis does not use the
  assigned method.

- DiD with staggered treatment timing: students are expected to address
  the problem with two-way fixed effects using a method from the course
  notes (Gardner’s two-stage estimator or Sun and Abraham’s estimator),
  **if** their setting has staggered timing. If it does not, no such
  method is required.

### Implementation: Interpretation (5)

Are the results understood and communicated honestly?

- **5**: The estimate is interpreted correctly in plain language, with
  units, magnitude, and uncertainty. The student says what the result
  does and does not show, whether the identifying assumptions held up in
  the diagnostics, and what would change their conclusion. Findings tie
  back to the question.
- **3**: Mostly correct, but vague, overstated, or thin on limitations.
  The assumptions are not revisited in light of the results.
- **1**: Several incorrect interpretations, or the estimate is restated
  without meaning (“the coefficient is 0.4”), or claims go well beyond
  the evidence.
- **0**: Little or no interpretation, or it contradicts the results
  shown.

## Recorded presentation (20 points)

Two parts, each scored 10, 7, 4, or 0.

**Content (10)**

- **10**: IE: states the question, the setting, the method and why, the
  key assumptions and threats, and the data plan. Implementation: states
  the question, the data, the method, the findings, and what they do and
  do not mean. Accurately represents the submitted report.
- **7**: Covers most of these, with one thin or missing piece.
- **4**: Covers only some of these, or is mostly a description of what
  was done with little on why.
- **0**: Missing, off topic, or does not reflect the submitted report.

**Articulation (10)**

- **10**: Explains the work in their own words, at a pace a boss or
  client could follow, without reading from the report. Shows they
  understand what the numbers or assumptions mean.
- **7**: Mostly clear and in their own words, but with some reading from
  the report, or with places where the explanation is shaky.
- **4**: Largely reads from the report or a script, or cannot explain
  key points in plain words.
- **0**: Cannot explain the work.

## Q&A (20 points)

Four questions asked by Claude, generated via [this
prompt](https://alexcardazzi.github.io/econ708a/ie_check_prompt.txt)
(for an IE) or [this
prompt](https://alexcardazzi.github.io/econ708a/implement_check_prompt.txt)
(for an Implementation), each scored 5, 3, or 0. The recording must show
the Q&A starting right after the presentation, in the same unbroken
recording. Because the design is built on a published paper, the
questions also cover the paper and how the student’s study builds on it.

- **5**: The answer shows the student understands the assumption,
  result, or choice being asked about, in plain words.
- **3**: Partially correct or hesitant, or the student explains the
  choice but not what would change if it had been made differently.
- **0**: No answer, or an answer that shows the student does not
  understand their own work.

Any method, estimator, or concept in the report that is not in the
course notes is fair game, and the Q&A will focus on it. A student who
used something beyond the notes is expected to explain it as well as
they would explain anything from the course.

## Notes for grading

- The recording must show the whole screen (with the time) and include
  the student’s voice. Recordings outside the five-to-twenty-minute
  range, or that do not show the full screen, may be penalized at my
  discretion.
- I may step in as the question-asker for any submission if the Q&A does
  not reach the level or type of questioning I am looking for. I may
  also request a short meeting with any student about a submitted
  assignment (see the syllabus). Scores on the underlying assignment may
  be revised afterward.
