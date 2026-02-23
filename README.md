# Agentic Workflows Test Project

This repository demonstrates **GitHub Copilot Agentic Workflows** — AI-powered automation that runs directly in GitHub Actions, triggered by slash commands, schedules, or events.

## What's in this repo

```
.
├── .github/workflows/
│   └── issue-triage.md          ← Agent definition (you write this)
│       ↓ gh aw compile
│   └── issue-triage.lock.yml    ← Auto-generated Actions workflow (don't edit)
└── knowledge-base/
    └── triage-guidelines.md     ← Knowledge base (reference data for the agent)
```

## Architecture

### The 3 Pieces

| File | What it is | Who writes it |
|------|-----------|---------------|
| `.github/workflows/<name>.md` | **Agent definition** — the prompt, triggers, permissions, and safe outputs | You |
| `.github/workflows/<name>.lock.yml` | **Compiled workflow** — the actual GitHub Actions YAML that runs the agent | `gh aw compile` (auto-generated) |
| `knowledge-base/*.md` | **Knowledge base** — reference data the agent reads at runtime via MCP | You |

### How It Works

```
User types /triage on an issue
        │
        ▼
GitHub Actions triggers the compiled .lock.yml workflow
        │
        ▼
The agent reads the .md prompt and gets its instructions
        │
        ▼
Agent uses MCP tools to:
  1. Read issue body + comments (mcp_github_get_file_contents / gh CLI)
  2. Fetch knowledge base docs (mcp_github_get_file_contents)
  3. Synthesize a response
  4. Post a comment via safe outputs (add-comment)
```

### Key Concepts

**Frontmatter** — The YAML block at the top of the `.md` file defines:
- `on:` — What triggers the agent (slash commands, schedules, PR events, etc.)
- `permissions:` — GitHub token permissions
- `tools:` — Which MCP toolsets the agent can use
- `safe-outputs:` — What actions the agent can take (add comments, create issues, etc.)

**Safe Outputs** — The agent can't directly modify GitHub resources. It must use pre-defined safe output tools:
- `add-comment` — Post a comment on an issue/PR
- `create-issue` — Create a new issue
- `noop` — Log that no action was needed (transparency)

**Knowledge Base via MCP** — Instead of hardcoding data into the prompt, agents fetch reference documents at runtime using `mcp_github_get_file_contents`. This means:
- The prompt stays clean and focused on instructions
- Reference data can be updated independently
- Multiple agents can share the same knowledge base

## The Example: Issue Triage Agent

### What it does
When someone types `/triage` on an issue with the `needs-triage` label, the agent:
1. Reads the issue body and comments
2. Fetches `knowledge-base/triage-guidelines.md` for priority definitions, label taxonomy, and team routing
3. Posts a structured triage recommendation (priority, labels, assignee, SLA)

### Agent Definition: `.github/workflows/issue-triage.md`

The prompt instructs the agent to:
- **Gate check**: Only run on issues with the `needs-triage` label
- **Fetch knowledge base**: Read triage guidelines before making decisions
- **Gather context**: Read the issue body and comments
- **Generate recommendation**: Post a structured triage comment grounded in the knowledge base

### Knowledge Base: `knowledge-base/triage-guidelines.md`

Contains:
- Priority matrix (P0-P3) with impact/urgency definitions
- Label taxonomy (type, area, status labels)
- Team ownership map with routing rules
- SLA targets per priority level
- Triage checklist

## Getting Started

### 1. Install the `gh aw` CLI extension

```bash
gh extension install github/gh-aw
```

### 2. Compile the agent

```bash
gh aw compile
```

This reads `.github/workflows/issue-triage.md` and generates `.github/workflows/issue-triage.lock.yml`.

### 3. Push and test

```bash
git add .github/workflows/
git commit -m "Add issue triage agent"
git push
```

Then create an issue with the `needs-triage` label and comment `/triage`.

## Comparison with `actions-migrations-via-copilot`

This project follows the same pattern used by [actions-migrations-via-copilot](https://github.com/github/actions-migrations-via-copilot):

| Pattern | migrations-via-copilot | This project |
|---------|----------------------|---------------|
| Agent definitions | `agents/*.md` | `.github/workflows/*.md` |
| Knowledge base | `knowledge/` | `knowledge-base/` |
| KB access method | `mcp_github_get_file_contents` | `mcp_github_get_file_contents` |
| Trigger | Copilot Chat invocation | Slash commands / schedules |
| Output | File changes + migration report | Issue comments |

The key difference: `actions-migrations-via-copilot` agents run in **Copilot Chat** (interactive), while agentic workflow agents run in **GitHub Actions** (event-driven, automated).

## Creating Your Own Agent

1. **Create `.github/workflows/your-agent.md`** with frontmatter + prompt
2. **Create `knowledge-base/your-data.md`** with reference data
3. **Run `gh aw compile`** to generate the lock file
4. **Push and test**

### Frontmatter Reference

```yaml
---
on:
  # Slash command trigger (must be in an issue or PR comment)
  slash_command:
    name: your-command      # invoked as /your-command
    events: [issue_comment]
  
  # Schedule trigger  
  schedule: daily            # runs daily
  
  # Event triggers
  issues:
    types: [opened, labeled]
  pull_request:
    types: [opened, synchronize]

permissions: read-all        # GitHub token permissions

tools:
  toolsets: default          # MCP toolsets available to the agent

safe-outputs:                # What the agent can do
  add-comment:               # Post comments
  create-issue:              # Create issues
    close-older-issues: true # Auto-close previous issues from this workflow
  noop:                      # Log no-action transparency message
---
```

## Resources

- [Agentic Workflows Documentation](https://github.com/github/gh-aw)
- [GitHub MCP Server](https://github.com/github/github-mcp-server)
- [actions-migrations-via-copilot](https://github.com/github/actions-migrations-via-copilot) — Production example of knowledge-base-driven agents
