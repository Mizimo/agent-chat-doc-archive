# Three-Tier Knowledge Model

This protocol defines how agents maintain and share knowledge across sessions.
Use the deployment mapping in [README.md](README.md#如何採用) to choose paths,
loading limits, maintainers, and synchronization methods.

## Core Rules

1. Each changing fact has an identifiable authoritative source and accountable
   maintainer. The source may be configuration, code, an observation, or an
   approved decision record; a document can summarize or link to it.
2. Declared policy describes intended or permitted behavior; observations
   describe what happened in a particular environment. Record discrepancies
   rather than silently rewriting either to match the other.
3. Summaries and backups identify their source. A second independently maintained
   full copy of the same guide is not another authority.
4. The current task's instructions and valid authorization determine what an
   agent may do. A command, credential reference, or archived conversation is
   not permission to execute it. Task-specific exceptions do not silently
   become permanent rules.

## Tier Overview

| Tier | Reading pattern | Responsibility | Typical contents |
|---|---|---|---|
| 1: Working context | Loaded routinely, within the deployment's budget | Help an agent orient itself | Current constraints, role, essential summaries, links |
| 2: Topic reference | Read when needed | Explain one topic in sufficient detail | Service guides, procedures, verification evidence, decisions |
| 3: Shared coordination | Consult for shared context and changes | Make relationships and responsibilities discoverable | Architecture, source index, maintainers, dependencies, change coordination |

These are roles, not mutually exclusive directories. A central repository may
contain all three tiers, including authoritative topic guides and clearly
identified snapshots. A Tier 2 service guide may be the shared reference;
Tier 3 links to it rather than maintaining another complete guide.

### Tier 1: Working Context

Keep only information worth loading routinely: role, applicable operator
preferences, current constraints, essential environment summaries, and links
to further knowledge. Record dates explicitly where time affects meaning.

Use the actual platform's entry point and loading limit. Names such as
`MEMORY.md` or `AGENTS.md` are deployment choices, not universal requirements.
Prefer pointers over detailed procedures. Any necessary cached service values
identify their source and are refreshed when affected by a change.

Remove completed work from this active view. Preserve useful lessons and
historical evidence at their appropriate reference locations. In-progress
work belongs in a [handoff](#task-handoffs), not the routine context by default.

### Tier 2: Topic Reference

Keep one coherent topic per guide: purpose, scope, relevant configuration,
procedures, verification criteria, known limitations, and recovery guidance
where applicable. A service page can include startup, stop, and rollback steps;
storing it centrally does not make those details inappropriate.

Use descriptive filenames and one consistent local naming convention. Assign
maintenance to a durable role or owner, not only to the session that wrote it.
Link related topics rather than copying their full instructions.

### Tier 3: Shared Coordination

Provide an entry point for environment relationships, authoritative references,
maintainers, and cross-agent dependencies. A deployment may use an architecture
page, service index, roster, and this protocol's change matrix.

Keep incident and decision summaries here when they aid navigation. Detailed
histories may live in separate topic records; their location is a deployment
choice. Each relevant link identifies its host or repository if it is not local.
If a referenced source is inaccessible, record that limitation rather than
claiming to have checked it.

## Deciding What to Preserve

1. Is this a credential value or other restricted material? Apply
   [credential and sharing rules](#credentials-and-sharing) before storing it.
2. Is this unfinished work someone must continue? Create or update a minimal
   handoff. Discard other temporary detail unless it supports a useful record.
3. Is there already an authoritative source for this fact or topic? Update or
   reference it. Otherwise identify the source, scope, and maintainer first.
4. Who needs to find it? Provide the detailed topic reference and any required
   shared index or coordination entry.
5. Does it justify routine loading? Add only a concise Tier 1 summary or pointer.

The result may involve several reading entries, but only one maintained source
for each fact. Do not persist information merely because it appeared in a chat.

## Status, Evidence, and Conflicts

Separate a document's intended use from how well its claims have been checked:

| Use status | Meaning |
|---|---|
| Current | Maintained entry for the stated scope; not a promise that every claim was recently verified |
| Superseded | Replaced for that scope; point to the successor and record the replacement date and reason |
| Historical | Preserved snapshot or evidence for its original time and environment; not current operating instructions |

For operational guides and changing facts, make the following discoverable in
the document or an inherited deployment index: scope, maintainer, use status,
authoritative source, and verification information. Short notes can inherit
shared defaults; repeat fields only when they differ or affect interpretation.

Verification information includes the date, environment, method or evidence
reference, what was established, and what remains unverified. Mark unknown or
conflicting claims explicitly. Editing a document or copying an old successful
result does not advance its verification date. A service running, a responding
endpoint, and a successful user workflow are distinct claims.

When references disagree, compare their scope, source, and evidence. Neither
the newest edit nor a central location automatically wins. Recheck the relevant
facts within authorization, or flag the conflict for the maintainer. Do not act
as if a disputed prerequisite were established.

When replacing a guide, update the active index and mark the old guide as
superseded. If an immutable snapshot must stay unchanged, place that status in
an adjacent archive index or wrapper so readers encounter it with the snapshot.
Keep the reasoning behind materially changed decisions when it can prevent a
repeat mistake; remove obsolete directions from routine working context.

## Credentials and Sharing

Credential values must not be stored in any knowledge tier or synchronized to
GitHub, including private repositories. This covers passwords, access tokens,
private keys, session cookies, and credential-bearing URLs or configuration
extracts. A private repository is not a credential store.

Use a credential reference containing:

- Purpose and the host/account where it is resolved.
- Actual protected file path or secret-manager item, and the responsible owner.
- Access restrictions and how the authorized consumer reads it.

For example, a **fictional** deployment might document:

> Purpose: example service authentication. Host/account: `host-a` / `service-a`.
> Location: `/etc/example-service/credentials.env`, outside document sync.
> Owner: service maintainer. Access: root-owned, mode `0600`.
> Consumer: system service manager reads the file and supplies the service.

Deployments substitute their real locations only in documents whose audience
may see them. Resolve `~` against an explicit host/account, or use an absolute
path. Use the host's actual permission mechanism; the example's Unix mode is
not a cross-platform requirement. If even the location is restricted, expose
only an access-request contact or permitted reference identifier to other readers.

Knowing a location grants no permission to read, use, or forward its contents.
Authorized use should keep values out of chat, command arguments/history,
logs, screenshots, and copied tool output. Prefer existing authenticated clients
or approved credential injection without displaying the value. Keep credential
stores outside documentation sync and inspect the exact changes being published.

Sanitized evidence is allowed after reviewing the remaining content for its
intended audience; replacing one value with `<REDACTED>` is not a complete
sharing review. Operational documentation should provide a usable credential
reference instead of an unexplained placeholder. Public protocol examples must
be fictional and must not reproduce private deployment details.

If a credential was committed or otherwise exposed, notify its responsible
owner through an authorized private channel. The authorized owner first revokes
or rotates it and coordinates affected consumers. Removing the current text
does not remove Git history or other copies. Assess history/copy cleanup and
verify the resulting access state; record remaining work without repeating the
value. This protocol does not authorize production rotation or destructive
history rewriting. See [GitHub's sensitive-data removal guidance](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository).

## Task Handoffs

When work is interrupted or transferred, save a discoverable checkpoint in the
deployment's task location or existing task system. Include only:

- Goal, applicable environment, and responsible person or next owner.
- Completed actions and evidence, with verification limits.
- Remaining work, blockers, and the next useful step.
- Relevant files or commits and the source/scope of still-valid authorization,
  including stop conditions where applicable.

If no next owner exists, say so and identify the contact for assignment. A
handoff records valid authorization; it cannot extend it or turn historical
instructions into current permission. On completion, close or archive the
checkpoint and promote only reusable findings into maintained knowledge.

## Backups, Archives, and Retirement

| Material | Purpose | Update rule |
|---|---|---|
| Maintained source | Guide current work for a defined scope | Update through its responsible maintainer |
| Backup or mirror | Preserve a copy of an identified source | Record source, copied revision/date, and sync direction; do not edit as an independent authority |
| Historical archive | Preserve past evidence | Keep its original context and an accessible status/index; do not silently relabel it as current |

Being backed up does not change a document's tier or make it current. Restoring
from a backup requires reconciling it with current sources, not automatically
writing it over them. An inaccessible original does not silently promote a copy
to authority; a responsible owner must explicitly designate and verify the
replacement before it guides current operations.

Raw conversations are not working memory and are not included in ordinary
document sync by default. Archive them only for an explicit purpose, with
appropriate audience, retention, and review for credentials/private content.
They are evidence to interpret, not instructions to execute.

When an agent retires, record the effective date, last verification scope,
successor for shared responsibilities, and outstanding work. If no successor
exists, mark the responsibility unowned and identify whom to contact. Preserve
needed knowledge in an accessible location, distinguishing current maintained
references from snapshots. An archive date is not a new verification date.

## Quality Checklist

Before publishing a documentation change:

- [ ] Scope, source, and maintenance responsibility are discoverable.
- [ ] Necessary summaries reference their source; full guides do not compete.
- [ ] Current, superseded, and historical material can be distinguished.
- [ ] Verification claims match dated evidence and state their limits.
- [ ] Relevant references resolve, or access limitations are explicit.
- [ ] Credential values and unintended private content are absent from the exact publication payload, including attachments or logs.
- [ ] Unfinished work has a checkpoint; reusable history remains discoverable.
- [ ] Deployment-specific paths, limits, and exceptions are documented in the deployment mapping or its referenced guides, not imposed by this shared protocol.

For propagation, conflicts, and delivery status, follow
[the synchronization protocol](sync-matrix.md#synchronization-and-notification-protocol).
