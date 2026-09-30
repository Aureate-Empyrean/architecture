# Empyrean Nexus

## Purpose

The shared foundation and user-facing control plane of the ecosystem. Nexus provides infrastructure and interoperability so that independent modules can form one coherent system.

Empyrean Nexus is intentionally the only two-word product/module-level name.

## Owns

Responsibilities that are established or expected eventually:

- identity/authentication
- permissions/capabilities
- module lifecycle and registry (install, enable, disable, update, uninstall); updates are reviewed before being applied ([updates](../concepts/updates.md))
- shared configuration and service/integration credentials (not user vault secrets; see [secrets](../concepts/secrets.md))
- ecosystem/default locale preference and localization interoperability (see [localization](../concepts/localization.md))
- gateway/routing
- versioned APIs
- lightweight system-event mechanism (change notifications between components; distinct from Chronos calendar Events and Meridian Life Events)
- notifications
- health/status
- shared storage primitives
- backup/export/restore infrastructure
- cross-module resource/reference infrastructure (index, resolution, backlinks)
- plugin/connector infrastructure
- optional synchronization infrastructure for local-first modules (Planned; see [local-first-and-sync](../concepts/local-first-and-sync.md))

## Does not own

- Domain data or domain concepts. Nexus must not understand Person, Message, Photo, Place, Event, Note, etc.
- A central database of every module's domain objects.
- Document editing (that is the Documents module).
- Module business logic.
- A Person or any other domain object representing the user. The user's own Person is a Meridian resource ([meridian](meridian.md#the-distinguished-self-person-me)).
- Plaintext module secrets. In particular, Nexus should not need plaintext access to [Janus](janus.md) vault contents, or the user's Janus master password, to synchronize them, and holds no universal recovery secret for Janus vaults.

## Integrations

- Modules connected to a Nexus installation register with it and are reached through its gateway. Local-first clients such as [Janus](janus.md) also work without any Nexus connection.
- Collectors send data to modules via Nexus APIs/events ([collectors](../concepts/collectors.md)).
- Nexus indexes references between resources ([cross-module-references](../concepts/cross-module-references.md)).
- Nexus provides blob/file primitives ([storage-and-files](../concepts/storage-and-files.md)) and orchestrates backup ([backups](../concepts/backups.md)).
- [Janus](janus.md): may synchronize encrypted vault state for authorized devices; Janus works without Nexus.
- Common integration surface for community modules ([interoperability](../concepts/interoperability.md)).

## Established decisions

- Nexus is small and infrastructure-focused; it does not understand domain concepts.
- Modules own domain data; Nexus owns interoperability.
- Docker Compose first; one public entry point; modules do not expose random user-facing ports; internal ports follow the project's existing convention.
- Single-user first; multi-user may come later.
- Nexus does not become a giant central database.
- Nexus is not a mandatory runtime dependency for modules that operate local-first (Janus requires this).
- Nexus updates and module updates are inspectable before they are applied, with manifest/capability changes compared and new privileges never silently granted ([updates](../concepts/updates.md)).
- Nexus is the control center/homepage; installed modules are full applications with their own navigation and UX, not permanent pages inside the Nexus management sidebar ([architecture](../architecture.md#application-boundary)).
- Nexus state about modules is derived where possible: the reference index and blob usage are rebuildable from modules through the [module contract](../concepts/module-contract.md).
- Uninstalling or disabling a module never deletes its user data; destructive deletion is separate and explicit ([resource lifecycle](../concepts/resource-identity-and-lifecycle.md#destructive-operations-and-module-removal)).
- Permissions constrain components, not only users; installed modules are not trusted by default ([principals-and-permissions](../concepts/principals-and-permissions.md)).
- Nexus owns the ecosystem locale preference; modules own their translatable messages ([localization](../concepts/localization.md)).

## Planned direction

- Module registry and lifecycle management.
- Versioned public APIs and a lightweight event mechanism.
- Reference index with backlink discovery, permission-aware.
- Shared blob storage and a user-facing file explorer (likely a Nexus capability rather than a module).
- Backup/export/restore orchestration across Nexus and modules.
- Notification and health/status services.

## Open questions

- Permission/capability model details: vocabulary, granularity, grant flow and enforcement (the minimum rule is in [principals-and-permissions](../concepts/principals-and-permissions.md)).
- Module contract wire format and manifest schema (required areas are in [module-contract](../concepts/module-contract.md)).
- Event mechanism: delivery guarantees, persistence, replay, transport.
- How the reference index stays consistent with module-owned data (push, pull, or both).
- Storage and at-rest protection of service/integration credentials ([secrets](../concepts/secrets.md#open-questions)).
- Which of the responsibilities above split into separate Nexus services versus one deployable.
- How Nexus stores and versions encrypted, opaque sync state, and how devices are authorized/revoked.
- Multi-user: what changes for identity, permissions, and data ownership.
