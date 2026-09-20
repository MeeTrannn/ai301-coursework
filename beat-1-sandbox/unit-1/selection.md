# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/37


**Verdict output**

Verdict: accept

```
#37 is the strongest fit: documenting the request bodies for POST /profiles and POST /reviews requires reading api/schemas/review.py and api/routes/profiles.py, which is real schema/data-contract work rather than pure infra or app plumbing. It passed all required checks (maintainer active 3 days ago, 5/5 recent commits within 90 days, no assignee or claim on the issue) and both preferred signals (good-first-issue label, tier-1). Ranked above #63 (self-contained test-file fix) and #73 (pure config/docs housekeeping) because it's the one that involves reading and understanding real backend code.
```

---

## Eval iterations

**Run history**

1. Full run, `--save-run eval-run.txt`: "agreement: 15/20 scored items (bar: 18/20: below the bar)". Categories: "claimed 4/4 clear-accept 5/8 dead-repo 3/3 policy 1/1 scope 2/4". Disagreements: issue-04, issue-09, issue-19 (gold accept, my verdict reject, failed "Scope fits my skill level"); issue-10, issue-15 (gold reject, my verdict accept, "graded accept").
2. Revised "Nobody else actively on it" from `preferred` to `required` in the checks table, to address the issue-10/issue-15 pattern (unconfirmed claim comments and prior inactivity-unassignment messages).
3. Targeted re-check, `--only issue-10,issue-15,issue-01,issue-06,issue-11,issue-14,issue-16`: "agreement: 5/7 scored items". issue-10 and issue-15 still graded "accept" against gold "reject"; issue-01, issue-06, issue-11, issue-14, issue-16 unaffected.
4. Found the Verdict rule still named only three required checks by name, so the new required row was not gating the verdict. Rewrote it to read: "Accept if all required checks pass: Maintainer alive, Repo in use, Scope fits my skill level, and Nobody else actively on it."
5. Re-ran the same targeted set: "agreement: 5/7 scored items" — issue-10 and issue-15 still graded "accept", indicating the check's pass condition itself, not just the wiring, was not matching the literal text in these two bundles.
6. Final full run, `--save-run eval-run.txt`, to regenerate the committed file against the current rubric.md: "agreement: 17/20 scored items (bar: 18/20: below the bar)". Categories: "claimed 4/4 clear-accept 6/8 dead-repo 3/3 policy 1/1 scope 3/4". Remaining disagreements: issue-09, issue-19 (gold accept, my verdict reject, failed "Scope fits my skill level"); issue-10 (gold reject, my verdict accept, "graded accept").

**Issue analysis**

issue-10: gold label is "reject"; my rubric's verdict was "accept" — "issue-10 reject accept NO graded accept". This issue's comment thread contains an unconfirmed claim: a contributor expressed intent to take on the issue, but no maintainer confirmed the assignment. My rubric's "Nobody else actively on it" check is written to catch "a comment... claiming or expressing intent to take the issue," which should match this pattern, but the check still returned pass and the issue was accepted. The check's intent covers this case correctly, but its evaluation on this specific issue did not, which suggests the pass condition's wording is not specific enough for the grading model to reliably apply against real comment-thread text, rather than a gap in what the check is meant to detect.

**Check rationale**

The current wording of "Scope fits my skill level" reads: "Issue is centered on data/database work — schema, queries, data pipelines, data validation, docs, or straightforward bug fixes — and does NOT require systems-level work (concurrency, memory management, build-system internals, low-level networking, or infra/CI configuration) as the core task." I wrote it this way because I am building toward data engineering rather than systems engineering, and I wanted a rule a grader could apply the same way I would without asking my opinion of each issue — naming the specific systems-level categories to exclude, rather than a vaguer term like "too advanced," was meant to make the check reproducible.

**Trade-offs**

This check is why issue-09 and issue-19 were incorrectly rejected against a gold label of "accept" — both failed with "failed: Scope fits my skill level" in the final run. Because the pass condition treats certain systems-adjacent language as disqualifying without weighing how central it actually is to the issue's core task, it appears to reject issues where such language appears only incidentally. I accept this trade-off deliberately: a rubric this strict about excluding systems-flavored work will sometimes reject issues gold considers fine for a newcomer, but that matches my stated goal of avoiding issues that would pull me into unfamiliar territory, even at the cost of some false rejections.
---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

[Answer all three:

1. The issue 37 fits my interest (I stated that I would be interested in more data engineering side (database, SQL, etc) and this issue would require reading and describing schema/data-contract shapes, not debugging a library version migration.
2. The verdict does weigh the data engineer vs software engineering part of my rubric but it might still miss the actual judgement on how related to data engineering the repo is (because it might match exact keywords instead of infering the actual task requirement)
3. I choose the tier 1 - Suitable for beginner so it matches my skill level

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
