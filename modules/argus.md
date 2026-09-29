# Argus

## Purpose

The photo, video and media domain. Detailed product scope is **not yet finalized**.

## Owns

Current direction:

- personal photo/video library
- media metadata, including EXIF
- local recognition capabilities
- face grouping/recognition

## Does not own

- People ([Meridian](meridian.md)); Argus links recognized faces to Meridian people by reference.
- Physical blobs ([storage-and-files](../concepts/storage-and-files.md)); media files are shared blobs.
- Places ([Atlas](atlas.md)) and events ([Chronos](chronos.md)).

## Integrations

- **Meridian**: `argus://photo/928 depicts meridian://person/42`. Meridian discovers related media through backlinks.
- **Atlas**: `taken_at` place. **Chronos**: `related_to` event.
- **Collectors**: photos from an opt-in Android companion.
- **Immich**: possible foundation or integration (see below).
- Media can be attached or embedded elsewhere (Hermes attachments, Documents) via shared blobs and references.

## Established decisions

- Recognition is **local**.
- Connecting a face to a person is an **explicit reference** to a Meridian person.
- Purpose is the user's own library and known-person organization, **not identifying arbitrary strangers**.
- Prefer integrating with strong existing open-source technology such as Immich where appropriate rather than unnecessarily rebuilding it.

## Planned direction

- Library ingestion, metadata extraction, browsing.
- Face grouping and user-confirmed linking to Meridian people.

## Open questions

- Build vs. integrate: whether Argus wraps Immich, sits alongside it, or replaces parts; what Argus owns if Immich remains the media store.
- Which recognition models, and their licensing/hardware requirements.
- How face-to-person confirmation works and where face embeddings are stored (privacy/portability implications).
- Ownership of media files when an external system (Immich) holds them, and consequences for backup/restore.
- Scope of video handling.
- Product scope and priorities.
