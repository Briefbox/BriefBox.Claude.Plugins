# CLAUDE.md

shiply-scheme: yolo

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Versioning

Every change must bump the shiply version in ALL of these locations:
- `.claude-plugin/marketplace.json` — the `"version"` field
- `plugins/shiply/commands/ship.md` — art banner
- `plugins/shiply/commands/dock.md` — art banner
- `plugins/shiply/commands/ship-corporate.md` — art banner
- `plugins/shiply/commands/chain.md` — art banner (also copy to `drop-anchor.md`)
- `plugins/shiply/commands/address-pr-comments.md` — art banner

Consider which semver segment to increment:
- **Patch** (2.10.x → 2.10.y): bugfixes, typos, wording tweaks that don't change behavior
- **Minor** (2.x.0 → 2.y.0): new commands, new features, behavioral changes to existing commands
- **Major** (x.0.0 → y.0.0): breaking changes that require consuming repos to update their config

## What This Is

A Claude Code plugin marketplace for BriefBox. Contains custom plugins distributed via the `.claude-plugin/marketplace.json` registry (e.g., shiply). Also contains a `skills/` directory for developing standalone skills — these are a separate concept from plugins (see below).

## Repository Structure

```
.claude-plugin/marketplace.json   # Plugin registry — lists all published plugins
plugins/                          # Plugins (installed via marketplace, provide slash commands)
  shiply/
    .claude-plugin/plugin.json    # Plugin metadata (name, description, author)
    commands/
      dock.md                     # /dock — connect work to a JIRA issue, create smart-commit branch
      ship.md                     # /ship — auto-detect scheme from CLAUDE.md, then ship
      ship-yolo.md                # /ship-yolo — commit + push to current branch
      ship-careful.md             # /ship-careful — branch, commit, push, open PR
      ship-corporate.md           # /ship-corporate — review + approve, then careful flow
      chain.md                     # /chain — create sequential chain branch (alias: /drop-anchor)
      drop-anchor.md              # /drop-anchor — alias for /chain
      address-pr-comments.md     # /address-pr-comments — evaluate PR comments, fix or push back
  obsidian-cli/                   # External plugin (upstream: pablo-mano/Obsidian-CLI-skill)
    .claude-plugin/plugin.json
    skills/obsidian-cli/
      SKILL.md
      references/command-reference.md
skills/                           # Standalone skills (development/authoring area)
```

## Plugin Architecture

### Plugins (slash commands)

Each plugin is a directory under `plugins/` containing:
- `.claude-plugin/plugin.json` — metadata (name, description, author)
- `commands/*.md` — slash commands as markdown files with YAML frontmatter

Command files use this structure:
- **Frontmatter** (`---` delimited): `allowed-tools` (whitelist of tool permissions), `description`
- **Context section**: Dynamic context via `!`-prefixed shell commands (e.g., `!`\``git status`\``)
- **Task section**: Step-by-step instructions for the command's behavior

### Skills (separate from plugins)

Skills are **not** part of the plugin/marketplace system. They are auto-discovered from the filesystem — no install command exists.

Claude Code loads skills from:
1. **Project**: `.claude/skills/<skill-name>/SKILL.md`
2. **User-wide**: `~/.claude/skills/<skill-name>/SKILL.md`

Skills activate automatically based on trigger keywords — no slash command needed.

Each skill directory contains:
- `SKILL.md` — skill definition with triggers, description, and instructions
- `references/` — optional supporting reference docs

Skill files use this structure:
- **Frontmatter** (`---` delimited): `name`, `version`, `description`, `triggers` (keyword list that auto-activates the skill)
- **Body**: Instructions, tool permissions, and workflow steps

The `skills/` directory in this repo is a **development/authoring area** for writing skills. To use a skill, copy it to `.claude/skills/` in the target project or `~/.claude/skills/` for user-wide access.

> **Note:** Skills can also be bundled inside plugins (like obsidian-cli under `plugins/`), in which case they're distributed via the marketplace. But standalone skills are just files on disk.

The marketplace registry (`.claude-plugin/marketplace.json`) indexes plugins only.

## Shiply Plugin — Shipping Schemes

The shiply plugin implements a dock-then-ship workflow:

| Command | Scheme | Flow |
|---------|--------|------|
| `/dock` | — | Search/create JIRA issue → create branch named `{prefix}{KEY}-{slug}` |
| `/ship` | auto-detect | Reads `shiply-scheme:` from consuming repo's CLAUDE.md, falls back to asking user |
| `/ship-yolo` | yolo | `git add` → `git commit` → `git push` (current branch) |
| `/ship-careful` | careful | Create branch if on main → `git add` → `git commit` → `git push -u` → `gh pr create` |
| `/ship-corporate` | corporate | Present diff for approval → if approved, run careful flow |
| `/chain` | chaining | Create sequential chain branch (`-a`, `-b`, ...); branch movement only — `/ship` handles shipping |
| `/drop-anchor` | chaining | Alias for `/chain` |
| `/address-pr-comments` | — | Fetch PR comments → evaluate → plan fixes or reply with pushback |

### Dock Configuration (read from consuming repo's CLAUDE.md)

| Key | Required | Default | Purpose |
|-----|----------|---------|---------|
| `dock-project` | Yes | — | JIRA project key (e.g., `BB`) |
| `dock-provider` | No | `jira` | Only `jira` supported currently |
| `dock-cloud-id` | No | auto-discovered | Atlassian cloud instance ID |
| `shiply-chaining` | No | `false` | Enable PR chaining — sequential snapshot branches (`-a`, `-b`, ...) with stacked PRs |

## Adding a New Plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with name, description, author
2. Add command markdown files to `plugins/<name>/commands/`
3. Register the plugin in `.claude-plugin/marketplace.json` under the `plugins` array

## Adding a New Skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter (name, version, description, triggers) and instructions
2. Optionally add a `references/` directory with supporting docs
3. To use: copy the skill directory to `.claude/skills/` in the target project or `~/.claude/skills/` for user-wide access
