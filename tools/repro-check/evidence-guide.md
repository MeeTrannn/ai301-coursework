# Evidence guide: where proof lives in a reproduction package

A reproduction package has three parts that must be read against each
other: the **issue** (what was claimed), the **claim comment** (what I
said I would do), and the **repro report** (what I actually ran and
saw). Almost every real failure is a mismatch between two of those
three, not a flaw inside one of them. So this guide maps each proof
family to the exact place it lives on both sides, and says what good
looks like when you get there.

One rule before the families. **The package is only what the posted
comments contain and quote.** A file open in my editor, a terminal I
ran an hour ago, a repo I have cloned locally — none of that is
evidence. A stranger reading the issue thread sees the comment text and
nothing else, and that stranger is who this guide grades for.

---

## Environment

Where the reader finds out what machine produced the result, and
whether that machine is close enough to the issue's target for the
result to mean anything.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The environment record itself | the "Environment" paragraph or block at the top of my draft repro comment | the "Environment" section of the candidate repro report |
| The version the issue targets | the issue body, plus the repo's own declared floor: `pyproject.toml` / `package.json` / the prerequisites table in `docs/SETUP.md` | the issue context section and the repo-facts block |
| The exact code state | the commit SHA, tag, or branch named in the draft; live you can confirm it resolves with `git rev-parse` in the clone | the SHA or branch quoted in the report's environment section |
| Platform-specific gotchas | the repo's setup doc, which often lists per-OS differences (ports, shells, toolchains) that change what "the same steps" means | the repo-facts block, if it carries the setup notes |

**What good looks like:** the record names (a) an OS, (b) the runtime
version of the language or tool the issue concerns, and (c) a pinned
code state — a commit SHA, tag, or release — specific enough that
someone else could stand where I stood. Where my versions differ from
what the issue targets, the difference is stated in the comment rather
than left for the maintainer to notice.

**What is not good enough:** "latest", "my machine", "main" with no
SHA, or a version list so generic it would describe any reader
(`Python 3`). A bare OS family is in this category and it is the one I
misread on `calib-03`: "Windows", "Mac", "Linux" name a family, not a
version, and the behavior of an issue can turn on 22H2 versus 23H2 or
on macOS 14 versus 15. The record has to carry a number a reader could
match. A pinned SHA on a *fork* is fine, and often better than a
branch name, as long as the comment says which fork.

**The trap:** an environment record that is complete but irrelevant.
Listing my Node version on a pure Python bug is padding, and padding is
how a thin report looks thick. The versions that matter are the ones
the issue's behavior could plausibly depend on.

---

## Steps

Where the reader finds out how to get from a clean checkout to the
moment the bug appears.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The steps themselves | the "Steps to reproduce" block of my draft, usually a fenced command list | the "Steps to reproduce" section of the candidate report |
| Whether they are runnable as written | paste them into a fresh shell in a fresh clone; anything that fails is a gap | read them as a stranger would: does each command follow from the previous one? |
| The starting state | the first line of the step block — a `git clone`, a checkout, or an explicit "from a clean `make setup`" | the same |
| Whether they match the project's real setup | the repo's `README.md` and setup doc: steps that skip a documented required step (services up, `.env` copied, migrations run) will not reproduce for anyone else | the repo-facts block or the quoted setup doc |

**What good looks like:** the steps start from a state a stranger can
reach (a clone at a named SHA, or a documented setup command), each
step is a command or an action with no unstated prerequisite between
them, and the last step is the one that triggers the behavior. A
stranger with the prerequisites could paste the block and land where I
landed.

**What is not good enough:** prose that describes the work instead of
reproducing it ("I looked at the docs and compared them to the code").
Description is not a procedure. The test is mechanical: could someone
run this without asking me a question first?

**The trap:** steps that silently depend on my setup — an env var I
exported once, a service already running, a file I created by hand. If
a step needs it, the step block must contain it.

---

## Behavior shown

