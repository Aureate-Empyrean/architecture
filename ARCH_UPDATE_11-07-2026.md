# Aureate Empyrean — Architecture Checkpoint (2026-11-07)

**Status: temporary authoritative checkpoint of owner-approved decisions from November 7, 2026.**

This document records the architectural direction agreed on that date and the deployment information reported after the previous documentation snapshot. It is the handoff for a later Claude Code session to synchronize canonical architecture documents, module specifications, implementation status and relevant READMEs. This task changes only this checkpoint; it does not implement application functionality or synchronize other documentation.

## 1. Authority, status and scope

Use the repository's status vocabulary:

| Label | Meaning in this checkpoint |
|---|---|
| **Established** | Owner-approved requirements and boundaries. Acceptance does not mean implemented. |
| **Planned** | Future architecture or delivery work needed to realize those requirements. No implementation or release date is implied. |
| **Open** | Technical choices that need design; do not implement a speculative choice as an accepted decision. |

Section 10 separately records **reported implementation and deployment facts**. The future mobile runtime, general ecosystem synchronization, complete NBM capability and shared Recovery Engine described here are target architecture, not claims about today's release.

For the subjects explicitly addressed here, this checkpoint takes precedence over older statements that conflict with the November 7 decisions. All other established decisions remain in force. In particular, preserve module domain ownership, a domain-ignorant Nexus, stable UUID resource identities, permission-aware references, least privilege, inspectable updates and Janus's independent security boundary.

### Relationship to existing decisions

