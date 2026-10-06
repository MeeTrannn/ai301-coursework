# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

Read every part before grading any check. Take notes as you go; the
checks later run against these notes.

1. **Set the mode.** If you were given a bundle file, this is eval mode:
   the bundle is the only evidence, and you skip `scope.md` and
   `voice-guide.md`. Otherwise this is live mode: read `scope.md` first
   and stop if the issue is outside the scope or the scope still has a
   placeholder. Then read `voice-guide.md`.
2. **Issue context.** Write down in one sentence what the issue asks
   for, plus the expected behavior and the actual behavior it reports.
3. **Repro evidence, before the plan.** Write down the repro steps, the
   observed result, the expected result, any artifacts (logs, output,
   stack traces), the environment and versions, and anything the repro
   says it did not test. Read it before the plan so you judge the
   diagnosis against what the evidence shows, not against how the plan
   frames it.
4. **Thread highlights** (live: the full issue thread). Write down
   every maintainer direction, rejected approach, open question
   addressed to contributors, and earlier claim or attempt. Tag each
   commenter as a maintainer (owner, member, collaborator, or the
   issue author speaking for the project) or a peer. Maintainer
   directions bind the comment. A peer's request or review, including
   a classmate's on a shared Path Review issue, is a thread item the
   comment should engage, not a direction it must follow. Disagreeing
   with a peer, with a reason given, passes.
5. **Repo facts** (live: CONTRIBUTING, issue and PR templates, the AI
   or contribution policy, the README's build and test section). Write
   down every requirement that applies to an issue comment or plan
   comment, plus the repo layout and the test commands. Treat the
   package as AI-assisted work, so an AI-use disclosure rule always
   applies when the repo states one. In live mode, where a repo
   convention conflicts with a `scope.md` house rule (for example, a
   branch-name format), the house rule wins. The comment passes if it
   follows the house rule and does not leave the thread pointing at the
   old convention.
6. **Candidate plan.** Read it in full once, then gather from it as
   described under Evidence gathering.
7. **Plan comment, last.** Read it the way a maintainer on the thread
   would, already knowing the issue, the repro, and the thread.

## Evidence gathering

Record each item below in your notes. Copy exact quotes wherever a
check will need to cite them. Live-mode locations follow
`references/evidence-guide.md`. In live mode, the repro evidence is the
student's posted repro comment on the issue. On the house issue it is
the house repro pack as the drafts quote it. If the drafts quote none,
record "no repro evidence".

- **Diagnosis** (for `diagnosis_grounded`): quote the plan's stated
  cause word for word. List which observed repro facts it explains.
  Then go through every control run, timing matrix, or isolating step
  in the repro. For each, write what it shows about the blamed
  component: the symptom happens without it, it works correctly, or
  the step is consistent with the plan's cause.
- **Cause targeting** (for `targets_cause`): quote the proposed change.
  Record where the change acts (file, function, or layer) and where the
  diagnosis places the cause. Note whether they are the same place.
- **Scope** (for `scope_bounded`): list every file, area, and step the
  plan names, including anything in its not-in-scope line. Next to
  each, write how it serves the issue's ask from Read order step 2, or
  "none".
- **Executability** (for `executable_by_stranger`): quote the plan's
  first concrete step. Record whether it names a location and the
  change made there. List every open decision and whether the plan
  says how that decision will be made. Check any named commands or
  paths against the repo facts.
- **Test plan** (for `test_proves_fix`): quote the test steps and the
  expected result. Put them beside the repro steps and observed result
  from Read order step 3. Note whether the expected result differs from
  what the repro observed today.
- **Honesty** (for `unknowns_stated`): list everything the repro
  evidence, issue, or thread explicitly marks as untested, unknown, or
  disputed. For each item, quote how the plan and the comment treat it:
  flagged as unknown, deferred, or stated as settled.
- **Comment against plan** (for `comment_matches_plan`): list the
  cause, change, and scope the comment states. Put the plan's version
  of each beside it.
- **Comment against thread** (for `comment_thread_aware`): for each
  thread item from Read order step 4, record whether the comment
  respects it, ignores it, or contradicts it.
- **Comment against repo** (for `comment_repo_conventions`): for each
  requirement from Read order step 5, record whether the comment meets
  it.
- **Comment on its own** (for `comment_reviewable`): record whether the
  comment alone says what is broken, what will change, how the fix will
  be shown, and any question that needs a maintainer's answer.

## Check execution

1. Run the checks in the rubric's table order and grade all of them.
   Do not stop at the first fail, because the output reports every
   check.
2. For each check, apply its pass condition word for word to your notes
   for that check. Grade it `pass`, `fail`, or `unclear`. Write one
   evidence line: the quote or fact that decided the grade.
3. Treat the plan saying nothing differently from the package lacking
   evidence:
   - **The plan or comment says nothing** about what the check judges
     (no test plan, no stated cause, no first step): grade `fail`. The
     missing content is the outcome being judged.
   - **No repro evidence in the package:** grade `diagnosis_grounded`
     and `test_proves_fix` as `fail`, because the plan has to follow
     from the reproduction.
   - **Eval mode, and the bundle lacks a section a check reads** (no
     thread highlights, or no repo-facts block): grade that check
     `unclear` and name the missing section.
   - **Live mode, and the repo states no requirement or the thread has
     no maintainer direction:** that is a fact, not missing evidence.
     Apply the pass condition, which passes these cases.
4. Use `unclear` only for the case above, or when your notes support
   both a pass and a fail reading and re-reading the cited section does
   not settle it. In the evidence line, name what would have settled
   it.
5. Grade from your notes. Re-read a part of the package only when the
   notes lack the fact a pass condition needs, and then re-read only
   that section. Never re-read to argue a check into a pass.
6. Grade each check on its own merits. A failed `diagnosis_grounded`
   does not by itself fail `targets_cause`. That check compares the
   change with the plan's own stated cause.

## Verdict assembly

1. Apply the rubric's verdict rule. If every `required` check is
   `pass`, the verdict is `accept`. If any `required` check is `fail`
   or `unclear`, it is `reject`. `preferred` checks never change the
   verdict.
2. On reject, the deciding check is the first `required` check in table
   order that graded `fail` or `unclear`. Lead the readable summary with
   it and its evidence line.
3. Write the readable summary: one line per check with its grade and
   evidence. In live mode, add any voice-guide rule the comment breaks,
   quoting the rule. List any gap you hit in this procedure.
4. Emit the JSON block from SKILL.md last. List the checks in table
   order, use the rubric's check names exactly, and use the same
   evidence lines as the summary. Write nothing after the block.
