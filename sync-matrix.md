# Sync Matrix: What Changed → What Docs to Update

Use this matrix to identify affected sources and readers before a change, then
to complete its documentation and handoff. File names below describe roles;
resolve them through the deployment mapping in [README.md](README.md#如何採用).

## Changes → Documentation Updates

| What changed | Responsible role | Sources and entries to review |
|---|---|---|
| Service or container added, changed, or removed | Service maintainer | Service guide, shared index/dependencies, and affected Tier 1 summaries; mark retired guides rather than silently deleting useful history |
| Service port, binding, or endpoint | Service maintainer | Authoritative configuration and guide, shared access map, client references, cached working-context values |
| SSH access path, key reference, or port | Access maintainer | Scoped access references, topology, and dependent clients; apply credential rules |
| Credential rotated, relocated, or access changed | Authorized credential owner | Protected store, reference/permissions, affected consumers, and verification status; never copy the value into docs or notifications |
| Hardware, OS, or driver changed | Host maintainer | Host reference, dependent service assumptions, and affected verification results |
| Network, firewall, tunnel, or proxy changed | Network/service maintainer | Topology, affected service/client guides, access boundaries, and recovery dependencies |
| Shared API or authentication contract changed | API maintainer | API reference, client configuration references, and consumers that must adopt the change |
| Monitoring or automation changed | Service maintainer | Endpoint or job reference, ownership, schedule, alerts, and shared dependencies |
| Incident resolved or a conclusion overturned | Responding maintainer | Current guidance, evidence and decision/incident record, affected summaries; mark replaced conclusions |
| Guide replaced or moved | Topic maintainer | Successor guide, old guide's status or archive wrapper, index, and inbound references |
| Agent deployed, moved, or retired | Deployment owner | Roster, host/account mapping, shared responsibilities, handoffs, archive status, and sync/access ownership |
| Work interrupted or transferred | Current task owner | Minimal checkpoint with evidence, remaining work, next owner or unowned status, and valid authorization scope |
| Operator preference or local exception changed | Receiving agent and relevant maintainer | Applicable scope and source in deployment/working context; notify only those affected |
| Protocol version or deployment mapping changed | Protocol/deployment maintainer | Adopted version, local differences, affected responsibilities and reading/sync paths |

## Knowledge Maintenance Rules

| Situation | Action |
|---|---|
| Stale current claim | Recheck as needed, correct its maintained source, and refresh affected summaries; do not rewrite historical evidence as though it were newly observed |
| Conflicting references | Compare scope and evidence, flag unresolved facts, and coordinate through the responsible maintainer; do not pick a winner by edit date alone |
| Relative date in a maintained claim | Use an absolute date and, when timing matters, timezone; preserve original dates in historical records |
| Competing full guides | Choose and label the maintained source; redirect other entries or mark them superseded/historical |
| Completed task or resolved pending item | Remove from active context; close the checkpoint and retain useful evidence or lessons in a reference/archive |
| Overturned decision | Update current guidance, link the replacement, and preserve relevant reasoning/history |
| Unverified claim later checked | Record evidence, date, environment, and established scope; do not promote unrelated claims to verified |
| Temporary session detail | Discard unless needed for a minimal handoff or useful evidence record |
| Credential value in published material | Follow the exposure procedure in [knowledge-model.md](knowledge-model.md#credentials-and-sharing); deleting the latest line alone is insufficient |
| Source unavailable or owner retired | Record the limitation, successor or unowned status, and outstanding work; do not quietly promote an old copy to current authority |

## Cross-Agent Impact Check

For shared changes, identify the consumers before deciding which summaries and
notifications need updating. In particular, check:

- Network paths, tunnels, proxies, DNS, and shared hosts used by other services.
- API/authentication changes and credential rotation affecting client access.
- Host restarts, drivers, or device permissions shared by several services.
- Moved guides, retired agents, and synchronization sources that other readers rely on.

Search the relevant guides, shared index, and working-context references, not
only one agent's memory file. Record important consumers that cannot be checked
or contacted. A backup or archive copy is not an active consumer unless the
deployment explicitly uses it for current work.

## Synchronization and Notification Protocol

1. **Establish scope.** Identify the authorized work, authoritative sources,
   responsible maintainers, and affected consumers. Check the starting revision
   before editing shared documents; preserve unrelated changes.
2. **Update and verify.** Update the maintained source and necessary summaries.
   Record what actually changed, the verification evidence and its limits, and
   any discrepancy between intended and observed state.
3. **Coordinate conflicts before publication.** Recheck the source and destination
   revisions before publishing. If either changed concurrently, reconcile the
   relevant edits and recheck affected claims with the maintainer before retrying.
   Do not force overwrite a shared source or destination, or copy an older backup
   over it to make sync pass. A conflict detected during publication returns to
   this step; preserve the unpublished work.
4. **Review and publish.** Review the exact publication payload using the
   [quality checklist](knowledge-model.md#quality-checklist). Commit only intended
   files. Publish/sync in the direction declared by the deployment and confirm
   the intended revision reached the destination; seeing a filename is not
   sufficient confirmation.
5. **Notify affected readers.** Within existing communication authorization,
   identify the change, scope, source/revision, required action, and known limits.
   Use an appropriate channel for its sensitivity. Significant infrastructure
   changes also reach the responsible operator. Reader count alone does not
   require a group broadcast. If notification is not authorized, include that
   dependency in the handoff rather than treating this protocol as permission.
6. **Track necessary adoption.** Require an `adopted` or `blocked` response only
   from consumers that must act. A delivered message is not adoption; an adoption
   response identifies the applied revision and relevant verification outcome.
   Keep offline or blocked consumers pending with an owner/contact. Routine
   editorial updates do not require universal acknowledgement.
7. **Report delivery state.** Distinguish the actual operation, verification,
   document update, publication, and required consumer adoption. Report completed,
   failed, pending, or not-applicable parts accurately. Publication may succeed
   while rollout remains pending; do not claim the whole change is adopted.

For significant changes, complete the necessary source updates and propagation
within the work session when possible. A periodic backup does not substitute
for notifying consumers of an immediately relevant change. If interrupted,
blocked, or unable to publish, leave a discoverable checkpoint with the local
revision, destination, pending steps, and owner/contact. Do not claim success,
discard the work, or erase evidence of the incomplete state.

The deployment chooses its sync mechanism and schedule. A single-agent setup
may need only a source update and operator handoff; it does not need to invent
other agents or acknowledgement traffic.

## Anti-Patterns

| Anti-pattern | Why it hurts |
|---|---|
| Two independently maintained copies of one guide | Readers cannot tell which version governs their scope |
| Treating every central-repository file as current authority | Backups and old conversations can silently become operating instructions |
| Treating a newer edit as stronger evidence | Formatting changes can hide stale or conflicting facts |
| Calling a service verified because its process is running | The relevant client workflow may still be untested |
| Publishing a masked credential without reviewing the rest | URLs, surrounding output, or other fields may still disclose restricted information |
| Deleting current plaintext and declaring an exposure resolved | Old commits and copies may still contain a usable credential |
| Replacing history with today's conclusions | Future readers lose the evidence and reason for a changed decision |
| Letting Tier 1 grow without a loading budget | Repeated and stale detail consumes context every session |
| Equating push success or message delivery with adoption | Consumers may still be using the old configuration or reference |
| Copying a task-specific exception into permanent rules | A later session can inherit a restriction or permission that no longer applies |
