# Aureate Empyrean — Architecture Checkpoint (2026-11-07)

**Status: temporary authoritative checkpoint of owner-approved decisions from November 7, 2026.**

This document records the architectural direction agreed on that date and the deployment information reported after the previous documentation snapshot. It is the handoff for a later Claude Code session to synchronize canonical architecture documents, module specifications, implementation status and relevant READMEs. This task changes only this checkpoint; it does not implement application functionality or synchronize other documentation.

**Continuation, November 7, 2026.** Extended after initial creation with owner-approved Android, cross-platform, distribution, module lifecycle, manual-update, mandatory pre-update backup and snapshot-retention decisions. Earlier decisions remain in force except where a previously provisional choice is explicitly resolved below.

**Synchronization continuation, November 7, 2026.** Section 4 now records approved independent per-module synchronization, protocol-based compatibility, field-level conflict detection and consolidated multi-device conflict resolution. These resolve earlier architectural uncertainty without selecting wire protocols or claiming implementation.

## 1. Authority, status and scope

Use the repository's status vocabulary:

| Label | Meaning in this checkpoint |
|---|---|
| **Established** | Owner-approved requirements and boundaries. Acceptance does not mean implemented. |
| **Planned** | Future architecture or delivery work needed to realize those requirements. No implementation or release date is implied. |
| **Open** | Technical choices that need design; do not implement a speculative choice as an accepted decision. |

Section 10 separately records **reported implementation and deployment facts**. The future mobile runtime, general ecosystem synchronization, complete NBM capability and shared Recovery Engine described here are target architecture, not claims about today's release.

Approval of the Android product architecture is not evidence that its custom runtime is technically feasible or secure. The Android module runtime, module marketplace and target automated backup/recovery system are not claimed to exist. Proposed implementation approaches and unresolved mechanisms remain distinct from approved requirements and deployed functionality.

For the subjects explicitly addressed here, this checkpoint takes precedence over older statements that conflict with the November 7 decisions. All other established decisions remain in force. In particular, preserve module domain ownership, a domain-ignorant Nexus, stable UUID resource identities, permission-aware references, least privilege, inspectable updates and Janus's independent security boundary.

### Relationship to existing decisions

