# Contribution: Chapters (Sidebar) On/Off shortcut not working in full screen

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Mee Tran
**GitHub Username:** MeeTrannn
**Issue:** https://github.com/code-charity/youtube/issues/4283
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

**Update, 2026-11-04: I switched issues.** I claimed pudl#4633 on 2026-10-26
and got no reply from the maintainers in nine days. That issue needs their
data-model decision before any code, so I couldn't build without them in the
time left. From the Issue Selection site's list for code-charity/youtube
(the ImprovedTube browser extension), I read all 16 threads. Most were
already claimed or fixed. #4283 was the one with a clear bug in one
function, no claimant, and an earlier attempt closed by its own author. My
scout's Domain fit check would reject it, because it is JavaScript extension
work, not geospatial or energy data. I chose it anyway, because a bounded
bug I can finish and test is worth more this term than a data issue I can't
get direction on. New claim comment:
https://github.com/code-charity/youtube/issues/4283#issuecomment-6648580529

What follows is my original write-up for pudl#4633, kept as the record of
how I got here.

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

ImprovedTube lets you bind a key to "Chapters (Sidebar) On/Off". The
reporter says the key toggles YouTube's chapters panel in normal view but
does nothing in full screen.

**Expected Behavior**

Each press toggles the chapters panel: the first press opens it, the next
closes it, in normal view and in full screen, with no errors.

**Current Behavior**

On today's YouTube (Chromium 141, Nov 2026) the first press does open the
panel, in full screen too, so the exact report didn't reproduce for me. What
does fail, in both modes, is the second press: the panel never closes. Every
press also throws `ReferenceError: modernChapters is not defined` in the
console.

**Affected Components**

`ImprovedTube.shortcutChapters` in
`js&css/web-accessible/www.youtube.com/shortcuts.js`, called by name from
the keyboard handler `ImprovedTube.shortcutsHandler` in the same file.
YouTube's chapters panel is a `[target-id*=chapters]` engagement panel inside
`ytd-watch-flexy #panels`.

**Toolkit**

- Tool: Claude Code, reading `shortcuts.js` on master and running the real
  `shortcutChapters` function in Node against stub pages, before I had a
  browser set up.
- What it produced: the line numbers, the fact that nothing on the shortcut
  path treats full screen differently, and two real errors (a
  `ReferenceError` on line 494, a `TypeError` on line 486 with no chapters
  panel).
- Difficulties: my first claim draft said the `ReferenceError` might be the
  cause. Running the function showed it fires after the click, so it is a
  side bug. And "YouTube hides the panel in full screen" was a guess that
  turned out to be wrong.
- What changed: I rewrote the claim to separate what I had verified from
  what I had guessed, and made "check it in a real browser" my first step.

---

## Reproduction Process

**Environment Setup**

macOS (Apple silicon), Node v25.8.0, npm 11.11.0. I forked
code-charity/youtube and cloned the fork at master `737bbfe6`, then ran
`npm ci --force` (CI uses `npm install --force`, but that rewrote
`package-lock.json`; `npm ci` installs the same lockfile without touching
it). `npx jest` passed 229 tests on master. To watch the bug, I installed
Playwright 1.56.1 and its Chromium 141.0.7390.37 outside the clone, and
loaded the clone as an unpacked extension.

Challenges: ESLint crashes on master while parsing its own config (CI runs
it with continue-on-error), so I left it out of my checks. The extension's
shortcut has no default key, so my script writes `shortcut_chapters` (bound
to H) into the extension's storage before opening YouTube.

**Steps to Reproduce**

1. Load the extension unpacked from master, bind "Chapters (Sidebar)
   On/Off" to H, and open a video with chapters
   (https://www.youtube.com/watch?v=rfscVS0vtbw).
2. Press H twice in normal view. Then press F for full screen and press H
   twice again. After each press, read every `[target-id*=chapters]`
   panel's `visibility` attribute and watch the console.
3. Observed: the first press opens the panel in both modes. The second
   press leaves it open in both modes. Every press logs
   `ReferenceError: modernChapters is not defined`.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/MeeTrannn/youtube/commit/7478d367
  (the unit test that reproduces the bug; it fails 4 of 4 on master)
- **Screenshots/logs:** browser run on master:

  ```
  B normal, after H:        ["EXPANDED h=465","HIDDEN h=0"]
  C normal, after 2nd H:    ["EXPANDED h=465","HIDDEN h=0"]
  E1 fullscreen, after H:   ["EXPANDED h=766","HIDDEN h=0"]
  E2 fullscreen, after 2nd H: ["EXPANDED h=766","HIDDEN h=0"]
  errors: ["modernChapters is not defined" x4]
  ```

- **My findings:** the reported symptom (no effect in full screen) did not
  reproduce on today's YouTube; a screenshot after the first press in full
  screen shows the chapters panel open beside the video. The real bug is
  that the shortcut can't close the panel, in either mode. After the first
  press the page holds two chapters panels in `#panels`, the open one and a
  hidden copy, and the function opens whenever a hidden one exists. I said
  all this on the thread and asked the reporter which browser still fails
  in full screen.

**Toolkit**

- Tool: repro-check's idea of reproduction (paste the output, name the
  environment), carried out with a Playwright script Claude Code wrote that
  loads the extension, presses the shortcut, and logs every chapters
  panel's state.