- [ADR 0001](decisions/0001-module-ownership-and-domain-ignorant-nexus.md) remains binding: a shared runtime is infrastructure, not central ownership of module domain objects.
- [Local-first and sync](concepts/local-first-and-sync.md) currently describes local-first as optional for selected modules and leaves offline interoperability undefined. This checkpoint establishes basic local module operation and same-device offline interoperability as ecosystem direction. It does not claim current server modules already meet it.
- The server-first sequencing in [architecture.md](architecture.md#strategic-sequencing) is refined: useful self-hosted functionality and data safety remain priorities, but mobile/local-first and synchronization implications must be considered before building many more server-only modules. Mobile is not merely a remote dashboard for a required server.
- Docker Compose, the gateway and today's browser UI contracts describe the existing server implementation. They do not select the future mobile runtime or packaging.
- [ADR 0007](decisions/0007-postgresql-default-database.md) remains the server relational-database preference, with its existing exceptions. It neither requires a shared application database nor decides mobile storage.
- Janus's independent clients, code-delivery trust boundary and module-specific product direction in [ADR 0017](decisions/0017-janus-devices-sync-and-clients.md) remain intact. The unified AE application preference does not authorize Nexus to serve, proxy, embed or update executable Janus client code.

## 2. Product vision: Nexus is AE Core

**Established direction.** Aureate Empyrean is a privacy-first, local-first, modular software ecosystem. **Empyrean Nexus is AE Core.** Do not introduce a separate competing AE Core product or platform.

Nexus is more than a web gateway, server dashboard or authentication service. Its long-term responsibility is the shared runtime and local integration platform through which independent AE modules form a coherent ecosystem.

A user must be able to install Aureate Empyrean on a supported mobile device without owning, configuring or connecting to a separate computer or server. The preferred experience is **one Aureate Empyrean application containing independently usable modules**, rather than numerous unrelated application installations, subject to existing security boundaries such as Janus's.

**Planned architecture.** Mobile Nexus should provide:

- A unified AE home screen and module launcher.
- A gallery of available modules.
- One-tap installation or activation, subject to platform distribution constraints.
- Local module lifecycle management.
- Shared navigation, identity, permissions, references and integration capabilities.
- Local storage and offline operation.
- Optional synchronization with other AE installations.

Modules remain logically independent, each with its own domain model, data ownership and lifecycle. Sharing one application or runtime does not make Nexus the owner of People, Places, Events, Notes or other module objects.

**Open.** Mobile frameworks, packaging, distribution and runtime technology are undecided. Do not assume that every mobile operating system permits dynamic installation of native executable code. Installation may need to mean activation of an already distributed module or another platform-compatible mechanism; no mechanism is selected here.

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

**Open.** The ecosystem synchronization transport and protocol, device identity, pairing, authentication, encryption and key management, conflict resolution, deletion propagation and recovery model require design. Do not select a protocol or implementation in this checkpoint.

Direct peer-to-peer synchronization without a hub is not an initial-version requirement. It may be evaluated later.

This general ecosystem uncertainty does not revoke Janus's existing module-specific product direction: independent local encrypted vaults, encrypted snapshot synchronization and explicitly authorized pairing remain its intended model under ADR 0017. Its cryptographic mechanisms still require design and review. Neither Janus pairing nor private-network enrollment automatically defines ecosystem synchronization identity or authorization.

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

Local/offline backups must be supported. No vendor cloud is required. Backup package format, storage layout, encryption/key management, retention details, incremental mechanisms and cross-module consistency strategy remain **Open**. Examples in older documents, such as tar plus zstd, are not selected formats by this checkpoint.

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

## 11. Open technical decisions

All choices below require later design. Requirements above constrain the outcomes without selecting mechanisms.

| Area | Open questions |
|---|---|
| Mobile implementation | Framework, supported-platform details, packaging and distribution. |
| Module availability | Platform-compliant installation versus activation mechanism. |
| Local execution | Module runtime, isolation and sandboxing; enforcement of permissions between co-installed modules. |
| Mobile persistence | Physical database topology and storage-engine selection. |
| Synchronization | Transport, protocol and hub behavior; optional peer-to-peer evaluation later. |
| Device trust | Identity, pairing, authentication, encryption, key management and revocation. |
| Reconciliation | Conflict resolution, deletion propagation and recovery semantics. |
| Backup representation | Package format/versioning, storage, encryption, key recovery, retention and incremental storage details. |
| Snapshot coordination | Consistency across module databases, Nexus state, files and references. |
| Recovery delivery | Shared engine implementation, compatibility/migration policy and platform-specific Nexus/AERU interfaces. |
| Transition | Detailed migration from today's server-based architecture while preserving data and implementation truth. |
| Execution plan | Exact roadmap, priorities beyond the stated Meridian emphasis, milestones and release dates. |

Do not turn an example, a current server implementation detail or a previously proposed mechanism into a new established ecosystem decision.

## 12. Future documentation synchronization — Claude Code handoff

**Planned follow-up; not performed here.** Inspect the actual repository state, uncommitted work, implementation sources and release/deployment reports before editing. Preserve the distinction between local source, tested behavior, deployed versions and production verification. Reconcile conflicts explicitly; do not rewrite implementation truth to match the long-term vision.

| Documents to review | Intended synchronization |
|---|---|
| Root [README.md](README.md), [architecture.md](architecture.md), [principles.md](principles.md) | Nexus is AE Core, expanded local runtime/integration role, mobile independence, sequencing and existing ownership boundaries. |
| [modules/nexus.md](modules/nexus.md) and Nexus repository `docs/application-infrastructure.md`, `docs/module-protocol.md` | Separate current server infrastructure from future local runtime, gallery/lifecycle and platform responsibilities. |
| [concepts/module-contract.md](concepts/module-contract.md), [interoperability](concepts/interoperability.md), [cross-module references](concepts/cross-module-references.md), [projections](concepts/projections.md), [permissions](concepts/principals-and-permissions.md), [resource lifecycle](concepts/resource-identity-and-lifecycle.md) | Local offline interoperability, permission-aware contracts, stable identities and independent data ownership; distinguish requirements from implemented wire forms. |
| [design/module-ui-contract.md](design/module-ui-contract.md) and Nexus UI/runtime specifications | Preserve today's browser bridge/sandbox truth; clarify future mobile runtime requirements without selecting packaging or extending pilot contracts by implication. |
| [concepts/backups.md](concepts/backups.md), [storage and files](concepts/storage-and-files.md), Nexus `docs/lifecycle-and-recovery.md` and `docs/deployment.md` | NBM, complete/partial backups, actual-file coverage, consistent snapshots, shared Recovery Engine and independent AERU; retain current limitations. Create dedicated recovery specifications if needed after inspection. |
| [concepts/local-first-and-sync.md](concepts/local-first-and-sync.md) | Broaden local-first scope, same-device offline integration and optional synchronization; preserve module-specific Janus direction. Review whether a dedicated mobile/local-first architecture document should be created; none exists as a dedicated mobile architecture document in the inspected tree. |
| [modules/janus.md](modules/janus.md), [Janus security design](modules/janus-security-design.md), relevant Janus ADRs and implementation security documents | Optional opaque encrypted backup, no plaintext or decryption keys in Nexus, independent protection layers and unlock; preserve independent client delivery and security-review status. |
| Relevant module specifications, especially [Meridian](modules/meridian.md), [Chronos](modules/chronos.md), [Atlas](modules/atlas.md) and [Mnemosyne](modules/mnemosyne.md) | Reflect future local workflows and integration requirements while retaining each module's actual implementation maturity and unfinished features. |
| [implementation-status.md](implementation-status.md) (`architecture/implementation-status.md` from the workspace root) | Reconcile Nexus 0.1.11/Meridian 0.4.1 deployment and navigation evidence without marking future architecture implemented. |
| Workspace root [PROJECT_STATUS.md](../PROJECT_STATUS.md) and relevant root README | Inspect current state, then update stale versions and verification boundaries in the later task. |
| Nexus [README](../empyrean-nexus/README.md) and [navigation-history guide](../empyrean-nexus/docs/navigation-history.md); Meridian [README](../meridian/README.md) and navigation documentation | Align release-specific default/kill-switch behavior, Meridian adapter, recorded tests, Mouse 4 confirmation and remaining production checks. Inspect Meridian's actual documentation layout before selecting or creating a navigation document. |
| [decisions/](decisions/README.md), including ADRs 0001, 0007, 0009, 0016, 0017 and 0018 | Record explicit amendments or new ADRs where needed; preserve accepted boundaries and keep Proposed/Open mechanisms distinct. |

Links beginning with `../` identify neighboring workspace files and may not resolve in a standalone checkout of this repository. Consult the deployment report if available; otherwise retain the explicit provenance that deployment updates and owner verification were supplied for this checkpoint.

The later session must inspect before changing, leave unrelated work intact, and avoid treating this checkpoint as authorization to implement, deploy, resume stopped services or choose unresolved technologies.
