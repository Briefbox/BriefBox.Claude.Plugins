---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git commit:*), Bash(git push:*), Bash(git branch:*), Bash(git switch:*), Bash(git checkout:*), Bash(gh pr create:*)
description: Ship it - create a branch, commit, push, and open a PR (careful mode)
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

## Your task

Ship these changes using the **careful** scheme: branch, commit, push, and create a PR.

1. Review the changes above
2. If already on a feature branch (not main/master), use it. If on main/master, ask the user for a branch name. If the user doesn't provide one or says to auto-generate, derive a short kebab-case branch name from the changes (e.g., `feature/add-retry-logic`). Create the branch with `git switch -c <branch>`.
3. Stage all relevant files with `git add` — never stage files that likely contain secrets (.env, credentials, keys, etc.)
4. Create a single commit with a message that matches the repo's existing commit style
5. Push the branch to origin with `git push -u origin <branch>`
6. Create a pull request with `gh pr create`. Use a concise title (under 70 chars) and a body with:
   - `## Summary` — 1-3 bullet points
   - `## Test plan` — checklist of how to verify the changes

Do not send any unnecessary text. Be efficient with tool calls.
