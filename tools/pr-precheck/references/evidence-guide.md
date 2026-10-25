# Evidence guide: where evidence lives in a PR package

The four families map to the failure categories: plan fidelity (silent drift), test evidence (not tested), diff quality (unreviewable), standards and comms (standards wall). A package that fails none of them is a clear accept.

## Plan fidelity (harness category: silent-drift)

**Where it lives.**
- Eval bundle: the "Plan context" block holds the plan's steps, its files list, its "not in scope" statement, its test plan, and the repro evidence it built on. The "Candidate PR" section holds the diff, commit list, and description. Deviation notes, if any, appear in the plan context or are stated in the description.
- Live: `plan.md` in the top folder of your working copy (steps, files, not-in-scope, and the `## Deviations` section), the output of `git diff main...HEAD` for what the branch actually changes, and the description in `pr_draft.md`.

**What good looks like.** Every changed file and substantive hunk is covered by the plan's scope or by a deviation note that names it, every planned step shows up in the diff or is plainly disclosed as left out, and each claim in the description is true of the diff. Silent drift runs in both directions: the diff does more than the plan (an unrelated refactor, an extra file or behavior change) or less (a planned step missing) with no note, or the description claims "exactly the plan, nothing else" while the diff contradicts it. A description that discloses an extra change or a shortfall accurately is honest, not drift.

## Test evidence (harness category: not-tested)

**Where it lives.**
- Eval bundle: the "Test evidence" section of the candidate PR, read against the plan's test plan and the repro evidence in the plan-context block, and against the repo's stated checks in the repo-facts block.
- Live: `test_evidence.md` (repro before and after, plus the output of `make test-unit`, `make test-integration`, `make lint`, `make typecheck`), read against `plan.md`'s test plan and the PR template's Testing checklist in `.github/PULL_REQUEST_TEMPLATE.md`.

**What good looks like.** Decisive evidence names the observable behavior from the repro, shows the before and the expected-after, and the after is consistent with the fix in the diff. The repo's own checks each have a visible outcome. A failing check reported honestly with its reason counts as evidence; hiding one does not. "Tests pass" with no repro re-run, or a ticked box with no output behind it, is not evidence.

## Diff quality (harness category: unreviewable)

**Where it lives.**
- Eval bundle: the unified diff and the commit list in the "Candidate PR" section.
- Live: `git diff main...HEAD` and `git log main..HEAD --oneline`. Use `git diff main...HEAD --stat` to spot files that do not belong.

**What good looks like.** The fix is visible without hunting, and nothing unrelated rides along. Debris tells: debug leftovers (print or console lines), commented-out blocks, dead code, leftover scratch files, formatting churn, and drive-by edits such as renames or reformatting of code the plan does not touch. A hunk that is behavior-neutral but unrelated to the plan still buries the change.

## Standards and comms (harness category: standards-wall)

**Where it lives.**
- Eval bundle: the "Repo facts" block (the PR template's required sections and checklist, contribution policy, and any stated AI-use policy) and the "Thread highlights" (explicit maintainer direction), read against the description in the candidate PR.
- Live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the Path Review repo, the issue's thread, and your draft description in `pr_draft.md`. The course's AI-use disclosure goes under Notes for Reviewers because the template has no section for it.

**What good looks like.** Each section the template asks for has real content (or "N/A" with a reason), each stated checklist item is addressed, any stated disclosure (including AI use) is present, and explicit maintainer direction in the thread is followed or engaged with. Boilerplate, an empty section, a skipped checklist item, or a visibly ignored ask is a miss. Whether the description's claims match the diff belongs to plan fidelity above, not here.
