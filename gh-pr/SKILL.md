---
name: gh-pr
description: Create or update GitHub pull requests using the gh CLI, including draft vs ready-for-review state. Use when the user wants to open a PR from the current branch, switch draft/in-review state, or auto-generate PR title and description from the branch diff.
---

# gh-pr

Create a GitHub pull request from the current branch using `gh` with a title and body derived from the branch diff. Support draft vs in-review (ready) state, and switch state for an existing PR when requested.

## Workflow

1. Inspect repo state. Run `git status --short --branch` to confirm current branch and working tree state. If the repo has uncommitted changes that should be included, confirm with the user whether to proceed or wait for commits.

2. Determine base branch. If the user specifies a base, use it. Otherwise check `git config --get branch.$(git rev-parse --abbrev-ref HEAD).gh-merge-base`. If unset, use `gh repo view --json defaultBranchRef -q .defaultBranchRef.name` to get the default branch. If the base branch is missing locally, run `git fetch origin <base>`.

3. Build PR title and description from the diff. Use `git diff --stat <base>...HEAD` to summarize changes. Use `git diff <base>...HEAD` (or `--stat` plus file-level highlights) to describe the diff in prose. Keep the title concise (about 50–72 chars) and focused on what changed. The description should include a short summary and a bullet list of key file-level changes.

4. Create or update the PR. If a PR already exists for the current branch (`gh pr view` succeeds), update state if requested: in-review/ready uses `gh pr ready`; draft uses `gh pr ready --undo`. If no PR exists, create one non-interactively: draft uses `gh pr create --draft --title "<title>" --body "<body>" --base <base>` and in-review/ready uses `gh pr create --title "<title>" --body "<body>" --base <base>`. If the body is multi-line, prefer `--body-file` with a temp file.

5. Report results. Share the PR URL, chosen base branch, and whether it is draft or ready. If creation fails due to auth, missing upstream, or permissions, report the exact error and next step.

## Guardrails

- Do not open the browser (`gh pr create --web`) unless explicitly asked.
- Do not push or rebase unless the user requests it.
- Keep the PR text derived from the diff; do not invent changes.
