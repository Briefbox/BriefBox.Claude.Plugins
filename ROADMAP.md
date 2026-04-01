# Plugin Roadmap

> All items are tagged **RFC** (Request for Comments). Open an issue or comment to vote, suggest changes, or claim one for implementation.

## Shipped

- **shiply** — Git workflow: dock to JIRA, ship with yolo/careful/corporate schemes (`/dock`, `/ship`, `/ship-yolo`, `/ship-careful`, `/ship-corporate`, `/address-pr-comments`)

---

## Tier 1 — High Value, Broad Applicability

### `review` — Code Review Automation `[RFC]`

Multi-perspective code review using parallel sub-agents. Checks for bugs, CLAUDE.md compliance, security issues, performance anti-patterns, and style consistency. Outputs a structured report with confidence scores.

- `/review` — Review staged or recent changes
- `/review-pr <number>` — Review an open PR

**Inspiration:** Anthropic's official `code-review` plugin (5 parallel Sonnet agents), community repos with multi-agent review patterns.

---

### `testsmith` — Test Generation & Gap Analysis `[RFC]`

Generate tests for files/functions with edge cases, and analyze coverage gaps to prioritize what matters.

- `/test-gen [file]` — Generate unit tests for a file or function
- `/test-gap` — Analyze coverage gaps, suggest high-value untested paths
- `/test-fix` — Diagnose and fix failing or flaky tests

**Why:** 30-60% time savings on test authoring is well-documented. Flaky tests are the #1 CI trust-killer.

---

### `scribe` — Documentation Generator `[RFC]`

Generate and maintain documentation from code analysis and conversation context.

- `/document [file|dir]` — Generate/update docs for code
- `/adr [title]` — Create an Architecture Decision Record from context
- `/runbook [service]` — Generate operational runbook for a service
- `/onboard` — Generate new-developer onboarding guide from CLAUDE.md + repo structure

**Why:** 64% of developers already use AI for docs. Onboarding time drops dramatically with good generated docs.

---

### `changelog` — Release Notes & Changelog `[RFC]`

Generate changelogs from git history with JIRA link extraction and semantic grouping.

- `/changelog` — Generate changelog since last tag
- `/changelog [from]..[to]` — Changelog for a specific range

Groups by type (feat/fix/refactor/docs), extracts JIRA keys from branch names and commit messages, links to PRs.

---

## Tier 2 — High Value, Targeted Use Cases

### `sentinel` — Security Scanning `[RFC]`

Security-focused analysis of code changes and repository state.

- `/secret-scan` — Scan for leaked secrets in code + git history
- `/security-review [file|dir]` — OWASP-aware review of auth, input validation, injection vectors
- `/threat-model [feature]` — Generate STRIDE threat model for a feature or service
- `/deps-audit` — Check dependencies for known vulnerabilities and license issues

---

### `pipeline` — CI/CD Debugging & Generation `[RFC]`

Diagnose CI failures and generate pipeline configurations.

- `/pipeline-debug [url|run-id]` — Analyze a failed CI run (Azure DevOps, GitHub Actions)
- `/pipeline-gen` — Generate pipeline config from repo structure
- `/deploy-check` — Pre-deploy validation checklist

**Why:** CI debugging is a universal daily pain point. Environment mismatches are the #1 failure cause.

---

### `migrator` — Database Migration Safety `[RFC]`

Generate and review database migrations with safety checks.

- `/migration-gen [description]` — Generate migration from model changes (EF Core)
- `/migration-review` — Review pending migrations for dangerous operations (column drops, data loss, missing rollbacks)
- `/seed-gen [entity]` — Generate realistic test/seed data from schema

---

### `orient` — Codebase Orientation & Onboarding `[RFC]`

Help developers quickly understand unfamiliar codebases or areas of code.

- `/orient` — High-level codebase walkthrough (structure, key patterns, entry points)
- `/orient [dir|file]` — Deep dive into a specific area
- `/explain [symbol]` — Explain a function, class, or module in context

**Why:** New team members spend days getting oriented. This also helps when context-switching between services in a polyrepo.

