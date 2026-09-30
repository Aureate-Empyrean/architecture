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
- Shared blob storage must not imply that confidential modules' files are stored in plaintext. [Janus](../modules/janus.md) attachments must not be stored plaintext merely because other modules use generic blob storage; the mechanism is Open.
- Permanently deleting a resource that references a blob must follow shared-storage ownership/reference rules and never blindly delete a blob still referenced elsewhere (first stated for [Mnemosyne Trash](../modules/mnemosyne.md#trash)). Modules using shared storage report which blobs they use ([module-contract](module-contract.md)), so blob usage is never known only to Nexus. No global garbage-collection algorithm is chosen (Open, below).
- Restore does not trigger blob deletion before blob usage has been reconciled ([resource lifecycle](resource-identity-and-lifecycle.md#restore)).

## Planned direction

- Investigate **content-addressed storage** and deduplication: `SHA-256(file) -> blob`, with multiple logical resources referencing the same blob.
- A user-facing file explorer offering: normal folders, recent files, search, module-based views, entity/reference-based views, metadata, tags.
- Modules store file content as blob references rather than private copies (e.g. a [Lyra](../modules/lyra.md) track references a shared audio blob while owning the music meaning).
- Standard access/sync (WebDAV or a dedicated sync client) may be considered later.

## Open questions

- Whether content-addressing is adopted at all (investigation, not a decision), and hash choice details.
- Blob lifecycle: reference counting vs. garbage collection; when a blob is deleted; safe deletion when several resources reference it.
- Ownership/permissions of a blob referenced by several modules and possibly different permission scopes.
- Relationship between the blob store and user-selected folders on disk (e.g. an existing photo or music library the user wants managed in place). External applications are not runtime backends ([ADR 0005](../decisions/0005-official-modules-own-product-implementations.md)).
- Physical layout on disk and how it stays understandable/recoverable without Nexus (portability).
- Metadata and tag storage: in Nexus, or per owning module.
- Encryption at rest, and how client-side-encrypted files (e.g. Janus attachments) coexist with content-addressing and deduplication (equality of ciphertext or plaintext hashes can itself leak information).
- Large-file handling, streaming, resumable upload.
- Sync mechanism (WebDAV vs. dedicated client) and conflict handling.
- Handling of files that users place in plain folders but which are also managed by a module.
- Interaction with backups: deduplication and incremental behavior ([backups.md](backups.md)).
