# Unit 2 — Claim and Reproduce

GitHub username: AlwaysOverrated

## Claim comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5859343873

Text posted:

```
Hi! I'd like to take this issue as my first contribution. From reading the code, verify_password in core/security.py currently raises an exception (UnknownHashError) instead of returning False when it hits an unrecognized hash, which means a caller could end up treating a failed verification as a pass if that exception isn't handled somewhere upstream. I'm going to set up the project, reproduce the failure, and follow up with a repro report shortly.
```

Reflection: I claimed the issue before reproducing anything, stating only what I understood from reading the code and promising the reproduction to follow, per the voice guide's rule about matching evidence to confidence.

## Repro comment

Link: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5859633759

Text posted:

```
Reproduction report for #72. Result: reproduced on current main.

Environment

- Linux (WSL2), Ubuntu 24.04.4 LTS
- Python 3.12.3
- passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1, installed via pip install -e ".[dev]"
- Code: commit 2f4e82f52efbcfcc57d65b3fa5348672163ca088, the current main of codepath/pathreview-ai301-fa26-s3, checked out with no local changes
- This bug only needs the Python backend; no Docker services were needed to reproduce it

Steps

git clone https://github.com/AlwaysOverrated/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# 1. As shipped, the strict xfail hides the error
pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings

# 2. The same test with the xfail marker ignored
pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings

Output of step 2:

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]

================================= FAILURES =================================
_____________ TestSecurity.test_verify_with_wrong_hash_format ______________
tests/unit/test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
core/security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv/lib/python3.12/site-packages/passlib/context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv/lib/python3.12/site-packages/passlib/context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.venv/lib/python3.12/site-packages/passlib/context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
========================= short test summary info ==========================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format - passlib.exc.UnknownHashError: hash could not be identified
====================== 1 failed, 2 warnings in 2.05s =======================

As a control, I ran the neighboring test, which checks that a wrong password against a valid bcrypt hash returns False rather than raising:

pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -v --disable-warnings

1 passed, 2 warnings in 1.32s

I also called verify_password directly, outside pytest, with a malformed string of my own choosing, to confirm the same exception surfaces from a plain function call and not just inside the test harness:

python3 -c "
from core.security import verify_password
result = verify_password('mypassword', 'not-a-real-hash')
print('result:', result)
"

This raised the same passlib.exc.UnknownHashError: hash could not be identified, from the same line, core/security.py:37, confirming the failure is in verify_password itself rather than specific to the test's exact string.

Expected: verify_password() returns False when the stored value is not a usable bcrypt hash, the same answer it gives for a wrong password.

Actual: passlib.exc.UnknownHashError escapes from core/security.py:37 for an unrecognizable stored hash. The normal wrong-password case (control test above) still correctly returns False, so the failure is specific to an unrecognizable stored hash rather than to verification in general.

Next I'll change verify_password() to catch this case and return False, remove the strict xfail marker on the covering test, and open a PR.

I used Claude to help set up the project and draft this report. The commands and output above are from my own environment.
```

Reflection: this matched the issue's own covering test exactly (same test path, same commit, same escaping exception at the same line) and included a control test to isolate the failure to the malformed-hash case specifically, rather than to verification in general.

## Eval iterations

### Run history

- Run 1 (smoke test, `--limit 3`): 2/3 agreement (pkg-01 disagreed: gold accept, graded reject)
- Run 2 (full run, 20 packages, after loosening "Steps trigger the same bug" to allow explained deviations): 17/20 agreement, below the bar
- Run 3 (full run, 20 packages, after tightening the disclosure check and adding a followable-by-a-stranger requirement): 16/20 agreement, below the bar, with two new false-positive fails on the disclosure check (pkg-03, pkg-12) and a drop in unfollowable-comms (1/3)
- Run 4 (`--only pkg-03,pkg-12,pkg-18,pkg-19,pkg-20`, after narrowing disclosure to only fire on policies that specifically ask for disclosure, and adding a followability requirement to the steps check): 5/5 agreement
- Run 5 (final full run, saved to `eval-run.txt`): 19/20 agreement, bar met, but with the disclosure category unmet (0/1) due to pkg-20 flipping back to a false accept

