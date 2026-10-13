# Procedure: how this tool grades a PR package

## Read order

Read every part before grading any check. Take notes as you go; the
checks later run against these notes.

1. **Set the mode.** If you were given a bundle file, this is eval
   mode: the bundle is the only evidence, and you skip `scope.md` and
   `voice-guide.md`. Otherwise this is live mode: read `scope.md`
   first and stop as SKILL.md's scope seam directs. Then read
   `voice-guide.md`.
2. **Issue context** (live: the issue thread). Write down in one
   sentence what the issue asks for, plus its expected and actual
   behavior. Write down the reproduction steps and input (live: the
   repro comment on the thread; house chain: the house repro pack),
   and any maintainer direction that changed the approach.
3. **Repo facts** (live: the PR template and `docs/CONTRIBUTING.md`).
   List every PR template section, every PR ask in the contributing
   guide (including any `xfail` marker or suppression to remove), the
   AI-use policy, and every check the template lists (on Path Review:
   `make test-unit`, `make test-integration`, `make lint`,
   `make typecheck`).
4. **Plan, before the diff** (live: `plan.md`). Write down:
   - every file and area inside the plan's boundary, and everything it
     puts out of scope;
   - every change the plan commits to;
   - every case in the test plan;
   - every deviation note (live: under `## Deviations`), with what it
     adds, drops, or changes.

   Read the plan first so the diff is judged against what was
   promised, not against a promise reconstructed from the diff.
5. **Diff** (live: `git diff main...HEAD --stat`, then
   `git diff main...HEAD`, then `git log main..HEAD`). List every
   changed file. For each hunk, write one line on what it does. In
   live mode, also note the branch name from
   `git branch --show-current` for the house-rule report.
6. **Test evidence** (live: `test_evidence.md`). List every command
   shown, whether its output is pasted, and every result sentence.
7. **Title and description, last** (live: `pr_draft.md`). Read them
   as a reviewer who already knows the plan and the diff. List every
   factual claim and every ticked checklist item as a separate line.

## Evidence gathering

Record each item below in your notes, copying exact quotes and file
paths wherever a check will cite them. Live-mode locations follow
`references/evidence-guide.md`.

- **Files against plan** (for `scope_fidelity`): make a table with
  one row per changed file from Read order step 5. In the next column
  write where the plan covers it: inside the boundary, a named test,
  a repo-required artifact, a deviation note, or "not named". Then
  list every planned change from step 4 and write "in diff", "disclosed
  as not done", or "missing".
- **Claims against diff** (for `description_truth`): for each claim
  from step 7 that says what the PR changes, adds, or leaves alone,
  write the file and hunk that proves it, or "not shown" or
  "contradicted by <file>". Mark fidelity claims ("exactly", "no
  changes beyond") so they can follow `scope_fidelity`. Set result
  claims aside for the next item.
- **Test plan against test files** (for `test_plan_coverage`): for
  each test plan case from step 4, write the test or fixture file in
  the diff that encodes it, or the sentence that discloses it as not
  covered, or "missing".
- **Results against output** (for `evidence_observable`): for each
  result sentence from steps 6 and 7, write the pasted output that
  backs it, or "no output". Record whether the reproduction from step
  2 has both a before and an after with output, and what the after
  shows. For each check listed in step 3, record "output pasted",
  "failed, reason stated", "failed, no reason", "not run, named with
  reason", or "missing".
- **Hunks against the fix** (for `diff_clean`): for each hunk line
  from step 5, mark it fix, test, required artifact, or debris, naming
  the debris tell from the evidence guide.
- **Template against description** (for `template_complete`): for
  each template section, contributing ask, and disclosure requirement
  from step 3, write "met" with the quote, "boilerplate", or
  "missing".

## Check execution

1. Run the checks in the rubric's table order and grade all of them.
   Do not stop at the first fail, because the output reports every
   check.
2. For each check, apply its pass condition word for word to your
   notes for that check. Grade it `pass`, `fail`, or `unclear`. Write
   one evidence line: the quote or fact that decided the grade (for a
   fail, the first offending file, claim, or case).
3. Tell an absent part of the PR apart from an absent part of the
   package:
   - **The PR lacks what the check judges** (no test evidence section,
     no output, no test for a planned case): grade `fail`. The missing
     content is the outcome being judged.
   - **Eval mode, and the bundle lacks a section a check reads** (no
     plan-context test plan, no repo-facts block): grade that check
     `unclear` and name the missing section.
   - **Live mode, and the repo states no template or no policy:** that
     is a fact, not missing evidence. Apply the pass condition, which
     passes this case.
4. Use `unclear` only for the eval case above, or when your notes
   support both a pass and a fail reading and re-reading the cited
   section does not settle it. In the evidence line, name what would
   have settled it.
5. Grade from your notes. Re-read a part of the package only when the
   notes lack the fact a pass condition needs, and then re-read only
   that section. Never re-read to argue a check into a pass.
6. Grade each check on its own merits. One stray file can fail both
   `scope_fidelity` and `diff_clean`; a missing planned test can fail
   both `test_plan_coverage` and `description_truth` when the
   checklist claims it. The only dependency is the one the rubric
   states: a fidelity claim follows `scope_fidelity`.

## Verdict assembly

1. Apply the rubric's verdict rule. If every check is `pass`, the
   verdict is `accept`. If any check is `fail` or `unclear`, it is
   `reject`; `unclear` counts as fail.
2. On reject, the deciding check is the first check in table order
   that graded `fail` or `unclear`. Lead the readable summary with it
   and its evidence line.
3. Write the readable summary: one line per check with its grade and
   evidence. In live mode, add any voice-guide rule the title or
   description breaks, quoting the rule; add any `scope.md` house rule
   the PR breaks. Neither changes the verdict. List any gap you hit in
   this procedure.
4. Emit the JSON block from SKILL.md last. Set `item` to the issue URL
   (live; the PR URL once one exists) or the bundle id (eval). List
   the checks in table order, use the rubric's check names exactly,
   and use the same evidence lines as the summary. Write nothing after
   the block.
