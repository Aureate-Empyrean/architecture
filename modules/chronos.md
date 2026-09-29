# Chronos

## Purpose

The time, calendar and event domain. Detailed product scope is **not yet finalized**.

## Owns

Potential responsibilities:

- calendar
- events
- reminders
- important dates
- timelines
- birthdays and name days (operationally, as projections)
- cross-module event references

## Does not own

- The authoritative identity data behind dates. Example: Meridian is authoritative for a person's birthday; Chronos may expose it as calendar entries/reminders/phone notifications without owning the person's identity.

## Integrations

- **Meridian**: birthdays, name days, other important dates (projection), people involved in events.
- **Atlas**, **Argus**, **Hermes**: events referenced by places, media, conversations (e.g. `argus://photo/928 related_to chronos://event/551`).
- Nexus notifications for reminders.
- Possible standard calendar interoperability (e.g. iCalendar/CalDAV) — not decided.

## Established decisions

- Chronos may project module-owned dates but does not become the authoritative owner of them.

## Planned direction

- Calendar, events, reminders.
- Projection of dates from other modules into calendars/reminders.
- Timeline views spanning the ecosystem.

## Open questions

- **Boundary between module-owned dates and Chronos projections**: how a source module publishes dates, whether Chronos stores copies or computes them on demand, how edits flow back to the owner. Must be designed carefully.
- Standard protocol support (CalDAV/iCalendar) and two-way sync with external calendars.
- Recurrence, time zones, and historical/uncertain dates (e.g. "year unknown").
- Whether cross-ecosystem timelines belong in Chronos or Nexus.
- Product scope and priorities.
