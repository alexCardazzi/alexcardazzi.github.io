# ECON 311: Analytical Tools for Economists
Dr. Alexander (Alex) Cardazzi



## Course Information

**Semester**: Spring 2027, Pt. 1 (8-week accelerated session)

**Delivery**: Asynchronous online (there are no required meeting times)

**Office Hours**: Wednesday 03:00 PM - 05:00 PM (or by appointment)

## Course Description


In this course, students will learn how to build statistical models and
use data to answer real-world questions. Through hands-on work with
modern statistical software, students will learn to visualize, estimate,
interpret, evaluate, and articulate relationships in data. Students will
obtain real, tangible skills such as experience with statistical
software as well as develop their economic intuition.

ECON 311 is a hands-on introduction to data analysis for economists. You
will learn to work with real datasets, summarize what you see, test
hypotheses, and build regression models — all using R, a free and widely
used statistical programming language. By the end of the course, you
will be able to take a dataset you have never seen before, ask a
meaningful question about it, and produce a clean, professional
analysis. You will also learn to use AI tools thoughtfully as part of
your analytical workflow, a skill that employers increasingly expect.

**Prerequisites**: ECON 202S or equivalent.

## Textbooks and Other Materials

There is no required text for this course. All course materials are
provided free on the course website.

### Required Software

