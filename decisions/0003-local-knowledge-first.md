# 0003. Local knowledge first; explicit external discovery

- Status: Accepted (2026-09-30)

## Context

Several modules (Atlas, Meridian research, Lyra matching, Astra) can search external sources as well as the user's own data. Silently mixing external queries into ordinary search leaks what the user is interested in, adds latency, makes results unpredictable and blurs where information came from.

## Decision

Ordinary search covers the user's local data and permitted local projections first. External/network discovery is clearly distinguishable and intentionally invoked. Saving an external result creates a module-owned resource with provenance. See [search-and-discovery](../concepts/search-and-discovery.md).

## Consequences

- External queries happen only when the user asks, which keeps privacy and provenance clear.
- Modules and Astra need a visible, separate action for external search.
- Some convenience is traded for predictability.
