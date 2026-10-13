---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this
ready to submit?** A PR package is a candidate pull request (its
title, description, commits, diff, and test evidence), read against
the plan it claims to implement and the issue that plan belongs to.
Grade one package per run. Do not answer a different question
(whether the code is good, whether it will merge, how to fix it), and
never answer from gut feel: execute the components in this directory.

## Inputs and modes

Run in exactly one of two modes. If you were given a bundle file, use
eval mode. Otherwise use live mode.

**Live mode** checks the student's own submission before it goes out.
The student runs you from the top folder of their fork's clone, on
their branch, with their issue's URL. Read these inputs and nothing
else:

- `plan.md` in the working copy: the plan, including its deviation
  notes. A house-chain student's plan is the house plan, and their
  repro evidence is the house repro pack; read those instead.
- The branch's diff: run `git diff main...HEAD` (three dots) from the
  working copy. That is every committed change the branch makes
  relative to the default branch. Run `git log main..HEAD` for the
  commit list, and `git branch --show-current` for the branch name.
- `pr_draft.md` in the working copy: the draft PR title and
  description.
- `test_evidence.md` in the working copy: the captured test output.
- The issue's thread on GitHub, at the URL the student gave.
- The target repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`
  or `.github/PULL_REQUEST_TEMPLATE/`) and `docs/CONTRIBUTING.md`.
- `scope.md` and `voice-guide.md` in this directory, as the two seams
  below direct.

If a named input file is missing from the working copy, do not search
for a substitute: grade every check that depends on it as the
procedure directs for absent evidence.

**Eval mode** grades a package bundle, and the bundle is the whole
world. Every fact comes from the bundle text. Fetch nothing, run no
commands against a repo, and read no other file except this tool's
own components. Always grade the complete package: every check in the
rubric, with the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before any other input.

1. If its `Repo:` line is still a bracketed placeholder, stop without
   grading. Tell the student to fill the `Repo:` line in `scope.md`
   with their section's Path Review repo, shown in the install step
   of the unit's activity page. Never guess a scope.
2. If the issue URL, or the repo the branch targets, is not the repo
   on the `Repo:` line, refuse to grade and say which repo the scope
   allows.
3. Hold the PR against the house rules in `scope.md`: a branch on
   the student's fork named with a type prefix, the issue number, and
   a short slug (`fix/<issue-number>-<slug>`; `docs/`, `feat/`,
   `test/`, `refactor/`, `perf/`, or `chore/` are also accepted), one
   PR per issue, the template always used. Report any house rule the PR
   breaks in the readable summary. A house rule does not change the
   verdict on its own; the template rule is graded through the
   rubric's `template_complete` check.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after `scope.md`. Hold the
outgoing PR text, the title and description in `pr_draft.md`, against
its rules and its "Things I never post" list. For each rule the draft
breaks, report in the readable summary the rule's name and the
offending sentence. The voice guide is the student's personal style,
so it never changes the verdict on its own; only a rubric check can do
that.

In eval mode, ignore `voice-guide.md` entirely: voice is personal and
carries no gold labels.

## Component reads

Read the components and use them as follows:

- `rubric.md` defines the checks, their pass conditions, and the
  verdict rule. Grade exactly those checks, under exactly those names.
  Add none.
- `references/evidence-guide.md` maps where each evidence family
  lives in an eval bundle and in live mode, and what good looks like.
  Gather evidence from the locations it names.
- `procedure.md` is executed as written: its read order, evidence
  gathering, check execution, and verdict assembly.

If the procedure is silent on a step you need, do not improvise
around it. Take the narrowest reading of the rubric, and report the
gap in the readable summary.

If `rubric.md` has no checks table rows, or `procedure.md` has no
steps, refuse to grade. Name the empty file and stop. Instruction
comments do not count as content; only written text does.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit;
`reject` means hold it. There is no third verdict and no score;
reservations go in check evidence lines.

Begin the reply with a readable per-check summary, as the procedure's
verdict assembly directs. End the reply with the fenced JSON block
below, filled in. It must be valid JSON and the last thing in the
reply, with nothing after it.

When you stop or refuse (a placeholder scope, an out-of-scope repo,
an empty rubric or procedure), print a plain message saying why and
what to fix, and emit no JSON block: there is no package grade to
report.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Do not grade any check without naming the fact
  or quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** Read the diff, the evidence,
  and the description against the plan, the issue, and the repo's
  stated standards. A terse, complete PR can be ready; a confident,
  well-formatted one can hide drift.
- **The rubric decides, not the run.** If a check passes by its
  stated condition but feels wrong, it still passes. Note the concern
  in its evidence line; the fix belongs in the rubric.
- **The procedure decides how, not the run.** Follow `procedure.md`
  as written and report its gaps instead of inventing steps.
- **Unclear counts as fail.** Apply the rubric's verdict rule, which
  treats `unclear` as fail. Where any rule is silent, an unverifiable
  claim is a failing one: a PR you cannot verify from the package is
  not ready to submit.