- [ADR 0001](decisions/0001-module-ownership-and-domain-ignorant-nexus.md) remains binding: a shared runtime is infrastructure, not central ownership of module domain objects.
- [Local-first and sync](concepts/local-first-and-sync.md) currently describes local-first as optional for selected modules and leaves offline interoperability undefined. This checkpoint establishes basic local module operation and same-device offline interoperability as ecosystem direction. It does not claim current server modules already meet it.
- The server-first sequencing in [architecture.md](architecture.md#strategic-sequencing) is refined: useful self-hosted functionality and data safety remain priorities, but mobile/local-first and synchronization implications must be considered before building many more server-only modules. Mobile is not merely a remote dashboard for a required server.
- Docker Compose, the gateway and today's browser UI contracts describe the existing server implementation. They do not select the future mobile runtime or packaging.
- [ADR 0007](decisions/0007-postgresql-default-database.md) remains the server relational-database preference, with its existing exceptions. It neither requires a shared application database nor decides mobile storage.
- Janus's independent clients, code-delivery trust boundary and module-specific product direction in [ADR 0017](decisions/0017-janus-devices-sync-and-clients.md) remain intact. The unified AE application preference does not authorize Nexus to serve, proxy, embed or update executable Janus client code.
- The continuation resolves the backup-before-update policy left Open in [updates](concepts/updates.md) and [backups](concepts/backups.md): every module update requires a verified recoverable backup. It also strengthens the initial update policy from unattended updates not being the default to no automatic module updates. Current implementation behavior is recorded separately in section 10.

## 2. Product vision: Nexus is AE Core

**Established direction.** Aureate Empyrean is a privacy-first, local-first, modular software ecosystem. **Empyrean Nexus is AE Core.** Do not introduce a separate competing AE Core product or platform.

Nexus is more than a web gateway, server dashboard or authentication service. Its long-term responsibility is the shared runtime and local integration platform through which independent AE modules form a coherent ecosystem.

A user must be able to install Aureate Empyrean on a supported mobile device without owning, configuring or connecting to a separate computer or server. The preferred experience is **one Aureate Empyrean application containing independently usable modules**, rather than numerous unrelated application installations, subject to existing security boundaries such as Janus's.

**Planned architecture.** Mobile Nexus should provide:

- A unified AE home screen and module launcher.
- A gallery of available modules.
- One-tap module availability, subject to platform distribution constraints. For the approved Android architecture this means physical installation/removal, not merely activation of executable code bundled with Nexus; distribution mechanisms are distinguished below.
- Local module lifecycle management.
- Shared navigation, identity, permissions, references and integration capabilities.
- Local storage and offline operation.
- Optional synchronization with other AE installations.

Modules remain logically independent, each with its own domain model, data ownership and lifecycle. Sharing one application or runtime does not make Nexus the owner of People, Places, Events, Notes or other module objects.

### Android architecture — approved direction

**Established. Kotlin + Jetpack Compose is the preferred Android technology.** Android must provide a genuinely native UI, not a WebView wrapper. **Nexus is the only required and user-visible Android application.** Individual AE modules must be physically installable and removable within Nexus; do not bundle every module's executable code into the initial Nexus installation. Disabled and uninstalled are distinct states.

Android Nexus will provide a custom module runtime and module manager. Installed modules retain independent domain ownership and local storage, operate offline without an external server, and integrate locally through Nexus contracts. These are approved product requirements, not a validated runtime design. Runtime implementation requires prototyping and security validation before its feasibility or safety can be claimed.

The earlier all-mobile framework uncertainty is resolved for Android's preferred stack only. Exact native package format and loading strategy remain Open. The initial implementation must not be designed around iOS distribution constraints, while avoiding gratuitous barriers to possible future iOS support. Do not assume dynamic native-code installation is available on every mobile operating system.

Janus's previously recorded independent code-delivery and custody boundary remains binding. The general single-application Android direction does not by itself approve loading Janus into the ordinary module runtime or weakening its isolation. How any future Android Janus experience satisfies both product direction and that stronger boundary requires explicit security design; no new Janus delivery mechanism is selected here.

### Shared foundations, platform-specific implementations

**Established.** Share domain contracts, data formats, interoperability rules and reusable business logic where practical, with platform-specific implementations. Avoid unnecessary duplication of domain logic.

| Platform | Approved implementation direction |
|---|---|
| Android | Native Kotlin/Compose UI, local-first module runtime, local storage and local integration. |
| Linux/self-hosted | Existing web UI and independent backend services, modular deployment and independent service lifecycle. |

Identical executable module packages across Android and Linux are **not required**. Shared foundations do not mean replacing the Linux service model with the Android runtime or requiring web UI on Android. The exact approach to business-logic reuse remains subject to practical design.

### Android distribution strategy

**Established direction; delivery implementation is Planned.** Preserve two potential channels, preferably from one Android codebase with distribution adapters/build variants rather than two independently maintained applications.

| Channel | Priority and requirements |
|---|---|
| **Independent Edition** | Main initial development target; distributed independently, initially through APK releases. Intended to support independently downloadable native module packages, installed, removed and manually updated inside Nexus. Runtime validation must cover authenticity, integrity, compatibility and permissions. Security isolation remains unresolved. |
| **Google Play Edition** | Deferred until AE is ready for broader public distribution. May use Google Play Feature Delivery or another compliant mechanism, must respect Google Play executable-code distribution restrictions, and may offer less module-distribution flexibility and different module-update behavior. |

Do not implement or maintain the Google Play variant yet. A single runtime shared across both distribution models is a preference to validate, not established technical feasibility. Neither Google Play nor F-Droid acceptance is guaranteed. Feature Delivery is a possible future approach, not a selected or validated solution. Any future Play-specific update behavior must be reconciled with the explicit-approval policy below rather than silently bypassing it.

### Module lifecycle — approved behavior

**Established behavior; not a claim of complete implementation on any platform.**

| Action | Runtime and code | Data and integration consequences |
|---|---|---|
| **Hide** | Remove from the Nexus home screen; keep installed and operational. | Services and integrations remain available. Hiding is not disabling. |
| **Disable** | Completely stop runtime activity, services, background jobs and integrations; retain executable code. | Preserve data, configuration and references. Other modules see it as unavailable. Re-enable only after compatibility checks. |
| **Uninstall** | Physically remove executable module code. | Ask whether to preserve or delete module data; **preserve by default**. Destructive deletion requires explicit confirmation. Never automatically delete another module's data or silently erase cross-module references. |

The uninstall choice does not make deletion implicit: it refines the existing separation between module removal and an explicitly confirmed destructive data action. Required technical dependencies must be evaluated separately. **Optional integrations must never block Disable or Uninstall.** Nexus may warn about affected modules and references, but unavailable references remain identifiable and recoverable where possible. Detailed installation/reinstallation workflows and required-dependency handling remain Open.

### Manual updates and coordinated compatibility

**Established. All module updates require explicit user approval; no automatic module updates in the initial architecture.** Nexus may display available updates and offer a manually approved **Update All** operation. Existing review, capability comparison and explicit approval of new privileges remain required.

If a module requires a newer Nexus version, offer a coordinated Nexus-plus-module update for explicit approval. Verify compatibility with other installed modules, protect the relevant Nexus and module state with the mandatory backups in section 6, and update Nexus before the dependent module. Do not update unrelated modules without necessity and consent. Abort safely when dependencies cannot be satisfied. Exact dependency/version negotiation and recovery orchestration still require design.

## 3. Basic local operation and offline interoperability

**Established direction. A running external Nexus server must never be a prerequisite for basic local module functionality.** For example:

| Module | Required local workflow in the target architecture |
|---|---|
| Meridian | Create, view and edit people locally. |
| Chronos | Manage local calendars and events. |
| Atlas | Manage saved places locally. |
| Mnemosyne | Manage notes and tasks locally. |

These are requirements for future local implementations, not declarations that all these implementations exist today.

Installed modules on the same device must also interoperate without internet access or an external server. Meridian must, for example, be able to reference and resolve a locally stored Atlas Place through Nexus-provided contracts. A launcher alone cannot satisfy this requirement: Nexus must provide local reference resolution, permission enforcement and communication mechanisms.

The owning module stays authoritative for its resource data. Cross-module reads use explicit contracts and permissions; no direct reads of private tables or files. References retain stable UUID identity, do not imply access, and survive temporary module absence as unresolved references. Existing rebuildable reference indexes and derived-data rules remain applicable; their local implementation is not chosen here.

Local offline operation does not require inherently online services to work without connectivity. External metadata providers, remote messaging and remote media acquisition may still need a network. This exception does not justify making basic local data workflows or same-device integration depend on an external server.

The local Nexus integration/runtime capability is distinct from a mandatory external Nexus host. Janus continues to work independently of Nexus under its existing requirements.

## 4. Optional device synchronization

**Established direction.** Synchronization is optional. Each device maintains its own usable local application data, and local modules must remain usable before pairing or configuring synchronization.

- Devices can later be paired and synchronized.
- A self-hosted Nexus instance may act as a synchronization hub.
- No vendor-hosted account or cloud subscription is mandatory.
- Offline changes remain available locally and synchronize when connectivity returns after synchronization has been configured.
- **Synchronization and backup are separate systems.** Synchronization does not replace recoverable backups.

### Independent per-module synchronization

**Established: Independent Per-Module Synchronization.** Nexus coordinates synchronization infrastructure; each module owns its domain data and synchronization semantics and synchronizes only its own data. Every module synchronizes independently and maintains its own synchronization state, progress, pending changes, errors and last successful synchronization information.

A synchronization failure or incompatibility in one module must not stop unrelated modules. Nexus must not synchronize the entire ecosystem as one indivisible operation. This differs deliberately from the single logical ecosystem backup model in section 6: backup coordination does not make synchronization an ecosystem-wide transaction.

Every installed module remains locally usable without synchronization. Offline changes must be retained; incompatible changes remain pending; failed synchronization must be recoverable. Synchronization must never silently discard user data and must not require all paired devices to be online simultaneously.

### Installation requirements and references

**Established.** A module's data synchronizes between devices only when the corresponding module is installed on both devices, subject to pairing, authentication, permissions and protocol compatibility.

| Installation | Installed modules | Synchronization outcome |
|---|---|---|
| Paired server | Meridian, Chronos, Atlas, Argus | Only modules also installed on the Android device participate in synchronization with that device. |
| Android device | Meridian, Chronos | Only Meridian and Chronos synchronize; Atlas and Argus data are not downloaded merely because they exist on the server. |

If Atlas is subsequently installed on Android, Nexus automatically initiates its initial synchronization with the compatible Atlas installation on the paired server, respecting the same trust, permission and compatibility checks. No manual import is required. Automatic initial synchronization after authorized setup is distinct from an automatic module installation or update; the manual-update policy remains unchanged. Installation is a prerequisite, not permission to bypass the Disable behavior in section 2.

References do not trigger installation or synchronization of their target modules. If a Meridian record references an Atlas Place but Atlas is absent on Android:

- Preserve the reference identifier; do not delete or silently invalidate it.
- Do not automatically install Atlas or download Atlas data.
- Display the referenced resource as unavailable when necessary.
- Optionally offer the user the ability to install Atlas.

An absent optional module is not a synchronization failure. A reference is not authorization to access its target. Preserve the existing permission-aware resolution and stable-identity rules.

### Safe backward compatibility

**Established.** Synchronization compatibility depends on supported synchronization protocols, not equality of application version numbers. Nexus versions and individual module versions may differ between devices; do not require simultaneous updates of every device or module.

Nexus transport and module data synchronization protocols are independently versioned. When possible, negotiate the newest mutually supported compatible protocol. Newer implementations should retain backward compatibility where safe and practical, but compatibility must be verified rather than assumed.

Unknown fields and unsupported changes must never be silently discarded. If compatibility cannot be established, pause synchronization for the affected module, preserve local data and pending changes, and resume when compatibility becomes available. Do not stop unrelated compatible modules. Exact protocol definitions, negotiation metadata and unsupported-data representation remain Open.

### Field-level conflict detection and safe merging

**Established.** Detect conflicts at field level, not by treating an entire database row as conflicting merely because records differ across devices. Use a common ancestor/base revision and revision/change tracking to distinguish concurrent modifications from sequential updates.

| Meridian contact field | Common base | Android change | Server change | Automatically merged result |
|---|---|---|---|---|
| Surname | Novák | Unchanged | Novotný | Novotný |
| Phone | 0900 111 222 | 0900 333 444 | Unchanged | 0900 333 444 |

These independent field modifications merge automatically without a conflict dialog, provided the result respects module integrity constraints. Three-way merging or an equivalent causally correct mechanism should be considered; no algorithm or revision metadata format is selected here.

A genuine conflict exists when concurrent changes cannot be safely merged. For example, Android changes a surname to Kováč while the server changes the same surname to Novotný from the same earlier revision. This requires user resolution. Nexus must not arbitrarily choose the latest timestamp or overwrite one value.

Collections, relationships, attachments, deletion and complex structured objects require module-defined merge semantics. Field-level merging must never violate cross-field invariants or data integrity constraints; differing fields alone do not prove that combining them is safe. Modules own these domain rules, not Nexus.

### Consolidated Conflict Window

**Established.** Collect genuine unresolved conflicts in one consolidated **Conflict Window**, rather than opening a separate popup for each conflict. Display affected modules, records, fields and competing values; allow individual resolutions and present a clear summary before applying them.

Preserve unaffected changes, and do not block unrelated module synchronization while conflicts remain pending. The device initiating synchronization is the preferred conflict-resolution interface: the user makes decisions there, but its data receives no automatic precedence. Detailed layout and interaction design remain Open.

This shared interface must preserve module permission and confidentiality boundaries. In particular, it does not authorize Nexus to receive Janus plaintext or decryption keys; any Janus conflict presentation and merging must satisfy its independently reviewed security design. An encrypted snapshot transport does not by itself decide conflict-detection granularity or grant access to vault contents.

### Three or more competing revisions

**Established.** Support conflicts involving more than two concurrent versions. Track the full set of competing revisions even if the UI presents pairwise choices. For Android value A, server value B and laptop value C, an interface may compare A against B, compare the selected candidate against C, then ask the user to confirm the final value.

Do not permanently commit intermediate pairwise selections before all currently known competing revisions have been evaluated. The resulting revision must causally supersede all resolved revisions so rejected values do not reappear as new conflicts. A device that was offline may later introduce additional concurrent changes requiring further resolution; resolving all known candidates is not proof that no unknown candidate exists.

### Concurrent conflict resolution

**Established.** Two devices may try to resolve the same conflict simultaneously. Prevent one resolution from silently overwriting another through appropriate revision validation, concurrency control or an equivalent mechanism. If the underlying conflict state changes before a resolution commits, detect the change and request reconciliation.

Do not assume a permanently available central server or global lock. This safety requirement does not add direct peer-to-peer synchronization to the initial scope or select a concurrency mechanism.

### Remaining design work and module-specific boundaries

**Open.** Exact synchronization transport; device identity and pairing implementation; authentication, encryption and key management; revision metadata and change-log storage; tombstones and deletion propagation; complex-data merge semantics; retry and batching; storage limits and change-log compaction; multi-device convergence guarantees; detailed Conflict Window UI; and synchronization recovery mechanisms require design. The approved causal and data-preservation requirements constrain that work without finalizing these mechanisms.

Direct peer-to-peer synchronization without a hub is not an initial-version requirement. It may be evaluated later.

These synchronization requirements do not revoke Janus's existing module-specific product direction: independent local encrypted vaults, encrypted snapshot synchronization and explicitly authorized pairing remain its intended model under ADR 0017. Its cryptographic mechanisms still require design and review. Neither Janus pairing nor private-network enrollment automatically defines ecosystem synchronization identity or authorization. How Janus satisfies field-level conflict requirements through its ciphertext-only transport remains security-design work, not permission for Nexus to inspect secrets.

**Implementation status: Planned.** The synchronization and conflict-resolution architecture in this section is an approved requirement set, not implemented functionality or a report of passing synchronization tests.

## 5. Database architecture and ownership

**Established.** Logical ownership remains with each module. The number of physical database files does not define modularity.

Separate database files are acceptable and may be preferable for fault isolation, portability, backups and independent module evolution. **Do not initiate migration to a single shared database.** Separate per-module databases are a valid starting point for future mobile implementations.

Even if a future platform uses a shared physical database, modules must not depend directly on each other's private tables or schemas. Cross-module access uses explicit contracts, references and permissions. Nexus does not become the central domain database.

**Open.** Future mobile physical database topology and storage engines remain undecided. This checkpoint neither mandates one physical layout everywhere nor initiates changes to existing server storage. Preserve ADR 0007's server preference and documented implementation exceptions.

## 6. Nexus Backup Manager (NBM)

**Established responsibility; full target capability is Planned.** Nexus centrally coordinates AE backups. **Nexus Backup Manager is integrated into Nexus**, rather than a separate competing platform or a collection of independent module backup products.

Modules must not independently manage user-facing backup systems. They may provide standardized consistent-snapshot, export and restore interfaces to the Nexus backup infrastructure. This coordination rule does not erase domain-specific portability or existing Janus encrypted export/local recovery mechanisms, nor does it require a never-connected Janus client to depend on Nexus.

NBM is responsible for:

- Complete ecosystem backups and partial/module-scoped backups.
- Scheduling, integrity verification and backup encryption.
- Retention policies and destination management.
- Manifests containing module versions, schema versions and relevant metadata.
- Coordinating consistent snapshots of databases, actual media and attachment files, configuration, Nexus-owned state and cross-module references.

Backups must include actual required files, not merely database records or paths to missing media. Shared storage and module-owned storage both need coverage; today's bounded Nexus blob primitive does not encompass all module media, as [Argus's interim storage decision](decisions/0019-argus-interim-media-storage.md) illustrates.