- What it produced: the before log above, a full-screen screenshot, and the
  "panel parents" line that located both panels in `ytd-watch-flexy #panels`.
- Difficulties: headless Chromium is not the reporter's browser, so I can't
  rule out the original full-screen failure elsewhere. The first browser run
  overturned my code-reading diagnosis.
- What changed: I re-scoped the fix to the bug I could prove, the toggle
  that can't close, and said so in the thread instead of claiming to fix
  the report as written.

---

## Solution Approach

**Analysis**

The "Chapters (Sidebar) On/Off" shortcut runs `ImprovedTube.shortcutChapters` in `js&css/web-accessible/www.youtube.com/shortcuts.js` (line 485 on `master` at `737bbfe6`). The keyboard handler, `ImprovedTube.shortcutsHandler`, calls it by name, and nothing on that path treats full screen differently.

I reproduced it in Chromium 141.0.7390.37 with the extension loaded unpacked from `master`, the shortcut bound to H, on a video with chapters. I pressed H twice in normal view and twice in full screen, and read each `[target-id*=chapters]` panel's `visibility` after every step:

```
A normal, before H:       ["HIDDEN h=0"]
B normal, after H:        ["EXPANDED h=465","HIDDEN h=0"]
C normal, after 2nd H:    ["EXPANDED h=465","HIDDEN h=0"]
D fullscreen, before H:   ["HIDDEN h=0","HIDDEN h=0"]
E1 fullscreen, after H:   ["EXPANDED h=766","HIDDEN h=0"]
E2 fullscreen, after 2nd H: ["EXPANDED h=766","HIDDEN h=0"]
panel parents: ["EXPANDED in div#panels < div#panels-full-bleed-container < div#full-bleed-container < ytd-watch-flexy",
                "HIDDEN in div#panels < div#panels-full-bleed-container < div#full-bleed-container < ytd-watch-flexy"]
errors: ["modernChapters is not defined" x4]
```

The first press opened the chapters panel in both modes (steps B and E1), so the issue's exact symptom (nothing happens in full screen) did not reproduce on today's YouTube. What did reproduce, in both modes, is that the second press never closes the panel (steps C and E2), and every press logs a `ReferenceError`.

The cause, traced in the code and in the page:

1. The function picks "open" when `document.querySelector('[target-id*=chapters][visibility*=HIDDEN]')` finds anything. After the first press the page holds two chapters panels in `ytd-watch-flexy #panels`, the open one and a hidden copy (step B and "panel parents" above). The hidden copy always matches, so every press takes the "open" branch and the panel can't be closed.
2. Line 494 reads `modernChapters` and `visibilityButton`, which are `const`s declared inside the `if`/`else` blocks above it. That throws a `ReferenceError` on every press, after the click has already happened.
3. Line 486 reads `.clientHeight` of `document.querySelector('[target-id*=chapters]')`, which is `null` on a page with no chapters panel, so it throws a `TypeError` there.

**Proposed Solution**

Rewrite `ImprovedTube.shortcutChapters` so it:

- returns early when the page has no chapters panel;
- closes the panel when any chapters panel is expanded, by clicking that panel's own `#visibility-button` button;
- otherwise opens it the way it does today, with the `[modern-chapters] #navigation-button` button, and falls back to removing the `visibility` attribute (logging the existing console message) only when that button is missing.

What I won't change: the shortcut's key binding, the menu entry and its translations, the keyboard handler, `shortcutTranscript` (it has the same shape and may have the same bug, but it belongs in its own PR), and the chapters code in `appearance.js`. I'm also not adding full-screen-specific code, because the open path already works in full screen once the toggle logic is right.

**Implementation Plan**

1. Change `ImprovedTube.shortcutChapters` in `js&css/web-accessible/www.youtube.com/shortcuts.js` as above, keeping the existing selectors and console message.
2. Add `tests/unit/shortcut-chapters-toggle.test.js`, which loads the real `shortcuts.js` the way `shortcuts-stuck-key-recovery.test.js` does, with a stub page that keeps a hidden copy of the panel after opening. It checks that the first press opens, the second press closes, no press throws, and a page with no chapters panel is left alone. It must fail on `master` and pass with the fix.
3. Re-run the browser reproduction on the branch (two presses in normal view, two in full screen) and confirm the panel opens and closes in both with no console errors.
4. Run the repo's CI checks: `npm ci --force`, `npx jest`, `npm test`.

