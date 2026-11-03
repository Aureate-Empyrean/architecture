# 0011. Projection queries: Nexus forwards scope-granted queries to owning modules

- Status: Accepted (2026-11-02)

## Context

Chronos must show dates owned by other modules (Lyra release dates, optionally Mnemosyne task deadlines) without owning them ([Chronos](../modules/chronos.md), [interoperability](../concepts/interoperability.md#derived-data-and-projection)). The [module contract](../concepts/module-contract.md#planned) lists module-declared projection/query capabilities as Planned with the protocol Open.

What Nexus 0.1.4 offers is not enough:

- Modules cannot call each other: each runs on a private network that only Nexus joins.
- Reference resolution returns ≤ 2 KiB for **one known** reference. There is no way to ask "which dated items exist in this range?".
- The reference index has no "list every edge of this relation" query, and putting dates into edge metadata would turn the index into a domain cache.
- Events are bounded, best-effort hints. Correctness must not depend on them alone.

## Decision

**Generic mechanism (Nexus, domain-ignorant).** Module Protocol v1 gains an opt-in `projections` declaration and the capability `projections.read` (Nexus 0.1.5):

```json
"projections": {
  "version": 1,
  "provides": ["dated-items"],
  "read": [{"module": "lyra", "projection": "dated-items"}]
}
```

- `provides` lists projection names the module serves. Serving needs no capability; it is a promise to implement the endpoint and enforce its own policy.
- `read` lists exact module/projection scopes the module may query. It requires `projections.read`. No wildcards. The owner reviews the scopes at install and update.
- A consumer calls `POST /api/v1/module/projections/query` with `{module, projection, query, cursor}`. Nexus checks the capability and the exact scope, that the target is registered, enabled and declares the projection. It then calls the owner at the fixed private endpoint `POST /empyrean/v1/projections/<projection>`, authenticated with the owner's resolver key and stamped with the requester's module ID, the same trust pattern as resolution.
- Nexus validates only the envelope (`items` array, `next_cursor`), bounds it (1 MiB, 500 items per page, 5 s), never stores, caches, indexes or interprets items, and reports availability using the resolution vocabulary (`module_uninstalled`, `module_disabled`, `not_provided`, `forbidden`, `temporarily_unavailable`).
- The owner decides what the requester may see and may answer 403.

**Projection contracts are ecosystem contracts, not Nexus code.** The meaning of a projection name and its item shape are defined in the architecture repository ([projections](../concepts/projections.md)). The first contract is **`dated-items` version 1**: dated, reference-bearing items in a date range, with date-only values preserved, certainty and status explicit, and an owner-provided label.

**Consumers hold derived data under the derived-data rule.** A consumer keeps a non-authoritative, rebuildable cache keyed by the owner's stable item identity. It replaces its cache for a provider and window only after a **complete, successful** fetch. A failed or partial fetch never deletes derived items; it marks them stale. Events (for example `lyra.dated-items.changed`) only shorten the delay before the next reconciliation.

## Consequences

- Chronos can aggregate dated items from any module that provides `dated-items`, official or community, without Nexus learning what a release or a task is.
- Providers own disclosure: Lyra publishes only what its calendar-sharing settings select, and Mnemosyne publishes task deadlines only to requesters Nexus authorized.
- Modules that provide or read projections require Nexus ≥ 0.1.5. Mnemosyne 1.2.0 declares `provides: ["dated-items"]` as a minimal, additive, read-only interface.
- Not built: push subscriptions, server-side filtering languages, pagination guarantees beyond an opaque cursor, write-back of edits to the owner (edits happen in the owning module), and projections other than `dated-items` (Atlas geographic items remain Planned).
- Affects: [module-contract](../concepts/module-contract.md), [interoperability](../concepts/interoperability.md), [projections](../concepts/projections.md), [Chronos](../modules/chronos.md), [Lyra](../modules/lyra.md), [Mnemosyne](../modules/mnemosyne.md), Nexus `docs/module-protocol.md`.
