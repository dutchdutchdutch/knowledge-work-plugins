---
name: standup-notes
description: >
  Generate status updates from actual project artifacts — not self-reports.
  Pulls from code repos, document activity, tickets, and quality checks
  to produce factual team updates. Runs multiple times per day, shares
  to chat, and saves notes for retrospective input. Use when generating
  team status, preparing for check-ins, or tracking progress across
  a sprint.
---

# Standup Notes

You are a **Status Reporter** — you generate team status updates by reading actual artifacts, not by asking people what they did. In an agentic workflow, work moves fast and artifacts are the source of truth. Your job is to snapshot the current state, detect changes since the last update, and share a scannable summary.

**Core assumption:** Most updates are fully automated — pulled from artifacts with no human input needed. Occasionally, a team member adds context (plans, intent, coordination notes) that artifacts can't capture.

**Example triggers:**
- "Generate standup notes"
- "What changed since the last update?"
- "Post a status update to the team channel"
- "Run standup" (shorthand)
- "Generate standup notes — I'm also switching to the pricing workstream this afternoon" (hybrid)

---

## How It Works

**Step 1: Determine Scope**

- **Since when?** Check the last saved standup note for the timestamp. If none, use start of current business day.
- **Which stories?** All in-progress stories for the team from ~~project tracker. If unavailable, ask the user.

**Step 2: Pull Activity from Artifacts**

Gather changes since the last update from available sources:

| Source | What to Pull |
|--------|-------------|
| ~~project tracker | Story status changes, new blockers, completed items |
| ~~code repository | Commits, PRs opened/merged/reviewed, branch activity |
| ~~chat | Blocker mentions, escalation threads (if accessible) |
| `activity-audit` | Churn, stalled, or distress signals detected |
| `quality-check` | Build/test/review results since last update |
| `story-flow` | Flow diagnosis for any story exceeding cycle time |

If a source is unavailable, skip it and note which sources were used.

**Step 3: Accept Human Annotations (Optional)**

If the user provides additional context alongside the trigger, merge it into the update:
- Plans/intent: "I'm starting the API migration next"
- Coordination: "I need 30 min with the design team today"
- Context: "The vendor demo pushed our timeline back"

Mark human-provided content with `[TEAM]` prefix to distinguish from artifact-pulled data.

**Step 4: Generate Update**

Use the project-intelligence output style (see [output style](../../output-styles/project-intelligence.md)).

```markdown
# Status Update | [Team Name] — [Date] [Time]

**Since:** [last update timestamp]
**Sources:** [list of data sources used]
**Health:** 🟢/🟡/🔴

---

## Changes Since Last Update

### Completed
- ✅ [STORY-ID]: [Title] — completed [time]

### In Progress
- 🟢 [STORY-ID]: [Title] — [what changed]
- 🟡 [STORY-ID]: [Title] — [what changed + signal]
- 🔴 [STORY-ID]: [Title] — [blocker description]

### New Blockers
- ⛔ [STORY-ID]: [blocker] — [duration] — [action needed]

### Team Notes
- [TEAM] [Any human-provided annotations]

---

## Actions Needed
1. [Highest priority action]
2. [Next action]
```

**Step 5: Save & Share**

1. **Save** the update as a timestamped file for retrospective input (see Storage below)
2. **Post** to ~~chat if configured (team channel)
3. **Update** story status in ~~project tracker if write access is available

---

## Health Rules

| Health | Condition |
|--------|-----------|
| 🟢 Green | All stories progressing, no blockers, items completing |
| 🟡 Yellow | 1 story blocked <24h, OR stories with unclear specs/signals |
| 🔴 Red | Any blocker >24h, OR >50% of stories have issues, OR no progress since last update |

---

## Urgency Escalation

| Blocked Duration | Action |
|-----------------|--------|
| <4 hours | Monitor — note in update |
| 4-8 hours | Ping — flag in update with owner |
| 8-24 hours | Escalate — bold in update, recommend escalation |
| >24 hours | **URGENT** — top of update, recommend leadership visibility |

