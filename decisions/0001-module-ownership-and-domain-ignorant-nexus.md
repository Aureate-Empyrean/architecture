# 0001. Module ownership and a domain-ignorant Nexus

- Status: Accepted (2026-09-30)

## Context

Aureate Empyrean is many interoperable applications holding a large part of a user's personal history. A central platform is needed for identity, lifecycle, routing and interoperability. The obvious failure mode is a central database that every module reads and writes, which couples every module to every schema and makes community participation, independent evolution and privacy boundaries impossible.

## Decision

- A module owns its domain data. Nexus owns interoperability.
- Modules never access each other's databases or storage; they use documented APIs, events, shared primitives, references and the [module contract](../concepts/module-contract.md).
- Nexus does not understand domain concepts (Person, Message, Photo, Place, Note). It holds infrastructure state, and state about modules is derived where possible (e.g. the reference index is rebuildable from modules).
- Other modules reference resources by identity rather than copying them. Derived, non-authoritative, rebuildable caches are allowed ([interoperability](../concepts/interoperability.md#derived-data-and-projection)).

## Consequences

- Cross-module features (aggregated profiles, maps, calendars, search) need references, owner-side resolution and derived data rather than joins.
- Nexus stays replaceable and restorable without being the source of domain truth.
- Community modules can participate through the same interfaces, but that requires an enforced isolation boundary before they can be treated as untrusted-but-safe ([principals-and-permissions](../concepts/principals-and-permissions.md)).
