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
