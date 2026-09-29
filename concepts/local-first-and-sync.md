# Local-First Clients and Synchronization

A pattern available to modules where offline operation makes product sense. It is **not** a requirement for every module.

## Concept

A local-first module may keep authoritative, usable state on the device and synchronize with Nexus when it becomes reachable. Nexus provides optional synchronization and ecosystem interoperability; it is not a mandatory runtime dependency for such a module.

```
Device (local state, fully usable) ⇄ Nexus (optional sync, interoperability) ⇄ other devices
```

Modules that may benefit: password management (Janus), notes (Mnemosyne), documents, and possibly future mobile/desktop clients. Others may stay server-centric.

## Established decisions

- Local-first is a pattern modules may adopt; not every module is local-first.
- [Janus](../modules/janus.md) is the first module for which offline/local-first behavior is an explicit requirement: it must be useful with no Nexus at all.
- Nexus is optional synchronization/interoperability for local-first modules, not a runtime dependency for using their local data.
- Changes made offline are eligible for later synchronization once the user configures Nexus sync.
- Consistent with the core rule: the module still owns its domain data. Nexus does not become authoritative for a local-first module's domain objects by synchronizing them.

## Planned direction

- Nexus offers synchronization infrastructure for client state that Nexus may treat as opaque. For sensitive modules (Janus), the intended security property is that Nexus does not need plaintext contents to synchronize (see [Janus](../modules/janus.md)).
- Clients sync when connectivity exists, including after long offline periods.
- Device authorization and revocation as part of enabling sync.

## Open questions

- Sync protocol and conflict resolution model; whether one mechanism serves all modules or each module defines its own.
- How Nexus stores and versions synchronized state, and whether it can be opaque for some modules and structured for others.
- Device pairing/authorization and revocation.
- Which modules adopt the pattern (notes, documents are candidates; none decided).
- How a local-first client participates in ecosystem features that need Nexus: the reference index, backlinks, events, permissions. Data referencing an unreachable resource is an unresolved reference ([cross-module-references](cross-module-references.md)); what a client offline for a long time sees is not defined.
- How a standalone client (never connected to Nexus) relates to Nexus's module registry/lifecycle and to Nexus backups ([backups](backups.md)).
- Version compatibility between long-offline clients and an updated Nexus/module API.
- Cryptographic aspects for sensitive modules: entirely Open and require dedicated security design; this document does not choose any protocol.
