# Review

Review another contributor's PR with [pr-review](../../pr-review/SKILL.md). It is how the user reviews.

Read the PR, its description, linked tickets and the diff at its head with read-only git and gh. Collect the expectations the user supplied. If they gave none, propose a numbered list from the ticket and description and ask the user to confirm it before running the review.

Run `pr-review` with those expectations and the PR link. Open its result with one verdict line, “sign off”, “look at these points first” or “do not merge”, and the gist in at most three lines. Then give its numbered comment list. Post only the comments the user approves, on instruction to post. Approve the PR only on separate instruction. Then keep watching the PR as Phase 5 of `pr-review` says.

On “be extra careful”, “find bugs” or “swarm”, also run [swarm](../../swarm/SKILL.md) on this PR for the rounds requested and merge its checked findings into the list.
