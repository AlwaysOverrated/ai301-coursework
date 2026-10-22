# Evidence guide: where evidence lives in a plan package

A package is one markdown file with these sections in order: Repo facts, Issue, Thread highlights, Repro evidence, Candidate plan, Candidate plan comment. In live mode the same evidence lives in the repo's docs, the issue thread, the student's posted repro comment, plan.md, and comment.md.

## Diagnosis and grounding

Where it lives: the plan's stated cause is the "Cause:" paragraph of the Candidate plan (live: the diagnosis in plan.md). The behavior that cause must explain is in the Repro evidence section: the numbered Steps, plus the "Expected" and "Actual" lines (live: the student's posted repro comment). The Issue section gives the reporter's account for cross-checking.

What good looks like: the cause explains the specific Actual behavior in the Repro evidence, using the same objects and steps (for example, a view that is not refreshed after a push, matching step 3 where the color stays stale). Bad looks like a cause that contradicts the quoted output, explains a different behavior, or never mentions the repro.

## Scope

Where it lives: the "Change:" paragraph of the Candidate plan, which carries an "In:" statement (what will change, with file or function named) and an "Out:" statement (what will not). Live: the scope and files-to-touch sections of plan.md.

What good looks like: one bounded change tied to the diagnosed cause, with a named place and an explicit Out line. Bad looks like extra cleanup, refactors, or "while I'm here" work, or a change in a place the cause does not point to.

## Executability

Where it lives: the "Change:" paragraph, which should name the file and the spot in it (for example, a specific callback in a specific file), and say what edit to make there. Live: the files and approach sections of plan.md.

What good looks like: a stranger could name the first file to open and the first edit to make without asking the author anything. Bad looks like "fix the logic" or "update the handler" with no location.

## Test plan

Where it lives: the "Test:" paragraph of the Candidate plan, read against the numbered Steps and the Expected line in the Repro evidence. Live: the test plan in plan.md against the student's repro steps.

What good looks like: it re-runs the repro steps and names the exact observable result at a specific step that would differ if the fix failed (for example, at step 3 the color must flip without leaving the view). Extra checks of neighboring paths sharing the same code are a plus. Bad looks like "run the tests" or "verify it works".

## Honesty

Where it lives: a package has no separate risks heading. Look inside the Candidate plan for hedged claims, named assumptions, and open questions (for example, "also check ... since both share the callback"), and compare them with what the Repro evidence actually establishes. Live: the risks and unknowns section of plan.md and, after the build, the "## Deviations" section.

What good looks like: anything the evidence does not establish is stated as an assumption or something to verify. Bad looks like an unverified claim (about other code paths, root cause breadth, or side effects) stated as fact, or a plan that names no uncertainty where the evidence leaves obvious gaps. If a package has no stated unknowns and the evidence leaves none open, that alone is not a failure.

## Comms

Where it lives: the "Candidate plan comment" section, read against the "Thread highlights" section (existing comments, maintainer requests, claimed work) and the "Repo facts" block (bug-report template, contribution policy from CONTRIBUTING, any AI-use disclosure requirement). Live: comment.md against the live issue thread and the repo's CONTRIBUTING and templates.

What good looks like: the comment agrees with the plan, does not duplicate or contradict the thread, and honors the repo's stated policy (for example, a maintainer who reviews outside PRs selectively and wants minimal changes, so the comment keeps the change minimal and says so). It also stands alone, so a reader who never saw the plan can tell what will change and why. Bad looks like boilerplate that ignores a maintainer request, claims work someone else already claimed, skips a required disclosure, or points elsewhere ("same approach as above").
