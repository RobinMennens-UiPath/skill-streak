# Upkeep

Fetch and rebase only the user's commits onto the base. Push with `--force-with-lease`. Restack dependent PRs lowest first with `gh stack`. If another thread holds the branch, send it the rebase with `t3_thread_send`.

Handle the open threads and red checks, then keep watching the PR, as “Watch the PR” in [open-pr](../../open-pr/SKILL.md) says. Merge only on the user's word for that PR.
