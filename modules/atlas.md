# Atlas

## Purpose

The location and place domain. Detailed product scope is **not yet finalized**.

## Owns

Current direction:

- places
- location history
- stays/visits
- movement between places
- map/timeline views
- manual correction

## Does not own

- People ([Meridian](meridian.md)), events ([Chronos](chronos.md)), or media ([Argus](argus.md)); it references them.
- Device-side location collection ([collectors](../concepts/collectors.md)); collectors feed Atlas.

## Integrations

- Opt-in device collectors (e.g. Android companion) supply location data via Nexus.
- References to and from Meridian people, Chronos events, Argus media and other resources (e.g. `argus://photo/928 taken_at atlas://place/17`).
- Addresses/places in Meridian may reference Atlas places.

## Established decisions

- Location collection must be **explicitly enabled and locally controlled**.
- Atlas resources may reference other modules' resources through cross-module references.
- Manual correction is part of the direction (automatically derived data must be correctable).

## Planned direction

- Place records, visit/stay derivation, movement, map and timeline views.
- Ingestion from opt-in collectors.

## Open questions

- Product scope and priorities.
- Data model for raw points vs. derived stays/visits, and retention of raw points.
- Map tiles/geocoding providers consistent with "no mandatory cloud" (self-hosted or optional).
- Relationship between Meridian addresses and Atlas places.
- Import formats (e.g. existing location-history exports).
- Precision/privacy controls (e.g. retention, redaction, sensitive places).
