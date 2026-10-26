# Contribution: Combine EIA 861 short form table with long form tables

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Mee Tran
**GitHub Username:** MeeTrannn
**Issue:** https://github.com/catalyst-cooperative/pudl/issues/4633
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: built issue-scout's fit profile, search strategy, and rubric, and ran two wild scans**

I rewrote my fit profile around geospatial and climate/energy data
engineering and wrote six search queries plus ranking filters, then ran
two scans (58 issues, then 95). Several things broke. A substring filter
on `CI` dropped my best-fitting issue. One query only returned stale hits.
A `chore` issue ranked first. And the rubric accepted pystac-client#582
even though it already had an open PR. I fixed the filters, made any open
PR fail "Nobody else actively on it", and added Domain fit and Contribution
policy checks. My queries still never reached the energy orgs, so I swept
them by hand, and that's how I found PUDL.

**Unit 6: installed the voice guard and patched the scout's scope check**

I merged my three voice-guide copies into one file under
`~/.claude/voice-guard/`, added an Upstream section with rules for claims,
questions, and pings, and installed the PreToolUse hook. The wiring test
was stopped as expected ("no body the guard can read"), and my claim on
pudl#4633 passed the guard before it posted, which the guard log records.
My strict scope row rejected most real issues, so I replaced it with
scope-feasible plus a difficulty note. Re-grading then showed the new
check passes container issues whose sub-issues are all closed (pudl#4754),
which I only caught by reading the thread. The repo-memory file
(`CLAUDE.local.md`) waits until I clone PUDL for the first step of my
investigation.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

My profile is a data engineer moving toward geospatial and climate/energy
data, so I aimed the scout at spatial databases, Earth-observation formats,
and climate/energy orgs. The first scans mostly turned up issues that were
already taken or were chores. A manual sweep of energy orgs led me to
Catalyst Cooperative's PUDL, and I first locked in on pudl#5566, a dbt
validation task. I stepped back from it and went to the Issue Selection
site, where I picked three candidates: OSGeo/grass#3252 and pudl#4633 and
#4754. I re-graded all three with the patched rubric on 2026-10-25. The
scout accepted #4633 with the difficulty note **ambitious**, because its
first success criterion is an open investigation (how EIA folded the
short-form data into the long-form tables, and whether that holds for
every year), so what I deliver depends on what I find. Then I read every
thread. #4633 had no comments, no assignee, and no open PR. #4754 turned
out to be a parent issue whose three sub-issues are all closed. grass#3252
has an open PR, and #5566 had been picked up by someone else.

#4633 fits me because it is a data-consistency problem across 12 EIA-861
energy tables: schema, year-by-year comparison, and getting data to live
in one place, which is the work I do every day. I hope to learn how a
real Dagster + dbt pipeline is organized, how a maintainer team decides
on a data model, and how to work through an open-ended investigation in
public. Because it is rated ambitious, my claim led with the open
question: should the investigation be reviewed on its own before I change
any table, or ship with the integration in one PR?

Honest read of the scout: it held up on what it can see and fell short on
what it can't. It correctly rejected grass#3252 for its open PR, and after
my unit-5 patch it caught the open PR that scan 1 had missed on
pystac-client#582. But my search queries never reached the climate orgs
where my issue actually lived; I found PUDL by hand. The original scope
check was so strict that it rejected most real issues. And even after the
patch, scope-feasible passed pudl#4754, a container issue that was
effectively finished, because the scout doesn't read sub-issue status.
Reading the threads myself was what caught it.

Claim comment: https://github.com/catalyst-cooperative/pudl/issues/4633#issuecomment-6406795246

**Toolkit**

- Tool: issue-scout, my search strategy (Q1-Q6 plus ranking filters) and
  my rubric. I ran two scans in unit 5, then graded my three picks
  directly (`issue-scout: grade <url> <url> <url>`) after this week's
  scope patch.
- What it produced: scan 1 accepted 3 of 58 issues, and scan 2 accepted
  only Xee#30 of 95. The direct re-grade accepted pudl#4633 and #4754
  (both difficulty: ambitious) and rejected grass#3252 ("Nobody else
  actively on it": open PR #4947).
- Difficulties: the queries missed the climate/energy orgs entirely, so
  my final issue came from the Issue Selection site, not a scan. The old
  scope row ("Scope fits my skill level") also required a perfectly
  bounded issue with no undecided design, which almost nothing in the
  wild meets. And scope-feasible passed #4754 even though all of its
  sub-issues were closed. The read-out also listed one check twice.
- What changed: in unit 5 I added Domain fit and Contribution policy, made
  any open PR fail "Nobody else actively on it", and fixed the
  CI-substring filter. In unit 6 I replaced "Scope fits my skill level"
  with scope-feasible plus a difficulty note that informs instead of
  rejecting. The patch also dropped my old skill-level gate (data work,
  not systems work), which I may re-add as its own row. I would also have
  the scout check sub-issue status, and add per-org queries for
  catalyst-cooperative, hotosm, and zarr-developers.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