The deciding family. Where the reader finds the artifact, and whether
that artifact shows *the issue's* behavior or a neighbor of it.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The artifact | the output excerpt, log, traceback, or screenshot pasted in my draft, usually in a fenced block | the "Observed output" section of the candidate report |
| What the issue said would happen | the issue title and body — the specific error string, the wrong value, the missing thing | the issue context section |
| The target of the issue | the file paths, symbols, endpoints, or functions the issue names | the same, plus the repo-facts block |
| Ground truth for a docs or content gap | the source file the doc is supposed to describe — the handler, the schema class, the config — read at the same SHA | the code excerpts quoted in the report |

**What good looks like:** the artifact is raw output, not my summary of
output, and something *in* it lines up with something *in* the issue —
the same error text, the same wrong value, the same absent field. For a
runtime bug that is a traceback or a wrong result. For a docs or
content gap the artifact is a **pair**: the current state of the doc
*and* the source of truth it disagrees with, both at the same pinned
SHA, so the gap is visible rather than asserted.

**What is not good enough:** an artifact that proves a real thing that
is not this thing. A traceback from a different code path, an error
from a misconfigured environment, a failing test that fails for setup
reasons. These are the most dangerous packages, because they look
exactly like proof.

**How to tell adjacent from on-target:** name the concrete thing the
issue promised, then point at where it appears in the artifact. If you
cannot point — if the link needs a sentence of reasoning to hold — the
artifact is adjacent. "Close enough" is the sound a wrong reproduction
makes.

---

## Honesty

Where the report's claims and the report's evidence are compared to
each other. This family never looks at new places; it re-reads the ones
above and asks whether they support what was said.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The claim | the "Expected behavior" / "Actual behavior" lines, and any conclusion sentence in my draft | the same lines of the candidate report |
| The backing | the artifact block those lines refer to | the "Observed output" section |
| Scope creep | comparison of what the report asserts against what the artifact covers: two endpoints claimed, one shown | the same |
| Uncertainty, where it exists | a stated "I could not verify X" in the draft | the same |

**What good looks like:** every assertion is traceable to a line of the
artifact, and anything not shown is named as not shown. A report that
says "I reproduced the `POST /profiles` gap; I did not test
`POST /reviews`" is stronger than one that quietly implies both.

**An evidenced cannot-reproduce is a pass.** If I followed the steps
faithfully and the bug did not appear, saying so — with the
environment, the steps, and the actual (working) output — is a complete
and useful reproduction. It tells the maintainer the bug is
conditional, which is real information. A cannot-reproduce fails only
when it is unevidenced: "didn't happen for me" with nothing attached.

**What is not good enough:** confidence that outruns the artifact. The
tells are hedge-free verbs over thin evidence ("this confirms",
"clearly caused by"), a root cause asserted when only a symptom was
observed, and any claim about code paths that were never executed.

**The trap:** honesty is not humility. Under-claiming a result the
artifact fully supports wastes the maintainer's time too. The standard
is *accuracy* — say exactly what happened, no more and no less.

---

## Comms

Where my words meet the repo's rules and the thread's history.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The claim comment | my draft claim, read against the issue body it answers | the candidate claim comment section |
| The repo's stated process | `CONTRIBUTING.md` (root or `docs/` or `.github/`), the issue templates in `.github/ISSUE_TEMPLATE/`, the PR template | the "contribution policy" line in the repo-facts block |
| AI-use disclosure requirements | the same files: some repos require disclosing AI assistance, some ban it, most say nothing | the same line |
| Thread history | the issue's existing comments: who has claimed it, what a maintainer has already answered, what has already been ruled out | the comments listed in the issue context |
| Acceptance criteria | the issue's own checklist, if its template has one | the issue context section |

**What good looks like:** the claim names *this* issue's specific
target — the files, endpoints, or symbols it mentions — and states a
concrete plan, so a maintainer can tell from the claim alone that I
read the issue rather than the title. Anything the repo requires in
writing (a disclosure line, a template section, a branch or commit
convention it asks contributors to follow) is present.

**What is not good enough:** boilerplate that would fit any issue in
any repo. "I'd like to work on this!" is indistinguishable from a
drive-by and tells the maintainer nothing they can use. Specificity is
the whole signal here.

