# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment block, read against the version the issue targets (issue body, or the repo's declared floor in `pyproject.toml` / `package.json` / the setup doc's prerequisites table) | The block names all three of: (a) the OS, pinned as precisely as that OS can be — a versioned OS needs its number ("macOS 14.5", "Windows 11 23H2", "Ubuntu 22.04"), and a bare "Windows" or "Mac" is a fail, but a rolling-release distro that publishes no version number is pinned by its name and architecture alone ("Arch Linux x86_64"), since there is no version token to withhold and demanding a kernel build invents a requirement the OS cannot meet. Judge this element by whether the missing precision could plausibly matter to *this* bug: it could for a bug about filesystem or terminal behavior, and it could not for a pure parsing bug in a released binary, (b) the version of the language or tool whose behavior the issue concerns, and (c) a pinned code state — a released version or tag counts; a commit SHA is required only when the report claims to have run unreleased code. Where the environment differs from what the issue targets, a difference that could explain the result away must be stated in words; an unstated one is a fail. A difference the report's own result overrides — it reproduced the failure anyway, on another OS or a later version — is not a fail, because reproducing somewhere else widens the bug rather than weakening the report. Versions the issue's behavior could not depend on are neither required nor credited | required |
| steps-runnable | The repro report's "steps to reproduce" block, read as a stranger who has never spoken to the author and is starting from a fresh clone | The block begins at a state that stranger can reach unaided (a clone at the named SHA, or a setup command the repo documents), and every later step is a command or action that follows from the one before it with no unstated prerequisite. Fail if the stranger would have to guess which command to run, supply a value the block never gives, or rely on state only the author had | required |
| input-matches | The exact trigger in the report's steps (the input file, command, request, or value that provokes the behavior), compared character by character against the trigger the issue names | Identify the one element the issue's failure actually turns on — the syntax, the flag, the count, the character. The check is whether *that element* survives into the report's trigger unchanged. It may pass even when the surrounding trigger does not match: a report is allowed to minimize (a hand-written three-line file standing in for the issue's 200-line one), to substitute a harness that does not change what is being parsed or executed (`--offline` to print a request rather than send it), and to drop flags that only affect display. It fails when the triggering element itself is altered — `1: {}` where the issue wrote `1 = {}` is a fail, because the parser reads a colon and an equals sign differently and the equals sign is the whole bug. Reduction passes; substitution of the thing under test fails. If you cannot name which element the failure turns on, grade `unclear` rather than guessing | required |
| output-matches | The raw artifact in the report's observed-output block, compared against the specific failure the issue describes | The artifact is raw output (not a summary of output), and the failure it shows is the same *class* of failure the issue reports, identifiable by a shared concrete marker: the same error text, exception type, wrong value, or absent field. A graceful parse error does not match a panic; an adjacent error is a fail. **A reported cannot-reproduce is judged on a different question.** Where the report's conclusion is that the failure did not occur, the artifact cannot show the failure by definition, and demanding that it does turns every honest negative result into a reject. Such a report passes this check when the artifact is raw output of the issue's own trigger showing the *working* behavior, and the report says plainly that this is a non-reproduction and names the condition it could not satisfy. It fails only if the artifact does not exercise the issue's trigger at all. For a docs or content gap, the artifact must be a pair at one pinned SHA — the current state of the doc and the source of truth it disagrees with — so the gap is shown rather than asserted | required |
| claims-backed | Every assertion in the report (its expected/actual lines and any conclusion sentence), traced back to the artifact block that is supposed to support it | Each assertion is traceable to a line of the pasted artifact, and anything the report did not verify is named as not verified. A cause asserted from a symptom alone is a fail. What this check is hunting is an assertion that would change a maintainer's reading of the bug and rests on nothing: an invented cause, a claim of coverage never run, a confidence word the artifact does not earn. A secondary or control observation the report describes without pasting — "dropping the flag prints the right numbers" — is a weakness to note, not a fail, provided the issue's own failure is pasted in full and the control agrees with what the thread already established. An explicit, evidenced cannot-reproduce — faithful steps, real environment, actual non-failing output shown — is a **pass**; an unevidenced "didn't happen for me" is a fail | required |
| claim-specific | The claim comment, read against the issue body it answers and against the repo's stated process (`CONTRIBUTING.md`, issue and PR templates, any AI-use policy) | The claim names this issue's own target — the specific files, endpoints, symbols, or acceptance criteria it mentions — and states the concrete next step the author will take, so it could not be pasted onto a different issue unchanged. Any requirement the repo states in writing (a disclosure line, a template section, a naming convention) is honored, and honored *visibly*: the requirement is satisfied by text in the package, because the package is all a maintainer gets. **Two different silences, and they grade opposite ways.** Silence from the *repo* imposes nothing — a repo with no stated AI policy cannot be failed for a missing disclosure, and inventing the requirement is its own error. Silence from the *contributor* is a fail where the repo states a mandatory **disclosure** requirement that covers what is being posted. Two things have to be true before this bites, and both are read off the repo's own words. First, the policy must demand a *disclosure*, not merely set a condition of conduct: "you must understand and take responsibility for what you submit", "we do not accept fully AI-generated contributions", "write to maintainers in your own words" are conditions, and a package that makes no AI claim satisfies them — they require no line of text. "All AI usage in any form must be disclosed, stating the tool and the extent" is a disclosure mandate and requires one. Second, the mandate's own scope must cover this artifact: a policy that asks for disclosure *in the pull request*, or that says in as many words that issue comments carry no disclosure ask, is not violated by a claim comment with no disclosure line. Where a disclosure mandate does cover the package and no disclosure line appears, grade `fail` — "no AI use is visible in the text" is not a finding, because an undisclosed draft and a human-written one look identical from outside, which is why the repo wrote the rule. Grade the package against what the repo demands be present, never against what an absence might imply | required |
| next-step-named | The package as a maintainer reads it — the claim comment *and* the repro report together, since both are posted to the same thread | Somewhere in the package, a next step is named in terms a maintainer can act on: the file or symbol a fix would touch, the direction that fix would take, or a specific question the maintainer has to answer. It does not matter which comment carries it; a claim that names the target and a report that stops at Expected/Actual is a complete package, because the reader has both. "I'll keep looking" and "will open a PR" name no target and are a fail. Never a date or an effort estimate | preferred |
| scope-stated | The report's assertions, read against what its artifacts actually cover | Fail only where the report asserts more than it showed: it claims a behavior across cases it never ran, or states a cause it never executed, without marking the gap. A report whose every assertion stays inside its own artifacts passes, whether or not it adds a sentence about scope — naming a boundary is what makes an over-reaching report honest, not a ritual every report owes. This check exists to catch the confident report, not the terse one | preferred |

