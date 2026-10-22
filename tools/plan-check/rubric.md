# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis fits the repro | The plan's stated cause, read against the repro evidence quoted in the plan (commands, output, error text) | The stated cause is consistent with what the quoted output shows and explains the observed behavior. Fail if it contradicts the evidence, ignores it, or no repro evidence is quoted. | required |
| Fixes cause, not symptom | The plan's approach, read against its own diagnosis | The change acts where the diagnosed wrong behavior originates. Fail if it only hides the symptom (swallowing the error, special-casing the repro input) while the diagnosed cause stays in place. | required |
| One bounded change | The plan's scope statement and its list of files to touch | States what will and will not change, and the listed files are what one fix to the diagnosed problem needs. Fail if it includes unrelated cleanup, refactors, or open-ended work. | required |
| A stranger could start | The approach and the files list | A contributor new to the repo could name the first file to open and the first edit to make. Fail if the steps are too vague to begin (for example "fix the logic"). | required |
| Test plan proves something | The test plan, read against the repro steps quoted in the plan | Re-runs the repro steps and states a specific observable result after the fix that would differ if the fix failed. Fail if it only says "run tests" or "verify it works". | required |
| Unknowns stay unknowns | The plan's diagnosis and approach, read against the repro evidence quoted in the plan | Fail if the plan states as settled fact something the quoted repro evidence contradicts, or presents as verified a claim that the plan's own text says is still untraced or unchecked. A code-level cause the plan names from reading the code, consistent with the repro and not contradicted by it, passes even though the repro cannot confirm it. A plan that lists no risks passes if it makes no claim beyond what the evidence supports. | required |
| Comment fits thread and repo | The plan comment, read against the thread highlights and the repo-facts block (including its contribution and AI-use policy) | Fail if the comment ignores an explicit maintainer request or direction in the thread, duplicates or contradicts what the thread already says, or says something different from the plan. Fail if the repo-facts block states a policy requiring disclosure of AI use in issue comments or thread contributions (the kind of text this comment is) and the comment contains no disclosure statement; do not read silence as evidence that no AI was used, because the policy applies to the comment as posted. Pass on the disclosure point if the repo states no such requirement, or if its disclosure requirement covers only pull requests or code and not comments. | required |
| Comment stands alone | The plan comment | A reader who has not seen plan.md can tell what will change and why. Fail on pointers like "same approach as above". | preferred |

## Verdict rule

Accept (ready) only if every required check passes. Reject (hold) if any required check fails. A grade of unclear on a required check counts as a fail. Preferred checks never change the verdict; an unclear on a preferred check is ignored.
