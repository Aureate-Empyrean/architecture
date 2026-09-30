# 0006. Astra as a contextual intelligence layer, not a data owner

- Status: Accepted (2026-09-30)

## Context

Assistant features (researching a person, planning a route, reasoning about a note) are useful across modules. Built naively, an assistant becomes a second database that replicates everything, bypasses module permissions, depends on one commercial provider and writes unreviewed "facts" into authoritative data.

## Decision

Astra is a Planned shared contextual intelligence layer, not a domain module. Local models are first-class and commercial providers optional. Modules expose tools and decide what context Astra receives. Astra proposes; the owning module commits after user approval. External research is explicit, keeps provenance and is never autonomous mass profiling. Astra is not a source of truth. See [astra](../modules/astra.md).

## Consequences

- Each module that wants Astra support must expose tools and a proposal/review flow.
- The tool protocol, hosting and session storage remain Open (MCP is not mandated).
- Astra respects the same capability model as other principals.
