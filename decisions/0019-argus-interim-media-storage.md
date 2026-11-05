# 0019. Argus keeps media in its own volume until shared storage can hold it

- Status: Accepted (2026-11-05)

## Context

[Argus](../modules/argus.md) says media files are shared blobs, and [storage-and-files](../concepts/storage-and-files.md) places physical blobs outside modules. Nexus implements a shared blob primitive (0.1.2), but it cannot hold a photo library:

- files are limited to 1 MiB;
- a blob can be read only by a module granted through its own upload; knowing a digest grants nothing, and no module can grant another access;
- usage reporting and garbage collection are not implemented, so blobs are never deleted.

Module requests and responses are limited to 2 MiB, modules run in 256 MiB with a read-only root filesystem, and a module UI can reach only its own API through the Nexus bridge.

The owner's Argus brief (2026-11-05) said not to assume shared storage, and to use an isolated, documented Argus-owned adapter if the primitive is unavailable, without building a storage platform in that milestone.

## Decision

Until shared storage can hold photos, Argus stores originals and derivatives in its own `storage.data` volume, behind one adapter (`backend/argus/storage.py`).

- A photo's identity is a UUID, never derived from its bytes or location, so moving bytes changes no reference.
- Inside the adapter, originals are content-addressed by SHA-256 and write-once. This is a local choice, not a decision on the Open question of ecosystem-wide content addressing.
- A blob is deleted only when no Argus photo uses it. Other modules never receive a path or blob key.
- Other modules obtain images through scope-granted [projection queries](../concepts/projections.md), not through the files.

This is a deviation from the planned shared-blob direction, not a replacement for it. The ownership statement in Argus's document stays the direction.

## Consequences

- Module backup carries the originals inside Argus's own artifact, and is limited to Nexus's 256 MiB per artifact, so it does not yet suit a large library.
- Identical bytes are de-duplicated only within Argus, not across modules.
- Moving to shared storage later requires larger files, cross-module read grants, and usage reporting in Nexus, followed by a migration inside Argus.
