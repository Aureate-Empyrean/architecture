# 0015. Training ownership, Lyra Books and self-hosted sequencing

- Status: Accepted (2026-11-03)
- Extends [0001](0001-module-ownership-and-domain-ignorant-nexus.md), [0005](0005-official-modules-own-product-implementations.md) and [0011](0011-projection-queries.md); preserves [0009](0009-janus-client-delivery.md).

## Context

An existing standalone training application is a possible module starting point, and books need a clear home including physical bookshelves. Neither should blur domain ownership, imply completed integration, or redirect current work toward app-store releases.

## Decision

- [Enceladus](../modules/enceladus.md) is the training module name. It owns training rules and records and works without Chronos, retaining simple schedule/date editing and full history. Chronos owns calendar presentation; the initial integration is Planned as a read-only, date-scoped projection with owner deep links. Exact contracts remain Open. Nexus supplies registration, permissions and integration, never workout data; databases are not shared.
- Gymless is an unrenamed, unintegrated React PWA/server-SQLite starting point. Audit blockers and persistence/backup hardening must be addressed before integration. Its convenience APK creates no current priority for further APK/mobile development.
- [Books](../modules/lyra.md#books-physical-and-digital) are a Lyra domain, including physical and digital copies linked to bibliographic works/editions. Lyra owns metadata and copy references; shared storage owns blobs. Schemas, reader/format capabilities, import and sync remain Open, not implemented claims.
- First build and stabilize a useful fully self-hosted integrated ecosystem. Only later consider standalone mobile/app-store distribution or managed SaaS. This does not make every module a mobile app.
- Janus remains independently delivered for its established security boundary; Nexus must never serve, embed or update executable Janus client code.

## Consequences

- Integration must preserve owner authority, UUID identities, permissions and reliable restore, rather than copy training truth into calendar Events or fragment book copies into unrelated titles.
- Existing Movies/TV tracking and future native media infrastructure in Lyra remain intact. External *arr tooling, Jellyfin and Plex are not runtime dependencies.
- This records scope and sequence, not deployed capabilities, release dates, pricing, database migration or a commitment to readers, wearables, nutrition tracking or an AI coach.
- Historical design ADRs 0012 and 0014 received terminology-only edits to describe the standalone book-library prototype without its former product name. Their accepted design decisions are unchanged.
