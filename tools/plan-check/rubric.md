# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis_grounded | The plan's stated cause, read against every step of the repro-evidence block: observed and expected behavior, artifacts (logs, output, debug traces), and especially any control run, timing matrix, or isolating step that varies one factor. | The stated cause explains the behavior the repro evidence shows, and no step in the repro evidence rules it out. Fail if a control or isolating step shows the symptom without the blamed component, or shows the blamed component working correctly. Also fail if the cause explains a different behavior from the one reproduced, or the plan ignores the repro. The repro evidence outranks the thread: adopting a confident diagnosis from the thread does not pass if the repro contradicts it. | required |
| targets_cause | The plan's proposed change, read against its own stated cause and the repro evidence. | The change acts where the diagnosis places the cause. Fail if the change would make the repro stop showing the symptom while the cause stays in place: catching or silencing the error, special-casing the repro's input, hiding the output, or patching a downstream caller. A guard, clamp, or check placed at the code site where the stated cause happens counts as acting on the cause. | required |
| scope_bounded | The plan's scope statement, its not-in-scope line, and every file or area it names, read against what the issue asks for. | The plan is one change: every file, area, or step it names is needed to fix the issue or to test that fix. Fail if any item could be dropped and the issue would still be fixed and tested (unrelated refactors, renames, dependency bumps, extra features, "while I'm here" cleanups), even when the core fix inside the bundle is right. Deliberately narrowing the plan is not a fail: deferring part of the issue, or a variant that can't be tested, passes when the plan says so. | required |
| executable_by_stranger | The plan's approach and order of work, read with the repo-facts block (layout, build and test commands). | A contributor who has not read the thread could start the first step without asking the author anything: the plan names where the change starts (file, function, or module) and what changes there, and any open decision comes with how it will be settled. Fail if the first concrete action is missing or depends on something only the author knows. | required |
| test_proves_fix | The test plan, read against the repro-evidence block's steps and observed result. | The test plan re-runs the reproduction (by hand, or as an automated test that encodes it) and names an observable expected result that differs from what the repro shows today, so it would fail before the fix and pass after. Fail if it only says "run the test suite", "verify it works", or checks something the repro never showed broken. | required |
| unknowns_stated | The claims in the plan and the plan comment, read against what the repro evidence, issue, and thread explicitly mark as untested, unknown, or disputed. | Nothing the package itself marks as untested, unknown, or disputed is stated in the plan or comment as settled. Examples: a platform or version the repro says it did not test, or a cause the thread is still arguing about. Fail if any such item is presented as certain. A stated cause that agrees with the repro evidence is not an unknown, and a plan needs no unknowns section when the package flags none. | required |
| comment_matches_plan | The plan comment, read against the candidate plan. | The comment's cause, change, and scope agree with the plan's. Fail if the comment states a different cause, describes a broader or different change, or hides a part of the plan a maintainer would object to. Tone, timelines, and brevity are not graded here. | required |
| comment_thread_aware | The plan comment, read against the issue context's thread highlights (live: the issue thread). | The comment does not propose an approach a maintainer has already rejected, does not ignore a question or direction a maintainer posted, and acknowledges earlier attempts or claims in the thread where they exist. Fail on any one of these. Pass if the thread contains no maintainer direction and the comment contradicts nothing in it. | required |
| comment_repo_conventions | The plan comment, read against the repo-facts block's templates, contributing asks, and contribution policy (including any AI-use disclosure requirement). | Treat every package as AI-assisted work. The comment meets every stated repo requirement that applies to a plan or claim comment. Fail if any applicable requirement is missed. For example, the repo policy requires disclosing AI use and the comment contains no disclosure, or the policy requires human-written comments and the comment reads as generated. Pass if the repo states no requirement that applies; a selective-review note or a bug-report template does not bind a plan comment. | required |
| comment_reviewable | The plan comment on its own, as a maintainer would read it in the thread. | A maintainer could tell from the comment alone what is broken, what will change, and how it will be shown fixed, and see any question that needs their answer. | preferred |

## Verdict rule

- **accept** only if every `required` check grades `pass`.
- **reject** if any `required` check grades `fail` or `unclear`.
- **unclear (`?`)** means the package does not contain enough to decide
  the check either way (the evidence is missing, or it supports both
  readings). On a `required` check it counts as a fail, because a plan
  that cannot be verified from the package is not ready to post and
  build from. The check's evidence line must name what was missing, so
  the author knows what to add.
- `preferred` checks are graded and reported, but never change the
  verdict, whether they pass, fail, or come back unclear.
- When the verdict is reject, the deciding evidence is the first failing
  or unclear `required` check in table order; every other failing check
  is still reported.
