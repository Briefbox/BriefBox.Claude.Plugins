---
allowed-tools: Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git commit:*), Bash(git push:*), Bash(git branch:*), Bash(git switch:*), Bash(git checkout:*), Bash(gh pr create:*), AskUserQuestion
description: Ship it - review changes with user approval, then branch, commit, push, and open a PR (corporate mode)
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

## Your task

Ship these changes using the **corporate** scheme: present changes for approval, then branch, commit, push, and create a PR.

### Step 1: Present changes for approval

Summarize the changes in a clear message to the user:
- List of files changed (new, modified, deleted)
- Brief description of what the changes do

Then use AskUserQuestion to ask:
> "Approve these changes for shipping?"

With options:
- **Approve** — proceed with branching, committing, pushing, and PR creation
- **Reject** — stop, make no changes

### Step 2: If approved

1. If already on a feature branch (not main/master), use it. If on main/master, ask the user for a branch name. If the user doesn't provide one or says to auto-generate, derive a short kebab-case branch name from the changes (e.g., `feature/add-retry-logic`). Create the branch with `git switch -c <branch>`.
2. All relevant files will be staged by the user, so DO NOT `git add` any file
3. Create a single commit with a message that matches the repo's existing commit style
4. Push the branch to origin with `git push -u origin <branch>`
5. Create a pull request with `gh pr create`. Use a concise title (under 70 chars) and a body with:
   - `## Summary` — 1-3 bullet points
   - `## Test plan` — checklist of how to verify the changes

### Step 3: If rejected

Stop immediately. Do not commit, push, or create a PR. Simply acknowledge the rejection.