---

## Tier 3 — Polyrepo & Cross-Service (BriefBox-Specific Value)

### `radar` — Change Impact Analysis `[RFC]`

Analyze blast radius of changes across a polyrepo workspace.

- `/affected` — Which services are impacted by current changes (shared libs, event contracts, API schemas)
- `/cross-check` — Validate contracts across services (Kafka event models, API schemas, shared NuGet versions)
- `/service-map` — Visualize service dependencies as a Mermaid diagram

**Why:** In a polyrepo with shared Kafka events and NuGet libraries, knowing what breaks downstream is critical.

---

### `standup` — Automated Standup & Sprint Tools `[RFC]`

Generate standup reports and sprint summaries from git + JIRA activity. Extends the existing `/dock` JIRA integration.

- `/standup` — Generate standup from yesterday's git commits + JIRA transitions
- `/sprint-summary` — Summarize sprint progress across repos
- `/retro-prep` — Gather data for sprint retrospective (velocity, blockers, patterns)

---

### `envsync` — Environment Configuration Validator `[RFC]`

Validate environment variables and config across local dev, Aspire, Kubernetes, and CI.

- `/env-check` — Compare env vars across appsettings, K8s manifests, CI pipelines, and Aspire config
- `/env-diff [env1] [env2]` — Diff config between two environments (e.g., dev1 vs prod)

**Why:** Config drift between environments is a top source of deployment failures.

---

## Tier 4 — Nice to Have

### `refactor` — Guided Refactoring `[RFC]`

Structured refactoring workflows with safety nets.

- `/tech-debt` — Scan for TODO/FIXME/HACK, estimate effort, create JIRA issues
- `/dead-code` — Find unused exports, unreachable code, orphaned files
- `/simplify [file]` — Simplify recently modified code while preserving behavior
- `/consistency-check` — Detect inconsistent patterns across codebase

---

### `release` — Release Management `[RFC]`

Orchestrate the release ceremony.

- `/release [major|minor|patch]` — Bump version, update changelog, tag, create GitHub release
- `/hotfix [branch]` — Cherry-pick fixes to release branch with proper tracking
- `/feature-flag [name]` — Scaffold a feature flag with toggle service integration

---

### `handover` — Session Continuity `[RFC]`

Structured session handoffs for team collaboration or context switching.

- `/handover` — Generate session summary (decisions made, blockers, next steps, open questions)
- `/catchup` — Reload working state after `/clear` or new session
- `/session-log` — Export conversation as structured markdown

**Inspiration:** Popular community pattern; especially valuable for team handoffs.

---

### `perf` — Performance & Observability `[RFC]`

Find performance issues and improve observability.

- `/perf-audit [file|dir]` — Find N+1 queries, missing indexes, expensive operations
- `/log-improve [file]` — Add structured logging with proper context to existing code
- `/trace [endpoint]` — Generate OpenTelemetry trace instrumentation

---

### `api` — API Development Tools `[RFC]`

API testing and documentation from the terminal.

- `/api-test [endpoint]` — Generate and run API tests
- `/api-doc [controller|file]` — Generate/update OpenAPI spec from code
- `/api-mock [spec]` — Generate mock server from OpenAPI spec

---

## How to Contribute

1. **Vote** — React or comment on the RFC issue for any plugin you want
2. **Discuss** — Open an issue tagged `rfc` to propose changes or new plugins
3. **Build** — See [CLAUDE.md](CLAUDE.md) for the plugin architecture and "Adding a New Plugin" guide
4. **Submit** — Open a PR adding your plugin under `plugins/<name>/` with commands in `commands/*.md`

### Plugin Design Principles

- Commands are markdown files — keep them focused and single-purpose
- Use `allowed-tools` frontmatter to whitelist only what's needed (principle of least privilege)
- Prefer composability over monoliths — multiple small commands > one mega-command
- Support both interactive and autonomous modes where it makes sense
- Read config from the consuming repo's CLAUDE.md (same pattern as shiply's `dock-project`)
