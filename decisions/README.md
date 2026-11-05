# Architecture Decisions

Lightweight Architecture Decision Records (ADRs) for foundational choices whose reasoning would otherwise be lost. The architecture documents remain canonical for current rules; ADRs explain **why** foundational decisions were made.

## When to write an ADR

- The choice constrains multiple modules or repositories.
- It would be expensive to reverse.
- Someone will later ask "why did we do it this way?"

Do not write ADRs for local implementation choices inside one repository, and do not create them for every decision.

## Format

File name: `NNNN-short-title.md` (zero-padded, sequential, never renumbered).

```markdown
# NNNN. Title

- Status: Proposed | Accepted (YYYY-MM-DD) | Superseded by NNNN | Rejected

## Context
What forces and constraints make this decision necessary.

## Decision
What was decided, stated plainly.

## Consequences
What becomes easier, what becomes harder, what is now required of other documents/repos.
```

## Rules

- Accepted ADRs are not edited except for status and links. Change a decision by writing a new ADR that supersedes it.
- When an ADR is accepted, update affected documents so they reference it and stay consistent.

## Index

| ADR | Title |
|---|---|
| [0001](0001-module-ownership-and-domain-ignorant-nexus.md) | Module ownership and a domain-ignorant Nexus |
| [0002](0002-uuid-resource-identity.md) | UUID resource identity |
| [0003](0003-local-knowledge-first.md) | Local knowledge first; explicit external discovery |
| [0004](0004-janus-custody-and-secret-classes.md) | Janus custody and the user-secret/service-secret distinction |
| [0005](0005-official-modules-own-product-implementations.md) | Official modules own their product implementations |
| [0006](0006-astra-contextual-intelligence.md) | Astra as a contextual intelligence layer, not a data owner |
| [0007](0007-postgresql-default-database.md) | PostgreSQL as the default server database |
| [0008](0008-shared-design-tokens.md) | One canonical token file, vendored by each application |
| [0009](0009-janus-client-delivery.md) | Janus clients are independently released standalone applications |
| [0010](0010-module-network-egress.md) | Outbound network access is an explicit, reviewable module capability |
| [0011](0011-projection-queries.md) | Projection queries: Nexus forwards scope-granted queries to owning modules |
| [0012](0012-design-system-v2.md) | Design system v2: layered surfaces, restrained translucency and a shared foundation stylesheet |
| [0013](0013-chronos-and-lyra-v1-boundaries.md) | Chronos and Lyra v1 boundaries: owned resources, external providers and derived calendar data |
| [0014](0014-liquid-glass-surfaces.md) | Liquid-glass surfaces and per-application atmosphere |
| [0015](0015-training-books-and-delivery-sequencing.md) | Training ownership, Lyra Books and self-hosted sequencing |
| [0016](0016-janus-independent-web-client.md) | Janus web client on an independent origin (Proposed) |
| [0017](0017-janus-devices-sync-and-clients.md) | Janus product direction: independent devices, encrypted snapshot sync and a family of clients |
| [0018](0018-nexus-remote-access.md) | Nexus Remote Access: private-network enrollment owned by Nexus (long-term) |
| [0019](0019-argus-interim-media-storage.md) | Argus keeps media in its own volume until shared storage can hold it |
