# skill-streak

My agent skills for day-to-day coding work in T3 Code: plan, build, review and ship a change, and review other people's PRs.

Forked from [bStack](https://github.com/0xr3ngar/bstack) by 0xr3ngar at version 1.3.1. I renamed the workflow skills, dropped the parts I don't use, and folded in my own review and comment-cleanup habits. Credits for bStack and the work it built on are in [third-party notices](THIRD_PARTY_NOTICES.md).

## Install

```bash
bunx --bun skills add RobinMennens-UiPath/skill-streak --global
```

Then run `/do-work-setup` to choose models and the reviewers a plain `/swarm` uses.

## Skills

| Skill | What it does |
| --- | --- |
| [do-work](skills/do-work/SKILL.md) | Start here. Routes a feature, bug, small change, PR review, question, audit or writing task to its playbook. |
| [do-work-setup](skills/do-work-setup/SKILL.md) | Choose models and swarm reviewers, then tune these skills from past conversations. |
| [build-plan](skills/build-plan/SKILL.md) | Build a reviewed feature plan into draft PRs, with proof on the real app. |
| [open-pr](skills/open-pr/SKILL.md) | Open a draft PR, then watch it and handle comments, reviews and failing checks as they arrive. |
| [swarm](skills/swarm/SKILL.md) | Review a plan, code change or open question in rounds with several models. |
| [skill-retro](skills/skill-retro/SKILL.md) | Find corrections you keep repeating in your agent history and update the skills behind them. |
| [pr-review](skills/pr-review/SKILL.md) | Review a PR or local branch against a list of expectations, split across focused review agents. Proposes comments for you to approve, then watches the PR for replies and new commits. |
| [fix-comments](skills/fix-comments/SKILL.md) | Cut comments and doc text that repeat the code. |
| [why](skills/why/SKILL.md) | Trace a decision through commits, PRs, tickets and old threads. |
| [coding-best-practices](skills/coding-best-practices/SKILL.md) | Small, verified changes and readable code. |
| [user-first](skills/user-first/SKILL.md) | Design from the user's side of a change. |
| [exhaust-the-design-space](skills/exhaust-the-design-space/SKILL.md) | Compare concrete designs before choosing one. |
| [foundational-thinking](skills/foundational-thinking/SKILL.md) | Define data and contracts before writing logic. |
| [root-cause-fix](skills/root-cause-fix/SKILL.md) | Trace a failure to its first wrong decision. |
| [redesign-from-first-principles](skills/redesign-from-first-principles/SKILL.md) | Migrate every caller of a redesigned contract. |
| [type-system](skills/type-system/SKILL.md) | Represent valid states in types. |
| [unslop](skills/unslop/SKILL.md) | Cut AI tells from any writing. |
| [bro](skills/bro/SKILL.md) | Restate the last response in plain language. |
| [time-report](skills/time-report/SKILL.md) | Estimate time spent on tickets from agent conversation history. |

## Develop

Needs Bun 1.4 or newer. See [AGENTS.md](AGENTS.md) for the skill conventions.

```bash
bun install --frozen-lockfile
bun run check
bun run check:install
```