## What a grade means

Three grades, and only three. Every check gets exactly one, with the
fact that decided it on the same line.

- **pass** — I found the evidence the check names, in the place the
  check says it lives, and it meets the pass condition as written. Not
  "close to it."
- **fail** — I found the evidence and it does not meet the condition,
  *or* the condition requires something the package simply does not
  contain. A missing required element is a `fail`, not an `unclear`;
  `unclear` is not the polite word for absent.
- **unclear** — the package contains something that might satisfy the
  check and I cannot tell which way it falls, or the check's own
  wording does not decide the case in front of me. When I grade
  `unclear`, I write the ambiguity down and fix the check's wording
  afterward, because an `unclear` is a defect in this file, not in the
  package.

A check is graded against its pass condition alone. Strength elsewhere
in the package never carries a check, and a weak package never drags
down a check whose own condition is met.

## Verdict rule

**accept** if and only if every `required` check grades `pass`. A
single `fail` on any required check produces **reject**, no matter how
strong the rest of the package looks — this rule exists because on
`calib-03` I graded a required check `fail` and still wrote "ready",
and no rule in my rubric stopped me.

`unclear` on a required check counts as `fail`. Proof I cannot verify
is proof that is not ready to post.

**There is no score.** Counting passes is not applying this rule. "4
of 5 — ready" is not a verdict this rubric can produce; on `calib-03`
a grader wrote exactly that, and the one failing check was the one
that mattered. Required checks are a conjunction, not a tally: five
passes and one fail is **reject**, and the verdict line names the
check that caused it.

