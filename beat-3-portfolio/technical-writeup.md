# Fixing a YouTube extension shortcut that could open the chapters panel but never close it

**Issue:** [code-charity/youtube#4283](https://github.com/code-charity/youtube/issues/4283)
**Pull request:** [code-charity/youtube#4469](https://github.com/code-charity/youtube/pull/4469) (awaiting review)

## Overview

ImprovedTube is an open-source browser extension that adds features to
YouTube. One of them is a keyboard shortcut, "Chapters (Sidebar) On/Off",
that should show or hide a video's chapters panel. The bug report said the
shortcut worked in normal view but did nothing in full screen.

When I reproduced it in a real browser, the report turned out to be wrong
about the symptom. The first press opened the panel in both modes,
including full screen. The real bug was that the second press never closed
it, in either mode, and every press logged a `ReferenceError`. The cause:
after the panel opens, YouTube keeps a hidden copy of it on the page. The
extension saw that hidden copy and decided the panel was closed, so every
press took the "open" branch.

I rewrote one function, `ImprovedTube.shortcutChapters`, so it decides by
whether a panel is actually expanded. The second press now closes the
panel in both modes, console errors went from 4 to 0, a new 4-check unit
test fails 4 of 4 on master and passes on the branch, and all 233 of the
repo's tests pass.

## What happened

**Reading the code first, and guessing wrong.** I started by reading
`shortcuts.js` and running the real function in Node against stub pages. I
found two real errors: line 494 reads two `const`s outside the blocks that
declare them (a `ReferenceError` on every press), and line 486 calls
`.clientHeight` on `null` when the page has no chapters panel (a
`TypeError`). My first draft said the `ReferenceError` might be the cause.
Running the function showed it fires *after* the click, so it's a side
bug. My other guess, that YouTube hides the panel in full screen, was also
wrong.

**Reproducing it in a real browser.** I loaded the extension unpacked into
Chromium 141 with Playwright, wrote the shortcut binding (H) into the
extension's storage, and pressed H twice in normal view and twice in full
screen. After every press I logged each `[target-id*=chapters]` panel's
`visibility`. On master:

```
B normal, after H:          ["EXPANDED h=465","HIDDEN h=0"]
C normal, after 2nd H:      ["EXPANDED h=465","HIDDEN h=0"]
E1 fullscreen, after H:     ["EXPANDED h=766","HIDDEN h=0"]
E2 fullscreen, after 2nd H: ["EXPANDED h=766","HIDDEN h=0"]
errors: ["modernChapters is not defined" x4]
```

Two panels after the first press: the open one and a hidden copy, both in
`ytd-watch-flexy #panels`. The function picked "open" whenever
`querySelector('[target-id*=chapters][visibility*=HIDDEN]')` found
anything, and the hidden copy always matched.

**Re-scoping.** The reported symptom didn't reproduce, so I didn't claim to
fix it. I posted the browser output on the issue, said the bug I could
prove was "the panel never closes", and asked the reporter which browser
still fails in full screen.

**The fix.** The new `shortcutChapters`:

- returns early when the page has no chapters panel;
- closes the panel when any chapters panel is expanded, by clicking that
  panel's own `#visibility-button`;
- otherwise opens it the way it did before, falling back to removing the
  `visibility` attribute only when the open button is missing.

That's a 23-line change to one function. I left the key binding, menu
entry, keyboard handler, and `appearance.js` alone, and added no
full-screen-specific code, because the open path already works there.

**Testing.** The repo has no jsdom, so
`tests/unit/shortcut-chapters-toggle.test.js` loads the real `shortcuts.js`
against a hand-built stub page that keeps a hidden copy of the panel after
opening, like YouTube does. It checks that the first press opens, the
second closes, no press throws, and a page with no chapters panel is left
alone. The stub throws on any selector it doesn't know, so a changed
selector fails loudly instead of returning `null`. Results:

```
Browser, on the branch:
C normal, after 2nd H:      ["HIDDEN h=0","HIDDEN h=0"]
E2 fullscreen, after 2nd H: ["HIDDEN h=0","HIDDEN h=0"]
errors: []

New unit test:  master 4 failed  →  branch 4 passed
npx jest / npm test:  38 suites, 233 of 233 passing
```

I left ESLint out of my checks because it crashes on master while parsing
its own config (CI runs it with `continue-on-error`), and I said so in the
PR.

## Limitations and what's next

- **The original report is still open.** I tested only in headless
  Chromium 141, which isn't the reporter's browser. I can't rule out the
  full-screen failure somewhere else, and I've asked the reporter which
  browser still shows it.
- **The test can't see YouTube change.** The stub page only knows the
  selectors the shortcut uses. If YouTube changes its markup again, the
  unit test will still pass; only the browser run would catch it.
- **`shortcutTranscript` likely has the same bug.** It has the same shape
  as `shortcutChapters`, but it's a different feature, so it belongs in its
  own PR. That's the natural next contribution.
- **Review is pending.** The PR opened on 2026-11-09. Maintainers here
  usually answer in 0.5 to 7.5 days, so I'll leave a single nudge around
  2026-11-19 if it's still quiet, and respond to whatever review comes.
- **What I'd do differently:** reproduce in a real environment before
  writing anything. Both of my code-reading guesses were wrong, and the
  browser run settled it in minutes.
