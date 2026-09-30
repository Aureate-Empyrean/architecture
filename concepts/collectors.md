# Collectors

Collectors are an **ingestion layer**, not necessarily normal user-facing modules.

## Concept

A collector gathers information from a source, typically a device, and delivers it to the owning module through Nexus APIs and events. Example: a future Android companion that, with explicit user permission, collects or syncs:

- location
- call history
- SMS
- RCS, where platform access permits it
- photos
- other explicitly enabled device data

```
device collector ──► Nexus APIs/events ──► Atlas / Hermes / Argus / …
```

## Established decisions

- Collectors are an ingestion layer; they do not own domain data. Modules do.
- Collectors deliver via Nexus APIs and events, not directly into module databases.
- **Collection is opt-in and transparent. No covert monitoring.**
- Location collection specifically must be explicitly enabled and locally controlled ([Atlas](../modules/atlas.md)).

## Planned direction

- An Android companion as the first collector.
- Per-data-type opt-in with visible status of what is being collected and when.

## Open questions

- Collector API contract: authentication of a device, per-collector permissions/capabilities, versioning.
- Routing: how Nexus knows which module receives a given data type; how community modules can receive collector data.
- Delivery semantics: offline buffering, retries, idempotency, deduplication (e.g. SMS also imported from a backup).
- Transparency requirements: indicators, audit log, easy revocation and deletion of collected data.
- Platform limits: what Android permits for RCS, call logs and background location, and Play Store policy implications for distribution.
- Whether collectors beyond mobile exist (desktop, browser). Browser-based Hermes sync is a connector concern; see [Hermes](../modules/hermes.md) and [interoperability.md](interoperability.md).
- Raw location samples: retention and raw-data policy, and how collectors' samples become Atlas visits/routes (an Atlas concern; see [Atlas](../modules/atlas.md#location-collection-and-visits-later)).
- Distinction between *collectors* (device-side push) and *connectors* (service-side integration): whether they share infrastructure ("plugin/connector infrastructure" in Nexus).
