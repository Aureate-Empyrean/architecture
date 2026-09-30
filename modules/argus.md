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
- Media the user collects and consumes (albums, movies, TV, books, audiobooks) ([Lyra](lyra.md)). Argus primarily owns the user's own captured/imported media, such as personal photos and videos; the boundary is conceptual, not absolute ([lyra](lyra.md#boundary-with-argus)).

## Integrations

- **Meridian**: `argus://photo/<uuid> depicts meridian://person/<uuid>`. Meridian discovers related media through backlinks. The user's own recognized face is associated with the distinguished self Person `@me` ([meridian](meridian.md#the-distinguished-self-person-me)).
- **Atlas**: `taken_at` place, and, since geographic meaning may be an Area or Route rather than a point, potentially other Atlas resources. Argus owns the photo and its metadata; Atlas may project its geographic metadata without owning it, including through a derived, non-authoritative cache for large libraries ([interoperability](../concepts/interoperability.md#derived-data-and-projection)). **Chronos**: `related_to` event.
- **Collectors**: photos from an opt-in Android companion.
- **Product references**: applications such as Immich are studied as references for capabilities Argus should provide; they are not runtime backends ([ADR 0005](../decisions/0005-official-modules-own-product-implementations.md)).
- Media can be attached or embedded elsewhere (Hermes attachments, Documents) via shared blobs and references.

## Established decisions

- Recognition is **local**.
- Connecting a face to a person is an **explicit reference** to a Meridian person.
- Purpose is the user's own library and known-person organization, **not identifying arbitrary strangers**.
- Argus owns its media-management implementation and user experience; it is not a frontend to another complete application such as Immich. Mature components and standards (e.g. FFmpeg, image/video codecs, focused recognition libraries) are reused where appropriate ([ADR 0005](../decisions/0005-official-modules-own-product-implementations.md)).

## Planned direction

- Library ingestion, metadata extraction, browsing.
- Face grouping and user-confirmed linking to Meridian people.

## Open questions

- Which components and libraries to reuse (media processing, thumbnails, recognition).
- Import from existing photo libraries and folders (including libraries previously managed by other applications) and whether files may be managed in place.
- Which recognition models, and their licensing/hardware requirements.
- How face-to-person confirmation works and where face embeddings are stored (privacy/portability implications).
- Scope of video handling.
- Product scope and priorities.