Use appropriate consistent snapshot mechanisms rather than blindly copying active database files. Modules retain responsibility for producing valid snapshots of their own storage; Nexus coordinates ecosystem consistency without learning private schemas.

The target model is **one logical ecosystem backup with selective restore**, while permitting independent module snapshots and future incremental storage. This is a logical recovery unit, not a decision that every backup must be one physical file.

Local/offline backups must be supported. No vendor cloud is required. Backup package format, storage layout, encryption/key management, ordinary scheduled-backup retention details, incremental mechanisms and cross-module consistency strategy remain **Open**. The approved pre-update retention rules below constrain their eventual implementation. Examples in older documents, such as tar plus zstd, are not selected formats by this checkpoint.

### Mandatory pre-update backup

**Established, non-negotiable. Before EVERY module update, Nexus must create and verify a recoverable backup of all relevant module data.** This includes patches and UI-only changes; there is no exemption based on the apparent size or scope of the update.

Required coverage includes module databases, configuration, attachments and media, relevant module-owned files and metadata, and any additional state needed for recovery. Coordinated Nexus-plus-module updates must protect relevant Nexus state as well as the dependent module's state. Verify available storage before creating mandatory backups and block the update when a required backup cannot be safely created.

Backups must be consistent, integrity-checked and recoverable, using proper database snapshot mechanisms. **The update must not start if backup creation or verification fails.** A checksum alone does not establish recoverability; the precise verification procedure remains to be designed. Janus backup participation remains subject to section 8's optional ciphertext-only boundary; this requirement grants no access to plaintext or keys and permits no backup-failure bypass for a Nexus-managed update.

