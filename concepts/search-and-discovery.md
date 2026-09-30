# Search and Discovery

A shared principle for how modules and [Astra](../modules/astra.md) search: **local knowledge first; external discovery explicitly second.** See [ADR 0003](../decisions/0003-local-knowledge-first.md).

## Established decisions

- Ordinary search first searches the user's locally owned and indexed data, plus permitted local projections of other modules' resources.
- External/network search is **clearly distinguishable and intentionally invoked** (e.g. an action such as "Search external sources"). External queries and results are not silently mixed into local search.
- Reasons: privacy (an external query reveals what the user is interested in), latency, predictability, relevance of personal knowledge, and clear provenance.
- Saving an external result creates a resource owned by the saving module, with provenance to the external source (e.g. an [Atlas](../modules/atlas.md#open-geodata-and-external-sources) Place snapshot).
- Search indexes over another module's data are derived data under the [derived-data rule](interoperability.md#derived-data-and-projection): non-authoritative, rebuildable, and subject to the same capabilities. User vault secrets are never placed in search indexes outside [Janus](../modules/janus.md).
- UI wording is not standardized globally.

Example (Atlas): searching for a place first searches saved Places, Areas and Routes and permitted local projections. If nothing suitable exists, or the user explicitly asks, Atlas searches configured external/open geographic sources.

## Open questions

- Ecosystem-wide search: which component owns it, and whether it queries modules or holds derived indexes.
- Semantic/vector search: not added merely because the technology exists ([architecture](../architecture.md#databases)); only where a concrete requirement justifies it.
- Which external sources each module offers and how they are configured.
