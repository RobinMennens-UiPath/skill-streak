---
name: coding-best-practices
description: Implement or review code through a verified result. Keep changes small and code readable.
disable-model-invocation: true
license: MIT
compatibility: "Requires access to the code under review."
---

# Coding best practices

Own the requested change through a working, verified result. Start with the problem and inspect the relevant code before choosing a solution.

## Resolve what you can

Use the repository, existing behavior, logs, and a reproduction to fill gaps. Make routine, reversible choices and state assumptions that affect the result. A short plan helps when the work has dependencies; a small fix does not need a planning ceremony.

Ask when missing information changes the intended outcome or exceeds your authority. Explain what you found and recommend a choice. Keep working on independent parts while waiting.

## Keep the change small

Implement the simplest solution that satisfies the request. Follow the repository's conventions. Avoid speculative options, one-use abstractions, and unrelated cleanup. Remove code your change makes obsolete and preserve other people's work.

A bug fix should address the cause. Reproduce the failure when possible, trace it, and check that the fix changes the observed behavior. Do not hide failures with fallback values or guards that make the symptom disappear.

## Write readable code

These are also the checks a review applies to every change.

- Use descriptive names, even long ones, over abbreviations that need explaining.
- Use early returns instead of if/else. Prefer `const`; return a computed value instead of assigning a variable across branches.
- Put magic values in named constants. Tests may use literals.
- Catch an exception only when you can do something meaningful with it. Never swallow one.
- Write comments only for why the code is this way, or for facts the code cannot show. Delete comments that repeat the code.
- Let a `switch` return the result for each case. Use a lookup object for fixed data mappings.
- Use loops when they make a transformation easier to follow. Avoid dense chains and nested ternaries.
- For types, parsing at boundaries and casts, follow [type system](../type-system/SKILL.md).

## Finish the job

Choose checks that could expose a wrong result. Run the changed behavior and the relevant repository checks. Add a focused test when it protects meaningful behavior; avoid tests that merely repeat the implementation. For a UI change, inspect the UI. A type check alone does not prove the feature works.

Investigate failures caused by the change, fix them, and rerun affected checks. If an approach keeps failing, revisit the cause instead of repeating it. For a blocker outside your control, finish the work you can and report the evidence and the specific missing input or access.

For implementation tasks in a repository that uses PRs, open and watch the PR with [open-pr](../open-pr/SKILL.md).

Continue through the delivery steps the user authorized. Merge, deployment, and messages require authority from the request or an applicable project policy. Do not ask for permission already given. Report the result, how you checked it, and any remaining limitation.
