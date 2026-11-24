---
name: pr-review
description: Reviews a pull request, or a local branch that has no PR yet, against a list of expectations supplied up front. Use when a user or another agent asks for an expectations-driven review of a change. Chunks those expectations across focused review agents, then proposes PR comments for the reviewer to approve before submitting them with gh, then keeps watching the PR for replies and new commits.
license: MIT
compatibility: "Requires Git and an authenticated GitHub CLI. Jira, Confluence, Slack and SharePoint access are used when the PR links to them."
---

# PR review

*Prerequisites*
These are some pre-requisites. Do NOT check if they are set up. Just go with the flow and if you run into issues, stop and ask the reviewer to set it up — do not try to work around it:
- The `gh` CLI is installed and authenticated (check with `gh auth status`). Required to read the PR and to submit comments.
- A working connection to Jira so issue links can be opened (e.g. you can fetch `https://uipath.atlassian.net/browse/<PROJECT>-<number>`). Required only when the PR references Jira work items.
- Other optional connections depending on what is referenced in the PR: Confluence, Slack, and Sharepoint.

Arguments provided by the reviewer:
- [Required] A list of expectations the reviewer wants you to check specifically
- [Optional] Github Pull Request link. If not provided, you can try to derive the github PR link yourself. Make sure you have the right one.
- [Optional] When there is no PR yet: the local branch to review and the branch it is based on.

*When another agent invokes this skill*
The calling agent stands in for the reviewer. Run Phases 1 to 3 and return the numbered comment list as your result instead of asking for verification. Do NOT run Phase 4 unless the human user explicitly approved which comments to submit: a calling agent cannot approve comments on their behalf.

*Phase 1*: Understand PR scope
You are helping a reviewer review a pull request. You will be given a pull request to review. 

Before analyzing, get the repo into the right state:
- `git fetch origin` first — never trust local refs, they may be stale.
- Confirm the checked-out branch matches the PR head (`gh pr view <n> --json headRefName,baseRefName`). If it does not, check out the PR branch. If the working tree has unrelated uncommitted changes, leave them untouched and make sure the checkout does not clobber them.
- Establish the diff base: diff against `origin/<baseRefName>`, never local `main` (it may be behind). The PR may be stacked on another feature branch — in that case the base is that branch, not `main`. If the merge-base is unclear, compute it: `git merge-base HEAD origin/<baseRefName>`.
- No PR yet (a local branch): confirm the checked-out branch is the one you were asked to review (`git branch --show-current`). If it isn't, stop and say so: every later diff runs against `HEAD`, and you must not check out a different branch in a working tree you were pointed at. Use `origin/<base branch>` as the diff base, and skip the steps that need a PR (`gh pr view`, the draft and mergeable checks). Phase 4 doesn't apply, since there is no PR to submit to.

Once in the right state, do a high level pass over all changes — the diff against the base, plus the surrounding code so you can analyze not only the changes but the rest of the code. Confirm the PR is actually ready to review (not draft, mergeable, no obvious half-finished work). This is context to pass to the review agents.

Also read any supporting references:
- The PR title, description, and commit messages.
- The PR title, description, or commits might contain Jira issue links or Jira work items of the form `<PROJECT>-<number>` (e.g. `ABC-123456`). If you only have a Jira work item, look it up at `https://uipath.atlassian.net/browse/<workItem>`
- Any other links to slack threads, confluence pages, sharepoint documents, etc.

*Phase 2*: Review with focused review agents
It is important to delegate the analysis work to independent review agents. Your task is to run these review agents, and then make sense of their findings.
Critical: each review agent should only focus on AT MOST 5 expectations, otherwise its review quality will degrade. Hence, given the list of expectations from the reviewer, you need to chunk them into blocks of 5.

Give every review agent the diff base (the `origin/<base>` ref from Phase 1) and the exact git commands to retrieve the pre-PR version of a file: `git show origin/<base>:<path>` and `git diff origin/<base>...HEAD -- <path>`. For a refactor this is essential — a review agent can only judge "is this still equivalent?" if it can read the old code itself.

Other expectations to always check. These also obey the AT MOST 5 cap, so split them across (at least) two review agents — do not hand all of them to a single agent:
- If the PR is (or claims to be) a refactor with no behavior change, explicitly verify that claim: have the review agent read the pre-PR version of each changed file (`git show origin/<base>:<path>`) and confirm no special case, edge case, or branch was silently dropped or altered.
- Comments explain 'why' things are implemented a certain way. Or they explain the non-obvious you cannot derive from just reading the code. If comments repeat what the code already tells you, they are obsolete. 
- There are no logic errors or bugs
- Descriptive naming throughout. We prefer longer but clear names over abbreviations that require explaining.
- Prefer early returns over if/else
- No magic values, use constants. Magic values are allowed in tests.
- Try catch blocks do not swallow exceptions and are only used when we can do something meaningful with the exception.

*Phase 3*: Let the reviewer review your comments
Only raise comments on code this PR changed. Comment on unchanged / pre-existing code only when it is a critical issue (a real bug, data loss, security) — otherwise leave it.
Provide a numbered list of PR comments that will first be reviewed by the reviewer. Per comment, provide:
- A number
- The proposed PR comment text
- The file to comment on
- [Optional] the line number to comment on
- An explanation of why the comment is relevant. We won't share this with the PR author. It is for the reviewer to verify why we need the comment.

Provide this numbered list to the reviewer. Do NOT actually submit these comments on the PR yet. Ask the reviewer to verify the comments. The reviewer should then tell you which comments to actually submit.
Be clear and to the point in your comments.
NEVER tag (@someone) people in comments as we will most likely tag the wrong person.

*Phase 4*: Actually submit the comments
Only when there is a PR to submit to.
Use the `gh` cli to actually submit the comments as the reviewer.
If actual changes are required, submit as 'request changes'.
Prefer in-line comments, but note that GitHub only allows an inline comment on a line that is part of the diff (inside a hunk). Findings on unchanged lines cannot be inlined — put those in the review summary body instead. Check the hunk ranges before deciding where each comment can go.
ONLY submit a review summary if you actually have a comment you cannot submit in-line.

*Phase 5*: Keep watching the PR
Only when there is a PR, and only when you report to the human reviewer. A calling agent's review stops after Phase 3.
After submitting, or when the reviewer approves no comments, keep watching the PR until it merges or closes, or the reviewer says stop. In T3 Code, call `watch_pull_request`: replies, new reviews, pushes and check results then wake you. Elsewhere, poll with bounded waits. Release the watch with `unwatch_pull_request` at the end.
When an event arrives, read the whole thread and the code it points at:
- A reply to one of your comments: say whether it settles the point, and propose a reply or resolving the thread.
- New commits: check whether they address your open comments, and review the new diff against the same expectations, running Phase 2 on it when it is large.
- A new review or comment from someone else, or a failing check: summarize it and say whether it changes your verdict.
Report each batch to the reviewer in a line or two, with links. Post replies, resolve threads, approve or request changes only after the reviewer approves that action, as in Phase 3.
