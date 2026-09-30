# Aureate Empyrean — Architecture

This repository is the canonical architecture and specification source for the Aureate Empyrean ecosystem. It contains **no application code**.

Architecture should not live only in AI conversations or in individual implementation repositories. Humans and AI coding agents should read the relevant documents here before working on any Aureate Empyrean repository. If an implementation repository contradicts this repository, either the implementation or this repository is wrong; resolve it explicitly (see [decisions/](decisions/README.md)) rather than letting them drift.

Aureate Empyrean is an open-source, self-hosted, privacy-first ecosystem of interoperable personal software.

> Aureate Empyrean does not observe your life.
> It remembers what you choose to give it.

## Status vocabulary

Every substantive statement in this repository should be classifiable as one of:

| Label | Meaning |
|---|---|
| **Established** | A decision that has been made. Changing it requires a deliberate decision (ideally an ADR). |
| **Planned** | The intended direction. Reasonably settled in spirit, not in detail. May change. |
| **Open** | An idea or question that needs design before anything depends on it. |

Documents that mix these use explicit headings ("Established decisions", "Planned direction", "Open questions"). Do not treat Planned or Open items as commitments, and do not implement them speculatively.

## Layout

| Path | Contents |
|---|---|
| [principles.md](principles.md) | Durable principles: privacy, ownership, self-hosting, technology posture |
| [architecture.md](architecture.md) | Ecosystem structure, Nexus/module boundary, deployment, naming, licensing, trust |
| [modules/](modules/) | One document per module: purpose, ownership, integrations, decisions, open questions |
| [concepts/](concepts/) | Shared mechanisms that span modules |
| [decisions/](decisions/README.md) | Lightweight ADRs for important architectural choices |

### Modules

[Nexus](modules/nexus.md) · [Meridian](modules/meridian.md) · [Hermes](modules/hermes.md) · [Atlas](modules/atlas.md) · [Argus](modules/argus.md) · [Janus](modules/janus.md) ([security design](modules/janus-security-design.md)) · [Lyra](modules/lyra.md) · [Chronos](modules/chronos.md) · [Mnemosyne](modules/mnemosyne.md) · [Documents](modules/documents.md)

### Concepts

[Cross-module references](concepts/cross-module-references.md) · [Storage and files](concepts/storage-and-files.md) · [Backups](concepts/backups.md) · [Collectors](concepts/collectors.md) · [Local-first and sync](concepts/local-first-and-sync.md) · [Localization](concepts/localization.md) · [Updates](concepts/updates.md) · [Design system](concepts/design-system.md) · [Interoperability](concepts/interoperability.md)

## Reading order

1. `principles.md`
2. `architecture.md`
3. The module document(s) for the area you are touching
4. Any concept documents those modules link to

## Editing rules

- Keep documents concise and technical. No marketing copy.
- Do not invent functionality to fill a section. "Not yet designed" is a valid content.
- Mark status honestly; promote Open → Planned → Established only when a decision is actually made.
- Record important architectural choices as ADRs in `decisions/` and link them from the affected documents.
- Exact identifier syntax, API shapes, schemas and wire formats belong in implementation-level specs once designed; this repository defines boundaries and responsibilities first.