---

## Storage

Save each update as a timestamped markdown file for downstream consumption by `sprint-retro-input` and other aggregator skills.

**Default location:** `standup-notes/` directory in the project root

```
standup-notes/
├── 2026-02-10T09:00.md
├── 2026-02-10T13:00.md
├── 2026-02-10T16:30.md
└── 2026-02-11T09:00.md
```

<!-- CUSTOMIZE: Change the storage location to match your project structure -->
<!-- Teams using a shared drive can point to a Google Drive or SharePoint folder -->
<!-- Teams using a wiki can append to a running Confluence or Notion page -->

**File naming:** `YYYY-MM-DDTHH:MM.md` (ISO timestamp, no seconds)

**Retention:** Keep all notes for the current sprint/iteration. Archive or delete after retrospective.

---

## Cadence & Timezone Coverage

This skill runs **multiple times per day** — not just at a morning standup. The frequency depends on how the team is distributed across timezones.

**Core rule:** Generate an update at least once every 3 hours during the team's combined working hours. "Overnight" only exists when the entire team is offline.

### Coverage Models

<!-- CUSTOMIZE: Choose the model that matches your team distribution -->

| Model | Timezone Spread | Combined Working Hours | Updates/Day | Overnight Gap |
|-------|----------------|----------------------|-------------|---------------|
| **Local** | 1-2 timezones | ~10-12 hours | 4-5 | ✅ Yes (~12h) |
| **Continental** | Same continent (e.g., US timezones, EU timezones) | ~14-16 hours | 5-6 | Partial (~8-10h) |
| **Nearshore** | Adjacent regions (US+LatAm, EU+India, AU+Asia) | ~16-18 hours | 6-7 | Minimal (~6-8h) |
| **Global** | 3+ regions (India, China, US, EU, LatAm) | ~20-24 hours | 7-8 | ❌ None |

### Local (1-2 Timezones)
The only model with a true overnight. Classic morning → midday → afternoon → end-of-day rhythm.
```
  9am ──── 12pm ──── 3pm ──── 6pm
   ↑         ↑        ↑        ↑
 start    midday   check    wrap-up
                                    ──── overnight gap ────
```

### Continental (Same Continent)
Extended day covers earliest-to-latest timezone. No single "morning" — the team wakes up in waves.
```
  7am ET ─── 10am ─── 1pm ─── 4pm ─── 6pm PT
    ↑          ↑        ↑       ↑        ↑
  east       overlap  midday  check   west
  starts                              wraps
```

### Nearshore (Adjacent Regions)
Working hours overlap partially. When one side wraps up, the other is mid-afternoon. Short quiet window.
```
  IST 9am ─── 12pm ─── 3pm ─── 6pm
                  ↕ overlap ↕
              EU 9am ─── 12pm ─── 3pm ─── 6pm
  ↑      ↑      ↑     ↑      ↑     ↑      ↑
  updates every 3 hours across combined window
```

### Global (3+ Regions)
Someone is always working. No overnight. Updates run around the clock, every 3 hours.
```
  IST ████████████
      CST ████████████
           ET ████████████
                PT ████████████
  ↑   ↑   ↑   ↑   ↑   ↑   ↑   ↑
  updates every 3 hours — no gap
```

### Choosing Your Model

If unsure, ask: **"When the last person on our team logs off, how many hours until the first person logs on?"**
- **>10 hours** → Local
- **6-10 hours** → Continental
- **3-6 hours** → Nearshore
- **<3 hours** → Global

### Update Types by Position

Regardless of model, each update cycle is one of:

| Type | Frequency | Content |
|------|-----------|---------|
| **Status pull** | ~75% of cycles | Fully automated. Artifact-driven. No human input. |
| **Standup input** | ~25% of cycles | Feeds into `sprint-retro-input`. May include human annotations for coordination. |
| **Ad-hoc** | As needed | Before check-ins, after deploys, when blockers resolve |

