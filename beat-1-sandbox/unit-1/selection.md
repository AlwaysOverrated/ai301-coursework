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

issue-01 (conda/conda#16475) has a gold label of accept. My first full run rejected it. It failed the "Maintainer responsiveness" check because the sampled issue threads mostly showed no maintainer reply within 30 days, one at 32.9 days and the rest with no reply at all. That check was looking at the wrong signal here. The repo's last 5 commits on the default branch were all made by a human within a day of the capture date, which says more about whether the repo is alive than how fast someone replies to an issue thread, especially on a repo where the maintainer merges instead of commenting. I changed the check ("Repo shows human activity") so it passes on either signal, a recent human commit or a maintainer reply, instead of requiring the reply specifically. After that change issue-01 graded accept, matching gold. On the final saved run it disagreed again, this time on a different check ("Issue has a settled, bounded spec"), which I get into under Trade-offs.

### Check rationale

Check quoted from rubric.md ("Issue has a settled, bounded spec," pass condition):

"Passes even when the issue lists multiple named causes or candidate approaches, as long as those come from a single author (e.g. a maintainer's own bug diagnosis) with no unresolved dispute -- offering options is normal scoping, not an unsettled debate."

An earlier version of this check counted any issue naming more than one possible cause or fix as unbounded. That wrongly rejected issue-19, where a maintainer laid out two causes and three fixes themselves with no comments and no disagreement from anyone. The current wording separates one person scoping out a problem from an actual unresolved argument between people, which is the real distinction the scope category is testing, shown by issue-15's years-long design debate and issue-20's request with no maintainer decision behind it.

### Trade-offs

With the current wording, issue-19 still passes (one author, several named options, no disagreement), while issue-20 (a bot-filed feature request nobody with authority has weighed in on) and issue-15 (years of back and forth with no resolution) still correctly fail. I reran all four together with --only issue-01,issue-15,issue-19,issue-20 and they matched gold at that point. The trade-off showed up later: on the final saved run issue-01 failed this same check even though nothing about the issue had changed, which tells me the check is close to a real edge case for that one issue rather than fully settled. The check can only see disagreement that shows up in the comment thread. If people disagreed somewhere it didn't capture, the check would read that as settled when it isn't.

## Selection rationale

1. Issue #72 fits what I'm good at. It's Python, a real logic bug in core/security.py, and comes with a test already pointed to by removing the xfail marker. That lines up with my background building a trading system and doing fraud detection research, and it's a reasonable size given how much time I have before the Unit 2 deadline.

2. The verdict picked up on the repo being active (a human commit five days before the check), the issue being open with no PRs or claims on it, and the fix being scoped to one function by a single author. What it couldn't weigh is that this bug is a security issue, verify_password should fail closed instead of raising an error, which makes it feel like a more worthwhile first contribution than a similar bug without that stake.

3. I don't expect claiming it to be hard. Nobody has commented on the issue or opened a PR for it, so there's no one to coordinate with, and the fix is narrow enough that the scope is already basically decided for me.
