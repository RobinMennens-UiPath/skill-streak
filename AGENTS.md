# Working on these skills

- Each skill lives in `skills/<name>/SKILL.md`. The folder name matches the frontmatter `name`: lowercase with hyphens.
- Frontmatter needs `name`, `description`, `license: MIT` and `compatibility`. The description says what the skill does and which requests should trigger it.
- A manual-only skill sets `disable-model-invocation: true` and adds `agents/openai.yaml` with `policy.allow_implicit_invocation: false`, so Codex respects it too.
- Link a sibling skill with a relative link: `../<name>/SKILL.md` from a `SKILL.md`, `../../<name>/SKILL.md` from `playbooks/` or `references/`. Say what happens when that skill is missing. Supporting files stay inside the folder of the skill that owns them.
- Name a skill from outside this repository, such as `pr-review`, in plain text, not as a link, and give a fallback.
- Add every skill to the catalog in `readme.md`. The validator fails without it.
- Write instructions with [unslop](skills/unslop/SKILL.md).
- Run `bun run check` and `bun run check:install` before pushing. Never commit personal conversations or tokens; time-report tests use synthetic transcripts.
- Keep the copyright notices in `LICENSE` and `THIRD_PARTY_NOTICES.md`.
