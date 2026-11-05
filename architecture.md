# Architecture

Ecosystem-level architecture and module boundaries. Per-module detail is in [modules/](modules/); shared mechanisms are in [concepts/](concepts/). Principles are in [principles.md](principles.md).

Status labels: **Established**, **Planned**, **Open** (see [README](README.md#status-vocabulary)).

## Structure

Aureate Empyrean consists of:

1. **Empyrean Nexus** — the central platform, control plane and shared infrastructure.
2. **Independent modules** — each owns a domain.

```
                 ┌──────────────────────────────┐
   user ───────► │        Empyrean Nexus        │
                 │ identity · permissions ·     │
                 │ registry · gateway · events ·│
                 │ references · storage · backup│
                 └───┬─────┬─────┬─────┬────────┘
                     │     │     │     │      (APIs, events, references)
                 Meridian Hermes Atlas Argus Janus Lyra Enceladus Chronos Mnemosyne Documents … community modules

  Collectors (e.g. Android companion) ──► Nexus APIs/events ──► owning module
```

Current conceptual modules:

| Module | Domain | Document |
|---|---|---|
| Meridian | People, organizations, groups, relationships, personal knowledge about them | [meridian](modules/meridian.md) |
| Hermes | Communications | [hermes](modules/hermes.md) |
| Atlas | Personal geographic layer: places, areas, routes, location history | [atlas](modules/atlas.md) |
| Argus | Photos, video, media metadata, local recognition | [argus](modules/argus.md) |
| Janus | Local-first encrypted vault: credentials, secrets, authentication material, payment and sensitive identity records | [janus](modules/janus.md) |
| Lyra | Multimedia library and playback: music first; movies, TV, books, audiobooks later | [lyra](modules/lyra.md) |
| Enceladus | Physical training, strength, schedules and progress (integration Planned) | [enceladus](modules/enceladus.md) |
| Chronos | Calendar, dates, events, time-oriented information | [chronos](modules/chronos.md) |
| Mnemosyne | Personal knowledge, notes, tasks, projects, workspaces | [mnemosyne](modules/mnemosyne.md) |
| Documents | Document creation/editing (final name undecided) | [documents](modules/documents.md) |

The status labels (Established/Planned/Open) classify **decisions**, not implementation maturity. Implementation maturity is tracked in the implementation repositories, not by these labels.

Shared capabilities that are not domain modules:

| Capability | Role | Document |
|---|---|---|
| Astra (Planned) | Contextual intelligence/assistant layer; owns no domain data | [astra](modules/astra.md) |

Some capabilities may exist as **Nexus services** rather than modules (the file explorer/shared storage is the main candidate). Not every capability must be a module.

### Private development tooling — implementation note

Implementation note (2026-11-05): [Zeus and Dev Orchestrator](modules/zeus.md) are private
internal development tooling, separate from Astra and from the public module family. They
are not production-ready and there is no Exodus installation. Their source, topology,
operating policy and evidence are maintained only in restricted private repositories and
are intentionally not described in this public architecture repository. This status note
does not change the established decisions below.

## Established decisions

### Core ownership rule

- A module **owns its domain data**.
- Nexus **owns interoperability**.
- Modules **must not directly access each other's databases**.
- Nexus **must not become a central database** of every module's domain objects.
- Modules communicate via **documented APIs, events, shared primitives and cross-module references**.
- Derived, non-authoritative, rebuildable data about another module's resources (caches, projections, search indexes) is allowed and does not transfer ownership ([interoperability.md](concepts/interoperability.md#derived-data-and-projection)).
- Rationale: [ADR 0001](decisions/0001-module-ownership-and-domain-ignorant-nexus.md).

### Resource identity and lifecycle

- Every persistent first-class resource has a stable UUID identity, generated without Nexus coordination and preserved by backup, restore and identity-preserving migration. Database-local numeric IDs are never cross-module identity.
- References are identity references, never silently retarget, and resolve to states such as available, unavailable, trashed, deleted, not accessible or redirected.
- Uninstalling or disabling a module never deletes its user data; destructive deletion is a separate explicit action. Restore does not trigger destructive retention.
- See [resource-identity-and-lifecycle.md](concepts/resource-identity-and-lifecycle.md) and [ADR 0002](decisions/0002-uuid-resource-identity.md).

### Principals, permissions and module contract

- A single human owner does not mean every component sees everything: capabilities constrain modules, devices, collectors/connectors and future third-party modules and users. Installed is not trusted. See [principals-and-permissions.md](concepts/principals-and-permissions.md).
- Every connected module provides a minimal contract: identity and protocol compatibility, lifecycle state, declared capabilities, referenceable resource types, reference resolution, outgoing-reference enumeration, blob usage reporting and export/restore participation where applicable. The contract is storage-engine independent. See [module-contract.md](concepts/module-contract.md).

### Secrets

- User vault secrets (Janus) and service/integration credentials are distinct categories. Integration credentials must be usable while the user is absent and Janus is locked, so Janus is not their runtime source. See [secrets.md](concepts/secrets.md) and [ADR 0004](decisions/0004-janus-custody-and-secret-classes.md).

### Search and discovery

- Local knowledge first; external discovery is explicit and clearly distinguishable. See [search-and-discovery.md](concepts/search-and-discovery.md) and [ADR 0003](decisions/0003-local-knowledge-first.md).

### Databases

- PostgreSQL is the preferred/default database for server-hosted official modules where a relational database is appropriate (scale, concurrency, indexing, JSONB, full-text search, operational maturity, PostGIS for Atlas, pgvector only where justified).
- This is not a shared application database: modules use separate databases, schemas, roles or equivalent isolation and never query another module's tables. Deployment topology is Open.
- SQLite remains acceptable for lightweight standalone tools, local caches, tests, prototypes and modules whose deployment genuinely favors it. The module contract is storage-engine independent. Janus storage follows its own security design. Vector search is not added merely because it is available.
- See [ADR 0007](decisions/0007-postgresql-default-database.md).

### Official modules and external products

- Official modules own their domain implementation and user experience. Complete applications such as Immich, Jellyfin or Navidrome are product references, not runtime backends. Mature libraries, protocols, codecs and focused infrastructure are reused. See [ADR 0005](decisions/0005-official-modules-own-product-implementations.md).

### Nexus scope

- Nexus is infrastructure-focused and small. It **must not understand domain concepts** such as Person, Message, Photo or Place. See [nexus.md](modules/nexus.md).

### Cross-module references

- References are a first-class primitive, provided (indexed/discoverable) by Nexus, with owning modules authoritative for the data. See [cross-module-references.md](concepts/cross-module-references.md).
- Not a graph database, semantic knowledge graph, inference engine or distributed identity system.

### Deployment

- Docker Compose first.
- One public entry point; modules do not expose random user-facing ports.
- Internal port allocation follows the project's existing convention (not defined here).
- Single-user first; multi-user may come later.
- Remote access requires no Aureate-operated cloud. Private overlay/VPN access is the preferred direction for personal remote access; Tailscale is the current recommended onboarding option, but the ecosystem does not depend on it as a vendor and other secure approaches remain possible.
- **Planned, long-term:** Nexus-owned [Remote Access](modules/nexus.md#remote-access-planned-long-term): QR enrollment of the Aureate Empyrean mobile application in a Tailscale/WireGuard-style private network, with the option of a self-hosted control plane (Headscale or equivalent), no port forwarding and no public endpoints. Network enrollment never replaces Nexus authentication or module authorization, and is separate from Janus vault-device pairing ([ADR 0018](decisions/0018-nexus-remote-access.md)).
- Collectors and mobile clients that can lose access to Nexus buffer permitted data locally and deliver it later rather than silently losing it ([collectors.md](concepts/collectors.md)).

### Strategic sequencing

**Established priority:** first build and stabilize a useful, fully self-hosted integrated ecosystem. Harden data integrity, restore, security and everyday cross-module workflows before expanding distribution. [Enceladus](modules/enceladus.md) and Lyra's [Books domain](modules/lyra.md#books-physical-and-digital) follow this sequence; their planned scope is not an implementation claim.

**Planned, later consideration:** standalone mobile/app-store distribution or a managed SaaS may be evaluated only after that foundation is useful and stable. This is not a program to turn every module into a mobile app now. Pricing, platforms, operations and release dates are **Open**; paid distribution, if pursued, must preserve self-hosting, open-source portability and the existing principles.

Janus's independently delivered client remains an established security requirement ([ADR 0009](decisions/0009-janus-client-delivery.md)), not a distribution experiment to defer or replace with a Nexus-served web client. Nexus must never serve, embed or update executable Janus client code. A Janus Web Vault is no exception, including the existing local milestone: it must run from its own origin under its own delivery trust model ([ADR 0017](decisions/0017-janus-devices-sync-and-clients.md); [ADR 0016](decisions/0016-janus-independent-web-client.md), Proposed).

### Application boundary

- Nexus is the central control plane and homepage of the ecosystem. Installed modules are **full applications**, not permanent pages embedded in Nexus's management sidebar.
- Path roles are distinct, though exact paths are not fixed: `/` is the Nexus control center; `/app/modules/<id>` is the Nexus route that hosts a module application; `/modules/<id>/...` is the owner-authenticated gateway document loaded inside the module frame. Module-owned domain routes below that point are data to Nexus, not routes Nexus interprets or proxies. Entering a module does not leave Nexus management chrome permanently consuming the module UI. A module owns its own navigation and product UX.
- Nexus remains underneath as infrastructure: authentication, permissions, lifecycle/module management, routing/gateway, references/backlinks, events, shared storage where applicable, notifications, updates and APIs.
- A common application/module switcher may provide navigation between Nexus and installed modules; its design is Open. The current implementation links modules through existing `/app/modules/<id>` routes; no general switcher exists.
- Local-first clients (e.g. Janus) are not necessarily served by Nexus. Janus clients (desktop development build and a local Web Vault milestone today; browser extension and mobile planned) are released independently of Nexus, because Nexus-delivered client code is a security threat ([ADR 0009](decisions/0009-janus-client-delivery.md), [ADR 0017](decisions/0017-janus-devices-sync-and-clients.md)). Janus does not use the module iframe, navigation or draft-channel mechanisms described here.

**Implementation note (2026-11-05):** a default-off local Nexus + Mnemosyne pilot implements part of this boundary. Nexus owns the top-level browser URL and History API writes; the module owns domain route parsing, normalization, rendering, save and conflict behavior. The frame is opaque and session/challenge/revision fenced. This is a local pilot, not a production rollout or an all-module migration, and the production gates and exact wire details live in the [Nexus navigation guide](https://github.com/Aureate-Empyrean/empyrean-nexus/blob/main/docs/navigation-history.md), not in this document.

### Updates

- Updates to Nexus and modules are inspectable before they are applied: review and explicit approval precede any change, manifest/capability changes are compared, and new privileges are never silently granted. Automatic unattended updates are not the default. See [updates.md](concepts/updates.md).

### Localization

- Localization is ecosystem-wide. Nexus owns the installation/user locale preference and localization interoperability; modules own their translatable messages. Official and community translations are supported without forking module source, and standalone clients stay localizable without Nexus. See [localization.md](concepts/localization.md).

### Local-first modules

- Local-first operation is a pattern available to modules where it makes product sense; not every module is local-first. Nexus is optional synchronization/interoperability for such modules, not a mandatory runtime dependency. Janus is the first module for which this is an explicit requirement. See [local-first-and-sync.md](concepts/local-first-and-sync.md).
- Secret material remains owned by Janus. A synchronization server (Nexus or an independent Janus host; Open) may store encrypted Janus snapshots; it needs no plaintext vault contents, master password or decryption keys. See [janus.md](modules/janus.md) and [ADR 0017](decisions/0017-janus-devices-sync-and-clients.md).

### Naming

- Parent organization: **Aureate Empyrean**.
- Central platform: **Empyrean Nexus** — intentionally the only two-word product/module-level name.
- Modules use single-word names. Future modules should follow this where a good name exists.

### Licensing

- **AGPL-3.0-or-later** for core official software.
- Separate Aureate Empyrean branding/trademark policy.
- Forking, modification and redistribution are legitimate; useful improvements are encouraged upstream. Independent forks must not falsely present themselves as official releases.
- Community modules may use their own compatible licenses.

### Module trust categories

- **Official**, **Verified**, **Community**.
- Verified means reviewed under a defined process, **not** guaranteed safe.
- A module is not trusted merely because it is installed; enforced isolation must exist before untrusted third-party modules are treated as safely isolated ([principals-and-permissions.md](concepts/principals-and-permissions.md)).
- The review process and enforcement mechanism are Open.

### Visual identity (summary)

Celestial, black/charcoal, restrained aureate gold; premium, contemporary and serious. Not cyberpunk, crypto or gaming-launcher; no excessive glow or gradients; liquid-glass surfaces over a per-application atmosphere, readable and with solid fallbacks ([ADR 0012](decisions/0012-design-system-v2.md), [ADR 0014](decisions/0014-liquid-glass-surfaces.md)). Nexus uses gold as its identity; each major module carries its own accent (Mnemosyne violet, Janus teal, Chronos celestial blue, Lyra orchid) on the same shared foundation. Tokens, foundation primitives and the module-accent rule are in [design-system.md](concepts/design-system.md).

## Planned direction

- Module lifecycle managed through Nexus (install / enable / disable / update / uninstall).
- Versioned APIs and a lightweight event mechanism as the standard inter-module channels.
- Collectors as an ingestion layer feeding modules through Nexus ([collectors.md](concepts/collectors.md)).
- Optional encrypted synchronization for local-first modules ([local-first-and-sync.md](concepts/local-first-and-sync.md)).
- Content-addressed shared storage ([storage-and-files.md](concepts/storage-and-files.md)).
- Portable, restorable backup/export ([backups.md](concepts/backups.md)).
- Pre-update review with manifest diff, compatibility checks and post-update verification ([updates.md](concepts/updates.md)).
- Nexus-communicated locale, module-declared supported locales and portable translation packs ([localization.md](concepts/localization.md)).
- Third-party module participation through the same public mechanisms official modules use ([interoperability.md](concepts/interoperability.md)).
- Further module-declared projection contracts feeding derived data; the mechanism and `dated-items` are Established ([projections.md](concepts/projections.md)).
- Astra, a provider-independent contextual intelligence layer using module-exposed tools ([astra](modules/astra.md)).

## Open questions

- Authentication/permission model details: capability vocabulary, grant flow and enforcement ([principals-and-permissions.md](concepts/principals-and-permissions.md#open-questions)).
- Module contract wire format and manifest schema ([module-contract.md](concepts/module-contract.md#open-questions)).
- Database deployment topology (one PostgreSQL service with per-module databases/roles, or more).
- Event delivery guarantees, ordering and persistence. (Implemented events are bounded best-effort hints; consumers reconcile through projections and enumeration.)
- Whether/when multi-user is introduced and its effect on ownership and permissions.
- Trust-category review process for Verified modules.
- How local-first clients relate to the module registry/lifecycle and to Nexus backups when they never connect to Nexus.
- Localization details (resource format, fallback chain, translation pack trust/distribution): see [localization.md](concepts/localization.md#open-questions).
- Update metadata, rollback, migration execution and release trust: see [updates.md](concepts/updates.md#open-questions).
- Application/module switcher design and how a module application is embedded or navigated to from Nexus (see [Application boundary](#application-boundary)).
- Which capabilities become Nexus services versus modules (files/storage is the leading candidate).

Scope and sequencing decision: [ADR 0015](decisions/0015-training-books-and-delivery-sequencing.md).
