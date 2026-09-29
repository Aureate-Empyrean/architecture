# Storage and Files

Aureate Empyrean should eventually provide a user-facing file explorer and shared storage system. This may be a **Nexus capability** rather than a domain module.

## Key distinction

**Logical organization does not have to equal physical disk layout.** A file may physically exist once while being referenced by multiple resources:

```
one image blob
    <- Argus photo
    <- Hermes attachment
    <- Meridian attachment
    <- Document reference
```

## Established decisions

- Logical organization is decoupled from physical layout.
- Not every file is forced into an entity graph; normal user-created folders must remain possible.
- Do not prematurely implement a Dropbox replacement.

## Planned direction

- Investigate **content-addressed storage** and deduplication: `SHA-256(file) -> blob`, with multiple logical resources referencing the same blob.
- A user-facing file explorer offering: normal folders, recent files, search, module-based views, entity/reference-based views, metadata, tags.
- Modules store file content as blob references rather than private copies.
- Standard access/sync (WebDAV or a dedicated sync client) may be considered later.

## Open questions

- Whether content-addressing is adopted at all (investigation, not a decision), and hash choice details.
- Blob lifecycle: reference counting vs. garbage collection; when a blob is deleted; safe deletion when several resources reference it.
- Ownership/permissions of a blob referenced by several modules and possibly different permission scopes.
- Relationship between blob store and external stores (e.g. Immich holding original media — see [Argus](../modules/argus.md)).
- Physical layout on disk and how it stays understandable/recoverable without Nexus (portability).
- Metadata and tag storage: in Nexus, or per owning module.
- Encryption at rest.
- Large-file handling, streaming, resumable upload.
- Sync mechanism (WebDAV vs. dedicated client) and conflict handling.
- Handling of files that users place in plain folders but which are also managed by a module.
- Interaction with backups: deduplication and incremental behavior ([backups.md](backups.md)).
