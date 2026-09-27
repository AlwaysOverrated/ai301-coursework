# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the repro report's Environment section, plus the issue's own stated version, OS, and install method near the top of the Issue section. In live mode, the repro comment's environment statement, checked against the version/OS the issue reporter gave.

What good looks like: the version, OS, and install method either match what the issue names, or the report says plainly that they differ and explains why that's still a valid test. A report that quietly uses a different version without saying so is not good.

## Steps

Where it lives: the repro report's Preparation and Execution sections, plus the exact input file and command shown in the Issue section.

What good looks like: the steps put the system in the same triggering condition as the issue, even if the exact command differs, as long as the difference is explained and does not change what's being tested.

## Behavior shown

Where it lives: the actual output, error message, or panic text shown in the report's Execution and Actual sections, next to the exact error or behavior described in the Issue section.

What good looks like: the output shown is the same failure as the one in the issue, not just "also broken."

## Honesty

Where it lives: the report's Analysis and Actual sections, checked against what its own Execution section actually produced.

What good looks like: the conclusion says only what the shown output supports, including an honest "could not reproduce" when that's what happened.

## Comms

Where it lives: the claim comment, checked against the repo's bug report template and any AI-use or contribution policy in the repo facts.

What good looks like: the comment claims only what the attached report actually backs up, and discloses AI assistance if the repo's policy requires it.