Recovery design must distinguish **code rollback** from **data rollback**. Reverting executable code does not prove that migrated data is compatible, and restoring old data must not silently destroy changes made after an update. The shared Recovery Engine must account for these cases; exact rollback and migration mechanisms remain Open.

### Pre-update snapshot retention

**Established.** Pre-update snapshots have a user-configurable snapshot count and/or retention period. Users can protect/pin individual snapshots, which are excluded from automatic cleanup. Explicit deletion of a protected snapshot requires confirmation.

Automatic cleanup must never remove the last usable recovery point for a module. Storage checks and cleanup must not weaken the mandatory-backup gate: block the update if a safe required backup cannot be created. Exact cleanup sequencing and recovery-point eligibility require design.

**Proposed default, awaiting confirmation:** five snapshots per module. The configurable-retention requirement is approved; this numerical default is not final.

Pre-update snapshot retention is distinct from ordinary scheduled-backup retention. Pinning, last-recovery-point protection and configurable policies are requirements, not claims that retention automation already exists.

## 7. Shared recovery architecture

**Established component boundaries; implementation is Planned.** There must be one underlying Recovery Engine shared by the integrated Nexus recovery interface and an independently usable recovery entry point.

| Component | Responsibility | Availability boundary |
|---|---|---|
| Nexus Backup Manager (NBM) | Backup creation, scheduling and backup lifecycle; integrated recovery interface delegates to the shared engine. | Integrated Nexus capability. |
| AE Recovery Engine | Shared validation, recovery planning and recovery execution. | Reusable underlying implementation, not a second backup manager. |
| AE Recovery Utility (AERU) | Independent entry point to the same Recovery Engine. | Usable when the primary Nexus installation cannot start. |

