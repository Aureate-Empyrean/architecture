# Documents

The final product name is **undecided**. "Documents" is a working title. If a name is chosen it should preferably follow the single-word module naming convention.

## Purpose

Document creation and editing, as a module, so that Nexus itself never becomes a document editor. Detailed scope is **not yet finalized**.

## Owns

Potential long-term capabilities:

- rich text
- Markdown
- templates
- version history
- autosave
- attachments
- PDF export
- possibly spreadsheets/presentations much later

## Does not own

- The referenced resources it embeds (people, places, events, conversations, media) — it stores references, not copies.
- Physical file storage ([storage-and-files](../concepts/storage-and-files.md)).
- Notes, tasks, projects and the knowledge base ([Mnemosyne](mnemosyne.md)). Conceptually, Mnemosyne is knowledge the user maintains and works in, while Documents is artifacts for reading, sending, publishing or export. The boundary is not absolute; resources may reference each other.

## Integrations

A defining capability is embedding/referencing resources from other modules through [cross-module references](../concepts/cross-module-references.md). Illustrative:

- `@person` → Meridian person
- `@place` → Atlas Place, Area or Route
- `@event` → Chronos event
- conversation → Hermes conversation

Documents and their attachments use shared blob storage.

## Established decisions

- Document editing is a module, not part of Nexus.
- Embedding/referencing other modules' resources is a core capability.

## Planned direction

- Rich text/Markdown editing with resource references, autosave, version history, attachments, PDF export.

## Open questions

- Product name.
- Exact boundary with Mnemosyne and whether they share a content model (they are conceptually separate modules).
- Document storage format (must be portable/open).
- How references render when the target is unavailable or the viewer lacks permission.
- Whether generic document rendering/PDF export is provided here for other modules, e.g. Meridian's selective Person PDF export ([meridian](meridian.md#selective-pdf-export)); undecided.
- Whether spreadsheets/presentations are in scope at all.
- Collaboration/real-time editing (out of scope for single-user first).
