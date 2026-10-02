# Foundations
Alex Cardazzi

All materials can be found at
<a href="https://alexcardazzi.github.io/econ311.html"
target="_blank">alexcardazzi.github.io</a>.

## What Is Claude Code?

You’ve probably used a chat-window AI tool before — you type a question,
it types back an answer. Claude Code is different: it works directly
inside your files and folders. Instead of copy-pasting code between a
chat window and RStudio, you point it at your project, and it can read
your scripts, write new code, run code, and edit files directly.

This matters for this course specifically: your process reflections
(part of every homework) ask you to describe how you used Claude Code on
that assignment. And to be clear, **you are required to use Claude
Code** (or something like it), both in this course and beyond it. AI is
now a standard part of the workplace, especially for new grads, so
learning to use it well is essential for staying competitive. However,
it’s far from perfect, which is why you, as the human, are 100%
responsible for its output.

## Ways to Run Claude Code

Claude Code is normally used from a terminal — a text-based way of
giving your computer instructions instead of clicking buttons — but you
have a few options, and any of them is fine for this course:

- **RStudio’s Terminal tab**: built into RStudio, next to the Console in
  the bottom-left panel. No extra setup required.
- **Your computer’s native terminal**: Terminal on Mac, PowerShell or
  Windows Terminal on Windows, if you’re already comfortable with one.
- **A GUI (desktop app) version**: Claude Code also has graphical
  desktop apps if you’d rather not use a command line at all. Check
  [claude.com/claude-code](https://claude.com/claude-code) for current
  availability on your OS.

Whichever you choose, the rest of this course will sometimes describe
things in terms of typing commands — if you use the GUI version, the
same ideas apply, just through buttons instead of typed commands.

## Installing Claude Code

Claude Code is maintained by Anthropic (the company behind the Claude
models). Install instructions are OS-specific and occasionally change,
so rather than list exact commands here that might go stale, install it
by following the current instructions at
[claude.com/claude-code](https://claude.com/claude-code).

<div class="aside">

<span class="fragment">The terminal-based version runs on Node.js. If
you don’t already have Node installed, the official instructions will
walk you through getting it first — this is a one-time setup
step.</span>

</div>

A few things to know before you start:

- You’ll need an **Anthropic account** to log in.
- Claude Code is **not free**. It requires either a paid subscription or
  per-token billing — see the syllabus and AI Policy for current pricing
  and the recommended starting tier. Budget for this the same way you’d
  budget for a textbook.
- Install it once, on your own machine. You’ll use the same install for
  the rest of the course.

## Checking It Worked

If you’re using a terminal (RStudio’s or your native one), open it and
type:

``` bash
claude
```

If it launches and asks you to log in (or greets you if you’re already
logged in), you’re set up correctly. If you get a “command not found”
error, the install didn’t complete. Go back to the official docs or
bring the error to office hours. If you’re using the GUI version, just
open the app directly and confirm you can log in.

Note that Claude may also ask: “*Is this a project you created or one
you trust? (Like your own code, a well-known open source project, or
work from your team). If not, take a moment to review what’s in this
folder first.*” Personally, I do not give Claude access to all of my
files/folders. Instead, I first navigate to the folder I want it to work
within (e.g., a folder where all of your ECON 311 material will live)
before launching it. To do so, I type:
`cd Documents/school/Spring 20XX/econ311` (or whatever your specific
filepath is), and then type `claude`. In this case, Claude is only given
access to files/folders in that folder, but not outside.

## Your Course CLAUDE.md

You’ll find the starter `CLAUDE.md` file on the course website at
<https://alexcardazzi.github.io/econ311/CLAUDE.md>. This is a plain-text
file that tells Claude Code the ground rules for how it should help you
in this specific class — think of it as instructions you give the tool,
not instructions it gives you. You will need to download this file and
place it in the folder you are starting Claude from (ideally your course
folder located at something like:
`Documents/school/Spring 20XX/econ311`). Alternatively, you can open
your notepad app or program, paste the contents of the link into it, and
save as “CLAUDE.md” (be sure to *not* have .txt as the file extension).

## Saving the Course Notes

Every lecture page on the course website has a plain-text version you
can download (a markdown, or `.md`, file). Download the ones you want
Claude Code to know about and save them in a folder called `notes/`
inside your course folder, next to `ai_logs/` (see below). With the
notes saved, Claude can answer questions using the same explanations and
code you see in the lectures rather than generic ones from the internet.

Your course `CLAUDE.md` already has this behavior built in: it tells
Claude to look in `notes/` first, and to suggest that you create the
folder if it is missing. Even so, you should know what it is doing on
your behalf.

## Session Logs

Your course `CLAUDE.md` also tells Claude to keep a running log of your
work together. It creates an `ai_logs/` folder inside your course folder
and keeps one file per assignment (for example, `ai_logs/hw1.md`). At
the end of a session, or after an important decision, Claude adds a
short dated entry: what you worked on, what you decided and why, the
errors you hit, and what is left to do. At the start of your next
session, it reads the most recent log so you can pick up where you left
off. The log is also candid about who did what, meaning which choices
were yours and which began as Claude’s suggestions.

These logs are useful to you, since they are good raw material for the
process reflection in each homework (which you must still write
yourself). They are also read by the separate Claude session that asks
you two questions at the end of your recorded presentation, so they help
it ask fair questions.

This behavior is built into your `CLAUDE.md` anyway, but you should know
what it is doing on your behalf. Feel free to open the files in
`ai_logs/` and read them.