Deviation, recorded 2026-11-05: the issue reports the shortcut failing to
open chapters in full screen. That didn't reproduce, so the plan fixes the
failure that did (the panel can't be closed, in either mode) and asks the
reporter which browser still fails in full screen.

**Toolkit**

- Tool: plan-check, grading plan.md and a draft plan comment
  (`plan-check: grade my plan in plan.md and draft comment in
  plan-comment.md for issue <url>`).
- What it produced: run 1 rejected (diagnosis_grounded: unclear). Run 2
  accepted, with all ten checks passing.
- Difficulties: run 1 was right. My cause ("YouTube keeps a hidden copy of
  the panel") was stated as fact without the browser output that showed it,
  and the comment described my results instead of pasting them. It also
  caught that the comment dropped two things my claim had promised: an
  answer to my own overlay-or-sidebar question, and results before any PR.
  One run took over ten minutes.
- What changed: I pasted the step-by-step panel states and the "panel
  parents" line into both plan.md and the comment, said the overlay question
  was moot, and labelled the post as the promised results. Run 2 also noted
  the comment dropped the AI disclosure my claim had; I added it back.

---

## Testing Strategy

1. **The browser reproduction, before and after.** On master the second
   press leaves the panel EXPANDED in both modes, with 4 ReferenceErrors.
   On the branch:

   ```
   C normal, after 2nd H:    ["HIDDEN h=0","HIDDEN h=0"]
   E2 fullscreen, after 2nd H: ["HIDDEN h=0","HIDDEN h=0"]
   errors: []
   ```

2. **A unit test that fails without the fix.**
   `tests/unit/shortcut-chapters-toggle.test.js` loads the real
   `shortcuts.js` with a stub page that keeps a hidden copy of the panel
   after opening, as YouTube does. It checks that the first press opens,
   the second closes, no press throws, and a page with no chapters panel is
   left alone. On master: 4 failed. On the branch: 4 passed.
3. **The repo's own CI checks** (my ship manifest): `npm ci --force`, then
   `npx jest` and `npm test`, both 38 suites and 233 tests passing on the
   branch. ESLint is excluded because it fails on master before linting
   anything.

Full output is in `test_evidence.md` in my clone.

**Toolkit**

- Tool: Jest (the repo's test runner), my Playwright browser script, and
  the checks in my ship manifest.
- What it produced: before and after for both the browser run and the new
  test, plus 233 of 233 tests passing on the branch.
- Difficulties: the repo has no jsdom, so the test uses a hand-built stub
  page, which only knows the selectors the shortcut uses. If YouTube changes
  its markup again, the stub won't notice; only the browser run would.
- What changed: I made the stub throw on any selector it doesn't know, so a
  changed selector fails the test loudly instead of returning null.

---

## Implementation Notes

**2026-11-04**

Switched from pudl#4633 to code-charity/youtube#4283 (see Why I Chose This
Issue). Read `shortcutChapters` and ran it in Node against stub pages: found
the `ReferenceError` (line 494) and the `TypeError` with no chapters panel
(line 486). My first claim draft overstated the `ReferenceError` as the
likely cause; checking showed it fires after the click, so I corrected the
draft before posting.

**2026-11-05**

Forked and cloned the repo and ran the ship-gate install against the new
clone. Baseline on master: 229 Jest tests pass, ESLint crashes on its own
config. Ran the bug in Chromium with the extension loaded. Wrong turn: I had
expected the full-screen open to fail, but it worked. The real failure is
that the second press never closes the panel, because YouTube leaves a
hidden copy of the panel next to the open one. Before writing the plan, I
prototyped the fix on `fix/4283-chapters-shortcut-toggle` to make sure the
diagnosis held in the browser. It did: open and close now work in both
modes, with no errors. Committed the fix (2702c5d1) and a unit test that
fails 4 of 4 on master (7478d367). Decided to leave `shortcutTranscript`
alone even though it has the same shape: different feature, its own PR.

**Toolkit**

- Tool: Claude Code for the edits and commits, git on a feature branch.
- What it produced: two commits, a 23-line change to one function and a
  117-line test file.
- Difficulties: the course order is plan first, then build. I built a
  prototype first because the browser had just overturned my diagnosis and
  I didn't trust a plan I hadn't seen work.
- What changed: I wrote plan.md from the prototype's evidence and ran
  plan-check on it before treating the branch as done.

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
