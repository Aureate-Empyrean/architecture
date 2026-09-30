# 0007. PostgreSQL as the default server database

- Status: Accepted (2026-09-30)

## Context

Official server-hosted modules need a relational store that scales, handles concurrent server workloads, and offers strong indexing, JSONB, full-text search, mature operations, PostGIS for Atlas and optional pgvector if semantic search is ever justified. SQLite was at risk of becoming the implicit default for server modules.

## Decision

PostgreSQL is the preferred/default database for server-hosted official modules where a relational database is appropriate. This does not create a shared application database: modules use separate databases, schemas, roles or equivalent isolation, and never query another module's tables. SQLite remains acceptable for lightweight standalone tools, local caches, tests, prototypes and modules whose deployment favors it. The module contract stays storage-engine independent. Janus storage follows its own security design. Vector search is not added merely because pgvector exists.

## Consequences

- Deployments include a PostgreSQL service; topology (one server with per-module databases, or more) is Open.
- Sharing a server increases the importance of enforced per-module isolation ([principals-and-permissions](../concepts/principals-and-permissions.md)).
