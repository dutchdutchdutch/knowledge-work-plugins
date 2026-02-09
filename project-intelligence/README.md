# Project Intelligence Skills

**Transform team workflow data into actionable insights**

A composable skill ecosystem that analyzes code, tickets, communication, and test data to surface patterns for retrospectives, release decisions, and process improvement.

---

## What This Does

**For Sprint Retrospectives:**
Get data-driven observations about cycle time, WIP, blockers, and team patterns - formatted as importable cards for Miro/Mural/Jira boards.

**For Release Decisions:**
Aggregate signals from test coverage, open incidents, code stability, and schedule status to inform go/no-go decisions.

**For Continuous Improvement:**
Track trends over time, correlate metrics across tools, identify process bottlenecks before they become problems.

---

## Architecture

```
┌─────────────────────────┐
│   INPUT SKILLS          │  ← Fetch/parse individual data sources
│  (Modular Collectors)   │
├─────────────────────────┤
│ • git-audit             │
│ • test-report           │
│ • story-flow            │
│ • standup-notes         │
│ • risk-issue-tracker    │
│ • schedule-forecast     │
└───────────┬─────────────┘
            │
            ↓
┌─────────────────────────┐
│   AGGREGATORS           │  ← Synthesize insights for specific use cases
│  (Analysis & Synthesis) │
├─────────────────────────┤
│ • sprint-retro-participant
│ • release-readiness     │
└───────────┬─────────────┘
            │
            ↓
┌─────────────────────────┐
│   OUTPUTS               │  ← Formatted for your tools
├─────────────────────────┤
│ • JSON (board import)   │
│ • CSV (spreadsheets)    │
│ • Markdown (docs)       │
└─────────────────────────┘
```

**How it works:**
1. **Input skills** are modular - each handles one data source
2. **Aggregator skills** combine inputs for specific decisions
3. **Outputs** adapt to your workflow tools

---

## Available Skills

### Aggregators (Use These)

| Skill | Purpose | Status |
|-------|---------|--------|
| `sprint-retro-participant` | Generate factual observations for retrospectives | ✅ Ready |
| `release-readiness` | Synthesize signals for release decisions | 🚧 Planned |

### Input Skills (Building Blocks)

| Skill | Data Source | Status |
|-------|-------------|--------|
| `git-audit` | Code commits, PRs, review patterns | 🚧 Planned |
| `test-report` | Test coverage, failures, flakiness | 🚧 Planned |
| `story-flow` | Ticket progression, cycle time, WIP | 🚧 Planned |
| `standup-notes` | Daily standup patterns, blockers | 🚧 Planned |
| `risk-issue-tracker` | Incidents, bugs, production issues | 🚧 Planned |
| `schedule-forecast` | Sprint burndown, velocity trends | 🚧 Planned |

*Input skills can be used standalone or composed by aggregators*

---

## Quick Start

### Scenario 1: Your First Retro Analysis

**Without MCP servers (manual input):**
```
Use sprint-retro-participant skill.

Sprint 42 data:
- 8 stories completed (previous 3 sprints avg: 10)
- Cycle time: 5 days (previous avg: 3.8 days)
- WIP peaked at 7 (team norm: 5)
- 4 blockers logged

Team norms:
- WIP limit: 5 stories
- Cycle time: <4 days
```

**With MCP servers (automated):**
```
Use sprint-retro-participant skill to analyze sprint 42.
Fetch from Jira and GitHub.
```

See [CONNECTORS.md](./CONNECTORS.md) for MCP setup.

---

### Scenario 2: Release Go/No-Go (Future)

```
Use release-readiness skill for v2.3 release.
Check: test coverage, open P0/P1 issues, code churn, schedule status.
```

---

## Data Sources & Connectors

These skills work **with or without** automated data fetching:

- ✅ **With MCP servers:** Auto-fetch from Jira, GitHub, Slack, etc.
- ✅ **Without MCP:** Paste sprint reports, CSV exports, screenshots

**See [CONNECTORS.md](./CONNECTORS.md) for:**
- MCP server setup guides
- Supported tools (Jira, GitHub, Linear, Slack, etc.)
- Manual input formats
- API alternatives

---

## Use Cases

### Development Teams
- Sprint retrospectives with cycle time analysis
- Release readiness checks (test coverage, open bugs)
- PR review bottleneck detection
- Code quality trend tracking

### Non-Dev Teams (OCM, Marketing, Operations)
- Campaign iteration analysis (ADKAR, marketing sprints)
- Training material production cycles
- Cross-functional dependency tracking
- Approval workflow pattern analysis

### Leadership
- Multi-team health dashboards (future)
- Process improvement trend analysis
- Resource allocation insights

---

## Design Principles

**1. Modularity**
Input skills are independent building blocks. Use them standalone or combine via aggregators.

**2. Graceful Degradation**
Works with whatever data you have - full automation, partial automation, or manual input.

**3. Factual Voice**
No opinions or fluff. Observations start with `[DATA]` and include percentage comparisons.

**4. Tool Agnostic**
Adapts to Jira, Linear, GitHub, GitLab, Asana - whatever your team uses.

**5. Actionable Format**
Outputs are ready to import into retro boards, spreadsheets, or documentation.

---

## Roadmap

**Phase 1 (Current):**
- ✅ `sprint-retro-participant` aggregator
- 🚧 First input skills (story-flow, standup-notes)

**Phase 2:**
- 🚧 `release-readiness` aggregator
- 🚧 Remaining input skills (git-audit, test-report, etc.)

**Phase 3 (Future):**
- 📋 Dashboard skill (visualize trends over time)
- 📋 Custom aggregators (team-specific workflows)
- 📋 Multi-sprint comparison tools

---

## Contributing

**Building an input skill?**
Follow this pattern:
1. Single responsibility (one data source)
2. Accept manual input OR auto-fetch
3. Output structured data (other skills can consume it)
4. Document expected data format

**Building an aggregator?**
1. Specify which input skills it uses
2. Define the decision/question it answers
3. Maintain factual, comparative voice
4. Provide multiple output formats

---

## Getting Help

**First time using these skills?**
Start with `sprint-retro-participant` and manual input. Once comfortable, add MCP servers for automation.

**Questions about MCP setup?**
See [CONNECTORS.md](./CONNECTORS.md) or search for "Anthropic MCP servers"

**Feature requests or bugs?**
Open an issue in this repository

---

## Example Output

**Sprint Retro Participant:**
```json
{
  "sprint": "Sprint 42",
  "observations": [
    {
      "title": "Cycle Time Degradation",
      "type": "improvement",
      "content": "[DATA] Cycle time increased 37% to 5.2 days (vs 3.8 day baseline). Violates <4 day norm.",
      "change": "+37%"
    }
  ]
}
```

Import this JSON directly to Miro, Mural, or Jira boards for your retrospective.

---

**Version:** 1.0
**Last Updated:** February 2026
**License:** MIT (or your chosen license)

---

## What Makes This Different

Traditional retro prep: 30+ minutes of manually gathering metrics, screenshots, and anecdotes.

With Project Intelligence skills: <5 minutes to generate comparative, multi-source observations ready for discussion.

**Focus shifts from "what happened" to "why it happened and what to do about it."**
