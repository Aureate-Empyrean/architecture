# Cross-Module References

Cross-module references are a **first-class architectural primitive**. They are how modules link to each other's resources without sharing databases.

## Concept

A reference identifies a resource owned by a module by its stable UUID identity ([resource-identity-and-lifecycle](resource-identity-and-lifecycle.md)). Conceptual examples (exact syntax is subject to implementation design; `<uuid>` stands for a resource's UUID):

```
meridian://person/0192a5d3-7c1e-7b2a-9f4e-3d8c1b6a2e10
meridian://organization/<uuid>
hermes://message/<uuid>
hermes://conversation/<uuid>
argus://photo/<uuid>
atlas://place/<uuid>     atlas://area/<uuid>     atlas://route/<uuid>
chronos://event/<uuid>
lyra://track/<uuid>
```

A **relation** connects two references:

```
argus://photo/<uuid>
    depicts    -> meridian://person/<uuid>
    taken_at   -> atlas://place/<uuid>   (or an Area or Route, where that is the real geography)
    related_to -> chronos://event/<uuid>
```

A reference may also point to a resource owned by a module the referring module knows nothing about, e.g. a Meridian favorite-song preference pointing at `lyra://track/<uuid>`. Meridian owns the preference; [Lyra](../modules/lyra.md) owns the track.

Given a `meridian://person/<uuid>`, the ecosystem should be able to discover resources that reference it (e.g. Argus photos, Hermes communication identities and conversations, Chronos events, Mnemosyne notes). This is **reverse lookup / backlinks**. Resources the Person record itself references (e.g. Atlas Places, Areas or Routes in Meridian place associations) are forward references, not backlinks.

## Established decisions

Requirements for references:

- Globally unambiguous within a Nexus installation.
- Identifies the owning module.
- Identifies the resource type.
- Carries the resource's stable UUID identity, never a database-local ID ([resource-identity-and-lifecycle](resource-identity-and-lifecycle.md)).
- Is an identity reference, not a frozen copy of the resource's values, and never silently points to a different resource after restore, import or recreation.
- Safe to parse and validate.
- Independent of UI routes.
- Supports resolution.
- Supports reverse lookup/backlinks.

Responsibilities:

- **Nexus** provides the reference/index/discovery mechanism.
- **Owning modules** remain authoritative for resource data and decide how references to their resources are presented.
- References survive temporary module disablement or unavailability as **unresolved references**; they are never silently destroyed.
- Cross-module access **respects permissions**. Permissions constrain the calling principal: module code, devices, connectors and future users, not only a human ([principals-and-permissions](principals-and-permissions.md)).
- Resolution yields a state (available, unavailable, trashed, deleted, not accessible, redirected); see [resolution states](resource-identity-and-lifecycle.md#resolution-states).
- **A reference does not imply access to the referenced resource's private contents.** Knowing or holding a reference is not authorization to read the resource.
- **Backlink/discovery queries respect permissions.** The existence of a relation can itself be sensitive information (see the Janus example below).
- **Third-party/community modules** can participate without being hard-coded into Nexus or official modules.
- Not a graph database, semantic knowledge graph, AI inference engine or distributed identity system.

## Example: why discovery must be permission-aware

A Janus item (e.g. a Login) may reference a Meridian person: `janus://login/<uuid> belongs_to -> meridian://person/<uuid>`. Meridian must not receive secret material merely because of this, and must also not automatically be able to answer "does Janus hold a credential belonging to this person?", because that fact, or even the credential's label, may itself be sensitive. A backlink query is therefore authorized like any other read: only contexts explicitly permitted to know that the relation exists may discover it. See [Janus](../modules/janus.md).

## Planned direction

- Modules declare their resource types to Nexus.
- Nexus maintains an index of references/relations to answer backlink queries. The index is derived state, rebuildable from modules' outgoing-reference enumeration ([module-contract](module-contract.md)).
- Consumers render references through owner-provided presentation (e.g. a title/summary and link) rather than knowing domain internals.

## Open questions

- **Exact syntax** and grammar (scheme, ID format, allowed characters, versioning).
- Identity details beyond the established UUID rule: syntax and encoding, how long deleted/redirected identities are remembered, redirect chains, identity policy for imports that do not preserve identity ([resource-identity-and-lifecycle](resource-identity-and-lifecycle.md#open-questions)). Merges may redirect identities (established direction).
- Relation vocabulary: free-form, registered, or namespaced (`depicts`, `taken_at`); who defines them; whether relations have direction/inverse/metadata.
- Index population: modules push changes, Nexus pulls, or events; consistency and rebuild after restore.
- Permission model for backlink queries (a backlink may itself reveal information); concretely the permission model for secret backlinks such as Janus → Meridian.
- Whether sensitive modules (e.g. Janus) publish relations to the normal Nexus index at all, since the index itself could reveal that a secret exists; alternatives (not centrally indexing some references, or a stricter protected index) are undecided, as is what reference metadata Nexus may know. Nexus not needing plaintext vault contents does not by itself answer this.
- How module-internal organizational context (e.g. a Mnemosyne Workspace) is carried in resolution/presentation so consumers can display or filter it. Workspaces are organizational, not authorization boundaries ([principals-and-permissions](principals-and-permissions.md)).
- Reference behavior for local-first clients that are offline or never connected ([local-first-and-sync](local-first-and-sync.md)).
- Generic resource picker and create-target mechanism: how a module referencing another module's resource can select an existing one, open it, or create a new target (e.g. creating an Atlas Place/Area/Route while editing a Meridian record) and receive the reference back, without module-specific integrations ([atlas](../modules/atlas.md#cross-module-resource-creation-planned-direction)). Not designed.
- How modules declare that their resources are projectable onto another module's view (e.g. geographic projection on the Atlas map), and how permissions apply.
- Resolution contract: what a module returns for a reference (summary, type, URL, availability) and how third-party modules implement it.
- Wire representation of resolution states, and how users repair or clean unresolved references ([resolution states](resource-identity-and-lifecycle.md#resolution-states)).
- Interaction with backup/restore (references must round-trip; see [backups.md](backups.md)).
- Embedding syntax in text content (Markdown/rich text), see [Documents](../modules/documents.md) and [Mnemosyne](../modules/mnemosyne.md).

## Non-goals (for now)

Graph query languages, inferred relations, cross-installation federation.
