# Evidence guide: where evidence lives in a PR package

The four families map one to one onto the harness's failure
categories. A package that fails none of them is a clear accept.

Live-mode inputs are the ones the live run names: `plan.md` (house
chain: the house plan and house repro pack), the branch diff from
`git diff main...HEAD` (three dots) with its commits from
`git log main..HEAD`, `pr_draft.md` (title and description),
`test_evidence.md`, the issue's GitHub thread, and the repo's PR
template and `docs/CONTRIBUTING.md`. The drafts `plan.md`,
`pr_draft.md`, `test_evidence.md`, and `comment.md` sit next to each
other in the top folder of the fork's clone and are never committed.

Path Review's PR template has six sections: Summary, Issue (`Closes
#<n>`), Changes (one bullet per change in the diff), Testing (a
checklist plus pasted evidence), Screenshots / Demo, and Notes for
Reviewers. The course asks for the AI-use disclosure and any
intentional departure from the plan under Notes for Reviewers.

## Plan fidelity (harness category: silent-drift)

Checks: `scope_fidelity`, `description_truth`.

**Where it lives.**
- Eval: the plan-context block's scope pair (what is in, what is out)
  and any deviation notes; the candidate PR's diff (file headers and
  hunks); the candidate PR's title, description, and checklist.
- Live: the scope and boundary sections of `plan.md` and its
  `## Deviations` section; the file list from
  `git diff main...HEAD --stat` and the hunks from
  `git diff main...HEAD`; the title (first line), Summary, Changes,
  and Testing checklist in `pr_draft.md`, plus any departure from the
  plan stated under Notes for Reviewers.

**Repo-required changes.** On Path Review's seeded bugs, the fix
removes each `@pytest.mark.xfail` marker naming the issue (found with
`grep -rn "issue #<n>:" tests`) and any `pyproject.toml` suppression
naming it, as `docs/CONTRIBUTING.md` explains. Those removals are
inside scope even when the plan does not list them.

**What good looks like.**
- Every changed file is inside the plan's boundary, a test the plan
  names, a repo-required artifact, or covered by a deviation note.
- Every change the plan commits to shows up in the diff, or its
  absence is disclosed.
- Every description and checklist claim about what changed can be
  pointed at in the diff.

**Silent drift, both directions.**
- *More than the plan:* a file or hunk the plan never names and no
  deviation note explains (an edit to a neighbouring printer, a
  refactor, a new option).
- *Less than the plan:* a planned change or test that is not in the
  diff and not disclosed.
- *Description drift:* "implements the plan exactly" over a diff that
  does more or less; "no options added" over a diff that adds one. A
  description claiming more or less than the diff delivers is silent
  drift, not a comms problem.

An honest deviation re-ties a mismatch: a deviation note in the plan
(or a plain statement in the description) that names the extra or
missing piece and says why turns the mismatch into a pass.

## Test evidence (harness category: not-tested)

Checks: `test_plan_coverage`, `evidence_observable`.

**Where it lives.**
- Eval: the plan-context block's test plan, read against the
  candidate PR's test-evidence section and the test files in its
  diff; the repo-facts block's test commands.
- Live: the test-plan section of `plan.md`; `test_evidence.md` and
  the Testing section of `pr_draft.md`; the test and fixture files in
  `git diff main...HEAD`; the checks the PR template lists (on Path
  Review: `make test-unit`, `make test-integration`, `make lint`,
  `make typecheck`); the reproduction steps from the repro comment on
  the issue thread (house chain: the house repro pack).

**What decisive looks like.**
- The reproduction's before (run on `main`, or the earlier repro
  output) and after (re-run on the branch), each with the command and
  its raw output pasted, the after showing the expected behavior named
  in the issue or plan.
- The repro is decisive; supporting checks are not. Every check the
  template lists is run and reported, at least as a one-line result
  naming the command and its outcome (`go test ./... passes (4108
  tests)`, `fmt and clippy clean`). A transcript with inline
  `# before:` / `# after:` annotations counts as pasted repro output. A failing check reported with its
  reason is honest evidence. On Path Review, `make test-integration`
  stopping with "no tests ran" is expected and passes when pasted.
- Every planned test case present as a test or fixture in the diff,
  or named as not covered with a reason.

**What fails.**
- No repro re-run at all: "works on my machine", "colors work now",
  "verified for a full day", with nothing before/after.
- Evidence that runs only the control or the unchanged path (the
  single-file case that never broke, a GET when the bug is a POST),
  or re-runs only one of several repros the plan names.
- A bare "tests pass" standing in for the repro.
- A `[paste output here]` placeholder left in.
- A test plan case the plan says to add as a test (a nested variant,
  a regression case) that has no test in the diff and no disclosure.
  Controls and re-runs are covered by the evidence, not by new files.

## Diff quality (harness category: unreviewable)

Check: `diff_clean`.

**Where it lives.**
- Eval: the candidate PR's unified diff and commit list.
- Live: `git diff main...HEAD` and `git log main..HEAD`.

**What reviewable looks like.** The fix is visible at a glance: every
hunk is the fix, its tests, or an artifact the repo requires, and
nothing rides along.

**Debris tells.**
- Debug leftovers: added `print`, `console.log`, logging, or `TODO`
  lines that exist only for local debugging.
- Commented-out blocks and dead code.
- Formatting or whitespace churn on lines the fix does not need.
- Drive-by edits: renames, typo fixes, or refactors outside the issue.
- Stray files: a committed draft (`plan.md`, `pr_draft.md`,
  `test_evidence.md`, `comment.md`), local config, `.DS_Store`, editor
  files, an unrequested lockfile change.

Not debris: removing an `xfail` marker or suppression that names the
issue (see Plan fidelity). Before pushing, `git diff main...HEAD --stat`
should list only the files the fix changes.

## Standards and comms (harness category: standards-wall)

Check: `template_complete`.

**Where it lives.**
- Eval: the repo-facts block's PR template sections, contributing
  instructions, and stated policy (including AI-use disclosure), read
  against the candidate PR's description.
- Live: the repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`, or
  the files in `.github/PULL_REQUEST_TEMPLATE/`) and
  `docs/CONTRIBUTING.md`, read against the description in
  `pr_draft.md`.

**What compliant looks like.**
- Every template section carries content about this PR; "N/A"
  carries a reason (Screenshots / Demo is often "N/A" for a change
  with nothing visual).
- The issue reference names this issue (`Closes #<n>`).
- Every PR ask in the contributing guide is met.
- Only true boxes are ticked. The CI box stays unticked before the PR
  opens, because CI has not run; that is correct, not a gap.
- Treating the package as AI-assisted, a disclosure in the
  contributor's own words is present wherever the policy asks for
  one. In live mode the course always asks, under Notes for
  Reviewers.

**What fails.** A section deleted, left as the template's placeholder
text, or ticked without the work behind it; a wrong or missing issue
reference; a required disclosure missing. Whether the description's claims match the diff is plan
fidelity, above, not this family.
