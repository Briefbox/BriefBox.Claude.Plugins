# BriefBox Claude Code Plugins

A plugin marketplace for [Claude Code](https://claude.ai/code) containing BriefBox's shared developer workflow commands.

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

## Available Plugins

### shiply

Git workflow commands for docking to JIRA issues and shipping code.

| Command | Description |
|---------|-------------|
| `/dock` | Search or create a JIRA issue, then create a smart-commit branch |
| `/shiply` | Auto-detect shipping scheme from your repo's CLAUDE.md and ship |
| `/shiply-yolo` | Commit and push to the current branch |
| `/shiply-careful` | Create a branch, commit, push, and open a PR |
| `/shiply-corporate` | Review changes with approval gate, then branch + commit + push + PR |

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
/shiply                # ships using the scheme configured in CLAUDE.md
```
