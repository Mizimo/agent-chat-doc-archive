# Sync Matrix: What Changed → What Docs to Update

When unsure which documentation needs updating after a change, consult this table.

## Changes → Documentation Updates

| What changed | Who updates | What to update |
|---|---|---|
| New/changed systemd service | Agent on that host | Own `MEMORY.md` (service list) → notify the infra-doc maintainer to update `architecture.md` |
| SSH path/key/port change | Agent who discovered it | Own `MEMORY.md` (SSH section) + notify all agents holding access paths → `architecture.md` network topology |
| New agent deployed | Deploying agent | `architecture.md` (agent roster) + each related agent's `MEMORY.md` (contacts) |
| Hardware change (GPU, RAM, PSU, DIMM) | Local agent | Own `MEMORY.md` → `architecture.md` (host specs + hardware history) |
| Network/firewall rule change | Agent who made the change | `architecture.md` (topology) + affected agents' `MEMORY.md` |
| Tunnel added/changed/removed | Agent on the tunnel client host | `architecture.md` (network section) + all agents routing through that tunnel |
| Proxy config change | Local agent | Own `MEMORY.md` + any agent depending on that proxy |
| Chat-backend change | Backend agent | All agents' `MEMORY.md` (chat section) if their client config is affected |
| New monitoring endpoint | Agent who built it | `architecture.md` (monitoring section) + operator notification |
| Incident resolved | Responding agent | `architecture.md` (incident history) + own `MEMORY.md` (lessons learned) |
| Cron job / automation added | Agent who set it up | Own `MEMORY.md` + centralized docs if cross-agent |
| Container added/changed | Local agent | Own `MEMORY.md` (services) → `architecture.md` if significant |
| Operator preference change | Agent who received it | Own `MEMORY.md` (operator preferences) + broadcast to other agents if universal |

## Memory Hygiene Rules

Apply these whenever editing any agent's memory files:

| Situation | Action |
|---|---|
| Stale fact (outdated host, old service name, etc.) | Fix immediately, update all references |
| Relative date ("today", "recently", "just now") | Convert to absolute: `2026-04-30` |
| Duplicate records (multiple entries about the same thing) | Merge into one, keep the most complete version |
| Completed TODO / resolved pending item | Delete — memory is not a changelog |
| Overturned decision | Delete the old entry, keep only the current decision |
| Temporary session context | Delete — it doesn't belong in persistent memory |
| Lesson learned from an incident | Keep, but ensure it has actionable guidance, not just "X happened" |

## Cross-Agent Impact Checklist

These are the changes most likely to require updates across multiple agents:

- **Tunnel configuration** → affects every agent that routes through the tunnel
- **Shared proxy** → affects every agent behind it, plus container configs that inherit it
- **Chat-backend API/auth** → affects all agents' client configs and credential files
- **SSH key rotation** → affects every agent holding stored access paths
- **Shared relay host changes** → affects tunnels, DNS records, and all remote access paths
- **Shared host service restart** → affects every agent hosted on it, plus the chat bridge and monitoring

**Rule of thumb**: If the change touches something shared (SSH keys, tunnels,
proxy, chat API, DNS), search every agent's `MEMORY.md` for references to it.

## Notification Protocol

After making changes that affect other agents:

1. Update your own docs first
2. Message affected agents directly with the specific change and what they need to update
3. Post to the relevant group if it affects three or more agents
4. Inform the operator for significant infrastructure changes
5. Trigger a sync to the central docs repository if docs were updated

## Anti-Patterns

Observed failure modes worth designing against:

| Anti-pattern | Why it hurts |
|---|---|
| Two copies of the same guide that drift apart | Audits only process one of them; the other becomes an unchecked copy |
| Placeholder-style redaction before sharing | Replacing values with `<REDACTED>` keeps the *structure* — ports, host roles, and topology remain readable, and the fact that "something real was here" is itself information |
| Recording incidents as narrative only | "X broke" without cause and fix cannot guide future action |
| Letting Tier 1 grow unbounded | It is loaded every session; every stale line costs context on every turn |
| Undated facts | You cannot tell current from obsolete without an absolute date |
