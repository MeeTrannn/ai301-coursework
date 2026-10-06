# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

## Diagnosis and grounding

**Where it lives.**
- Eval: the plan's cause is the "Cause:" line or opening paragraph of
  the Candidate plan section. The behavior that cause has to explain is
  in the Repro evidence section: Environment, the numbered Steps,
  Expected, and Actual. Read the Thread highlights only to see where
  the plan's diagnosis came from.
- Live: the cause is in the draft `plan.md`. The repro evidence is the
  student's posted repro comment on the issue. On the house issue, it
  is the house repro pack as the drafts quote it.

**What good looks like.** The cause explains the Actual result. Any step
that varies one factor points the same way the plan does. That includes
a run without a flag, with colors off, with no pager, at the top level,
or a debug or timing trace. Rule-out patterns that mean fail:
- The control step reproduces the symptom without the blamed component.
- The control step shows the blamed component already behaving
  correctly.
- A trace shows the damage happens before the blamed code runs.

A confident diagnosis repeated from the thread is still a fail when the
repro steps rule it out.

## Scope

**Where it lives.**
- Eval: the Candidate plan's "Change:" paragraph, its In:/Out: or
  not-in-scope lines, and every file, module, option, dependency, or CI
  change it names. Read these against the Issue section's title, body,
  and Expected line.
- Live: the same parts of the draft `plan.md`, read against the issue
  body.

**What good looks like.**
- *Bounded:* one change at the site the repro isolates, plus the tests
  for it. Every named item traces back to the issue's Expected line.
- *Deliberate narrowing passes:* a not-in-scope line that defers part
  of the issue, or an untestable platform variant, with a reason.
- *Scope creep fails:* any rewrite, migration, dependency upgrade, new
  option or setting, UI rework, framework, abstraction, CI matrix, or
  module restructure the issue never asked for. This holds even when
  the core fix is in there and correct.

## Executability

**Where it lives.**
- Eval: the Candidate plan's "Change:" paragraph and any order of work,
  read with the Repo facts section.
- Live: the draft `plan.md` and the repo's README or CONTRIBUTING build
  instructions.

**What good looks like.** The plan names the file, function, or callback
where the change goes, and what changes there. A stranger could open
that file and start.

Unbuildable signals:
- "Investigate", "profile", or "poke around" with no chosen approach.
- An unresolved choice of layer, such as "gocui? tcell? not sure".
- "Somewhere".
- "Whichever is easier".
- "Maybe also".
- No files or areas named at all.

Each of these leaves a real decision for build time. A short plan that
names the file and the change is executable.

## Test plan

**Where it lives.**
- Eval: the Candidate plan's "Test:" paragraph, read against the Repro
  evidence Steps and Actual line.
- Live: the test section of the draft `plan.md`, read against the steps
  in the posted repro comment.

**What good looks like.**
- *Decisive:* re-runs the repro steps, or a regression test or fixture
  that encodes them. It names the observable result that changes, for
  example "at step 3 the color flips", "exit 0", "three spellings all
  match", or "cache older than the TTL is refetched".
- *Not decisive (fail):* "run the full test suite", "should feel fast",
  "nothing else should feel broken", or "verify it works". None of
  these name an outcome tied to the reproduced bug, even when the rest
  of the plan is solid.

## Honesty

**Where it lives.**
- Eval: the things the package marks as open. Look in the Repro
  evidence's Environment and any "did not test" notes, the issue's
  reported environment when it differs from the repro's, and Thread
  highlights where the cause or approach is still disputed. Read these
  against the Candidate plan and Candidate plan comment.
- Live: the same parts of the repro comment and thread, plus any
  deviation note added to `plan.md` after a build changed course.

**What good looks like.** Each open item stays open in the plan. It is
named as unknown, deferred, or scheduled to be checked, as in "Windows
variant untested, deferred". A plan fails when it states one of those
items as settled, for example claiming a fix for a platform the repro
never ran. A cause that agrees with the repro is not an open item. In
live mode, a mid-build deviation is honest only when it is recorded in
`plan.md`, not just in the diff.

## Comms

**Where it lives.**
- *Eval:* the Candidate plan comment, read against three things:
  - Thread highlights: maintainer or owner comments, directions,
    patched builds posted for testing, rejected approaches, open PRs or
    prior art.
  - The Repo facts section's contribution policy line, which holds the
    AI-use rule and any disclosure requirement.
  - The Candidate plan, which the comment must agree with.
- *Live:* the draft comment, read against the issue thread,
  CONTRIBUTING.md, any AI policy file, and the comment templates.

**What good looks like.**
- *Thread-aware:*
  - The comment answers or follows each explicit maintainer direction.
    If the owner isolated a culprit file or asked for testing, the
    comment engages that rather than proposing around it.
  - It engages open PRs or prior art instead of racing them.
- *Policy-compliant:* treat the package as AI-assisted. Where the repo
  requires disclosing AI use, the comment carries a disclosure. Where
  it requires human-written comments, the comment reads as one
  person's specific words.
- *Doesn't bind a plan comment:* a selective-review note, or a
  bug-report template's fields.
