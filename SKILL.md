---
name: atomic-plans
description: |
  Planning system where plans are atomic units of work - executable, reviewable, and coordinated.
  Based on "The plan is the program" - plans are the operating surface, not documentation.

  Use when: (1) Starting work on a project or subproject, (2) Coordinating with other Claude instances,
  (3) Reviewing what's happening across plans, (4) Recording decisions and execution progress.

  Triggers: "use the planning skill", "update the plan", "what's the current plan state",
  "create a plan for", "review plans in [folder]".

  Default location: /Users/tylernishida/Documents/obsidian/tyler
  User may specify: /Users/tylernishida/Documents/obsidian/tyler/Work/Proj-Evals or other paths.
---

# Atomic Plans

Plans are the operating surface for work, not upstream documentation. They encode intent, constraints, and decision history. Multiple Claude instances may work in parallel - plans are the coordination protocol.

**Core principle:** "What is already happening?" not "What do you need to do?"

## Starting Work

Before ANY work on a project:

1. Check if `_index.md` exists in the target folder
2. If yes: read it to understand current state, find your plan or create new one
3. If no: create the index and first plan
4. Update your plan's state to `active` before starting work
5. Update execution trail as you work

## Folder Structure

```
<target-folder>/
├── _index.md              # Project overview, all plans, what's happening
├── 001-<name>.md          # Plans numbered sequentially
├── 002-<name>.md
└── <subproject>/          # Nested plans for sub-scope
    ├── _index.md
    └── 001-<name>.md
```

## Index File Template

```markdown
---
project: <name>
updated: <YYYY-MM-DD>
---

# <Project Name>

## What's Happening Now
<!-- Active work across all plans. Update this section frequently. -->

## Plans
| # | Plan | State | Focus | Updated |
|---|------|-------|-------|---------|

## Subprojects
<!-- Links to subproject indexes -->
```

## Plan File Template

```markdown
---
plan: <NNN>
title: <title>
state: provisional
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
---

# <Title>

## Intent
Why this plan exists. What outcome we're driving toward.

## Constraints
Known limitations, requirements, dependencies, boundaries.

## Decisions
| Decision | Rationale | Date |
|----------|-----------|------|

## Execution Trail
### <YYYY-MM-DD>
- What happened
- What was observed
- What changed

## Next
- [ ] Concrete next steps

## Coordination
<!-- Notes for other Claude instances -->
```

## Plan States

| State | Meaning |
|-------|---------|
| `provisional` | Draft, not yet committed to |
| `active` | Currently being executed |
| `blocked` | Waiting on external dependency |
| `completed` | Finished, outcomes achieved |
| `discarded` | Abandoned, superseded, or no longer relevant |

## Multi-Instance Coordination

When working alongside other Claude instances:

1. **Read index first** - understand what's already happening
2. **Claim your work** - set plan state to `active`, note in Coordination section
3. **Update frequently** - execution trail shows progress, helps others avoid conflicts
4. **Signal completion** - update state, summarize outcomes in trail
5. **Leave breadcrumbs** - Coordination section: what you touched, what to avoid, what's next

## Operations

**Create new plan:**
1. Read `_index.md` to get next number
2. Create `<NNN>-<kebab-name>.md` with template
3. Add row to index Plans table
4. Update "What's Happening Now" if starting immediately

**Update existing plan:**
1. Add entry to Execution Trail with date
2. Update `updated:` in frontmatter
3. Modify Next section as needed
4. Update index "What's Happening Now" if significant

**Complete plan:**
1. Final Execution Trail entry with outcomes
2. Set `state: completed`
3. Clear Next section or note follow-ups
4. Update index table and "What's Happening Now"

**Review plans:**
1. Read `_index.md` for overview
2. Check "What's Happening Now" for active work
3. Scan Plans table for states
4. Read specific plans for detail

## Execution Trail Format

The trail is append-only history. Each entry:

```markdown
### YYYY-MM-DD
- Completed: what was done
- Evidence: concrete references captured (see Evidence Capture below)
- Observed: patterns/conclusions drawn FROM the evidence
- Decided: any new decisions (add to Decisions table too)
- Blocked: any blockers encountered
- Next: immediate next steps
```

Keep entries concise. The trail answers "what happened" for anyone reviewing later.

## Evidence Capture (Critical)

**Core principle:** Capture evidence AS YOU GO, not just conclusions.

Observations without evidence are unverifiable and unreviewable. Conclusions drawn from exploration are only as credible as the evidence supporting them.

### The Rule

Every observation or conclusion should be traceable to specific, concrete evidence. Record references **as you encounter them**, not retroactively.

### What Counts as Evidence

Evidence is anything concrete and referenceable that supports an observation:

- **Quotes** - exact words from a person, document, or output
- **References** - file:line, URLs, document sections, page numbers
- **Data points** - specific numbers, metrics, counts, timestamps
- **Identifiers** - trace IDs, ticket numbers, commit hashes, record IDs
- **Test results** - what you tried, exact result observed

The format adapts to your project. The requirement is: **someone reviewing later can verify or revisit what you found.**

### Exploration Session Template

When doing research, investigation, or exploration:

```markdown
### YYYY-MM-DD - Exploration: <topic>

**Evidence Collected:**
| Source | Reference | Finding |
|--------|-----------|---------|
| <type> | <specific ref> | <what it shows> |

**Conclusions:**
- <conclusion> (based on: <which evidence>)

**Gaps:**
- [ ] Couldn't verify X - need Y
```

### Examples by Project Type

**Technical/Code Debugging:**
```markdown
**Evidence Collected:**
| Source | Reference | Finding |
|--------|-----------|---------|
| LangSmith | trace `abc123` 01/15 14:32 | Tool call returned empty, no error raised |
| Code | `handler.py:89` | except block swallows error silently |
| Logs | 01/15 14:32:01 | No error entry for this request |

**Conclusions:**
- Silent failure pattern: errors swallowed at handler.py:89 (trace abc123 confirms)
```

**User Research:**
```markdown
**Evidence Collected:**
| Source | Reference | Finding |
|--------|-----------|---------|
| Interview | User 3, 01/12 | "I didn't know the button was clickable" |
| Analytics | Mixpanel Jan report | 23% click-through on CTA |
| Support | Ticket #4521 | User couldn't find settings |

**Conclusions:**
- CTA visibility issue: low click-through + direct user feedback about button
```

**Market/Competitive Research:**
```markdown
**Evidence Collected:**
| Source | Reference | Finding |
|--------|-----------|---------|
| Competitor | acme.com/pricing (accessed 01/15) | $49/mo base tier |
| Report | Gartner 2024 Q3, p.12 | Market growing 15% YoY |
| Interview | Sales call 01/10 | Prospect comparing us to Acme |

**Conclusions:**
- Price pressure from Acme ($49 vs our $59) mentioned in sales call
```

### Anti-pattern

```markdown
# BAD - conclusion without evidence
- Observed: The retry logic seems inconsistent across services

# GOOD - evidence first
- Evidence:
  - `services/email.py:45` - 3 retries, exponential
  - `services/sms.py:32` - 5 retries, fixed
  - `services/push.py:28` - no retries
- Observed: Inconsistent retry logic (email/sms have it, push doesn't)
```

### Key Discipline

**Capture evidence as you go, not just observations.** When exploring and you find something interesting, immediately note the specific reference—don't just remember "I saw something about X."
