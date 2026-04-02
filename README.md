# BriefBox Claude Code Plugins

A plugin marketplace and skill repository for [Claude Code](https://claude.ai/code) containing BriefBox's shared developer workflow commands and auto-triggered skills.

## Installation

### From GitHub (recommended)

```shell
/plugin marketplace add Briefbox/BriefBox.Claude.Plugins
```

### From a local clone

```shell
git clone https://github.com/Briefbox/BriefBox.Claude.Plugins.git
/plugin marketplace add ./BriefBox.Claude.Plugins
```

### Pin to a specific version

```shell
/plugin marketplace add Briefbox/BriefBox.Claude.Plugins#v1.0.0
```

### Install via settings.json

Add to `~/.claude/settings.json` (user-wide) or `.claude/settings.json` (per-project):

```json
{
  "extraKnownMarketplaces": {
    "briefbox": {
      "source": {
        "source": "github",
        "repo": "Briefbox/BriefBox.Claude.Plugins"
      }
    }
  }
}
```

## Installing Plugins

Once the marketplace is added, install individual plugins:

```shell
/plugin install shiply@briefbox
```

Then reload to activate:

```shell
/reload-plugins
```

## Installing Skills

Skills are **separate from plugins** — they're auto-triggered capabilities that Claude Code discovers from the filesystem, not from marketplaces. There is no `/plugin install` for standalone skills.

To use a skill from this repo's `skills/` directory:

```shell
# Project-wide (committed to the repo that needs the skill)
cp -r skills/<skill-name> <your-project>/.claude/skills/

# User-wide (available in all your projects)
cp -r skills/<skill-name> ~/.claude/skills/
```

Once in place, skills activate automatically when your message matches their trigger keywords — no slash command needed.

> **Note:** Some skills (like obsidian-cli) are bundled inside plugins under `plugins/` and distributed via the marketplace. BriefBox-authored standalone skills live in `skills/` and are copied manually.

## Available Plugins

### shiply

Git workflow commands for docking to JIRA issues and shipping code.

| Command | Description |
|---------|-------------|
| `/dock` | Search or create a JIRA issue, then create a smart-commit branch |
| `/ship` | Auto-detect shipping scheme from your repo's CLAUDE.md and ship |
| `/ship-yolo` | Commit and push to the current branch |
| `/ship-careful` | Create a branch, commit, push, and open a PR |
| `/ship-corporate` | Review changes with approval gate, then branch + commit + push + PR |

### Configuring shiply for your repo

Add these keys to your repo's `CLAUDE.md` to configure behavior:

```markdown
shiply-scheme: careful
dock-project: BB
dock-branch-prefix: feature/
```

| Key | Required | Default | Description |
|-----|----------|---------|-------------|
| `shiply-scheme` | No | asks user | `yolo`, `careful`, or `corporate` |
| `dock-project` | For `/dock` | — | JIRA project key |
| `dock-provider` | No | `jira` | Issue tracker (`jira` only for now) |
| `dock-cloud-id` | No | auto-discovered | Atlassian cloud instance ID |
| `dock-branch-prefix` | No | *(empty)* | Prefix for branch names (e.g. `feature/`) |

### Typical workflow

```
/dock                  # find/create JIRA issue → creates branch BB-142-add-retry-logic
# ... do your work ...
/ship                  # ships using the scheme configured in CLAUDE.md
```

## Available Skills

### obsidian-cli (bundled as plugin)

Obsidian vault operations via the official Obsidian CLI (v1.12+). Triggers automatically on keywords like "obsidian", "vault", "daily note", etc. Distributed as a plugin since it's an external fork:

```shell
/plugin install obsidian-cli@briefbox
```
