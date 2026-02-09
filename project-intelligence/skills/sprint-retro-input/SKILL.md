---
name: sprint-retrospective-input
description: Generates data-driven sprint observations with cycle time focus - adapts to available tools or manual input
version: 1.0
---

# Sprint Retrospective Input

Analyze sprint performance and generate factual, comparative observations formatted for import into retrospective boards.

---

## How It Works

**Step 1: Gather Sprint Data**

First, check what data sources are available:
- Attempt to fetch from sprint tools (Jira, Linear, etc.)
- Attempt to fetch from code repos (GitHub, GitLab)
- Attempt to fetch from team communication (Slack)

If tools unavailable, ask user for: sprint reports, CSV exports, screenshots, or pasted data.

**Always tell the user which sources you're using:**
> "Fetching from GitHub and Jira. I don't have Slack access - share standup notes if you'd like them included."

**Step 2: Collect These Metrics**

**Required (get from any source):**
- Stories/tickets completed
- **Cycle time** (start → done) - PRIORITY METRIC
- Blockers/impediments count
- Work in progress

**Optional (team decides):**
- PR review time
- Deployment frequency
- Sprint goal achievement %
- Communication patterns
- Any team-specific metrics

**Sprint Scope:**
- Current sprint (being analyzed)
- Previous 3 sprints (for comparison)

**Step 3: Generate Observations**

### Analysis Rules

**ALWAYS Compare**
Format: `[Metric] is [value] (±X% vs previous 3 sprint avg of [baseline])`

Never: "Cycle time is 45 hours"
Always: "Cycle time is 45hr (+25% vs previous 3 sprint avg of 36hr)"

**Prioritize Cycle Time**
If data is limited, cycle time analysis comes first. Other metrics are secondary.

**Cross-Reference When Possible**
If multiple sources available:
- "10 tickets completed (Jira) but 7 PRs merged (GitHub) - 3 tickets may be non-code work"

**Check Team Norms (if provided)**
Users can optionally provide team agreements. Flag compliance explicitly:
- "Meets 'cycle time <48hr' norm"
- "Violates 'WIP limit of 4' norm: avg WIP was 5.2"

**Emotionless Voice**
- ❌ "Great improvement!"
- ✅ "[DATA] Cycle time decreased 35%"

Start every observation with `[DATA]` prefix.

**Step 4: Output Format**

Default is JSON for board import:
```json
{
  "sprint": "Sprint 42",
  "baseline": "Sprints 39-41",
  "sources": ["jira", "github"],
  "observations": [
    {
      "title": "Cycle Time Trend",
      "type": "success",
      "content": "[DATA] Cycle time decreased 35% to 31hr (vs 48hr baseline)",
      "change": "-35%"
    },
    {
      "title": "Blocker Spike",
      "type": "improvement",
      "content": "[DATA] Blockers increased to 7 (vs 3 baseline). Violates 'max 5 blockers' norm.",
      "change": "+133%"
    }
  ]
}
```

**Types:** `success`, `improvement`, `risk`, `informational`

Alternative formats on request: CSV, Markdown, plain text.

---

## Edge Cases

**No historical data:**
- Report current sprint only with note: "No baseline available - run this skill regularly to enable comparisons"

**Conflicting data:**
- Flag explicitly: "[DATA] Jira shows 8 completed, GitHub shows 5 merged - investigate gap"

**Partial data:**
- Use what's available: "Analysis from GitHub only - Jira unavailable"

**No team norms:**
- Skip norm checking, focus on trends only

---

## Examples

### Example: Automated
```
User: "Analyze sprint 42"

Claude:
- Fetches Jira sprint 42 → 8 stories, 45hr cycle time
- Fetches GitHub PRs → 12 merged, 6hr review time
- Compares vs sprints 39-41
- Outputs JSON with 5 observations
```

### Example: Manual
```
User: "Here's our sprint summary [paste]. Previous sprints averaged 36hr cycle time."

Claude:
- Parses pasted data
- Generates observations with provided baseline
- Outputs JSON
```

### Example: Hybrid
```
User: "Pull Jira data, I'll give you our deployment stats"

Claude:
- Fetches Jira
- Accepts manual deployment data
- Synthesizes both into observations
```

---

## Optional: Team Norms

Users can provide team agreements to check:
```
"Cycle time < 48 hours"
"WIP limit: 4 stories"
"PR review time < 4 hours"
```

Observations will reference these explicitly.

---

## Getting Started

**For users without MCP servers configured:**
Share sprint data manually - this skill works fine without automation.

**For users who want automated fetching:**
Search for "Anthropic MCP servers" or "Claude MCP [your tool]" to find configuration guides. Common servers: Jira, GitHub, Linear, Slack.

Note: MCP configuration is separate from this skill - consult current Anthropic documentation.

---

## Success Metrics

This skill delivers value when it:
- Reduces retro prep time from 30+ min to <5 min
- Surfaces trends teams would miss manually
- Produces cards ready to import into retro boards
- Maintains factual, actionable observations