### AE Recovery Engine

The engine should support:

- Backup integrity and compatibility checks before recovery.
- Full ecosystem recovery.
- Selective module recovery within an AE installation.
- Safe restoration of Nexus-owned state and cross-module references.
- Conservative handling of migrations and incompatible versions.
- Recovery safety checks and rollback where feasible; rollback is not an unconditional guarantee.

Preserve existing recovery invariants: restored resources keep their UUIDs; references never silently retarget; missing modules leave unresolved references; derived indexes and blob usage are rebuilt or reconciled. Restore must not immediately trigger destructive retention or blob deletion because restored timestamps are old or reconciliation is incomplete. See [resource identity and lifecycle](concepts/resource-identity-and-lifecycle.md) and [backups](concepts/backups.md).

### AE Recovery Utility

AERU must cover failure of the normal Nexus entry point, including corrupted configuration or databases, failed Nexus updates, broken authentication or gateway functionality, a damaged application runtime, and device replacement or disaster recovery.

Server environments may provide AERU as a standalone CLI or recovery tool. On mobile, provide a minimal recovery mode independent of normal module initialization where technically feasible. If the mobile application itself cannot start, an external backup plus a clean installation must remain a recovery path.

**Do not implement two independent recovery engines.** The normal Nexus interface and AERU reuse the same validation, planning and execution logic, with platform-specific interfaces as needed. Exact implementation and platform recovery packaging remain Open.

