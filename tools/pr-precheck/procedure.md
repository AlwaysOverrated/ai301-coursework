# Procedure: how this tool grades a PR package

## Read order

Read in this order, and note down the listed items as you go. The plan comes before the diff and the description because every side-by-side check later needs the plan's boundary in hand first.

1. The repo facts (eval: the repo-facts block; live: the PR template and `docs/CONTRIBUTING.md`). Note each template section the repo asks for, each checklist item, and any stated AI-use or disclosure policy.
2. The issue and its thread (eval: the issue and thread-highlights blocks; live: the issue page). Note the reported behavior and any explicit maintainer direction.
3. The plan (eval: the plan-context block; live: `plan.md` including `## Deviations`). Note four lists: the steps of the fix, the files it names, what it says is not in scope, and the test plan. Note each deviation note and the change it covers.
4. The diff and commit list. For each changed file note what the hunks do in one phrase. Mark every hunk as inside the plan, covered by a deviation, or neither. Mark debris: debug lines, commented-out code, dead code, formatting churn, unrelated renames.
5. The description and title. List every factual claim it makes (what changed, "no changes beyond the plan", tests added, docs updated, behavior unchanged, boxes ticked, disclosures).
6. The test evidence. Note each command or repro shown, its before and after output, and the outcome of each repo check.

## Evidence gathering

Gather evidence per rubric check, using `references/evidence-guide.md` for where each family lives.

- **Diff inside plan.** Compare the diff's changed files from read step 4 against the plan's files list and "not in scope" list from step 3. Record each changed file or hunk that is neither covered by the plan nor named in a deviation note.
- **Plan delivered.** Walk the plan's numbered steps from step 3. For each, record the hunk that delivers it, or record "missing" and whether the description or a deviation note says so.
- **Description matches diff.** For each claim listed in step 5, find the hunk or commit that makes it true. Record every claim with no support in the diff and every extra change the description does not mention.
- **Behavior evidence.** Pair the test plan and the issue's repro (step 3 and 2) with the evidence (step 6). Record whether the named behavior appears, whether a before and an expected-after are both shown, and whether the shown output is consistent with the fix.
- **Repo checks run.** List the checks the template or repo names (step 1). For each, find its command and visible outcome in the evidence. Record any that are missing or whose result contradicts a ticked box.
- **No debris and Reviewable change.** Use the debris marks from read step 4. Record each debug line, commented-out block, dead code, churn hunk, or unrelated hunk, by file.
- **Template and disclosure.** Pair the template sections and policy from step 1 with the description's sections. Record each section that is missing, empty, or boilerplate, and whether a required disclosure is present.
- **Maintainer direction.** Pair the thread's direction from step 2 with the plan and description. Record any direction not followed or engaged with.
- **Title.** Compare the title against the diff summary from step 4.

## Check execution

Execute the checks in the order they appear in the rubric table. Grade each one only from the evidence gathered for it, and name the deciding fact or quote in its evidence line.

- If the evidence for a check is genuinely absent from the package (a section the check needs does not exist), grade it `unclear`. Do not guess and do not fill the gap from outside the package.
- You do not need to re-read the whole package for each check; use your gathered notes. Re-read only the specific hunk or sentence a grade turns on, to quote it exactly.
- Apply each pass condition as written. Do not grade on how long, polished, or confident the PR is. If an honest disclosure ties a mismatch (a deviation note or a plain statement in the description that matches the diff), the check passes by the rubric's wording.
- If a step you need is not in this file, say so in the summary under "procedure gaps" and keep going with the stated steps.

## Verdict assembly

Apply the rubric's verdict rule: accept if every `required` check passes; reject if any `required` check fails; `unclear` counts as `fail`; `preferred` checks never change the verdict.

When more than one required check failed, quote the first failing required check in rubric order as the deciding check in the readable summary, and list the others briefly beneath it. The JSON block still lists every check with its own grade and one-line evidence. Produce the same verdict from the same grades every time, then end the reply with the JSON block from SKILL.md, valid and last.
