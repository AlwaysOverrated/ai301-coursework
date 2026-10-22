# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

AlwaysOverrated

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-6342427111

Text posted:

~~~text
Plan for #72, built from my own reproduction above (Ubuntu 24.04 on WSL2, Python 3.12.3, passlib 1.7.4, main at 2f4e82f).

Cause: `verify_password()` in `core/security.py` is one line: `return bool(pwd_context.verify(plain_password, hashed_password))`. Nothing wraps the call. An unrecognizable stored hash (`'not-a-real-hash'`, `''`) raises `UnknownHashError` from `identify_record`. A truncated hash like `'$2b$12$abc'` gets past identification and raises `ValueError: salt too small`. Both escape at line 37. My control (`test_verify_password_incorrect`, valid hash, wrong password) returns False, so the break is only in the malformed-hash case.

Change: catch `UnknownHashError` and `ValueError` in `verify_password()` and return False, update the docstring, delete the strict xfail marker on `test_verify_with_wrong_hash_format`, and add one test for `''` and `'$2b$12$abc'`. Files: `core/security.py` and `tests/unit/test_security.py`. Out of scope: callers, logging, other functions, and any other exception type. If you want this kept to `UnknownHashError` only, say so and I'll drop the `ValueError` catch and the extra test.

Test: same commands as my repro. Today, `--runxfail` gives 1 failed with `UnknownHashError`. After the fix, the test passes without the marker, the new test passes, all three malformed inputs return False when I call `verify_password` directly, and the control still passes. I will also run `make check` and `make test-unit`.

Open: I tried three malformed inputs. Others may raise something else. Catching `ValueError` is broader than the exception named in the issue, and I picked it only because the truncated hash raises it at the same line. I also have not read the callers, so I won't claim anything about login behavior.

I know several others have claimed this and some PRs are linked. I haven't reviewed them. This plan comes from my own repro.

I used Claude to help draft this comment. The repro commands and output ran on my machine.
~~~

---

## Your branch

**Branch**

`fix/72-verify-password-unrecognized-hash`

**Evidence**

My Unit 2 repro steps, re-run on the built change. Commit `6f50aee`, same base commit as the repro (`2f4e82f`).

Before (`before-72.txt`, run on `main` before any edit):

~~~text
$ pytest tests/unit/test_security.py -q --disable-warnings
.....................x...                                                [100%]
24 passed, 1 xfailed, 2 warnings in 5.00s

$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings
============================= test session starts ==============================
platform linux -- Python 3.12.3, pytest-9.1.1, pluggy-1.6.0 -- /home/alwaysoverrated/pathreview-ai301-fa26-s3/.venv/bin/python3
cachedir: .pytest_cache
hypothesis profile 'default'
benchmark: 5.3.0 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/alwaysoverrated/pathreview-ai301-fa26-s3
configfile: pyproject.toml
plugins: cov-7.1.0, hypothesis-6.168.2, platformdirs-4.12.0, anyio-4.15.1, benchmark-5.3.0, asyncio-1.4.0, pytest_httpserver-1.1.5
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 1 item

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]

=================================== FAILURES ===================================
_______________ TestSecurity.test_verify_with_wrong_hash_format ________________
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
=========================== short test summary info ============================
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================== 1 failed, 2 warnings in 0.26s =========================

$ pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -v --disable-warnings
============================= test session starts ==============================
platform linux -- Python 3.12.3, pytest-9.1.1, pluggy-1.6.0 -- /home/alwaysoverrated/pathreview-ai301-fa26-s3/.venv/bin/python3
cachedir: .pytest_cache
hypothesis profile 'default'
benchmark: 5.3.0 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/alwaysoverrated/pathreview-ai301-fa26-s3
configfile: pyproject.toml
plugins: cov-7.1.0, hypothesis-6.168.2, platformdirs-4.12.0, anyio-4.15.1, benchmark-5.3.0, asyncio-1.4.0, pytest_httpserver-1.1.5
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 1 item

tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect PASSED [100%]

======================== 1 passed, 2 warnings in 0.59s =========================

$ direct calls to verify_password
'not-a-real-hash' -> raised UnknownHashError: hash could not be identified
'' -> raised UnknownHashError: hash could not be identified
'$2b$12$abc' -> raised ValueError: salt too small (bcrypt requires exactly 22 chars)
~~~

After (`after-72.txt`, run on the branch):

~~~text
$ pytest tests/unit/test_security.py -q --disable-warnings
...........................                                              [100%]
27 passed, 2 warnings in 5.08s

$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings
============================= test session starts ==============================
platform linux -- Python 3.12.3, pytest-9.1.1, pluggy-1.6.0 -- /home/alwaysoverrated/pathreview-ai301-fa26-s3/.venv/bin/python3
cachedir: .pytest_cache
hypothesis profile 'default'
benchmark: 5.3.0 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/alwaysoverrated/pathreview-ai301-fa26-s3
configfile: pyproject.toml
plugins: cov-7.1.0, hypothesis-6.168.2, platformdirs-4.12.0, anyio-4.15.1, benchmark-5.3.0, asyncio-1.4.0, pytest_httpserver-1.1.5
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 1 item

tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format PASSED [100%]

======================== 1 passed, 2 warnings in 0.22s =========================

$ pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -v --disable-warnings
============================= test session starts ==============================
platform linux -- Python 3.12.3, pytest-9.1.1, pluggy-1.6.0 -- /home/alwaysoverrated/pathreview-ai301-fa26-s3/.venv/bin/python3
cachedir: .pytest_cache
hypothesis profile 'default'
benchmark: 5.3.0 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/alwaysoverrated/pathreview-ai301-fa26-s3
configfile: pyproject.toml
plugins: cov-7.1.0, hypothesis-6.168.2, platformdirs-4.12.0, anyio-4.15.1, benchmark-5.3.0, asyncio-1.4.0, pytest_httpserver-1.1.5
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 1 item

tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect PASSED [100%]

======================== 1 passed, 2 warnings in 0.58s =========================

$ direct calls to verify_password
'not-a-real-hash' -> False
'' -> False
'$2b$12$abc' -> False
~~~

The whole security test file went from `24 passed, 1 xfailed` to `27 passed`. The covering test went from `FAILED` (with `--runxfail`) to `PASSED`. All three direct calls went from raising to returning `False`.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

3/3, 2/2, 17/20, 4/6, 5/5, 19/20

- 3/3: smoke run, `--limit 3` (`pkg-01`, `pkg-02`, `pkg-03`). Two attempts before it errored with `claude exited 1` because my login had expired, and graded nothing, so I do not count them.
- 2/2: `--only pkg-04,pkg-20`, the two `thread-convention` packages.
- 17/20: first full run. `pkg-05`, `pkg-09` and `pkg-14` (all `clear-accept`) graded reject.
- 4/6: after narrowing "Unknowns stay unknowns". `pkg-05` and `pkg-09` flipped to accept, `pkg-14` stayed reject, and `pkg-20` flipped to accept when gold says reject.
- 5/5: after adding the disclosure rule to "Comment fits thread and repo" (`--only pkg-20,pkg-09,pkg-05,pkg-04,pkg-17`).
- 19/20: final full run, saved in `eval-run.txt`.

**Package analysis**

`pkg-14`, category `clear-accept`. The gold label is `accept`. My rubric graded it `reject` on my final full run, so it is my one miss (19/20). It failed two required checks. The grader's notes, from `full2.json`:

- **A stranger could start**: "Files are only crate-level areas; 'exact functions to be pinned in the PR after tracing' means no first file or edit is named."
- **Unknowns stay unknowns**: "States as fact the 0.44.2 reattach-path change and the stdin-wiring cause and cache-refetch explanation, while its own Files line says the origin is still untraced."

Why my rubric read it that way. "A stranger could start" passes when "A contributor new to the repo could name the first file to open and the first edit to make." The note says the plan names only a path area and defers the functions until after tracing, so no first edit can be named. "Unknowns stay unknowns" fails a plan that "presents as verified a claim that the plan's own text says is still untraced or unchecked", and the note says this plan does that. I did not loosen either check to flip this package. I also did not run a loosened version, so I cannot say what it would change on the packages that currently match.

**Check rationale**

The check as it reads now in `tools/plan-check/rubric.md`:

> | Comment fits thread and repo | The plan comment, read against the thread highlights and the repo-facts block (including its contribution and AI-use policy) | Fail if the comment ignores an explicit maintainer request or direction in the thread, duplicates or contradicts what the thread already says, or says something different from the plan. Fail if the repo-facts block states a policy requiring disclosure of AI use in issue comments or thread contributions (the kind of text this comment is) and the comment contains no disclosure statement; do not read silence as evidence that no AI was used, because the policy applies to the comment as posted. Pass on the disclosure point if the repo states no such requirement, or if its disclosure requirement covers only pull requests or code and not comments. | required |

Why it reads that way. My first version only said the comment should follow "any convention the repo states", and said nothing about AI disclosure. On `pkg-20` that one check graded three different ways across three runs on the same input. The grader failed it ("the comment contains no AI disclosure"), then marked it `unclear`, then passed it with "no evidence in the package that AI was used, so no disclosure gap can be established." The repo's policy there says "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance", and gold is reject. So I wrote in "do not read silence as evidence that no AI was used, because the policy applies to the comment as posted."

I rejected treating unknown AI use as `unclear`, because `unclear` was one of the three answers the grader had already flipped between. Then `pkg-09` showed the first rewrite was too broad. Its repo facts say "the policy states no disclosure ask for issue comments", and its disclosure rule applies to pull requests. So I scoped the rule to "issue comments or thread contributions" and added a pass when the requirement "covers only pull requests or code and not comments".

**Trade-offs**

- Narrowing "Unknowns stay unknowns" fixed `pkg-05` and `pkg-09`. The cost is that the check no longer fails a plan for hedging too little, only for claims the evidence contradicts or the plan itself admits are untraced. I re-ran canaries `pkg-04`, `pkg-15` and `pkg-20` with `--only`. `pkg-20` (gold reject) flipped to accept. That flip was not caused by the Unknowns edit, since its comms check had already graded fail, unclear and pass across runs. It sent me to the disclosure wording above.
- The disclosure rule fails a comment in a repo that requires disclosure in comments even if the author used no AI, and passes a comment in a repo whose disclosure rule covers only pull requests.
- I accept missing `pkg-14` (see Package analysis).
- A case it misses in live use: on my own plan, the skill graded "Comment fits thread and repo" as pass and said "No maintainer has posted, and no other student has posted a competing plan." Other students had posted plans on #72. The verdict was not affected because my comment already says several others have claimed the issue, but it shows the check can pass on a misread thread, so an accept is a signal and not a guarantee.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