### Explicitly deferred

Restoring an individual module as a completely standalone application without Nexus is **not currently required**. Selective recovery means restoring a module within an AE installation. Reconsider standalone module recovery only if the owner requests it or real user demand emerges. Janus's pre-existing independent-client recovery requirements remain in force.

## 8. Janus backup security boundary

**Established.** Janus may optionally participate in Nexus-managed ecosystem backups, but it has a stronger security boundary than ordinary modules:

- Nexus may handle an **opaque, encrypted Janus vault backup** only.
- Nexus must never receive plaintext secrets, the Janus master password or Janus decryption keys.
- Restoring encrypted vault data must not automatically unlock the vault.
- Janus retains control of its encryption and unlocking mechanisms.
- Ecosystem backup encryption and Janus vault encryption are independent protection layers. Removing the outer backup layer does not decrypt Janus data.
- Users must be able to exclude Janus from backup destinations they do not trust.

Do not weaken Janus's local-first custody or the requirement that Nexus cannot learn its secrets. Optional backup inclusion does not authorize wider reference-metadata disclosure, code delivery by Nexus or universal recovery secrets.

These are required properties, not a reviewed cryptographic design or a claim that the current implementation provides a proven zero-knowledge security model. [Janus security design](modules/janus-security-design.md) remains a placeholder pending design and independent review. Existing development clients and formats must not be promoted to production-safe status by this checkpoint. Janus key-loss recovery, backup metadata exposure and cryptographic interaction remain design work under that boundary.

## 9. Development strategy and priorities

**Established direction.** Avoid a dependency loop in which every module waits for another unfinished infrastructure feature before useful work can continue.

1. Establish a coherent architecture and product vision.
2. Define minimal shared platform contracts.
3. Build modules to genuinely usable MVP functionality.
4. Connect modules through Nexus contracts.
5. Expand functionality and polish iteratively.
6. Add infrastructure when it unlocks concrete functionality, mitigates a meaningful risk or protects user data.

Every MVP must provide at least one complete, useful end-to-end workflow. Do not create empty module shells merely to increase module count, and do not require every module to reach full feature completeness before another can be developed.

Meridian remains the owner's strongest current functional priority. The exact implementation roadmap and milestones are **Open**, not finalized by this sequence. Mobile/local-first architecture and synchronization implications must be considered before producing many more server-only modules that would require extensive redesign. This does not mandate implementing every future infrastructure capability before advancing Meridian.

### Platform priorities

**Established order:**

1. Existing Linux/self-hosted ecosystem.
2. Native Android Nexus and modules.
3. Google Play Edition when the product is ready for public distribution.
4. iOS deferred indefinitely pending demand and economic justification.

This priority order does not finalize dates or the detailed implementation roadmap. Do not design the initial implementation around iOS distribution constraints, and avoid gratuitously preventing later support.

## 10. Reported implementation and deployment context

**Reported facts, distinct from future architecture.** These updates postdate the earlier status snapshot. Sources are the workspace [navigation deployment report](../DEPLOYMENT_NAVIGATION_2026-11-07.md), dated November 7, 2026 at 13:35 UTC, and the owner's subsequent report supplied for this checkpoint. This documentation task did not rerun application suites, inspect production or deploy anything.

| Item | Reported status as of this checkpoint |
|---|---|
| Nexus | **0.1.11 deployed on Exodus.** |
| Meridian | **0.4.1 deployed on Exodus.** |
| Browser navigation | Meridian Back/Forward navigation pilot integrated with Nexus; Meridian route adapter added. |
| Feature configuration | `NEXUS_NAVIGATION_HISTORY` enabled by default; `0` is an internal kill switch. |
| Automated browser checks | Navigation **17/17** and Meridian photo checks **12/12**, reported successful on aetest, not a claim of complete production browser verification. |
| Owner production check | Owner manually confirmed **Mouse 4 / browser Back works in production**. |
| Remaining production checks | Other browser navigation scenarios remain for later manual verification; do not mark all Back/Forward, direct-link, refresh, Mouse 5 or browser-engine scenarios verified in production. |
| Mnemosyne | Navigation adapter exists locally but is **not deployed**. |
| Zeus | Remains intentionally stopped. This checkpoint does not authorize resuming it. |

The deployment report predates the owner's Mouse 4 confirmation and still lists that check as outstanding. The later owner report supersedes that one outstanding item only. Automated photo checks do not establish that the full photo workflow has been manually verified in production.

