---
name: fix-comments
description: "Cut obsolete and verbose comments in code, and in the docs a change touches (README, CLAUDE.md, AGENTS.md and other markdown). Use after implementing a change, before review."
license: MIT
compatibility: "Requires Git and the change under review."
---

# Fix comments

Make a pass over every comment and doc paragraph in the change and cut the ones that don't earn their place. The bar: the text explains **why** something is done this way, or states a fact a reader can't deduce from the code. A reader who knows the language, can open the code, and can see the rendered UI is the audience.

## Scope

- Diff against the base branch (`git diff origin/main...HEAD`) and read every file it touches **in full**, not only the grepped comment lines. Multi-line docstrings and JSX `{/* */}` comments slip past a line grep.
- That includes docs: `README.md`, `CLAUDE.md`, `AGENTS.md`, `DEPLOYMENT.md`, `docs/**` and any other markdown in the diff.
- Include text the change *touched* (an extended docstring or rewritten doc paragraph is now yours), not only text it added.
- Include text the change made **stale** elsewhere: a whitelist, field list, or "X is not used" note in code, and any doc that names a function, file, message key or behaviour the change renamed, removed or reversed. Grep the docs for the old identifiers.

## Delete on sight

- **Describes what a component renders or looks like.** "Severity-chipped cards grouped by category", "rendered as side-by-side pairs", "a single group renders flat". The reviewer can see the UI.
- **Restates a well-named identifier.** `isPolicyCheck: boolean` needs no doc. A `"Policy check" vs "AI-evaluated"` doc on `isPolicyCheckAgentFinding` adds nothing. Prop docs that repeat the name, type or default are the most common offender.
- **Restates the signature or body.** "Groups by category: groups alphabetical, items ordered by `compare`" on a function named `groupByCategory(items, getCategory, compare)`. A numbered "matching rules" list in a docstring that mirrors the `if` chain below it is the same thing.
- **Points to other functions** ("Derive with `foo` / `bar`"). That's what find-usages is for.
- **Framework truisms.** "Must return a keyed element" on a React render prop.
- **Cross-system trivia that reads as a puzzle.** "The engine persists these as `Source = PolicyEngine` (`SRC_AGENT_EVALUATE_CASE`), so both predicates agree" left the reviewer more confused than without it. If you have to know three other files to parse a comment, it either needs rewriting into a plain one-line why, or deleting. Usually deleting.
- **Narrates a choice that is the natural default.** "The new run's category decides where it is listed" on `actual ?? expected`.
- Ticket references, author attribution, "added for X" history. These go in the commit.

## Docs (README, CLAUDE.md, AGENTS.md, …)

A doc reader is deciding whether and how to use something. They can open the file, so the doc should not walk them through it.

Cut:

- **How it works inside.** Which field it matches on, which endpoint it calls, which response field it reads, the order of its fallbacks. "It matches the deployment by `installedRootFolderKey` … It reads `currentPackageVersion`, NOT `newPackageVersionAvailable`" is implementation, and drifts the moment the code changes.
- **Restating a docstring, signature or config** that lives in the code. Field lists, parameter lists and copied code blocks are drift bait.
- **Fix narration.** "No longer resolves folder paths", "also fixes the X warning", "now async". That's the commit message.

Keep:

- **When to use it, and when not.**
- **The caller's contract**, the thing that breaks at the call site if ignored: "pass the solution's root folder key; a subfolder's key won't match".
- **Setup the code can't show**: scopes, permissions, env vars, deploy steps, ordering between systems.
- Follow the repo's own doc rules (size ceilings, digest-not-duplicate between `CLAUDE.md` and `AGENTS.md`, sync rules).

## Keep (one line)

- A non-obvious constraint the code depends on: "the shadcn TableCell sets `whitespace-nowrap`, which inherits".
- A lint suppression's reason.
- A cross-boundary contract: "must match the Python `FOO` constant".
- What a misleading external name actually means: "`installedRootFolderKey` is the folder the solution was installed into (`A/B/QAQC`), not a top-level parent". A concrete example beats a definition.
- Why a tempting simpler approach is wrong.

## Then

- Ask of each survivor: would the reviewer miss it if it were gone? If not sure, cut it.
- Rewrite survivors to one line where possible; a doc paragraph to one or two sentences.
- Run the formatter and linter, since removing a comment can change wrapping.
