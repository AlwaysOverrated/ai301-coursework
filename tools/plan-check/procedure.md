# Procedure: how this skill grades a plan package

## Read order

1. Read the repro evidence first (the commands, output, and error text quoted in the package). Note the exact behavior it pins down: what was run, what was observed, and what was expected instead. Every later check is judged against this, so read it before forming any opinion of the plan.
2. Read the plan's diagnosis next. Note the stated cause in one sentence and whether it cites the repro evidence.
3. Read the plan's scope statement and files list. Note what will change, what will not, and every file named.
4. Read the approach, then the test plan. Note the first concrete edit and the specific result the test plan expects after the fix.
5. Read the Candidate plan again for stated assumptions and open questions. Note which claims are labeled as assumptions and which are stated as fact.
6. Read the thread highlights and the repo-facts block. Note any maintainer request, claimed work, or stated convention for proposing changes.
7. Read the plan comment last, so it is judged against everything above.

## Evidence gathering

1. For "Diagnosis fits the repro": record the stated cause and the quoted repro output side by side.
2. For "Fixes cause, not symptom": record where the approach makes its change, and where the diagnosis says the wrong behavior originates.
3. For "One bounded change": record the scope statement's in/out lists and the files list, and mark anything that is not needed to fix the diagnosed problem.
4. For "A stranger could start": record the first file to open and the first edit to make, as written in the approach. If neither can be named from the text, record "none".
5. For "Test plan proves something": record the repro steps the test plan re-runs and the specific observable result it expects after the fix.
6. For "Unknowns stay unknowns": list each claim in the diagnosis or approach that the quoted evidence does not establish, and record whether the plan labels it as an assumption or states it as fact.
7. For "Comment fits thread and repo": record each maintainer request or repo convention from the thread highlights and repo-facts block, and whether the comment honors it. Also record whether the comment matches the plan.
8. For "Comment stands alone": record whether the comment states what will change and why without pointing elsewhere.
9. Use only text that is in the package. Do not assume or invent evidence.

## Check execution

1. Run the checks in the order they appear in the rubric table.
2. Grade each check as pass, fail, or unclear, using only the facts recorded for it in Evidence gathering and the pass condition in the rubric.
3. If the evidence a check needs is genuinely absent from the package (for example, no repro output is quoted), grade it fail when the pass condition says missing evidence fails; otherwise grade it unclear. Do not fill the gap with a guess.
4. If a check can be decided from its recorded facts alone, grade it without re-reading the whole package. Re-read only the specific part when the recorded facts are ambiguous.
5. Judge the outcome the pass condition describes, never the write-up's length, headings, or formatting.
6. For each check, write one sentence of reasoning that quotes the package text it relied on.

## Verdict assembly

1. List all check grades with their weights.
2. Apply the rubric's verdict rule exactly: accept only if every required check passes; reject if any required check fails; unclear on a required check counts as fail; preferred checks never change the verdict.
3. If the verdict is reject, identify the required check that decided it. If several failed, use the first one in table order.
4. In the output, quote the package text that decided that check, using the check's name as written in the rubric.
5. Output the verdict as accept (ready) or reject (hold), with the per-check grades and reasoning, ending with the JSON block the skill specifies.
