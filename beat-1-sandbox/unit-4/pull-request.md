# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/NUMBER

**Branch**

fix/72-verify-password-unrecognized-hash

## Eval iterations

**Run history**

One full run, so one score: 19/20. The agreement line in my committed `eval-run.txt` reads: "agreement: 19/20 scored items  (bar: 18/20: PASS)". I did not do any partial runs.

**Package analysis**

pkg-08 (jesseduffield/lazygit#5883). The gold label is accept; my rubric said reject, so this is the one package where I disagreed with staff (its note read "failed: Repo checks run"). The lazygit template asks for formatted code, tests, and regenerated cheatsheets, and the PR description says "Requirements: cheatsheets regenerated (no binding changes, no diff); code formatted; integration test added". The test evidence shows `go test ./...`, the integration test, and `go generate ./...` with no diff, but no formatter output. My "Repo checks run" pass condition says to "Fail if ... the description ticks a box the evidence does not support", so the unsupported "code formatted" claim failed the check even though the diff, the test evidence, and the description otherwise agree. Staff accepted it, so my rule is stricter than the gold label about claims that have no output behind them.

**Check rationale**

The row as it reads in the `rubric.md` I uploaded:

> | Repo checks run | The test evidence section, read against the repo's stated checks (the PR template's testing checklist and the repo's own test, lint, and typecheck commands). | Each check the repo or template names was run and its outcome is visible in the evidence, including any that failed or did not run, with a stated reason for each. Fail if a named check has no visible outcome, or if the description ticks a box the evidence does not support. An honestly reported failing check with its reason passes. | required |

It reads that way because it is the "not tested" check: a PR should show output for the repo's own checks, not just claim them. "An honestly reported failing check with its reason passes" is there so a PR that discloses a failure is not held for it. "Ticks a box the evidence does not support" catches claimed checks with nothing behind them. This is my first draft of the row; I did not revise it after the full run. When I ran the skill on my own PR it failed this check once because my CI box was unticked with no explanation, and I fixed that by adding a note in `pr_draft.md` rather than by editing the rubric.

**Trade-offs**

This check costs me pkg-08, as described above. I considered narrowing it to only the test and behavior checks, which would probably have fixed pkg-08, but loosening it could flip the not-tested packages that agree today (pkg-04, pkg-07, pkg-10 and pkg-14 all matched), so I did not change it. I ran no canaries because I changed nothing after the full run. Nothing else changed, and I know because the one full run already met the bar with every category matched ("clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3"), so I kept the uploaded files identical to the ones the run's fingerprints record.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
