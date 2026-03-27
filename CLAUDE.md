# CLAUDE.md

shiply-scheme: yolo

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code plugin marketplace for BriefBox. Contains custom slash-command plugins that are distributed via the `.claude-plugin/marketplace.json` registry. Currently ships one plugin: **shiply**.

## Repository Structure

```
.claude-plugin/marketplace.json   # Plugin registry — lists all published plugins
plugins/
  shiply/
    .claude-plugin/plugin.json    # Plugin metadata (name, description, author)
    commands/
      dock.md                     # /dock — connect work to a JIRA issue, create smart-commit branch
      shiply.md                   # /shiply — auto-detect scheme from CLAUDE.md, then ship
      shiply-yolo.md              # /shiply-yolo — commit + push to current branch
      shiply-careful.md           # /shiply-careful — branch, commit, push, open PR
      shiply-corporate.md         # /shiply-corporate — review + approve, then careful flow
```

## Plugin Architecture

Each plugin is a directory under `plugins/` containing:
- `.claude-plugin/plugin.json` — metadata (name, description, author)
- `commands/*.md` — slash commands as markdown files with YAML frontmatter

Command files use this structure:
- **Frontmatter** (`---` delimited): `allowed-tools` (whitelist of tool permissions), `description`
- **Context section**: Dynamic context via `!`-prefixed shell commands (e.g., `!`\``git status`\``)
- **Task section**: Step-by-step instructions for the command's behavior

The marketplace registry (`.claude-plugin/marketplace.json`) indexes all plugins with name, version, source path, description, category, and tags.

## Shiply Plugin — Shipping Schemes

The shiply plugin implements a dock-then-ship workflow:

| Command | Scheme | Flow |
|---------|--------|------|
| `/dock` | — | Search/create JIRA issue → create branch named `{prefix}{KEY}-{slug}` |
| `/shiply` | auto-detect | Reads `shiply-scheme:` from consuming repo's CLAUDE.md, falls back to asking user |
| `/shiply-yolo` | yolo | `git add` → `git commit` → `git push` (current branch) |
| `/shiply-careful` | careful | Create branch if on main → `git add` → `git commit` → `git push -u` → `gh pr create` |
| `/shiply-corporate` | corporate | Present diff for approval → if approved, run careful flow |

### Dock Configuration (read from consuming repo's CLAUDE.md)

| Key | Required | Default | Purpose |
|-----|----------|---------|---------|
| `dock-project` | Yes | — | JIRA project key (e.g., `BB`) |
| `dock-provider` | No | `jira` | Only `jira` supported currently |
| `dock-cloud-id` | No | auto-discovered | Atlassian cloud instance ID |
| `dock-branch-prefix` | No | *(empty)* | Branch name prefix (e.g., `feature/`) |

## Adding a New Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with name, description, author
2. Add command markdown files to `plugins/<name>/commands/`
3. Register the plugin in `.claude-plugin/marketplace.json` under the `plugins` array
