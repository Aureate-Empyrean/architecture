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

These labels classify **decisions**, not implementation maturity: a module can have Established decisions while not yet being implemented. For what actually exists, see [implementation status](implementation-status.md).

Documents that mix these use explicit headings ("Established decisions", "Planned direction", "Open questions"). Do not treat Planned or Open items as commitments, and do not implement them speculatively.

## Layout

| Path | Contents |
|---|---|
| [principles.md](principles.md) | Durable principles: privacy, ownership, self-hosting, technology posture |
| [architecture.md](architecture.md) | Ecosystem structure, Nexus/module boundary, deployment, naming, licensing, trust |
| [implementation-status.md](implementation-status.md) | What is implemented, verified and deployed, per module |
| [modules/](modules/) | One document per module or named ecosystem capability: purpose, ownership, integrations, decisions, open questions |
| [concepts/](concepts/) | Shared mechanisms that span modules |
| [design/](design/tokens.css) | Canonical shared design tokens and foundation stylesheet, vendored by applications ([ADR 0008](decisions/0008-shared-design-tokens.md), [ADR 0012](decisions/0012-design-system-v2.md)) |
| [decisions/](decisions/README.md) | Lightweight ADRs explaining why foundational decisions were made |

### Modules

[Nexus](modules/nexus.md) · [Meridian](modules/meridian.md) · [Hermes](modules/hermes.md) · [Atlas](modules/atlas.md) · [Argus](modules/argus.md) · [Janus](modules/janus.md) ([security design](modules/janus-security-design.md)) · [Lyra](modules/lyra.md) · [Enceladus](modules/enceladus.md) · [Chronos](modules/chronos.md) · [Mnemosyne](modules/mnemosyne.md) · [Documents](modules/documents.md) · [Astra](modules/astra.md) (shared capability, not a domain module)

### Private development tooling

Private development tooling: [Zeus and Dev Orchestrator](modules/zeus.md). They are separate
from Astra, are not public modules and are not production-ready. Their implementation,
topology, operating policy and evidence are maintained in restricted private repositories
and are intentionally not reproduced in this public architecture repository.

### Concepts

[Resource identity and lifecycle](concepts/resource-identity-and-lifecycle.md) · [Cross-module references](concepts/cross-module-references.md) · [Module contract](concepts/module-contract.md) · [Principals and permissions](concepts/principals-and-permissions.md) · [Secrets](concepts/secrets.md) · [Search and discovery](concepts/search-and-discovery.md) · [Storage and files](concepts/storage-and-files.md) · [Backups](concepts/backups.md) · [Collectors](concepts/collectors.md) · [Local-first and sync](concepts/local-first-and-sync.md) · [Localization](concepts/localization.md) · [Updates](concepts/updates.md) · [Design system](concepts/design-system.md) · [Interoperability](concepts/interoperability.md) · [Projections](concepts/projections.md)

## Current direction (2026-11-03)

- First stabilize the useful self-hosted ecosystem; app stores and managed SaaS are later considerations ([sequencing](architecture.md#strategic-sequencing)).
- [Enceladus](modules/enceladus.md): training module agreed; existing Gymless is an unintegrated starting point with audit blockers. Further APK development is not a current priority.
- [Lyra Books](modules/lyra.md#books-physical-and-digital): physical and digital copies belong to one media domain; implementation remains Planned. Existing Movies/TV tracking remains the implemented baseline.
- [Janus](modules/janus.md): independent local vaults per device, encrypted snapshot sync, QR pairing with explicit approval, a planned recovery key, and a family of clients (desktop development build and a local Web Vault milestone existing; signed delivery, browser extension and mobile still planned). Mechanisms await the reviewed security design ([ADR 0017](decisions/0017-janus-devices-sync-and-clients.md)).
- [Nexus Remote Access](modules/nexus.md#remote-access-planned-long-term): long-term, Nexus-owned private-network enrollment of a mobile application; not implemented ([ADR 0018](decisions/0018-nexus-remote-access.md)).

Added 2026-11-05: [Meridian](modules/meridian.md#implementation-status) has relationships, a graph and a timeline. [Argus](modules/argus.md#implementation-status-2026-11-05) exists as a first module; profile images in Meridian are the work in progress. The project is in a stabilization phase, with documentation being synchronized before further feature work.

Added 2026-11-05: a **default-off local pilot** of shared navigation history and deep links exists in the Nexus and Mnemosyne working trees. It is not an ecosystem requirement, is not forwarded by the checked-in Compose configuration and is not deployed. Only Mnemosyne has an adapter. See the [Nexus navigation guide](https://github.com/Aureate-Empyrean/empyrean-nexus/blob/main/docs/navigation-history.md) and [Mnemosyne notes](https://github.com/Aureate-Empyrean/mnemosyne/blob/main/docs/NAVIGATION_HISTORY.md); production rollout and other-module migration remain unvalidated.

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