The report also distinguishes deployed Nexus 0.1.11 navigation from the separate bridge `session` action, recorded there as 0.1.12 local-only. Do not interpret the older status files' association of source 0.1.11 with `session` as evidence that `session` shipped with the navigation release.

Earlier [implementation status](implementation-status.md), the architecture README and workspace `PROJECT_STATUS.md` may still list Nexus 0.1.10, Meridian 0.4.0, or navigation as a default-off local pilot. Preserve them in this task; synchronize later using actual release/source state and these dated reports. Navigation support remains adapter-specific; this is not evidence of all-module adoption or a stable universal mobile UI contract.

### Existing backup foundations versus the new target

The Nexus implementation guide [lifecycle-and-recovery.md](../empyrean-nexus/docs/lifecycle-and-recovery.md) documents module backup v1: opaque module export, validation, restore/finalize, artifact/blob checks, compatibility checks and reference reconciliation. It also documents limitations: bounded artifacts, no Nexus backup encryption, no scheduling/retention, no import into a different installation and no module-independent full-installation backup. These are documented implementation facts, not freshly verified runtime claims.

Those foundations must not be described as the complete NBM target, a deployed AERU or an implemented shared Recovery Engine. Likewise, browser draft checkpoints are tab-local recovery copies, not durable saved data or ecosystem backups. Deployment-time backup copies do not demonstrate the future cross-module consistent-snapshot design.

The current server implementation's configurable backup-before-update hook does not establish compliance with the continuation's mandatory verified-backup requirement for every update. The Android runtime/manager, downloadable native module distribution, marketplace, coordinated updater and target automated backup/recovery and retention system remain future work. The continuation records no new application deployment or test results.

## 11. Open technical decisions

All choices below require later design. Requirements above constrain the outcomes without selecting mechanisms.

| Area | Open questions |
|---|---|
| Android module packaging | Exact native module package format and dynamic loading strategy for Kotlin/DEX/Compose modules. Kotlin/Compose is the preferred stack; the loader is not selected. |
| Android local execution | Secure isolation within one application and enforcement of cross-module permissions; full prototype validation and testing strategy. |
| Independent Edition trust | Module signing, publisher trust and authenticity-validation model. |
| Contract compatibility | Exact versioning and negotiation metadata for shared domain/module contracts. Synchronization already requires independently versioned Nexus transport/module data protocols and verified safe backward compatibility as specified in section 4. |
| Google Play Edition | Technical feasibility, compliant delivery mechanism, update semantics and whether one runtime can serve both editions; migration between Independent and Play editions. |
| Module admission and installation | Whether third-party modules will ever be permitted in the Android runtime; detailed installation/reinstallation workflows and required technical dependencies. Existing ecosystem community-interoperability principles do not settle Android code admission. |
| Mobile persistence | Physical database topology and storage-engine selection. |
| Synchronization | Exact transport, wire protocols and hub behavior; revision metadata/change-log storage, retry/batching, storage limits and compaction, multi-device convergence guarantees and detailed UI. Independent per-module synchronization and causal safety requirements are approved; optional peer-to-peer evaluation remains later. |
| Device trust | Identity, pairing, authentication, encryption, key management and revocation. |
| Reconciliation | Module-defined merge semantics for complex data, tombstones/deletion propagation, synchronization recovery and exact concurrency-control mechanisms. Field-level detection, the consolidated Conflict Window, multi-revision tracking and protection against concurrent resolution overwrites are approved in section 4. |
| Backup representation | Package format/versioning, storage, encryption, key recovery, recoverability verification, ordinary scheduled retention and incremental storage details. Pre-update retention requirements are approved; the suggested five-snapshot default awaits confirmation. |
| Snapshot coordination | Consistency across module databases, Nexus state, files and references. |
| Recovery delivery | Shared engine implementation, compatibility/migration policy and platform-specific Nexus/AERU interfaces. |
| Transition | Detailed migration from today's server-based architecture while preserving data and implementation truth. |
| Execution plan | Exact roadmap, milestones and release dates within the approved platform order and Meridian emphasis. |

Do not turn an example, a current server implementation detail or a previously proposed mechanism into a new established ecosystem decision.

**Security validation boundary.** Dynamically loaded code inside one Android application must not be described as having Android-level sandbox isolation. A manifest alone does not enforce security. Prototype and validate runtime feasibility, isolation, permission enforcement and trust mechanisms before claiming safe module execution. No detailed prototype/testing strategy or distribution-store acceptance is approved by implication.

## 12. Future documentation synchronization — Claude Code handoff

**Planned follow-up; not performed here.** Inspect the actual repository state, uncommitted work, implementation sources and release/deployment reports before editing. Preserve the distinction between local source, tested behavior, deployed versions and production verification. Reconcile conflicts explicitly; do not rewrite implementation truth to match the long-term vision.

