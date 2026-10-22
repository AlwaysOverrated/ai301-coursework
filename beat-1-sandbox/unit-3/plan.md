# Plan for #72: make verify_password return False on a malformed stored hash

## Diagnosis

Repro environment: Ubuntu 24.04.4 LTS (WSL2), Python 3.12.3, passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1. Code: main at commit 2f4e82f52efbcfcc57d65b3fa5348672163ca088, no local changes.

What I ran and saw (the first three are from my repro report on #72, the last two I added since):

- `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings` gives `1 failed` with:

      core/security.py:37: in verify_password
          return bool(pwd_context.verify(plain_password, hashed_password))
      .venv/lib/python3.12/site-packages/passlib/context.py:1132: in identify_record
          raise exc.UnknownHashError("hash could not be identified")
      E passlib.exc.UnknownHashError: hash could not be identified

- Control: `pytest tests/unit/test_security.py::TestSecurity::test_verify_password_incorrect -v --disable-warnings` gives `1 passed`. A wrong password against a valid bcrypt hash returns False.
- Direct call outside pytest: `verify_password('mypassword', 'not-a-real-hash')` raised the same `UnknownHashError` from the same line, `core/security.py:37`.
- Direct call: `verify_password('password', '')` raised `passlib.exc.UnknownHashError: hash could not be identified` from `core/security.py:37`.
- Direct call: `verify_password('password', '$2b$12$abc')` raised `ValueError: salt too small (bcrypt requires exactly 22 chars)` from `core/security.py:37`, deeper in passlib's bcrypt `_norm_salt`.

Cause: the whole body of `verify_password` is `return bool(pwd_context.verify(plain_password, hashed_password))`. Nothing wraps that call. A stored hash passlib cannot identify raises `UnknownHashError`. A truncated bcrypt-looking hash gets past identification and fails later with `ValueError`. Two exception types, one unwrapped call. The control shows normal verification is fine, so the break is only the malformed-hash case.

What my repro does not show: that any caller treats these exceptions as a pass. It shows exceptions escaping where the docstring promises `False`.

## Scope

In scope:

- `core/security.py`: `verify_password` catches `UnknownHashError` and `ValueError` and returns False.
- `tests/unit/test_security.py`: delete the strict xfail marker on `test_verify_with_wrong_hash_format`, and add one test for the two extra malformed shapes I reproduced. The issue asks for the marker removal, and so does CONTRIBUTING (a strict xfail makes CI fail with XPASS once the test passes).

Out of scope:

- Callers of `verify_password`. I have not read them.
- Logging the failure.
- `hash_password` and the JWT functions.
- Any exception type other than `UnknownHashError` and `ValueError`.

## Files I'll touch

- `core/security.py`
- `tests/unit/test_security.py`

## Approach

1. In `core/security.py`, add `from passlib.exc import UnknownHashError` next to the existing passlib import.
2. Wrap the line 37 call: `try: return bool(pwd_context.verify(...))`, then `except (UnknownHashError, ValueError): return False`. The same file already uses a narrow except in `decode_access_token` (`except JWTError: return None`).
3. Update the `Returns:` line of the docstring (Google style, per CONTRIBUTING) to say False also covers a stored hash that is malformed.
4. In `tests/unit/test_security.py`, delete the `@pytest.mark.xfail(strict=True, reason="issue #72 ...")` decorator above `test_verify_with_wrong_hash_format`. Add one test that calls `verify_password("password", stored)` for `stored` in `""` and `"$2b$12$abc"` and asserts the result is False.
5. Run `make check` and `make test-unit`.

Branch: `fix/72-verify-password-unrecognized-hash`. Commit message: `fix(api): return False from verify_password on malformed stored hash`, with `Fixes #72` in the footer.

## Test plan

Before (recorded in my repro and the two direct calls above):

- As shipped, the covering test reports `1 xfailed`.
- With `--runxfail`: `1 failed`, `UnknownHashError` at `core/security.py:37`.
- Control `test_verify_password_incorrect`: `1 passed`.
- Direct calls: `'not-a-real-hash'` and `''` raise `UnknownHashError`, `'$2b$12$abc'` raises `ValueError`.

After the fix, same commands, expecting:

- `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings` gives `1 passed` (marker removed, so no xfail).
- The new test passes for `''` and `'$2b$12$abc'`.
- The direct calls with `'not-a-real-hash'`, `''`, and `'$2b$12$abc'` each return False.
- The control still gives `1 passed`.
- `make check` and `make test-unit` pass.

What would show the fix failed: any of those calls still raising, the covering test still failing with `UnknownHashError`, or `XPASS(strict)` because the marker is still there.

I have not yet run the whole `tests/unit/test_security.py` file on main. I will record that count first so the after count has something to compare to.

## Risks and unknowns

- I tested three malformed inputs: `'not-a-real-hash'`, `''`, and `'$2b$12$abc'`. Other malformed values may raise other exception types. I have not tried them.
- Catching `ValueError` is broader than the exception the issue names. It could also hide a `ValueError` from a cause I have not seen. I chose it because the truncated-hash input raises it from the same line. If reviewers want only `UnknownHashError`, I will drop the `ValueError` catch and the extra test.
- I have not read the callers (for example the auth route), so I do not know whether any of them depends on the exception. I will not claim anything about login behavior.
- `make typecheck` might complain about the passlib import. I won't know until I run it.
- Several others have claimed #72 and some PRs are linked. I have not reviewed them. This plan comes from my own repro.

## Deviations

The build followed the approach as written, steps 1 to 5. Two things came up that the plan did not predict.

`make typecheck` failed. The error is in numpy's own stubs (`numpy/__init__.pyi:737: Type statement is only supported in Python 3.12 and greater`), and mypy stopped before it checked any of my code. I stashed my change and ran it on untouched main. It fails the same way. The repo's mypy config sets `python_version = "3.11"` in `pyproject.toml` and my venv runs Python 3.12. I did not change either. I ran `mypy --python-version 3.12 core/security.py` instead and it passed: no issues found. So the typing risk in my plan did not apply to this change.

I did not record the whole-suite count on main before the change, only the count for `tests/unit/test_security.py` (24 passed, 1 xfailed). After the change that file is 27 passed, 0 xfailed. The full unit suite after the change is 378 passed, 52 xfailed. The 52 xfails are other seeded bugs and I left them alone.

Scope did not change. I did not touch callers or logging. The comment I posted on #72 still matches what I built, so I did not post a correction.
