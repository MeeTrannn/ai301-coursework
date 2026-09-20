## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer alive | Repo-facts block: date of the most recent maintainer commit or maintainer comment on any issue/PR, whichever is more recent | Maintainer activity (commit or comment) within the last 60 days | required |
| Repo in use | Repo-facts block: dates of the last 5 commits on the default branch | At least 2 of the last 5 commits landed within the last 90 days | required |
| Scope fits my skill level | Issue title, body, and any referenced file paths. If no files are referenced, fall back to the issue's labels and the repo's primary language/topic tags | Issue is centered on data/database work — schema, queries, data pipelines, data validation, docs, or straightforward bug fixes — and does NOT require systems-level work (concurrency, memory management, build-system internals, low-level networking, or infra/CI configuration) as the core task | required |
| Nobody else actively on it | Issue's assignee field and full comment thread | No current assignee, no comment in the last 14 days claiming or expressing intent to take the issue, and no bot message indicating a prior contributor was unassigned for inactivity | required |
| Good first issue signal | Issue labels | Labeled `good-first-issue`, `beginner-friendly`, `help-wanted`, or similar | preferred |

## Verdict rule

Accept if all required checks pass: Maintainer alive, Repo in use, Scope fits my skill level, and Nobody else actively on it. `unclear` on any required check counts as a fail on that check. The preferred check (Good first issue signal) never changes the verdict; among accepted issues, rank higher the ones passing it.