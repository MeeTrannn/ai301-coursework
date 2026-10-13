# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/100

**Branch**

`fix/37-api-request-body-schemas`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run, first version of the rubric: `agreement: 14/20 scored items  (bar: 18/20: below the bar)`.
   The categories line was `clear-accept 1/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.
   Every reject matched, but six of the seven clear accepts (pkg-05, 08, 11, 13, 16, 19)
   were graded reject.
2. Partial run (`--only`) after revising `rubric.md` and `references/evidence-guide.md`:
   the six missed packages plus canaries pkg-01, pkg-04, pkg-07, pkg-10, pkg-14, pkg-20, and
   calib-04 (with `--include-calibration`). `agreement: 12/12 scored items`, and calib-04
   also agreed. A partial run does not write `eval-run.txt`.
3. Full run, revised rubric, saved with `--save-run eval-run.txt`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

pkg-13 (neovim/neovim#41337, clear-accept). In my first full run, my rubric decided
**reject** while the gold label is **accept**. In my final run it decided **accept**, which
matches gold.

The first run failed two checks. `test_plan_coverage` failed with the evidence line
"Plan's test plan names a 'no-ampersand control unchanged' case; no test/fixture for it
appears in the diff and it is not disclosed as uncovered." `evidence_observable` failed with
"Test evidence states 'No-ampersand control unchanged' with no command or output pasted to
back it."

My rubric read it that way because my first version took every result sentence literally.
It said "Every named case has a test or fixture in the diff that encodes it" and "Every
result sentence ... is backed by pasted output." But the package's real evidence was
decisive: the repro was re-run with the before and after annotated inline (`-- before: { code
= 1, stderr = "'t' is not recognized ..." }` and `-- after:  { code = 0, stderr = "" }`), and
the after showed both query parameters reaching the browser. The only things missing raw
output were a control run and the spec run, which were each reported in a one-line result.
The gold note calls it "honestly disclosed shortfall ... ready as scoped". So my rubric was
holding a ready PR on supporting details instead of on whether the fix was proven.

**Check rationale**

From `tools/pr-precheck/rubric.md`, the `evidence_observable` pass condition, exactly as it
reads now:

> (1) Decisive repro: the reproduction the issue or plan names (the input that actually breaks, not a control or an unchanged path) is re-run on the branch, with its command or input shown and both its before result (default branch or the earlier repro output) and its after result shown. A transcript or code block with the before and after results annotated inline counts as pasted output. The after shows the expected behavior the issue or plan names. When the plan names more than one failure mode or repro, each is re-run. (2) Supporting checks (the test suite, lint, format, typecheck, controls): a one-line result that names the specific command or check and its outcome ("`cargo test -p ignore` passes (214 tests)", "fmt and clippy clean", "no-ampersand control unchanged") is enough once (1) holds; full raw output is not required for these. A failing check passes when the PR states the reason. Fail when the repro is never re-run or has no before/after ("works on my machine", "colors work now", "verified for a day"), when the evidence exercises only a control or the unchanged path instead of the broken one, when one of the plan's named repros is silently not re-run, when a `[paste output here]` placeholder is left in, or when pasted output shows a failure the PR does not disclose.

It reads this way because my first version required "Every check the repo's template or guide
lists has its command and raw output pasted" and failed "a result stated without output".
That version caught all four not-tested packages, but it also rejected six of the seven clear
accepts over lines like "`go test ./...` passes (4108 tests)" (pkg-19) and "fmt and clippy
clean" (pkg-05).

When I compared the gold rejects with the gold accepts, the difference was not how much raw
output there was. It was whether the broken path was shown before and after. pkg-04 ("colors
work now in paging mode") and pkg-10 ("Verified working on my machine") never re-run the
repro. pkg-07 and pkg-14 re-run only the path that never broke. So I made the repro the
decisive part (1), allowed a one-line named result for supporting checks in part (2), and
moved the four real failure patterns into the fail list. I kept "each is re-run" for plans
with more than one repro, so that calib-04 (only one of two failure modes re-run) still fails.

**Trade-offs**

Loosening `evidence_observable` could have let not-tested packages through. So I re-ran
canaries with `--only` before the full run: all four not-tested packages (pkg-04, pkg-07,
pkg-10, pkg-14), both standards-wall packages (pkg-01, pkg-20, because I also loosened the
issue-reference wording in `template_complete`), and calib-04. All of them still graded
reject, and the confirming full run kept `not-tested 4/4` and `standards-wall 2/2`.

The case I accept it will miss: a PR that shows a real before and after for the repro, but
whose one-line "suite passes" or "lint clean" claim is false. Part (2) now trusts a named
one-line result without raw output, so the rubric cannot catch a fabricated summary line. A
reviewer, or CI after the PR opens, would have to catch it instead.