Claude Code should also propagate the continuation's decisions into canonical documentation, particularly Nexus, module lifecycle, local-first/mobile architecture, interoperability, backup/recovery and deployment planning. Resolve stale all-mobile framework uncertainty, activation-only Android proposals, automatic-update assumptions and optional pre-update backup policy while preserving earlier decisions and current implementation facts. Document distribution variants as future work and keep Janus's stronger boundary explicit.

For the synchronization continuation, propagate section 4 into the appropriate canonical local-first/sync, Nexus, module-contract, interoperability, reference/lifecycle, permissions and module specifications. Record independent per-module progress/failure, installation-gated initial sync, reference preservation, protocol compatibility, causal field merging and consolidated multi-device conflict handling. Keep mechanisms still Open explicit, preserve Janus confidentiality, and do not mark synchronization or conflict resolution implemented. This checkpoint remains a temporary decision record.

| Documents to review | Intended synchronization |
|---|---|
| Root [README.md](README.md), [architecture.md](architecture.md), [principles.md](principles.md) | Nexus is AE Core, expanded local runtime/integration role, mobile independence, sequencing and existing ownership boundaries. |
| [modules/nexus.md](modules/nexus.md) and Nexus repository `docs/application-infrastructure.md`, `docs/module-protocol.md` | Separate current server infrastructure from future local runtime, gallery/lifecycle and platform responsibilities. |
| [concepts/module-contract.md](concepts/module-contract.md), [interoperability](concepts/interoperability.md), [cross-module references](concepts/cross-module-references.md), [projections](concepts/projections.md), [permissions](concepts/principals-and-permissions.md), [resource lifecycle](concepts/resource-identity-and-lifecycle.md) | Local offline interoperability, permission-aware contracts, stable identities and independent data ownership; distinguish requirements from implemented wire forms. |
| [design/module-ui-contract.md](design/module-ui-contract.md) and Nexus UI/runtime specifications | Preserve today's browser bridge/sandbox truth; clarify future mobile runtime requirements without selecting packaging or extending pilot contracts by implication. |
| [concepts/backups.md](concepts/backups.md), [storage and files](concepts/storage-and-files.md), Nexus `docs/lifecycle-and-recovery.md` and `docs/deployment.md` | NBM, complete/partial backups, actual-file coverage, consistent snapshots, shared Recovery Engine and independent AERU; retain current limitations. Create dedicated recovery specifications if needed after inspection. |
| [concepts/updates.md](concepts/updates.md), lifecycle specifications and deployment planning | Manual-only module updates, coordinated Nexus-first compatibility checks, mandatory verified backups for every update, code versus data rollback, pre-update snapshot retention, and distinct Hide/Disable/Uninstall behavior. |
| [concepts/local-first-and-sync.md](concepts/local-first-and-sync.md) | Broaden local-first scope, same-device offline integration and optional synchronization; preserve module-specific Janus direction. Review whether a dedicated mobile/local-first architecture document should be created; none exists as a dedicated mobile architecture document in the inspected tree. |
| [modules/janus.md](modules/janus.md), [Janus security design](modules/janus-security-design.md), relevant Janus ADRs and implementation security documents | Optional opaque encrypted backup, no plaintext or decryption keys in Nexus, independent protection layers and unlock; preserve independent client delivery and security-review status. |
| Relevant module specifications, especially [Meridian](modules/meridian.md), [Chronos](modules/chronos.md), [Atlas](modules/atlas.md) and [Mnemosyne](modules/mnemosyne.md) | Reflect future local workflows and integration requirements while retaining each module's actual implementation maturity and unfinished features. |
| [implementation-status.md](implementation-status.md) (`architecture/implementation-status.md` from the workspace root) | Reconcile Nexus 0.1.11/Meridian 0.4.1 deployment and navigation evidence without marking future architecture implemented. |
| Workspace root [PROJECT_STATUS.md](../PROJECT_STATUS.md) and relevant root README | Inspect current state, then update stale versions and verification boundaries in the later task. |
| Nexus [README](../empyrean-nexus/README.md) and [navigation-history guide](../empyrean-nexus/docs/navigation-history.md); Meridian [README](../meridian/README.md) and navigation documentation | Align release-specific default/kill-switch behavior, Meridian adapter, recorded tests, Mouse 4 confirmation and remaining production checks. Inspect Meridian's actual documentation layout before selecting or creating a navigation document. |
| [decisions/](decisions/README.md), including ADRs 0001, 0007, 0009, 0016, 0017 and 0018 | Record explicit amendments or new ADRs where needed; preserve accepted boundaries and keep Proposed/Open mechanisms distinct. |

Links beginning with `../` identify neighboring workspace files and may not resolve in a standalone checkout of this repository. Consult the deployment report if available; otherwise retain the explicit provenance that deployment updates and owner verification were supplied for this checkpoint.

The later session must inspect before changing, leave unrelated work intact, and avoid treating this checkpoint as authorization to implement, deploy, resume stopped services or choose unresolved technologies.
