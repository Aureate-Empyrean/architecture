# 0002. UUID resource identity

- Status: Accepted (2026-09-30)

## Context

Every module stores references to other modules' resources in its own storage. Early examples used small integers (`person/42`), which invite database auto-increment IDs. Those break under local-first creation (collisions), restore or import into a fresh installation (IDs reassigned, so references silently point to the wrong resource) and merges. Changing identity later would be a data migration across every repository.

## Decision

Every persistent first-class resource has a stable UUID identity, generated without coordination through Nexus, never reused, stable for the resource's lifetime, preserved by backup/restore and identity-preserving migration, and used by all cross-module references. Database-local numeric IDs may exist internally but are never public or cross-module identity. UUIDv7 is preferred if a version must be chosen; syntax and encoding stay implementation details. See [resource-identity-and-lifecycle](../concepts/resource-identity-and-lifecycle.md).

## Consequences

- Offline and local-first clients can create resources safely.
- References cannot silently retarget after restore or import; a recreated resource is a new identity.
- Merges can redirect identities instead of breaking references.
- Modules pay a small cost of carrying a second identifier where they also use internal integer keys.
