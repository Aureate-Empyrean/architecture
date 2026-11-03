# Projections

How one module shows another module's resources in its own views without owning them. The mechanism is [ADR 0011](../decisions/0011-projection-queries.md); the rules for the derived copies are the [derived-data rule](interoperability.md#derived-data-and-projection).

## Established decisions

- A **projection** is a named, versioned, read-only query contract that a module may *provide* and another may *read*. Nexus forwards queries for exact, owner-reviewed module/projection scopes and never stores, caches, indexes or interprets the items.
- Projection names and item shapes are **ecosystem contracts defined here**, not Nexus code and not owned by one module. Any module may provide a contract it implements faithfully.
- The **provider** decides what each requester may see (it receives the requester's module ID, authenticated by Nexus) and may refuse with 403. A provider publishes only what its own user-facing settings allow.
- The **consumer** keeps only derived data: non-authoritative, keyed by the provider's stable identities, replaced only after a complete successful fetch, never independently edited, and shown as coming from the provider. A failed or partial fetch never deletes derived items; it marks them stale.
- Events are hints that shorten the delay before reconciliation, never the only source of truth.

## Wire contract (Nexus 0.1.5)

Consumer → Nexus: `POST /api/v1/module/projections/query`

```json
{"module": "lyra", "projection": "dated-items", "query": {"from": "2026-10-01", "to": "2027-04-01"}, "cursor": null}
```

Nexus → provider (private network, `X-Nexus-Resolver-Key`): `POST /empyrean/v1/projections/dated-items`

```json
{"requester": "chronos", "query": {"from": "2026-10-01", "to": "2027-04-01"}, "cursor": null}
```

Provider reply (HTTP 200; ≤ 1 MiB, ≤ 500 items, 5 s):

```json
{"items": [ … ], "next_cursor": null}
```

Nexus reply to the consumer: `{"module", "projection", "availability", "items", "next_cursor"}`. `availability` is `available`, or one of `module_uninstalled`, `module_disabled`, `not_provided`, `forbidden` (provider 403) and `temporarily_unavailable` (anything else, including oversized or malformed replies). `query` is a JSON object ≤ 4 KiB whose meaning belongs to the projection contract.

## `dated-items` version 1

Dated items intersecting a date range, for calendar-style consumers such as [Chronos](../modules/chronos.md).

**Query:** `from`, `to`: ISO dates, `from` inclusive, `to` exclusive, span ≤ 800 days. Providers may ignore unknown query fields.

**Item:**

| Field | Required | Meaning |
|---|---|---|
| `id` | yes | Provider-stable item key (≤ 160 chars), unique per provider. Stays the same when the date changes. |
| `resource` | yes | Canonical reference of the provider resource the date belongs to (`nexus:v1:<module>:<type>:<uuid>`). |
| `kind` | yes | Provider-namespaced kind, e.g. `lyra.episode-air`, `lyra.movie-release`, `mnemosyne.task-deadline`. Consumers may group or style by it; they must not need to understand it. |
| `title` | yes | Owner-provided plain-text label (≤ 200). |
| `start` | yes | ISO date (`2026-11-14`) for date-only items, or ISO date-time **with offset** for timed items. |
| `end` | no | Same form as `start`; exclusive. Absent means a single day (date-only) or an instant. |
| `all_day` | yes | `true` exactly when `start` is a date. A provider never invents a time for a date-only fact. |
| `time_zone` | no | IANA zone for timed items, if known. |
| `certainty` | yes | `confirmed` (the date happened or is authoritative), `tentative` (announced, may change), `estimated` (derived or fallback date). Undated items are not projected. |
| `status` | yes | `scheduled`, `postponed`, `cancelled`, `open`, `completed`. |
| `summary` | no | Short plain text (≤ 300). |
| `context` | no | Presentation-only organizational label (≤ 80), e.g. a Mnemosyne Workspace title. Not an authorization boundary. |
| `group` | no | `{resource, label}` of a parent resource (e.g. the Series of an Episode) for grouping. |
| `updated_at` | no | When the provider last changed this item. |

Rules: items are complete for the requested range when `next_cursor` is null; providers return deterministic order; a consumer that receives the final page may replace its derived items for that provider and range.

## Providers and consumers (2026-11)

| Module | Provides | Reads |
|---|---|---|
| Lyra 0.1 | `dated-items` (selected episode air dates and movie release dates) | — |
| Mnemosyne 1.2 | `dated-items` (Task deadlines, all-day) | — |
| Chronos 0.1 | — | `lyra`/`dated-items`, `mnemosyne`/`dated-items` |

## Planned direction

- [Enceladus](../modules/enceladus.md) training dates for Chronos: initially read-only, date-scoped and linked back to the owner. Evaluate `dated-items` v1; stable occurrence identity, kinds and status mapping remain Open. Enceladus expands its own schedule/date exceptions within validated bounded ranges and works without Chronos. This is not an implemented provider in the table above.
- Geographic items for Atlas, under the same mechanism, once Atlas is designed.
- Meridian birthdays and name days as `dated-items` (date precision such as "year unknown" needs a contract extension first).

## Open questions

- Recurrence in `dated-items` (birthdays): expanded by the provider per range, or a recurrence field.
- Per-requester disclosure settings that providers expose in their own UI beyond the Nexus scope grant.
- Whether projection scopes should be grantable after install without a manifest update.
- Push-style change feeds or longer-lived cursors; currently consumers poll and use events as hints.
