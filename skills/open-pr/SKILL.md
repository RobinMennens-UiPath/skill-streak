---
name: open-pr
description: Open a draft PR, then watch it and handle new comments, reviews and failing checks as they arrive. Trigger on "open a PR" or "yeet".
disable-model-invocation: true
license: MIT
compatibility: "Requires Git, an authenticated GitHub CLI, and repository push access. The PR diagram needs GitHub CLI 2.102 or newer for image attachments, headless Chrome or another browser screenshot tool, and internet access."
---

# Open-pr

Push the current work, open a draft PR, and watch it. Handle new comments, reviews and failing checks as they arrive, until the PR merges or closes.

## Prepare the change

Inspect the branch, base, diff, and worktree. Include only work in scope and preserve unrelated changes. Use a feature branch when needed. Never force-push a shared default branch.

Run checks appropriate to the change and resolve failures it introduced. If GitHub access is missing, finish local preparation and report the specific access needed.

Use Conventional Commits with an imperative subject, `type(scope): summary`, without a trailing period. Follow configured commit signing, including when rewriting commits. Do not silently fall back to unsigned commits when signing fails.

After the subject, use Linux-style git trailers only. Add `Assisted-by: Vendor:model` for models known to have contributed; do not invent model identities. Preserve authorship and existing trailers when amending.

## Open the draft

Find the branch's existing PR before creating one. Open new PRs in draft and preserve an existing PR's status. The user decides when to mark it ready. Follow an explicit request for a different status.

Assign the PR to the requesting user's GitHub account: `gh pr create --assignee <login>`, or `gh pr edit --add-assignee <login>` when the PR exists without them. Find the login as “Watch the PR” describes.

Match the title to the main commit subject. Then write the description as described below.

## Write the description

The description is short enough to read in ten seconds. It has these parts, in this order, with no headings:

1. One or two sentences that state the problem and what the PR changes.
2. A hand-drawn diagram of the change.
3. One or two sentences under the diagram that say what it shows.
4. For a change the user can see, before and after screenshots in a two-column table.

Leave out checklists, test plans, file lists and summaries of the diff. Mention a testing limit only when it changes the reviewer's decision. If the repository has a PR template with required sections, keep its sections and fill each one this briefly.

Write the title and the body in plain words and short sentences, in the active voice. Cut filler, chatbot phrases and metaphors.

### Draw the diagram

Copy the [diagram template](assets/diagram.html) to a scratch folder outside the repository. Set `WIDTH`, `HEIGHT` and `SHAPES` for this change; the template lists the shape kinds. Draw what changed: the flow, the structure, or before and now. Keep it to about ten shapes.

Put only short labels in the drawing, never sentences, because small handwriting is hard to read on GitHub. Show text examples, such as old and new messages, in a Markdown table in the body.

Take a screenshot in a window of `WIDTH` by `HEIGHT`. With Chrome:

```bash
"<chrome>" --headless=new --hide-scrollbars --force-device-scale-factor=2 \
  --window-size=<WIDTH>,<HEIGHT> --virtual-time-budget=5000 \
  --screenshot=<scratch>/diagram.png "file://<scratch>/diagram.html"
```

Look at the image. Fix text that leaves its box and shapes that overlap, then take it again.

### Attach the images

Reference each image in the body by its local path, then upload it in the same command. GitHub CLI replaces the path with the uploaded address:

```bash
gh pr edit <number> --body-file <scratch>/body.md \
  --attach "<scratch>/diagram.png#What the diagram shows"
```

In `body.md`, write a Markdown image whose address is the local path of the file, here `<scratch>/diagram.png`. Do not commit images to the repository or to a separate branch.

When the scope of the PR changes, update the sentences, draw the diagram again and replace the image. If no screenshot tool is available, open the PR without the diagram and tell the user that it is missing.

## Report the draft

Tell the user the draft is open, with the link and current check status, then start watching it. This does not mark the PR ready on GitHub.

## Watch the PR

Watch every PR you open until it merges or closes, or the user says stop. In T3 Code, call `watch_pull_request`; new comments, reviews and check results then wake you. Elsewhere, use the host's PR subscription, or poll with bounded waits when nothing can deliver events. Report the watch as active only after confirming it. Only the thread that reports to the user watches. A builder, delegate or PR owner thread hands back instead, and its parent watches. Release the watch with `unwatch_pull_request` when the PR merges or closes, the user says stop, or you hand the work back.

Identify the requesting user's GitHub account from established context. If it is unknown, ask for it while completing the draft. Do not assume the PR author or the authenticated account is that person.

When an event arrives, read the whole thread and the code it points at, then handle it right away:

- **The user or a review bot such as Copilot.** Fix what is valid, run the affected checks and push. Reply in the thread with what changed, or why you left the code as it is, and resolve the thread.
- **Another person.** Fix what is clearly valid and within scope, push, and reply with what changed. Leave resolving to that reviewer. For a disputed point, a design change or work outside scope, give the user a verdict and a draft reply and wait for their decision.
- **A failing check.** Reproduce it and check whether the base also fails. Fix failures this change caused and push. Report the others.

After each batch, tell the user in a line or two what came in and what you did, with links to the threads. Fetch the comments that arrived while you were fixing before you report.

Track the processed comment IDs, edits and the last pushed head, so a reconnect does not repeat work. Before pushing, recheck that the PR is still open. On an access failure, keep your place and report the blocker instead of silently dropping the watch.

Marking the PR ready, approving, re-requesting review and merging need the user's word for that action.
