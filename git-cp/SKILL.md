---
name: git-cp
description: Commit and push the current branch changes in a local git repository. Use when the user asks to run git commit + git push on the current branch, especially when the commit message should summarize the staged or working-tree changes.
---

# git-cp

Commit and push the current branch using a concise, change-focused commit message.

## Workflow

1. Inspect repository state.
- Run `git status --short --branch`.
- Run `git diff --staged --stat` and `git diff --stat` to understand staged and unstaged changes.

2. Prepare commit scope.
- If nothing is staged but there are tracked modifications, stage intended files with `git add <paths>`.
- If only untracked files exist, stage only files that are clearly part of the requested change.
- If the workspace contains unrelated changes, avoid touching unrelated files and explain what was committed.

3. Write commit message.
- Summarize the actual change, not the process.
- Use one short subject line in imperative style.
- Keep subject typically within 50-72 characters.
- Prefer patterns like `feat: ...`, `fix: ...`, `chore: ...`, `docs: ...`, or plain imperative text when repo conventions are unknown.

4. Commit and push.
- Run `git commit -m "<summary>"`.
- Identify current branch with `git rev-parse --abbrev-ref HEAD`.
- Push with `git push -u origin <branch>` if upstream is missing, otherwise `git push`.

5. Report result.
- Share branch name, commit hash, commit subject, and push target.
- If push fails (auth, protected branch, divergence), report exact failure and next command to resolve.

## Guardrails

- Never rewrite history (`git rebase`, `git commit --amend`, force-push) unless explicitly requested.
- Never commit generated secrets or environment files unless the user explicitly requests it.
- Keep scope minimal: commit only files related to the user request.
