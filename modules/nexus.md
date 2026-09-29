# Empyrean Nexus

## Purpose

The shared foundation and user-facing control plane of the ecosystem. Nexus provides infrastructure and interoperability so that independent modules can form one coherent system.

Empyrean Nexus is intentionally the only two-word product/module-level name.

## Owns

Responsibilities that are established or expected eventually:

- identity/authentication
- permissions/capabilities
- module lifecycle and registry (install, enable, disable, update, uninstall)
- shared configuration and secrets
- gateway/routing
- versioned APIs
- lightweight event mechanism
- notifications
- health/status
- shared storage primitives
- backup/export/restore infrastructure
- cross-module resource/reference infrastructure (index, resolution, backlinks)
- plugin/connector infrastructure

## Does not own

- Domain data or domain concepts. Nexus must not understand Person, Message, Photo, Place, Event, Note, etc.
- A central database of every module's domain objects.
- Document editing (that is the Documents module).
- Module business logic.

## Integrations

- Every module registers with Nexus and is reached through its gateway.
- Collectors send data to modules via Nexus APIs/events ([collectors](../concepts/collectors.md)).
- Nexus indexes references between resources ([cross-module-references](../concepts/cross-module-references.md)).
- Nexus provides blob/file primitives ([storage-and-files](../concepts/storage-and-files.md)) and orchestrates backup ([backups](../concepts/backups.md)).
- Common integration surface for community modules ([interoperability](../concepts/interoperability.md)).

## Established decisions

- Nexus is small and infrastructure-focused; it does not understand domain concepts.
- Modules own domain data; Nexus owns interoperability.
- Docker Compose first; one public entry point; modules do not expose random user-facing ports; internal ports follow the project's existing convention.
- Single-user first; multi-user may come later.
- Nexus does not become a giant central database.

## Planned direction

- Module registry and lifecycle management.
- Versioned public APIs and a lightweight event mechanism.
- Reference index with backlink discovery, permission-aware.
- Shared blob storage and a user-facing file explorer (likely a Nexus capability rather than a module).
- Backup/export/restore orchestration across Nexus and modules.
- Notification and health/status services.

## Open questions

- Permission/capability model: granularity, how modules request and are granted access, how cross-module reads are authorized.
- Module manifest/contract: what a module declares (APIs, event types, resource types, permissions, storage needs).
- Event mechanism: delivery guarantees, persistence, replay, transport.
- How the reference index stays consistent with module-owned data (push, pull, or both).
- Secrets handling and where they are stored.
- Which of the responsibilities above split into separate Nexus services versus one deployable.
- Multi-user: what changes for identity, permissions, and data ownership.
