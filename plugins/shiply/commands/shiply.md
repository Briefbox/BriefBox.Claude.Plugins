---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git commit:*), Bash(git push:*), Bash(git branch:*), Bash(git switch:*), Bash(git checkout:*), Bash(gh pr create:*), AskUserQuestion
description: Ship it - auto-detects repo scheme from CLAUDE.md and runs the matching workflow
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`
- Repo CLAUDE.md: !`cat ./CLAUDE.md 2>/dev/null || cat .claude/CLAUDE.md 2>/dev/null || echo "No .claude/CLAUDE.md found"`

## Your task

Ship these changes using the scheme configured in this repo's `CLAUDE.md` or `.claude/CLAUDE.md`.

### Step 1: Detect scheme

Look for a line in the CLAUDE.md content above matching `ship-scheme: <scheme>` where scheme is one of: `yolo`, `careful`, `corporate`.

- If found, use that scheme.
- If not found, use AskUserQuestion to ask the user which scheme to use, with options: Yolo, Careful, Corporate. Add the user's response to this repo's `CLAUDE.md`

### Step 2: Execute the scheme

---

#### If scheme is `yolo`:

1. Stage all relevant files with `git add` — never stage files that likely contain secrets (.env, credentials, keys, etc.)
2. Create a single commit with a message that matches the repo's existing commit style
3. Push to origin on the current branch

---

#### If scheme is `careful`:

1. If already on a feature branch (not main/master), use it. If on main/master, ask the user for a branch name. If the user doesn't provide one or says to auto-generate, derive a short kebab-case branch name from the changes (e.g., `feature/add-retry-logic`). Create the branch with `git switch -c <branch>`.
2. Stage all relevant files with `git add` — never stage files that likely contain secrets (.env, credentials, keys, etc.)
3. Create a single commit with a message that matches the repo's existing commit style
4. Push the branch to origin with `git push -u origin <branch>`
5. Create a pull request with `gh pr create`. Use a concise title (under 70 chars) and a body with:
   - `## Summary` — 1-3 bullet points
   - `## Test plan` — checklist of how to verify the changes

---

#### If scheme is `corporate`:

**First, present changes for approval:**

Summarize the changes in a clear message:
- List of files changed (new, modified, deleted)
- Brief description of what the changes do

Then use AskUserQuestion to ask:
> "Approve these changes for shipping?"

With options:
- **Approve** — proceed
- **Reject** — stop immediately, do nothing

**If approved, follow the careful flow above** (branch, commit, push, PR).

**If rejected, stop.** Do not stage, commit, push, or create a PR.
