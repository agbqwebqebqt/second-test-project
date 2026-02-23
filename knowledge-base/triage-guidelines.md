# Issue Triage Guidelines

> **Purpose**: Reference data for triaging incoming issues. Defines priority levels, label taxonomy, team ownership, and SLA targets.

---

## Priority Matrix

| Priority | Impact | Urgency | Examples |
|----------|--------|---------|----------|
| **P0 — Critical** | Production down, data loss, security vulnerability | Immediate action required | Service outage, auth bypass, data corruption |
| **P1 — High** | Major feature broken, significant user impact | Next business day | Login failures for subset of users, broken CI pipeline on main |
| **P2 — Medium** | Minor feature issue, workaround available | Within the sprint | UI rendering bug, slow query, non-critical integration failure |
| **P3 — Low** | Cosmetic, nice-to-have, minor improvement | Backlog | Typo in docs, minor UI polish, dependency version bump |

### Priority Decision Guide

1. **Is production impacted right now?** → P0
2. **Is a core workflow blocked with no workaround?** → P1
3. **Is functionality degraded but usable?** → P2
4. **Is this an improvement or cosmetic fix?** → P3

---

## Label Taxonomy

### Type Labels
| Label | When to Apply |
|-------|---------------|
| `bug` | Something is broken or not working as expected |
| `feature-request` | New functionality or enhancement |
| `question` | Asking for help or clarification |
| `documentation` | Docs need to be added or updated |
| `task` | Internal work item, chore, or maintenance |

### Area Labels
| Label | Scope |
|-------|-------|
| `area/api` | REST or GraphQL API issues |
| `area/frontend` | UI, UX, client-side issues |
| `area/backend` | Server-side, business logic |
| `area/infra` | Infrastructure, deployment, CI/CD |
| `area/auth` | Authentication and authorization |
| `area/docs` | Documentation site or README |

### Status Labels
| Label | Meaning |
|-------|--------|
| `needs-triage` | Awaiting triage (applied automatically on new issues) |
| `needs-info` | More information needed from reporter |
| `confirmed` | Bug confirmed and reproducible |
| `wont-fix` | Intentional behavior, closing |
| `duplicate` | Duplicate of another issue |

---

## Team Ownership Map

| Area | Team / Owner | Escalation Contact |
|------|-------------|--------------------|
| API | @api-team | @api-lead |
| Frontend | @frontend-team | @frontend-lead |
| Backend | @backend-team | @backend-lead |
| Infrastructure | @infra-team | @infra-lead |
| Auth & Security | @security-team | @security-lead |
| Documentation | @docs-team | @docs-lead |

### Routing Rules
- **Security issues (P0)**: Always route to `@security-team` regardless of area
- **Cross-cutting issues**: Route to the team most affected; mention other teams in the triage comment
- **Unknown area**: Route to `@backend-team` as default; they'll redirect if needed

---

## SLA Targets

| Priority | First Response | Resolution Target | Escalation |
|----------|---------------|-------------------|------------|
| **P0** | 1 hour | 4 hours | Immediate to team lead + on-call |
| **P1** | 4 hours | 2 business days | After 1 business day without progress |
| **P2** | 1 business day | Current sprint | After 1 sprint without progress |
| **P3** | 1 week | Best effort | No auto-escalation |

---

## Triage Checklist

Before completing triage, verify:

1. **Is there enough information to understand the issue?** If not, apply `needs-info` and ask the reporter.
2. **Is this a duplicate?** Search existing issues before routing.
3. **Is the priority justified?** Use the Priority Decision Guide above.
4. **Is the right team tagged?** Use the Ownership Map.
5. **Are the labels complete?** Apply one Type label + one Area label + priority.

---

*Last updated: February 2026*
