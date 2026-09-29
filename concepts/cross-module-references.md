# Cross-Module References

Cross-module references are a **first-class architectural primitive**. They are how modules link to each other's resources without sharing databases.

## Concept

A reference identifies a resource owned by a module. Conceptual examples (exact syntax is subject to implementation design):

```
meridian://person/42
meridian://organization/18
hermes://message/918281
hermes://conversation/91
argus://photo/928
atlas://place/17
chronos://event/551
```

A **relation** connects two references:

```
argus://photo/928
    depicts    -> meridian://person/42
    taken_at   -> atlas://place/17
    related_to -> chronos://event/551
```

Given `meridian://person/42`, the ecosystem should be able to discover resources that reference it (Argus photos, Hermes conversations/messages, Chronos events, Atlas places/visits). This is **reverse lookup / backlinks**.

## Established decisions

Requirements for references:

- Globally unambiguous within a Nexus installation.
- Identifies the owning module.
- Identifies the resource type.
- Carries a stable resource identifier.
- Safe to parse and validate.
- Independent of UI routes.
- Supports resolution.
- Supports reverse lookup/backlinks.

Responsibilities:

- **Nexus** provides the reference/index/discovery mechanism.
- **Owning modules** remain authoritative for resource data and decide how references to their resources are presented.
- References survive temporary module disablement or unavailability as **unresolved references**; they are never silently destroyed.
- Cross-module access **respects permissions**.
- **A reference does not imply access to the referenced resource's private contents.** Knowing or holding a reference is not authorization to read the resource.
- **Backlink/discovery queries respect permissions.** The existence of a relation can itself be sensitive information (see the Janus example below).
- **Third-party/community modules** can participate without being hard-coded into Nexus or official modules.
- Not a graph database, semantic knowledge graph, AI inference engine or distributed identity system.

## Example: why discovery must be permission-aware

A Janus credential may reference a Meridian person: `janus://credential/… belongs_to -> meridian://person/123`. Meridian must not receive secret material merely because of this, and must also not automatically be able to answer "does Janus hold a credential belonging to this person?", because that fact may itself be sensitive. A backlink query is therefore authorized like any other read: only contexts explicitly permitted to know that the relation exists may discover it. See [Janus](../modules/janus.md).

## Planned direction

- Modules declare their resource types to Nexus.
- Nexus maintains an index of references/relations to answer backlink queries.
- Consumers render references through owner-provided presentation (e.g. a title/summary and link) rather than knowing domain internals.

## Open questions

- **Exact syntax** and grammar (scheme, ID format, allowed characters, versioning).
- Identifier stability: what happens on module reinstall, ID migration, import into a fresh installation, and merging (e.g. two Meridian persons merged — do references redirect?).
- Relation vocabulary: free-form, registered, or namespaced (`depicts`, `taken_at`); who defines them; whether relations have direction/inverse/metadata.
- Index population: modules push changes, Nexus pulls, or events; consistency and rebuild after restore.
- Permission model for backlink queries (a backlink may itself reveal information); concretely the permission model for secret backlinks such as Janus → Meridian.
- Whether sensitive modules (e.g. Janus) publish relations to the normal Nexus index at all, since the index itself could reveal that a secret exists; alternatives (not centrally indexing some references, or a stricter protected index) are undecided, as is what reference metadata Nexus may know. Nexus not needing plaintext vault contents does not by itself answer this.
- Reference behavior for local-first clients that are offline or never connected ([local-first-and-sync](local-first-and-sync.md)).
- Resolution contract: what a module returns for a reference (summary, type, URL, availability) and how third-party modules implement it.
- Representation of unresolved references and how users repair or clean them.
- Interaction with backup/restore (references must round-trip; see [backups.md](backups.md)).
- Embedding syntax in text content (Markdown/rich text), see [Documents](../modules/documents.md) and [Mnemosyne](../modules/mnemosyne.md).

## Non-goals (for now)

Graph query languages, inferred relations, cross-installation federation.