**Whose silence?** Two silences meet in this family and they point
opposite ways, so read the repo's line before the package's.

*The repo's silence passes.* Most repos state no AI policy. Absence of
a policy is not a violated policy, and a check should not invent a
requirement the repo never made.

*The contributor's silence fails, but only under a disclosure
mandate.* Where the repo's own words require that AI assistance be
disclosed — naming the tool, the extent, or both — the disclosure is a
line of text that has to be in the package, and its absence is the
violation. Do not reason from "nothing in this draft looks
AI-written": that is unknowable from outside, and it is the reason the
repo stopped relying on it.

Sorting the policy takes two questions, both answered from the repo's
text and neither from the draft:

| The repo says | Kind | A package with no AI line is |
|---|---|---|
| "All AI usage in any form must be disclosed, stating the tool and the extent" | disclosure mandate, unscoped | **failing** — the required line is missing |
| "State the tool and extent of AI use *in the pull request*"; "no disclosure ask for issue comments" | disclosure mandate, scoped elsewhere | passing — this artifact is outside its scope |
| "You must understand and take responsibility for every change" | condition of conduct | passing — nothing is required to be written |
| "Fully AI-generated contributions are not accepted" | ban on a kind of work | passing — unless the package shows the banned thing |
| "Comments to maintainers must be in your own words" | condition on voice | passing — judge the words, not a missing label |
| nothing | no policy | passing |

The trap in this table is the middle rows. A policy that sounds strict
("not accepted", "closed immediately") may still require no text at
all, while a mildly worded one that uses the word *disclose* requires
a sentence. Strictness of tone is not the signal; the demand for a
statement is.

**The trap:** in a classroom repo, a classmate's existing claim or
repro on the thread is not a blocker and not a template. Read the
thread to know what has been said; write your own words anyway. Copying
a classmate's structure because it is there is how a package ends up
proving their setup rather than mine.

---

## Handoff

The last family, and the one easiest to forget because by the time a
reader reaches it the proof is already done. Where the reader finds
out what the report wants to happen next, and what it is not claiming.

| Signal | In live mode | In the eval bundle |
|---|---|---|
| The next step | the closing lines of my draft repro comment | the closing section of the candidate report |
| Whether the named target is real | the repo itself: does the file, symbol, or endpoint the closing line names exist at the pinned SHA? | the repo-facts block and any code excerpts the report quotes |
| The open question, if there is one | the closing line, read against the issue's own unanswered points and the maintainer's prior comments on the thread | the issue context section |
| The boundary of the work | comparison of the closing lines against what the artifacts above actually cover | the same |

**What good looks like:** a maintainer finishes the report knowing two
things they did not know before reading it — where a fix would land,
and where my checking stopped. "The mismatch is between
`docs/API.md:18` and the `Form`/`File` parameters on
`create_profile`; a fix edits the doc, not the handler. I did not
check the other endpoints in that file." That is two sentences and it
is the whole family.

**What is not good enough:** a closing line that names no target.
"I'll keep digging", "happy to open a PR", "let me know how you'd like
to proceed" — each of these hands the triage back to the maintainer
while sounding like it is offering help. A next step is a noun: a
file, a symbol, a question with an answerable shape.

**The trap:** the estimate that sneaks in as a next step. "Should be a
one-line fix, I'll have it up shortly" names a target *and* prices the
work, and the price is the part a maintainer will remember when it is
wrong. Strip the timing and the sizing; keep the target.

---

## Reading a package that is claim-only

Early in the week the package is a claim comment and nothing else.
Only two families have evidence yet: **Comms** (the claim against the
issue and the repo's rules) and, partially, **Environment** if the
claim names where the work will happen. **Handoff** is a borderline
case and worth naming: a claim comment that states a concrete next
step does carry Handoff evidence, but a claim that stops at "I'd like
to take this" carries none, and the difference is a real signal about
the claim rather than a gap in the package. The other families have
no source to read, which is different from having a bad source. Grade them
`unclear` with the reason stated, and leave them out of the verdict —
a claim comment cannot fail a check about an artifact it does not
contain yet.