`preferred` checks never change the verdict. Among accepted packages,
one that also passes `scope-stated` is the stronger post.

**Claim-only packages** (a claim comment with no repro report yet)
grade only `claim-specific`, plus `env-recorded` if the claim names
where the work will happen. Every check whose evidence is the repro
report is reported `unclear` with the reason "not yet applicable:
claim-only draft" and is excluded from the verdict rule above. The
verdict then answers only: is this claim comment ready to post?

## Revision log

What the group calibration on `calib-03` changed here, and the line
that forced each change.

1. **`env-recorded` now names the OS version explicitly.** Two graders
   read the same report and split — one failed it for "there is no
   window version," another passed the same block. The check said "the
   OS with its version" and I had been reading a bare OS name as
   satisfying it. It does not.
2. **Added the grade definitions above.** My grader's note was "Not
   specified what is required to pass, and what isn't." The pass
   conditions were written; what `fail` versus `unclear` meant was
   not, so a missing element could be graded either way depending on
   mood.
3. **`next-step-named` promoted to required, and `scope-stated`
   added.** Two sections independently asked for a conclusion check
   ("can also add a rubric for conclusion", "also for conclusion and
   next steps"). A report that proves the bug and then stops leaves
   the maintainer to do the triage I claimed to be saving them.

Then the eval set graded the rubric back, and most of what it found
was that change 3 was wrong in the way new checks usually are.

4. **`next-step-named` demoted to preferred and widened to read the
   whole package.** Written as a required check over the repro report
   alone, it rejected six of the eight packages the gold labels call
   ready — every one of them because the next step was in the *claim
   comment* instead of the report. Both comments land in the same
   thread; a reader who has one has both. A check is allowed to want
   more than the package gives, but not to call a complete package
   incomplete because a sentence sits in the other half of it.
5. **`scope-stated` inverted.** As first written it demanded a
   boundary sentence from every report and so fired on almost all of
   them, which is a check that carries no information. It now fires
   only where a report asserts past its own artifacts. The point was
   never the sentence; it was the over-reach the sentence prevents.
6. **`env-recorded` stopped demanding tokens an OS cannot issue.** It
   failed Arch Linux for having no version number. Arch does not have
   one. "As precise as this OS can be pinned" is the real standard,
   and a platform difference the report reproduced the bug *anyway*
   widens the report rather than weakening it.
7. **`input-matches` distinguishes reduction from substitution.** It
   was failing reports that minimized a 200-line input to three lines
   while keeping the triggering construct verbatim. What matters is
   the one element the failure turns on: keep it and reduce freely;
   change it and the reproduction is of a different bug. This is the
   `1 = {}` / `1: {}` line from calib-03, stated as a principle rather
   than as one example.
8. **`output-matches` stopped punishing honest negatives.** Two
   packages reported a careful, fully evidenced cannot-reproduce, and
   the check failed them for not showing a failure that by definition
   did not occur. `claims-backed` had said such a report passes; this
   check had not, so the rubric contradicted itself and the stricter
   half won. A negative result is a result.
9. **`claim-specific` learned which silence is which** — the change
   the eval set exists to force, and the one I would not have found by
   reading my own rubric. A repo requiring that *all AI usage in any
   form be disclosed* was passing a package with no disclosure line,
   on the reasoning that no AI use was visible in the text. That
   reasoning is backwards: invisibility is the condition the rule was
   written to address. The check now separates a disclosure mandate
   from a condition of conduct ("understand what you submit" requires
   no line of text), and honors the mandate's own scope — a repo that
   asks for disclosure in pull requests and says issue comments need
   none is satisfied by a claim comment without one.

The pattern across 4 through 8 is one mistake made five times: I wrote
each check as the strictest reading of a real principle, and the
strictest reading rejected good work. A check that fails everything
discriminates exactly as poorly as a check that passes everything, and
costs more to read.