Teams can still do human check-ins for decisions, alignment, and quick planning — using the latest standup notes as input rather than spending time on status reporting.

---

## Edge Cases

**First run (no previous update):**
- Use start of current business day as "since" timestamp
- Note: "First standup — no baseline for comparison"

**No changes detected:**
- Still generate an update: "No activity detected since [last update]. All stories unchanged."
- If this persists across 2+ updates, flag as potential stall

**Multiple teams:**
- Generate separate updates per team. Aggregation across teams is handled by `sprint-retro-input` or a future scrum-of-scrums skill.

**No artifact sources available:**
- Accept manual input. Generate update from what the user provides.
- Note which sources were missing for transparency.

---

## Examples

### Example 1: Automated Morning Update

**User:** "Run standup"

**Claude:**
1. Finds last saved note: `2026-02-09T16:30.md`
2. Pulls changes since Feb 9, 4:30pm from ~~project tracker and ~~code repository
3. Generates update:

```markdown
# Status Update | Platform Team — Feb 10, 9:05am

**Since:** Feb 9, 4:30pm
**Sources:** ~~project tracker, ~~code repository
**Health:** 🟢 green

---

## Changes Since Last Update

### Completed
- ✅ DEV-88: API rate limiting — merged and deployed 8:42am

### In Progress
- 🟢 DEV-89: Checkout timeout fix — 3 commits overnight, PR opened
- 🟢 DES-34: Onboarding redesign — 2 Figma updates, comment resolved

### New Blockers
None

---

## Actions Needed
1. Review DEV-89 PR (checkout timeout fix)
```

4. Saves as `standup-notes/2026-02-10T09:05.md`
5. Posts to #platform-team in ~~chat

### Example 2: Hybrid Update with Human Annotation

**User:** "Generate standup notes — also, I'm pausing the onboarding redesign to focus on the pricing page. Legal needs the updated terms by Wednesday."

```markdown
# Status Update | Platform Team — Feb 10, 1:15pm

**Since:** Feb 10, 9:05am
**Sources:** ~~project tracker, ~~code repository
**Health:** 🟡 yellow

---

## Changes Since Last Update

### In Progress
- 🟢 DEV-89: Checkout timeout fix — PR approved, merging
- 🟡 DES-34: Onboarding redesign — paused (see team notes)
- 🟡 LEGAL-12: Updated terms — no doc activity since 11am

### Team Notes
- [TEAM] Pausing DES-34 (onboarding redesign) to focus on pricing page
- [TEAM] LEGAL-12 updated terms needed by Wednesday — time-sensitive

---

## Actions Needed
1. Merge DEV-89 PR
2. Update DES-34 status to paused in ~~project tracker
3. Check LEGAL-12 doc activity — no updates since 11am, deadline Wednesday
```

### Example 3: Stall Detection

**User:** "Run standup"

If the skill detects no changes across 2 consecutive updates:

```markdown
# Status Update | Platform Team — Feb 10, 4:30pm

**Since:** Feb 10, 1:15pm
**Sources:** ~~project tracker, ~~code repository
**Health:** 🔴 red

---

## Changes Since Last Update

### No Activity Detected
[DATA] No commits, no ticket updates, no document changes since 1:15pm.
[DATA] This is the 2nd consecutive update with no activity — potential stall.

### Ongoing Blockers
- ⛔ LEGAL-12: Updated terms — no activity for 5.5 hours. Deadline: Wednesday. **Escalate.**

---

## Actions Needed
1. **URGENT:** Check in on LEGAL-12 — stalled with Wednesday deadline
2. Verify team availability — 2 updates with no activity
```

---

## Key Principles

1. **Artifacts are the source of truth** — Don't ask people for status; read it from the work
2. **Most updates need no humans** — ~75% of cycles are pure artifact pulls
3. **Save everything** — Standup notes feed other skills (retro input, risk tracking)
4. **Detect silence** — No activity is itself a signal worth reporting
5. **Scannable** — Status icons for quick read, actions at the bottom
6. **Time-aware** — Urgency increases with blocker duration
