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
- Changes made offline are eligible for later synchronization once the user configures synchronization (Nexus sync for most modules; for Janus the server location is Open, [ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md)).
- Resources created offline receive their stable UUID identity locally, without Nexus coordination ([resource-identity-and-lifecycle](resource-identity-and-lifecycle.md)).
- Consistent with the core rule: the module still owns its domain data. Nexus does not become authoritative for a local-first module's domain objects by synchronizing them.

**Implementation note (2026-11-05).** Janus's desktop build and local Web Vault milestone operate locally with encrypted storage and no server dependency. No synchronization protocol, synchronization server, device pairing or multi-device reconciliation code exists. The Web Vault's IndexedDB revision check is a local stale-writer guard, not multi-device conflict resolution. See [Janus](../modules/janus.md#clients) and [ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md).

## Planned direction

- Nexus offers synchronization infrastructure for client state that Nexus may treat as opaque. For sensitive modules (Janus), the intended security property is that Nexus does not need plaintext contents to synchronize (see [Janus](../modules/janus.md)).
- Janus's initial direction is encrypted vault snapshots with versioned, overwrite-protected writes and preserved conflicts; its synchronization server may be Nexus or an independent Janus host (Open). Every Janus device unlocks its own local vault without another device or the server ([ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md)).
- Clients sync when connectivity exists, including after long offline periods.
- Device authorization and revocation as part of enabling sync.

## Open questions

- Sync protocol and conflict resolution model; whether one mechanism serves all modules or each module defines its own.
- How Nexus stores and versions synchronized state, and whether it can be opaque for some modules and structured for others.
- Device pairing/authorization and revocation. Module-level device pairing (such as Janus vault pairing) is distinct from enrolling a device in Nexus [Remote Access](../modules/nexus.md#remote-access-planned-long-term) ([ADR 0018](../decisions/0018-nexus-remote-access.md)); reaching Nexus over a private network is not a sync authorization.
- Which modules adopt the pattern (Mnemosyne is a strong candidate but deferred from its V1; documents are a candidate; none decided).
- How a local-first client participates in ecosystem features that need Nexus: the reference index, backlinks, events, permissions. Data referencing an unreachable resource is an unresolved reference ([cross-module-references](cross-module-references.md)); what a client offline for a long time sees is not defined.
- How a standalone client (never connected to Nexus) relates to Nexus's module registry/lifecycle and to Nexus backups ([backups](backups.md)).
- Version compatibility between long-offline clients and an updated Nexus/module API.
- Cryptographic aspects for sensitive modules: entirely Open and require dedicated security design; this document does not choose any protocol.
