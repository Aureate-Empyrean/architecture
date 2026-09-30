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

Terminology: a Chronos **Event** is a calendar event. It is distinct from Nexus system events (change notifications between modules) and from Meridian **Life Events**.

## Does not own

- The authoritative identity data behind dates. Example: Meridian is authoritative for a person's birthday; Chronos may expose it as calendar entries/reminders/phone notifications without owning the person's identity.

## Integrations

- **Meridian**: birthdays, name days, other important dates (projection), people involved in events.
- **Atlas**, **Argus**, **Hermes**: events referenced by places, media, conversations (e.g. `argus://photo/<uuid> related_to chronos://event/<uuid>`).
- Nexus notifications for reminders.
- **Mnemosyne**: Mnemosyne owns the semantic intent of reminders on its own resources (Tasks, Notes) and deadlines; Nexus delivers notifications. A Mnemosyne deadline does not automatically become a Chronos event; any projection would have to be explicitly defined ([mnemosyne](mnemosyne.md)).
- Possible standard calendar interoperability (e.g. iCalendar/CalDAV) — not decided.

## Established decisions

- Chronos may project module-owned dates but does not become the authoritative owner of them.

## Planned direction

- Calendar, events, reminders.
- Projection of dates from other modules into calendars/reminders.
- Timeline views spanning the ecosystem.

## Open questions

- **Boundary between module-owned dates and Chronos projections**: how a source module publishes dates and how edits flow back to the owner. Chronos may keep derived, non-authoritative representations of other modules' dates under the [derived-data rule](../concepts/interoperability.md#derived-data-and-projection); the mechanism must be designed carefully.
- Standard protocol support (CalDAV/iCalendar) and two-way sync with external calendars.
- Recurrence, time zones, and historical/uncertain dates (e.g. "year unknown").
- Whether cross-ecosystem timelines belong in Chronos or Nexus. Module-specific timelines, e.g. Meridian's Person timeline, are views derived from that module's own records ([meridian](meridian.md#timeline)) and are not Chronos-owned copies.
- Product scope and priorities.
