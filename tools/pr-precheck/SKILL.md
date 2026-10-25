---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this ready to submit?** Never answer a different question, never grade more than one package per run, and never answer from gut feel. Execute the components in this directory.

A PR package is a candidate pull request plus what it is read against. It contains the PR's title, description, commit list, diff, and test evidence. These are read against the plan the PR claims to implement (its scope, its boundary, its test plan, and any deviation notes) and against the issue that plan belongs to (the issue body, its thread, and the repo's stated PR template and policies, including any AI-use policy).

## Inputs and modes

Run in exactly one of two modes. If the user gave you a bundle or package id, run eval mode. If they gave you their own branch or issue, run live mode. If you cannot tell, ask once.

**Live mode** grades the student's own submission before it goes out. Read these inputs:

- `plan.md` in the top folder of the working copy, including its `## Deviations` section if present. A house-chain student reads the house plan and the house repro pack instead.
- The diff of the branch: run `git diff main...HEAD` (three dots) from the working copy. That command shows every committed change the branch makes relative to `main`. Uncommitted changes are not part of the PR; if `git status` shows uncommitted code changes, say so in the summary.
- The commit list: `git log main..HEAD --oneline`.
- `pr_draft.md` for the PR title (first line) and the filled-in description.
- `test_evidence.md` for the before/after repro output and the results of the repo's checks (`make test-unit`, `make test-integration`, `make lint`, `make typecheck`).
- The issue's thread, fetched from the real repo.
- The repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`) and `docs/CONTRIBUTING.md`, which hold the stated asks and policies.

If an input is missing, record it as missing in the affected check's evidence line. Do not invent it.

**Eval mode** grades a package bundle. The bundle is the whole world: every fact comes from the bundle text, nothing is fetched, and nothing else is read. Do not open GitHub, do not read `scope.md`, do not read `voice-guide.md`. Eval mode always grades a complete package: every check in the rubric and the full verdict rule. When the live issue and the bundle disagree, the bundle wins.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else, before opening the diff or any other input.

- Read the `Repo:` line. If it still holds a placeholder (anything in angle brackets such as `<ORG>/<PATH-REVIEW-REPO>`), stop without grading. Tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo, then run again. Do not emit a verdict.
- If the PR or branch targets a repository other than the one named on the `Repo:` line, refuse to grade it and say why. Never guess a scope.
- Apply the house rules in `scope.md` as part of the standards the PR is held to: the PR comes from a branch on the student's own fork, one PR per issue per student, the template is always used with every section filled, and the PR must implement the posted plan.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after reading the inputs, read `voice-guide.md`. Hold the outgoing PR text (the title and the description in `pr_draft.md`) against the rules in it. In the readable summary, list every rule the draft breaks, quoting the offending words. The voice guide never changes the verdict on its own. Only a check in `rubric.md` that reads it can do that, and personal voice carries no gold labels. If `voice-guide.md` is still an empty template, say so in one line and move on.

In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read the components in this order, and use each for what it is for:

1. `rubric.md` defines the checks and the verdict rule. It decides what is graded and how grades combine.
2. `references/evidence-guide.md` maps where each kind of evidence lives, in a bundle and in live mode, and what good looks like.
3. `procedure.md` is executed as written, stage by stage.

If `procedure.md` is silent on a step you need, report the gap in the summary and continue with the stated steps. Never improvise a new step or invent a check. If `rubric.md` has no checks or no verdict rule, or `procedure.md` has no steps (a file that is still an empty template counts), refuse to grade. Say which file is empty and stop without emitting a verdict.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit, and `reject` means hold it. There is no third verdict, no "accept with reservations", and no score. Reservations belong in the evidence lines of individual checks.

Write a short readable summary first: one line per check with its grade and the deciding evidence, then (in live mode) the voice-guide findings and any procedure gaps. Then end your reply with the fenced JSON block below. It must be valid JSON, it must be the last thing in the reply, and nothing may follow it. Use the PR URL (live) or the bundle id (eval) as `item`. List every check from the rubric, in rubric order, with a one-line `evidence` quoting the fact that decided it.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Grade no check without naming the fact or quote that decided it. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A short, plain PR can be ready, and a long, confident one can hide drift. Read each artifact itself against the plan, the issue, and the stated standards, never the formatting or the tone.
- **The rubric decides, not the run.** If a check passes by its stated condition but feels wrong, it still passes. If it fails by its stated condition but feels harsh, it still fails. Say so in the evidence line instead of bending the grade.
- **The procedure decides how, not the run.** Follow `procedure.md` as written and report its gaps instead of silently inventing steps.
- **Honest shortfalls can be ready.** A PR that discloses a gap plainly (in the description or a deviation note) and whose disclosure matches the diff is not silent drift. Do not hold a PR merely for doing less than everything.
- **Unclear is treated as the rubric's verdict rule directs.** Where the rule is silent, an unverifiable claim is a failing one. A PR you cannot verify from the package is a PR that is not ready to submit.
- **Never embellish.** Do not add facts the package does not contain, and do not grade the live issue in eval mode.
