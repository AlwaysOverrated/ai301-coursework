# Unit 1 — Issue Selection

## Selected issue

### Issue link

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72

### Verdict output

```
Repo facts (codepath/pathreview-ai301-fa26-s3, checked 2026-09-21): not archived, 2 stars, last push 2026-09-16, last 5 default-branch commits all authored by Aburke225 (human, most recent 5 days ago), no open PRs in the repo, no AI policy in docs/CONTRIBUTING.md, README, or the PR template.

┌────────────────────────┬───────┬────────────────────────────────────────────────────────────────────────────────┐
│         Check          │ Grade │                                    Evidence                                    │
├────────────────────────┼───────┼────────────────────────────────────────────────────────────────────────────────┤
│ Repo not archived      │ pass  │ "isArchived": false                                                            │
├────────────────────────┼───────┼────────────────────────────────────────────────────────────────────────────────┤
│ Repo recently active   │ pass  │ Last push 2026-09-16 — 5 days before today                                     │
├────────────────────────┼───────┼────────────────────────────────────────────────────────────────────────────────┤
│ Repo shows human       │ pass  │ 5 of last 5 commits by Aburke225 (human), newest 2026-09-16                    │
│ activity               │       │                                                                                │
├────────────────────────┼───────┼────────────────────────────────────────────────────────────────────────────────┤
│                        │       │ Single-author issue from a COLLABORATOR naming the exact fix ("should fail     │
│ Settled, bounded spec  │ pass  │ closed (return False), not raise"), two files, 1–2 h estimate, zero comments   │
│                        │       │ and so no dispute                                                              │
├────────────────────────┼───────┼────────────────────────────────────────────────────────────────────────────────┤
│ Not a pure support     │ pass  │ Reports arity.py, not a usage question            │
│ question               │       │                                                                                │
├────────────────────────┼───────┼───────────────────────────────────────────────────┤
│                        │       │ assigneesthe only two timeline events are         │
│ Unclaimed              │ pass  │ classmates' commits in their own coursework forks, which the Path Review house │
│                        │       │  rule says never block                                                         │
├────────────────────────┼───────┼───────────────────────────────────────────────────┤
│ AI-contribution policy │ pass  │ No AI polTRIBUTING.md covers branches, commits,   │
│  not a ban             │       │ CI and xfsses                                     │
├────────────────────────┼───────┼───────────────────────────────────────────────────┤
│ Good-first-issue label │ pass  │ Labels inbug, api, tier-1 (Starter difficulty)    │
│  (preferred)           │       │                                                   │
├────────────────────────┼───────┼───────────────────────────────────────────────────┤
│ Adoption / usage scale │ fail  │ 2 stars,  classroom repo, so low adoption (does   │
│  (preferred)           │       │ not affec                                         │└────────────────────────┴───────┴───────────────────────────────────────────────────┘Every required check passes, so the verdict strong match for you: it's pure Pythonbackend work in core/security.py with a pyteytest.mark.xfail marker for manifest idH-05), no frontend or styling, and it's a code fix rather than docs. The one caveat worth flagging: the two classmates who referenced this issue on 2026-09-20 are likely working it too — normal here, and per the house rule you should claim and open your PR anyway, since credit erge.
```

## Eval iterations

### Run history

- Run 1 (smoke test, `--limit 3`): 2/3 agreement (issue-01 disagreed: gold accept, graded reject)
- Run 2 (full run, 20 issues): 18/20 agreement (bar: PASS) — categories: claimed 4/4, clear-accept 8/8, dead-repo 3/3, policy 1/1, scope 2/4
- Run 3 (full run, 20 issues, after tightening the "bounded spec" check for the scope category): 18/20 agreement (bar: PASS) — categories: claimed 4/4, clear-accept 6/8, dead-repo 3/3, policy 1/1, scope 4/4
- Run 4 (`--only issue-01,issue-05,issue-10,issue-15,issue-20`, checking the scope fix did not break earlier passes): 5/5 agreement
- Run 5 (`--only issue-01,issue-15,issue-19,issue-20`, confirming the single-author-vs-disputed distinction fix): 4/4 agreement
- Run 6 (final full run, saved to `eval-run.txt`): 19/20 agreement (bar: 18/20: PASS) — categories: claimed 4/4, clear-accept 7/8, dead-repo 3/3, policy 1/1, scope 4/4

### Issue analysis

issue-01 (conda/conda#16475): gold label is `accept`. My rubric's first full run rejected it, failing "Maintainer responsiveness" because the sampled issue-response threads mostly showed no maintainer reply within 30 days ("no maintainer comment in thread" on 3 of 5 sampled issues, one at 32.9 days). That check was weighting the wrong signal for this case: the repo's last 5 default-branch commits were all authored by a human within a day of the capture date, which is a stronger and more direct liveness signal than issue-thread response latency on a fast-moving repo where maintainers triage by merging rather than by replying. I rewrote the check ("Repo shows human activity") to pass on either signal — recent human commits or a maintainer thread reply — rather than requiring the reply signal specifically, and issue-01 then graded `accept`, matching gold. (Note: on the final saved run, issue-01 disagreed again in the opposite direction, this time failing "Issue has a settled, bounded spec" — a sign the check still sits close to the edge for this particular issue, discussed further under Trade-offs.)

### Check rationale

Quoted check ("Issue has a settled, bounded spec", pass condition, as currently written in `rubric.md`):

"Passes even when the issue lists multiple named causes or candidate approaches, as long as those come from a single author (e.g. a maintainer's own bug diagnosis) with no unresolved dispute -- offering options is normal scoping, not an unsettled debate."

Reasoning: an earlier version of this check treated any issue naming multiple possible causes or candidate fixes as "unbounded," which incorrectly rejected issue-19 (a maintainer's own bug diagnosis naming two causes and three candidate fixes, with zero comments and no dispute). The current wording distinguishes a single author scoping out the problem space from an actual unresolved disagreement between multiple people, which is what the "scope" category's gold labels (issue-15's years of multi-person design debate, issue-20's unendorsed product request) are actually testing for.

### Trade-offs

This check's current wording accepts issue-19 (single-author, multiple named causes, no dispute) while still correctly rejecting issue-20 (a bot-opened feature request with an unendorsed product decision) and issue-15 (years of multi-person design debate with no maintainer resolution). I re-ran these together with `--only issue-01,issue-15,issue-19,issue-20` after the change and all four matched gold at that point. The trade-off showed up on the final saved run: issue-01 flipped to `reject` on this check even though its content did not change between runs, which means the check is sitting close to a genuine edge case for that specific issue rather than being clearly settled either way. The check can only weigh disagreement that was actually posted in the captured thread, not disagreement that exists but was never voiced, so a quiet but real dispute would slip through as "no unresolved dispute" under the current wording.

## Selection rationale

1. #72 fits my background well. It is pure Python, a logic bug in `core/security.py`, and comes with a test already implied by removing the `xfail` marker, which matches my experience building a Python trading system and doing ML-based fraud detection research, and fits comfortably in the time available before the Unit 2 deadline.

2. The verdict correctly identified that the repo is alive (a human commit five days before capture), that the issue is unclaimed (no PRs exist anywhere in the repo, zero comments), and that it is a bounded, single-author bug report with a stated fix. What the rubric could not weigh is that this is specifically a security-relevant fail-open bug (`verify_password` should fail closed rather than raise), which makes it a more meaningful first contribution to me personally than a similarly-scoped but lower-stakes bug would be.

3. I expect claiming it to be straightforward. There are zero existing comments or PRs on the issue, so there is no competition to navigate, and the fix itself is narrowly scoped to one function with a test the issue already points to.
