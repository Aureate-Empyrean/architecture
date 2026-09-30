# Module Contract

The minimum a module must provide to participate in the ecosystem. This is a list of required areas, not a plugin framework, and not a wire format. The existing Nexus implementation and its Example Module are the reference for protocol version 0; where they diverge from this document, the difference is resolved explicitly (see [README](../README.md)).

Requirements apply to modules connected to a Nexus installation. How standalone local-first clients (e.g. [Janus](../modules/janus.md)) relate to the contract is Open ([local-first-and-sync](local-first-and-sync.md)).

## Required areas (Established)

| Area | What the module provides |
|---|---|
| **Identity and compatibility** | A stable module identifier, the module version, and the module-protocol version(s) it supports, so Nexus can detect incompatibility before enabling or updating it ([updates](updates.md)). |
| **Lifecycle state** | Participation in install, enable, disable, update and uninstall, plus health. Uninstall and disable never delete user data; destructive data deletion is separate ([resource lifecycle](resource-identity-and-lifecycle.md#destructive-operations-and-module-removal)). |
| **Declared capabilities** | The capabilities/permissions it requests, declared up front and compared on update. It receives only what its declared function requires ([principals-and-permissions](principals-and-permissions.md)). |
| **Referenceable resource types** | The resource types other modules may reference, identified by UUID ([resource identity](resource-identity-and-lifecycle.md)). |
| **Reference resolution** | Given a reference and the calling principal, the resolution state and an owner-provided presentation (e.g. title, summary, link). The owner decides what the caller may see. |
| **Outgoing reference enumeration** | On request, the references its resources currently hold, so that derived Nexus state (the reference index) can be rebuilt after loss, restore or inconsistency. |
| **Storage/blob usage reporting** | Where it uses shared storage, which blobs it currently uses, so shared storage never deletes a blob still in use ([storage-and-files](storage-and-files.md)). |
| **Export/restore participation** | Where it holds user data, participation in backup, export and restore, including suspending automatic purges during restore ([backups](backups.md)). |

Further metadata (localization, [localization](localization.md); projection/query capabilities, below) is Planned.

## Principles

- **Storage-engine independent.** The contract never exposes or depends on a module's database engine or schema. A module's internal schema is not a public API.
- **Nexus state about modules is derived where possible.** The reference index and blob usage are rebuildable from modules; Nexus is not the only record of them.
- **Approved interfaces only.** Modules interoperate through this contract and documented APIs, never through another module's database or storage ([interoperability](interoperability.md)).

## Planned

- Projection/query capabilities a module may declare (e.g. dated items for Chronos, geographic items for Atlas), feeding derived data under the [derived-data rule](interoperability.md#derived-data-and-projection).
- Tools a module exposes to [Astra](../modules/astra.md).
- Localization metadata.

## Open questions

- Wire format, transport and manifest schema; how the contract is versioned.
- Exact capability vocabulary and grant flow ([nexus](../modules/nexus.md)).
- Event subscriptions and delivery guarantees; nothing whose correctness matters should depend solely on lossy events, since enumeration exists for rebuilding.
- Relationship of standalone local-first clients to registration and lifecycle.