As this course focuses on the learning and application of econometric
techniques, a computer or laptop (Mac, Windows, or Linux) with R and
RStudio installed is required for this course. Students can obtain the
latest version of R from [r-project.org](https://www.r-project.org/).
Students can obtain a free version of RStudio from
[posit.co](https://posit.co/downloads/). While students can directly
code in R, it is recommended that student use RStudio to facilitate
interactions with the R language. RStudio also comes bundled with
Quarto, which is what students will use to turn their work into `.html`
reports. Students will also install numerous open source packages
throughout the course.

Students are also required to use Claude Code, an AI coding assistant.
Claude Code is **not free**; see the [Artificial
Intelligence](#artificial-intelligence) section below for pricing and
setup information.

**Students need *not* install this software prior to beginning the
course.** Setup instructions are provided in Module 1.

### Required Hardware

In addition to a computer, a microphone is required for the recorded
presentations. A webcam is optional.

## Course Learning Objectives

1.  Use R and RStudio to load, manipulate, and visualize real-world
    datasets.
2.  Compute and interpret descriptive statistics, probability concepts,
    and inferential tools including confidence intervals and hypothesis
    tests.
3.  Estimate, interpret, and communicate the results of linear
    regression models, including simple regression, multiple regression,
    and common extensions.
4.  Diagnose and explain common regression problems, including omitted
    variable bias and multicollinearity.
5.  Evaluate the limitations of correlational analysis and articulate
    conditions under which regression estimates do and do not support a
    causal interpretation.
6.  Communicate quantitative findings clearly in written and oral form,
    including appropriate acknowledgment of uncertainty and limitations.

## Course Schedule

This is an accelerated, fully asynchronous course. Below is a schedule
for course topics with corresponding assignments. Due dates are listed
in the table; they are also listed under *Grading Policy* below. Within
each module, you may work through the notes at your own pace, but plan
to finish each module by the end of its week.

To help set expectations, Modules 1, 4, and 6 will likely be the most
intensive modules. Module 1 requires a lot of set up, which can cause
headaches for students. However, it is very important to get the
software installed and working ASAP given the time constraints. **Please
reach out early if there are issues.** Modules 4-6 are probably the most
content-rich and represent discrete steps in knowledge.

| Module | Dates | Topics | Lectures | Assignment |
|:---|:---|:---|:---|:---|
| Module 1 | Jan 11 - Jan 17 | R Bootcamp | 1.1 - 1.8 | Onboarding HW (40 pts), due Sun, Jan 17 |
| Module 2 | Jan 18 - Jan 24 | Descriptive Statistics | 2.1 - 2.3 | \- |
| Module 3 | Jan 25 - Jan 31 | Distributions, CLT, and Inference | 3.1 - 3.3 | HW 1 (90 pts), due Sun, Jan 31 |
| Module 4 | Feb 1 - Feb 7 | Simple Regression | 4.1 - 4.6 | HW 2 (90 pts), due Sun, Feb 7 |
| Module 5 | Feb 8 - Feb 14 | Multiple Regression and OVB | 5.1 - 5.3 | \- |
| Module 6 | Feb 15 - Feb 21 | Dummies, Interactions, Categorical Variables, and Binary Outcomes | 6.1 - 6.4 | HW 3 (90 pts), due Sun, Feb 21 |
| Module 7 | Feb 22 - Feb 28 | Fixed Effects and the Limits of Regression | 7.1 - 7.4 | \- |
| Finals Week | Mar 1 - Mar 5 | \- | \- | HW 4 (90 pts), due Tue, Mar 2 |

Course Schedule

## Grading Policy

The evaluation for this course consists of an onboarding homework and
four larger homework assignments, each of which includes a recorded
presentation. The final grade is comprised of the following elements.
All assignments are due at 11:59 p.m. on the date listed.

| Assignment    | Points |         Due |
|:--------------|-------:|------------:|
| Onboarding HW |     40 | Sun, Jan 17 |
| HW 1          |     90 | Sun, Jan 31 |
| HW 2          |     90 |  Sun, Feb 7 |
| HW 3          |     90 | Sun, Feb 21 |
| HW 4          |     90 |  Tue, Mar 2 |
| Total         |    400 |             |

Assignment Point Totals and Due Dates

\

Grades will be determined by the sum of points earned, and then
converted using this table:

| Letter | Minimum | Maximum |
|:------:|--------:|--------:|
|   A    |     372 |     400 |
|   A-   |     360 |     371 |
|   B+   |     348 |     359 |
|   B    |     332 |     347 |
|   B-   |     320 |     331 |
|   C+   |     308 |     319 |
|   C    |     292 |     307 |
|   C-   |     280 |     291 |
|   D+   |     268 |     279 |
|   D    |     252 |     267 |
|   D-   |     240 |     251 |
|   F    |       0 |     239 |

Final Grade Conversion Table

Except for grades of “Incomplete”, all grades are considered final when
reported by a faculty member at the end of a semester. A change in grade
may only be requested when a calculation, clerical, administrative, or
recording error is discovered in the original assignment of a course
grade or when a decision is made by the faculty member to change the
course grade because of the disputed academic evaluation procedures.

Grade changes necessitated by a calculation, administrative, or
recording error must be reported within a period of six months from the
time the grade is awarded. No grade may be changed as the result of a
re-evaluation of a student’s work or the submission of supplemental work
following the close of a semester.

### Onboarding Homework

The Onboarding HW verifies that you have your technical environment
configured and can produce and submit course deliverables before
substantive graded work begins. It is more about logistics rather than
content. You will install R and RStudio, install Claude Code and
initialize it with the course’s
[`CLAUDE.md`](https://alexcardazzi.github.io/econ311/CLAUDE.md), set up
a student ePortfolio account, render the provided Quarto template to
`.html`, and upload it to your ePortfolio. You will submit a link to
your ePortfolio via Canvas. The Onboarding HW is graded on completion:
full credit for a submission that renders correctly and shows evidence
of completing all steps, partial credit for a good-faith attempt with
rendering issues, and zero for non-submission.

### Homework

Each of the four homework assignments provides a pre-supplied dataset
(or datasets) and a set of open-ended research questions. You will
conduct full analyses in R, render all output to `.html` using Quarto,
and submit the rendered file on Canvas along with a recorded
presentation (described below). The `.html` file must render without
error, include properly labeled figures, and contain a **Process
Reflection** section. Please see the following sections about
[Artificial Intelligence](#artificial-intelligence) for additional
details on how AI fits into the homework.

Each homework is worth 90 points: a 60-point written report and a
30-point recorded presentation.

- **Written report (60 points)**: the report is scored on four
  dimensions, each worth 15 points: **Presentation** (the `.html`
  renders cleanly, figures are labeled, code is organized, and the
  document is structured), **Analysis** (the approach is appropriate for
  the question, uses methods from the course material, and documents and
  explains any out-of-scope methods in simple terms), **Interpretation**
  (results are interpreted correctly in plain language, with direction,
  magnitude, and units stated and limitations acknowledged), and
  **Reflection** (the Process Reflection shows specific, iterative
  engagement with Claude, describing what was tried, changed, and why).
- **Recorded presentation (30 points)**: you will record yourself
  presenting your work, as if you were presenting to relevant
  stakeholders (e.g., your boss, clients, investors, etc.). The
  presentation itself is worth 20 points (graded on **content** and
  **articulation**), and 10 points come from answering two questions
  asked by Claude, playing the role of the audience (5 points each). You
  need not submit the materials you use for your presentation, since you
  will submit a recording.

The [full rubric](https://alexcardazzi.github.io/econ311/rubric.html) is
posted on the course website.

### ePortfolio

In an effort to help students reflect on and synthesize their learning
experiences, as well as demonstrate their skills to potential employers,
certain courses taught by faculty in the Economics department will
require the creation of, or addition to, an ePortfolio. Given the status
of this course as the capstone of the Economics major, this course will
contain an ePortfolio component.

Your Onboarding HW asks you to set up an ePortfolio and upload a
rendered document to it. Beyond that, the extent to which students use
their ePortfolio is ultimately up to them, but adding your homework
reports to it should help to differentiate you from competing job
seekers. As a note, all material generated in this course will be
portable `.html` files that can easily be uploaded to ePortfolios.

Online ePortfolio resources for ODU students can be found at
[odu.edu/asis/eportfolio](https://www.odu.edu/asis/eportfolio).

**Disclaimer**: this course incorporates various online software and
other technologies. Some technologies require you to either create an
account on an external site or develop assignment content using them.
The content, as well as your name/username or other personally
identifying information may be publicly available as a result. While the
purpose of these assignments is to engage with technology as a means for
representing the content we are covering in class, please see me for an
alternative activity if you object to potentially sharing your account,
name, or other content you create in these technologies.

### Incomplete Grades

A grade of “I” indicates assigned work yet to be completed in a given
course, or absence from the final examination, and is assigned only upon
instructor approval of a student request. The “I” grade may be awarded
only in exceptional circumstances beyond the student’s control. The “I”
grade becomes an “F” if not removed by the day grades are due for
following term based on specific criteria: [Incomplete, Withdraws and Z
grades](https://ww1.odu.edu/academics/academic-records/grades/incompletes-withdraws-zgrades).

## Expectations

**What you can expect from me, as your instructor:**

- I will ensure the accuracy of the course content.
- I will provide clear guidelines regarding the course assignments.
- I will be available to answer your questions.
- I will provide meaningful feedback in a timely manner.
- I will do my best to create a worthwhile learning experience for all
  my students.
- I will follow fair and clear evaluation guidelines for all the course
  assignments.
- I will do my best to prepare you for assignments.

**What I expect from you, as my student:**

- You will complete all course readings and assignments in a timely
  manner.
- You will follow the ODU honor pledge.
- You will follow proper “netiquette.”
- You will interact with your faculty and classmates professionally and
  respectfully.
- You will avoid sarcasm and inappropriate language, including the use
  of ALL CAPS.
- You will acknowledge your classmates’ ideas and build on them to
  contribute to the discussion.

## Course Policies

### Communication

Students should feel welcome to contact me via email
(<acardazz@odu.edu>). Generally, I respond to well-crafted emails within
48 business hours. I have an ‘open door’ policy for student questions
and strongly encourage students to communicate with me. Of course, since
this is an online course, I will be available over Zoom as well.

Students should take the time to craft complete, professional emails.
The more information that you can provide about a question or problem,
the more likely that my response will be helpful. Avoid non-professional
language and practice communicating in the corporate workplace. Emails
that are unprofessional will be returned with no action. There are many
guides on how to compose a professional email which you can easily find
online.

### Attendance and Participation

This is a fully asynchronous course with no required meeting times.
Students are expected to engage with the lecture materials and complete
assignments within the designated windows each week. There are no
attendance points or participation grades, as students are responsible
for their own learning. Students are expected to monitor Canvas
regularly for announcements.

### Late Assignments

All due dates are firm. Late submissions of any assignment will receive
a score of zero unless discussed at least forty-eight hours prior to the
deadline. Special circumstances that are communicated in advanced will
be handled on a case by case basis.

### Plagiarism

Plagiarism and turning in work that is not yours is grounds for being
assigned a zero on an assignment, is a violation of the [University
Honor Code](syllabus.html#honor-pledge), and could result in failure in
the course and/or academic action by the university.

### Artificial Intelligence

You are currently enrolled in a course you can think of as a
simultaneous introduction to econometrics, the programming language `R`,
and (properly using) AI tools. You are enrolled in said course during a
time in which artificial intelligence (AI) is booming. It is quite
possible that AI will end up being the most transformative technology
since the internet, and ignoring it would be foolish. As we get started
in this course, I want to provide a few additional thoughts on AI and
its use in this course.

As I am sure you understand by now, education is having to rapidly
adjust to AI. This means that much of what previously worked, especially
with regards to assessment, no longer does. In a fully asynchronous
course, I cannot (and will not try to) police what you do on your own
computer. At this point, the only path forward is to embrace the idea
that AI will forever be part of the “toolkit,” much like how
calculators, spell-check, and search engines are. Therefore, **Claude
Code is a required tool in this course**, and thoughtful AI use is
expected. Reckless AI use, much like reckless use of other tools (e.g.,
copying and pasting text from Wikipedia and claiming it as your own), is
not.

What constitutes appropriate AI use? Appropriate AI use is
*collaborative* rather than *substitutive*. Using AI to help you
understand why your code is not working is appropriate (collaborative),
but having it write an entire analysis for you, which you then submit
with no meaningful engagement, is not (substitutive). When determining
the appropriateness of their AI use, students may find it helpful to ask
themselves whether someone with no training, but access to an AI, could
have produced the same thing. If the answer is yes, then the student has
not added any value, and their AI use was substitutive. Students may
also frame this question through the lens of employment and ask
themselves whether someone would hire them for what they produced, or if
this is instead something an AI could produce (for much a lower cost)?
Companies are chomping at the bit to cut labor costs by replacing
employees with AI; do something that makes this a difficult decision for
them.

Rather than policing AI use, the course is designed so that the work
itself shows whether you understand it. There are two mechanisms for
this:

- **Process Reflection**. Claude Code does not produce shareable
  conversation transcripts. Instead, every homework must include a
  Process Reflection section, written by you, that answers: What did you
  try first? What did Claude suggest? What did you change, and why?
  Where did you push back on the AI’s suggestion? (Claude Code is not
  allowed to write this section, and it knows that.)
- **Recorded presentation**. Every homework also requires a recorded
  presentation of your work, followed by two questions from Claude
  playing the role of your audience. Students who cannot explain their
  work intuitively will receive reduced scores.

Note that I reserve the right to request a short individual meeting with
any student to discuss a submitted assignment. This is entirely at my
discretion; it is not scheduled, not guaranteed for any student, and not
a graded assignment in its own right. It is most likely to be invoked
when a submission reads as though it relies too heavily on AI output
rather than the student’s own understanding. In that meeting, the
student will be asked to explain their work and answer follow-up
questions. A student who cannot do so satisfactorily should expect their
grade on the underlying assignment to be revised accordingly.

#### Directions for using AI

Claude Code requires a paid subscription or per-token billing. Plans are
currently available at \$20/month, \$100/month, or \$200/month, and
per-token billing is also an option. The \$20/month plan is a reasonable
starting point (and what I personally subscribe to); if you find that
you need more capacity, you can upgrade at your own discretion.

Students will use Claude Code with a starter
[`CLAUDE.md`](https://alexcardazzi.github.io/econ311/CLAUDE.md) file
provided on the course website. This file initializes Claude Code with
the constraints of this course. For example, it tells Claude to use the
same (base R) approach as the course notes, and to help you without
doing your thinking for you. It also has Claude keep session notes in an
`ai_logs/` folder, which are for your own use (so Claude remembers
what’s going on between sessions) and are *not* submitted. Setup
instructions are in Module 1.

For example, consider the following good and bad uses of AI:

> [!TIP]
>
> ### Good AI Use
>
> I am working on a homework problem and my regression keeps returning
> `NA` for one of my coefficients. Here is my code and the error. Can
> you help me figure out what is going on?
>
> *or*
>
> I have been able to clean the data and estimate my model, but I’m not
> sure how to interpret the coefficient on my interaction term. Can you
> talk through it with me, and then I will write up the interpretation
> myself?

> [!IMPORTANT]
>
> ### Bad AI Use
>
> Here is my homework. Please do the whole analysis and write it up for
> me.
>
> *or*
>
> Please write my Process Reflection.

#### Steps to Set Up Claude Code

1.  Follow the instructions in Module 1.4 to install Claude Code.
2.  Download the starter
    [`CLAUDE.md`](https://alexcardazzi.github.io/econ311/CLAUDE.md) file
    from the course website and save it in your course folder.
3.  Optionally, save the course notes (each lecture page has a
    plain-text `.md` version) in a `notes/` folder inside your course
    folder so that Claude Code can read them.

### Course Disclaimer

The course schedule and activities are subject to change. Changes will
be posted as Announcements in Canvas. All instructional materials and
homework assignments can be found
[here](https://alexcardazzi.github.io/econ311.html).

## University Policies

### Code of Student Conduct and Academic Integrity

The [Office of Student Accountability & Academic
Integrity](https://ww1.odu.edu/oscai) (OSAAI) oversees the
administration of the student conduct system, as outlined in the Code of
Student Conduct. Old Dominion University is committed to fostering an
environment that is: safe and secure, inclusive, and conducive to
academic integrity, student engagement, and student success. The
University expects students and student organizations/groups to uphold
and abide by standards included in the Code of Student Conduct. These
standards are embodied within a set of core values that include personal
and academic integrity, fairness, respect, community, and
responsibility.

### Honor Pledge

By attending Old Dominion University, you have accepted the
responsibility to abide by the Honor Pledge:

*I pledge to support the Honor System of Old Dominion University. I will
refrain from any form of academic dishonesty or deception, such as
cheating or plagiarism. I am aware that as a member of the academic
community it is my responsibility to turn in all suspected violations of
the Honor Code. I will report to a hearing if summoned.*

### Discrimination Policy

The purpose of this policy is to establish uniform guidelines to promote
a work and education environment that is free from harassment and
discrimination, as defined below, and to affirm the University’s
commitment to foster an environment that emphasizes the dignity and
worth of every member of the Old Dominion University community. The
[Discrimination
Policy](https://ww1.odu.edu/about/policiesandprocedures/university/1000/1005)
details the process to address complaints or reports of retaliation, as
defined by this policy.

### Diversity and Inclusion

The [Division of Student & Campus Life](https://ww1.odu.edu/sees) values
the uniqueness of our Monarch community. The word “engagement” reflects
our commitment to embrace the differences in our cultural backgrounds,
perceptions, beliefs, traditions, world views, socio-economic status,
cognitive and physical abilities.

We will strive to serve as the pre-eminent model for engaging every
student to achieve their own success. Our core values are fueled by our
responsibility and actions toward community development and engagement,
cultural competence and understanding, physical and mental wellness and
inclusion for every member of ODU. We will embrace our greatest
strength - the diverse composition of our student body and workforce.
For more information regarding diversity and inclusion, please visit the
[Office of Intercultural
Relations](https://www.odu.edu/intercultural-relations).

### Educational Accessibility and Accommodations

Old Dominion University is committed to ensuring equal access to all
qualified students with disabilities in accordance with the Americans
with Disabilities Act. The [Office of Educational
Accessibility](https://www.odu.edu/accessibility) (OEA) is the campus
office that works with students who have disabilities to provide and/or
arrange reasonable accommodations.

The [Accommodations for Students with
Disabilities](https://ww1.odu.edu/about/policiesandprocedures/university/4000/4500)
define the procedures used to accommodate student with disabilities.
Students are encouraged to self-disclose disabilities that the Office of
Educational Accessibility has verified by providing Accommodation
Letters to their instructors early in the semester in order to start
receiving accommodations. Accommodations will not be made until the
Accommodation Letters are provided to instructors each semester

### University Email Policy

With the increasing reliance and acceptance of electronic communication,
email is considered an official means for University communication. Old
Dominion University provides each student an email account for the
purposes of teaching and learning, research, administration, and
service. It is the responsibility of every eligible student to activate
MIDAS, the Monarch Identification and Authorization System, to obtain
email access. It is important that all students are aware of the
expectations associated with email use as outlined in the [Student Email
Standard](https://ww1.odu.edu/about/policiesandprocedures/computing/standards/11/02).
The email account provided by the University is considered to be an
official point of contact for correspondence. Students are expected to
check their official e-mail account on a frequent and consistent basis
in order to stay current with University communications. Mail sent to
the ODU email address may include notification of University-related
actions, including academic, financial, and disciplinary actions. For
more information about student email, please visit [Student
Computing](https://ww1.odu.edu/academics/student-computing).

### Withdrawal

A syllabus constitutes an agreement between the student and the course
instructor about course requirements. Participation in this course
indicates your acceptance of its teaching focus, requirements, and
policies. Please review the syllabus and the course requirements as soon
as possible. If you believe that the nature of this course does not meet
your interests, needs or expectations, if you are not prepared for the
amount of work involved – or if you anticipate assignment deadlines or
abiding by the course policies will constitute an unacceptable hardship
for you – you should drop the course by the drop/add deadline, which is
listed in the [ODU Academic
Calendar](https://www.odu.edu/academics/calendar). For more information,
please visit the [Office of the University
Registrar](https://ww1.odu.edu/registrar).

### Privacy of Student Information

Old Dominion University recognizes its duty to uphold the public’s trust
and confidence, not only in following laws and regulations, but in
following high standards of ethical behavior. Members of the Old
Dominion University community are responsible for maintaining the
highest ethical standards and principles of integrity. The [Code of
Ethics](https://ww1.odu.edu/content/odu/about/policiesandprocedures/university/1000/1002.html)
is a set of values-based statements that demonstrate the University’s
commitment to this goal. The [Privacy of Student
Information](https://ww1.odu.edu/about/policiesandprocedures/studentinfo)
details Family Educational Rights & Privacy Act (FERPA), along with
other information regarding privacy.

### Other Academic Policies

Please see the following link for other academic policies at the
university level:
<https://catalog.odu.edu/undergraduate/policies/academic-policies/>
