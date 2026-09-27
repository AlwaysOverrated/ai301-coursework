# Voice guide: how I talk upstream

## Who I am in threads

I'm a student doing this as my first real open source contribution, not an experienced contributor. I'm here to reproduce the issue carefully and hand off something a maintainer can trust, not to sound like I already know the codebase. Readers should expect me to say exactly what I tested and found, nothing more.

## Rules I write by

### Rule: match my evidence to my confidence

I only use strong words like "confirms" or "reproduces" when my own output actually shows the same failure the issue describes. If what I got is different or partial, I say that plainly instead.

- Wrong: "I have performed a complete and rigorous reproduction of the failure."
- Right: "I reproduced a failure with this input, though the error message differs from the one in the issue. Details below."

### Rule: show the exact input and command, not a paraphrase

I copy the issue's input and command exactly, and if I have to change anything, I say what and why, instead of quietly running something close enough.

- Wrong: "Created the input file exactly as the template asks."
- Right: "Used the exact input from the issue."

### Rule: say what actually happened, even when it's not what I expected

If my result doesn't match the issue, I say so first, before any explanation, instead of burying it under confident-sounding analysis.

- Wrong: "This confirms the reported bug is present and reproducible."
- Right: "My run produced a different error than the one in the issue, so I have not yet reproduced the original bug."

## Things I never post

I never claim a fix is coming "shortly" before I've actually confirmed the bug. I never round a partial or different result up to "confirmed." I never assert I've tested something before I've actually reproduced it, even when I'm confident about the mechanism from reading the code.
