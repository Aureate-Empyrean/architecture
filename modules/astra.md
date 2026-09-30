# Astra

**Planned long-term capability.** Astra is the shared contextual intelligence and assistant layer of Aureate Empyrean. It is **not** a domain module: it owns no Person, Place, Note or other domain truth. See [ADR 0006](../decisions/0006-astra-contextual-intelligence.md).

## Purpose

Applications may expose an Astra side panel, a contextual assistant surface alongside the current resource. Examples:

- With a Meridian Person open, Astra can help research that Person ([meridian](meridian.md#research-and-enrichment-planned)).
- With an Atlas Route open, Astra can help plan or enrich the route ([atlas](atlas.md#route-intelligence-with-astra-planned)).
- With a Mnemosyne Note open, Astra can reason about the note or related knowledge the user permits.

The current module provides the context explicitly. Astra does not automatically receive the ecosystem's data.

## Established principles

- **A. Provider independence.** Local models are first-class; commercial/cloud AI providers are optional. No ordinary feature requires a specific commercial provider unless it inherently depends on that provider. Provider classes may include local inference, custom/self-hosted endpoints and optional commercial APIs. No vendors or runtimes are chosen. Using a non-local provider sends context outside the installation; this must be explicit and visible to the user, consistent with the [privacy principles](../principles.md).
- **B. Tools belong to modules.** Astra never reads or queries module databases or understands their private schemas. Modules expose approved capabilities/tools (e.g. for Atlas: search local places, search external places, calculate or inspect a route, find places near a route, propose a Place or Route, obtain current route-related external information). The protocol is Open; MCP may be investigated but is not mandated.
- **C. Context is explicit.** Modules decide what resource/context Astra receives. Opening Astra never implies unrestricted access to ecosystem data; Astra is a principal constrained by capabilities like any other ([principals-and-permissions](../concepts/principals-and-permissions.md)).
- **D. AI proposes, the user commits.** For meaningful persistent changes (research findings, candidate facts, proposed Places or Routes, metadata corrections), Astra produces drafts/proposals for review. The owning module performs the authoritative mutation after approval.
- **E. Local knowledge first.** Astra follows [search-and-discovery](../concepts/search-and-discovery.md): external research is explicit.
- **F. Provenance.** Externally researched findings intended for import keep their sources/provenance. AI-generated synthesis never silently becomes authoritative fact.
- **G. Not a copy of the user's database.** Astra is not a second source of truth and does not replicate Person/Place/Note data. Any conversation/session storage must not undermine module ownership.
- **H. No autonomous mass profiling.** External research about people and organizations is user-directed and transparent. Astra is not an always-on crawler building dossiers.
- Astra is not given [Janus](janus.md) vault contents. Whether any Janus interaction with Astra is ever appropriate is Open and belongs to the Janus security design.

## Open questions

- Tool protocol (MCP or other) and how module tools declare capabilities.
- Where Astra runs (Nexus service, separate service) and how providers are configured.
- Conversation/session history: whether stored, where, retention, and how it avoids becoming a replica of module data.
- Representation and review flow of proposals/drafts across modules.
- How users see and control what context left the installation when a non-local provider is used.
