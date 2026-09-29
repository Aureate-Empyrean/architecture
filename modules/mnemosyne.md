# Mnemosyne

## Purpose

The personal knowledge domain. Detailed product scope is **not yet finalized**.

## Owns

Potential direction:

- notes
- Markdown
- backlinks
- documents/knowledge entries
- references to ecosystem resources
- search

## Does not own

- Knowledge specifically about people (Meridian facts, notes, quotes).
- Communications, media, places, events (other modules).
- Rich document authoring ([Documents](documents.md)) — the exact boundary is undecided.

## Integrations

- References to and from any ecosystem resource (people, conversations, places, events, media) via [cross-module references](../concepts/cross-module-references.md).
- Backlinks: notes link to resources, and resources can discover the notes that mention them.
- Search across its own domain; ecosystem-wide search is an open question.

## Established decisions

- Mnemosyne is not to be a simple clone of Obsidian; its value is in participating in the ecosystem's references.

## Planned direction

- Markdown notes with links/backlinks and embedded ecosystem references.
- Search.

## Open questions

- **Boundary with [Documents](documents.md)**: whether they are separate modules, one module, or share a content model.
- Storage model: database-owned notes vs. plain files on disk (interop with existing Markdown tools).
- Whether Meridian notes about a person stay in Meridian or are Mnemosyne notes referencing that person.
- Ecosystem-wide search: which module or Nexus service owns it.
- Reference syntax inside Markdown (see [cross-module-references](../concepts/cross-module-references.md)).
- Product scope and priorities.
