# Three-Tier Knowledge Model

A documentation model for long-running agents that share an environment.
It defines what goes where, who maintains it, and what each tier is for.

## Tier Overview

```
Tier 1: Agent Memory (MEMORY.md)  → Per-agent, self-use, operational context
Tier 2: Topic Files (*.md)        → Per-agent, detailed reference, shareable
Tier 3: Centralized Docs          → Cross-agent, single source of truth
```

## Tier 1: Agent Memory (MEMORY.md)

**Purpose**: Operational context that the agent needs in every session.
**Audience**: The agent itself (loaded into the system prompt automatically).
**Size target**: Under 200 lines (truncated beyond that).

### What belongs here

- Agent identity and role
- Key contacts (other agents, operators)
- Environment map (hosts and services this agent interacts with)
- Services this agent manages (names, ports, paths)
- Groups and communication channels
- Active runtime rules (e.g. "never enter plan mode")
- Lessons learned (actionable, not just "X happened")
- Operator preferences that affect behavior
- Links to Tier 2 topic files

### What does NOT belong here

- Full deployment guides (→ Tier 2)
- Architecture details beyond what this agent needs (→ Tier 3)
- Session-specific context or in-progress work
- Incident narratives (→ Tier 3 incident history)
- Step-by-step procedures (→ Tier 2)

### Style rules

- Terse bullet points, not prose
- Absolute dates (`2026-04-30`), never relative ("today", "recently")
- Merge duplicates — one entry per topic
- Delete completed TODOs and resolved items
- Link to Tier 2 files for details: `[topic_file.md](topic_file.md) — brief description`

## Tier 2: Topic Files

**Purpose**: Detailed reference on a specific topic, reusable across sessions.
**Audience**: The agent that owns it + any agent or human who needs the information.
**Location**: Same memory directory as `MEMORY.md`, or project-specific paths.
**Naming**: `snake_case.md` — descriptive, e.g. `streaming-setup-guide.md`, `chat-backend-architecture.md`

### What belongs here

- Deployment guides and setup procedures
- Feedback/preferences from operators (one file per distinct preference)
- Project-specific notes (e.g. game setup, app configuration)
- Detailed docs for a specific service or subsystem
- Debugging guides and troubleshooting procedures
- Hardware-specific knowledge (GPU configs, driver notes)

### What does NOT belong here

- Information that changes every session (→ don't persist at all)
- Cross-agent architecture (→ Tier 3)
- Incident history (→ Tier 3)
- Duplicates of what's already in `MEMORY.md`

### Style rules

- Use headers and tables for scanability
- Include a "last updated" date if the content may go stale
- One topic per file — don't combine unrelated subjects
- Prefix with category when helpful: `feedback_`, `project_`, `infra_`

## Tier 3: Centralized Docs

**Purpose**: Single source of truth for cross-agent information.
**Audience**: All agents, operators, and humans.
**Location**: A dedicated repository, synced from the agents' host machines.

### Documents in this tier

| Document | Purpose | Maintainer |
|----------|---------|------------|
| `architecture.md` | Full environment architecture, topology, agent roster, incident history | Designated infra agent (with input from all agents) |
| `sync-matrix.md` | What changed → what docs to update | Designated infra agent |
| `knowledge-model.md` | This document — documentation standards | Designated infra agent |
| `README.md` | Directory index of all synced docs | Designated infra agent |
| `<agent>/*.md` | Each agent's synced Tier 1 + Tier 2 files | That agent |

### What belongs here

- Environment architecture (hosts, network, services)
- Incident history with root cause and fix
- Cross-agent coordination docs (`sync-matrix`, this knowledge model)
- Agent roster and roles
- Network topology and access paths

### What does NOT belong here

- Agent-specific operational context (→ Tier 1)
- Detailed how-to guides for one agent's services (→ Tier 2, synced as backup)

## Decision Flowchart

When you have new information to persist, ask:

```
1. Does every session of MY agent need this?
   YES → Tier 1 (MEMORY.md)
   NO  ↓

2. Is it detailed reference I or others might need later?
   YES → Tier 2 (topic file in my memory directory)
   NO  ↓

3. Does it affect multiple agents or the overall architecture?
   YES → Tier 3 (architecture.md or new centralized doc)
   NO  → Don't persist it. Not everything needs to be written down.
```

## Sync Responsibilities

Each agent's memory directory lives on the machine where that agent runs.
A central backup repository aggregates them:

| Agent | Syncs to backup repo | How |
|-------|---------------------|-----|
| Primary host agent | Directly (local filesystem) | Sync script or manual copy |
| Remote agents | Via the primary host | Scheduled SSH pull from each remote host |

Automated sync typically runs on a weekly schedule; manual sync runs after
significant doc updates.

## Quality Checklist

Before committing any documentation update, verify:

- [ ] No relative dates — all dates are absolute (`2026-04-30`)
- [ ] No duplicates — each fact appears in exactly one place per tier
- [ ] No stale facts — hosts, ports, service names are current
- [ ] No session context — temporary info was not persisted
- [ ] Correct tier — information is at the right level
- [ ] Cross-references updated — if a Tier 1 entry links to Tier 2, the link works
- [ ] Sync triggered — if Tier 2/3 docs changed, sync to the backup repo

## Why three tiers

The split exists because the three audiences have different requirements:

- **Tier 1 is loaded automatically every session**, so it competes for context
  budget. It must stay small, and everything in it must be worth that cost.
- **Tier 2 is read on demand**, so it can be long — but it must be findable,
  which is why one topic per file and descriptive names matter.
- **Tier 3 is shared**, so it must be unambiguous and consistent. It is also
  the only tier where a fact can be wrong for *everyone* at once, which is why
  it has an explicit maintainer and a change-impact matrix.

The model is deliberately conservative about persistence: the flowchart's
default answer is "don't write it down". Most operational detail is only
useful once, and stale notes are worse than missing ones.