### Package analysis

pkg-20 (a repo with an `AI_POLICY.md` stating "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance"): gold label is `reject`, since the candidate's comment says nothing about AI at all under that policy. My rubric graded it `accept` on the final saved run. Earlier, my disclosure check correctly failed this package once I added wording that treated silence about AI use as never sufficient evidence that no AI was used. But when I later rewrote the same check to stop it from misfiring on two other packages (pkg-03 and pkg-12, whose policies only restricted AI-generated content without asking for disclosure), the "treat silence as failing" instruction did not survive the rewrite as clearly as I intended, and the model went back to reading the comment's silence about AI as evidence that no disclosure was owed. The two goals, not failing packages with a real disclosure requirement, and not failing packages whose policy is about something else, are both real constraints on the same check, and I did not manage to satisfy both together within the runs I had budget for.

### Check rationale

Quoted check, as currently written in `rubric.md`:

"Fails only when the repo's policy specifically and explicitly requires disclosing that AI was used (naming the tool or extent of assistance), AND the comment contains no such disclosure statement. When that specific kind of policy is present, the comment's silence about AI use is never read as evidence that no AI was used; grade this fail by default unless the comment contains an explicit disclosure line, regardless of how the comment otherwise reads. A policy that only restricts AI-generated content in other ways (e.g. "must be written by humans," "low-quality AI content is closed," "AI comments may be hidden") without asking the contributor to state that AI was used is not a disclosure requirement, and this check passes in that case regardless of whether the comment mentions AI. Passes if the repo has no policy, has a policy that does not ask for disclosure, or asks for disclosure and the comment provides it"

Reasoning: I wrote this distinction after finding that an earlier, simpler version of the check ("fails whenever the repo states an AI disclosure requirement and the comment doesn't disclose") was catching real disclosure-wall packages correctly but also failing two packages (pkg-03, pkg-12) whose policies were about restricting AI-generated content quality, not about requiring disclosure. The rewrite tries to hold both of those apart by name, but the final run shows it did not fully succeed at keeping the "silence is never evidence of no use" instruction as forceful as it needs to be for a strict disclosure policy like pkg-20's.

### Trade-offs

This check's current wording avoids the false-positive failures on pkg-03 and pkg-12 that an earlier, blunter version produced, and it still correctly passes packages with no AI policy at all. The trade-off is the one named above: on at least one confirmed run, it let a package with a genuine, explicit disclosure requirement through as `accept` when the comment never mentioned AI. I re-ran the narrower wording against pkg-20 in isolation (`--only pkg-03,pkg-12,pkg-18,pkg-19,pkg-20`) and it passed there, so the check can produce the right answer on this exact package; the miss on the final full run means the result is not yet stable, and I would want to re-run pkg-20 as a canary again before trusting a future full run's disclosure category.

## Selection rationale

1. I continued the same issue from Unit 1 (#72, the `verify_password` fail-open bug), since I already understood the code well from that unit's write-up, and the fix is scoped to a single function, which fit the time available this week.
2. The skill's live-mode verdict correctly identified that my claim comment only promised future work and that my repro report's conclusion matched what its own commands actually showed. What I weighed that the tool couldn't was how much value there was in matching my classmates' independent reports almost exactly (same test path, same commit, same escaping exception) — that convergence across several separate reproductions is stronger evidence of a real bug than any one report alone, even though the skill only grades my report on its own.
3. Several classmates have already claimed and reproduced this same issue, so per the house rules a shared issue costs nobody anything; I expect claiming here to be routine, since credit attaches to my own posted work rather than to being first, and my report stands on its own regardless of the others already posted.
