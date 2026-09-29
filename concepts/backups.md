# Backups, Export and Restore

Aureate Empyrean may hold a large portion of a user's personal digital history. Backup and restore are first-class system concerns.

## Established decisions

- **Restore is as important as backup creation.** A backup that cannot be reliably restored is not a valid backup system.
- **Backups must not casually convert module data that is locally or end-to-end encrypted into plaintext.** For [Janus](../modules/janus.md), a Nexus system backup must not turn an encrypted vault into plaintext merely because Nexus is creating the backup. How this is achieved is not decided.
- Do not invent proprietary compression algorithms.
- Already-compressed media (JPEG, modern video, compressed audio) must not waste significant CPU on ineffective recompression.

## Planned direction

Desired properties:

- portable, documented/open format
- compressed
- optionally strongly encrypted
- integrity checked
- restorable
- incremental where practical, so unchanged large media libraries are not repeatedly archived
- deduplicated where practical

Conceptual export contents:

- manifest
- Nexus metadata
- module databases/data
- files/blobs
- module configuration
- cross-module references
- checksums

Text/database/JSON-style data compresses heavily; media generally does not, so compression should be applied selectively.

A future archive format may use established components (e.g. tar + zstd + encryption) while presenting a recognizable Aureate Empyrean archive format.

Nexus provides backup/export/restore infrastructure; modules participate by exporting and restoring their own data.

## Open questions

- Module contract for data that Nexus cannot or should not read (e.g. encrypted Janus state): how it is exported, whether it is included opaquely, and how integrity is checked without plaintext.
- Recovery vs. confidentiality for Janus: key-loss recovery is essential for a password manager but must not undermine vault confidentiality. This tension is unresolved and requires dedicated security design.
- Standalone local-first clients that never connect to Nexus are not covered by Nexus backups; how they back up and restore is undefined ([local-first-and-sync.md](local-first-and-sync.md)).
- Archive format specification and versioning, and whether it is "tar + zstd + X" or something else.
- Encryption scheme and key management (passphrase, key files), and recovery story.
- Module contract: how a module exports a consistent snapshot of its data (e.g. DB dump vs. native export), and how it restores.
- Consistency across modules: point-in-time snapshot vs. per-module backups.
- Incremental design: chunking, deduplication interplay with content-addressed storage ([storage-and-files.md](storage-and-files.md)), retention.
- Backup-before-update: whether/when a backup is recommended or required before updates or migrations ([updates.md](updates.md)).
- Restore validation: verification, dry-run, partial restore (single module), restore into a different version, disaster-recovery tests.
- Handling of modules that are absent at restore time (references become unresolved, per [cross-module-references.md](cross-module-references.md)).
- Handling data held by external systems (e.g. Immich) that Aureate Empyrean does not fully own.
- Scheduling and destinations (local disk, remote self-hosted targets) without requiring a cloud.
- Relationship between backup format and general user-facing data export/portability.
