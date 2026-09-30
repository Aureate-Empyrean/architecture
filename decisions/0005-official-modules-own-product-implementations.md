# 0005. Official modules own their product implementations

- Status: Accepted (2026-09-30)

## Context

Earlier documents suggested Argus might wrap Immich and Lyra might build on Jellyfin as runtime backends. A wrapped complete application brings its own storage, identities, backups, permissions and UX, which conflicts with module ownership, UUID identity, shared backup/restore and a coherent ecosystem experience.

## Decision

Official modules own their domain implementation and user experience. Complete end-user applications such as Immich, Jellyfin and Navidrome are product references ("they provide capability X; Aureate Empyrean should provide an integrated equivalent"), not runtime backends. Mature libraries, protocols, codecs, databases and focused infrastructure components (e.g. FFmpeg, PostgreSQL, open standards) should be reused where appropriate.

Reuse components and standards; do not build an official product as a thin wrapper around another complete product.

## Consequences

- Argus and Lyra carry more implementation work than a wrapper would.
- Their data follows ecosystem identity, reference, storage and backup rules.
- Existing libraries in those products' formats may still be import sources.
