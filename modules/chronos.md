# Chronos

## Purpose

The time, calendar and event domain: the user's calendars, their events, and a calendar view of dated information other modules choose to share. The calendar itself is the primary surface, answering *what is happening today, what is coming up, and where each entry comes from*.

Implementation: `chronos/` (v0.1.1, Nexus ≥ 0.1.8). Decisions: [ADR 0013](../decisions/0013-chronos-and-lyra-v1-boundaries.md), [ADR 0011](../decisions/0011-projection-queries.md), [ADR 0010](../decisions/0010-module-network-egress.md).

## Owns

- **Calendars** (native, import, subscription) and their user settings (name, color, visibility).
- **Events** (native, and Chronos-held copies of imported/subscribed iCalendar events with their provenance).
- **Categories** (user-defined name + color).
- Calendar presentation, filtering and search; Chronos-side display settings for projection sources.
- Derived, non-authoritative calendar entries projected from other modules.

Terminology: a Chronos **Event** is a calendar event. It is distinct from Nexus system events (change notifications between modules) and from Meridian **Life Events**.

## Does not own

- Dates owned by other modules: Lyra release and air dates, Mnemosyne Task deadlines, Meridian birthdays, Enceladus training schedules/date exceptions. Chronos shows them as derived entries and links back; edits happen in the owner.
- Reminder intent for other modules' resources ([Mnemosyne](mnemosyne.md) owns Task reminder intent) and notification delivery (Nexus).
- External calendars: an imported file or subscribed feed stays authoritative for its events.

## Resources and references

| Type | Reference (Nexus Entity References v2) | Resolvable |
|---|---|---|
| Calendar | `nexus:v1:chronos:calendar:<uuidv7>` | no (referenceable, not disclosed) |
| Event | `nexus:v1:chronos:event:<uuidv7>` | yes |

Illustrative architectural form: `chronos://event/<uuid>` ([cross-module-references](../concepts/cross-module-references.md)); the implemented grammar is Nexus's `nexus:v1:` form.

Relations created by Chronos (namespaced, source-owned, enumerated for index rebuild):

| Relation | Meaning | Default readers |
|---|---|---|
| `chronos.about` | This Event is about the target resource (e.g. "Watch S2 premiere" → `lyra:episode`). | the target's owning module |

Resolution policy: an Event discloses its label, start and all-day flag only to requesters it has been explicitly shared with through a relation's readers; otherwise 403. Deleted Events resolve as deleted (410) via tombstones. Projected entries are not Chronos resources.

## Event model (Established for v1)

- **Origin** (permanent): `native`, `import`, `subscription`, or (for derived entries) `projection`.
- **Timed** events: start/end UTC instants plus the IANA zone they were defined in. **All-day** events: start date and exclusive end date, no time and no zone. iCalendar floating times are pinned at import to the feed's `X-WR-TIMEZONE`, or else to the user's Chronos time zone.
- **Recurrence**: RFC 5545 `RRULE` with `EXDATE` and `RECURRENCE-ID` overrides, expanded at query time in the event's zone so wall-clock times survive DST. Native editing supports daily/weekly/monthly/yearly with interval, weekdays, count or until; editing a recurring native event edits the series, and a single occurrence can be skipped. "This and following" edits are not supported.
- Status `confirmed`, `tentative`, `cancelled`; title, description, location, category, optional color override.
- External events are **read-only**; "Copy to my calendar" creates a new native Event.

## iCalendar import (one-time `.ics`)

- Upload (≤ 2 MiB through the module bridge) → **preview** (calendar name, counts, recurring and all-day counts, time zones, duplicates, per-component errors, sample) → **commit** into a new or existing *import* calendar. Imports never write into native or subscription calendars.
- Parsing uses the `icalendar` library. Supported: `VEVENT`, `UID`, `DTSTART`, `DTEND`/`DURATION`, `SUMMARY`, `DESCRIPTION`, `LOCATION`, `STATUS`, `SEQUENCE`, `RRULE`, `EXDATE`, `RECURRENCE-ID`, `VTIMEZONE`/`TZID` (IANA names directly; Windows and custom `VTIMEZONE` names via the library's resolution), `X-WR-CALNAME`, `X-WR-TIMEZONE`. `VTODO`, `VJOURNAL`, alarms and attendees are ignored and counted.
- Identity: (`UID`, `RECURRENCE-ID`) within the target calendar maps to a stable Chronos UUID. Re-importing the same file creates nothing (duplicates are reported); a changed event (higher `SEQUENCE` or different content) is updated; events missing from a re-import are **kept** (one-time imports never delete). A component without `UID` gets a deterministic content-derived UID. A cancelled override removes that occurrence.

## iCalendar subscriptions

- A subscription is a calendar with: name, URL (`https`, `http`, or `webcal` treated as `https`), enabled, refresh interval (15 min – 24 h), last attempt, last success, last error, event count, ETag/Last-Modified.
- Synchronization is **read-only, one-way**: each successful fetch makes the calendar equal the feed (insert new, update changed, delete events whose `UID`/`RECURRENCE-ID` disappeared). Repeated refreshes never duplicate. If a feed that previously had events returns none, existing events are kept and a warning is recorded. A failed fetch or parse changes nothing and records the error.
- Network safety (requires `network.egress`, [ADR 0010](../decisions/0010-module-network-egress.md)): only ports 80/443/8080/8443; no credentials in URLs; the host is resolved and every address must be public (loopback, private, link-local, CGNAT, multicast, reserved, unspecified and IPv4-mapped forms are refused); the connection is pinned to the vetted address with TLS verified against the hostname; at most 3 redirects, each re-validated; 15 s timeout; 5 MiB limit; `text/calendar` or parseable content required. URLs are never logged and are shown masked.
- No CalDAV and no two-way synchronization.

## Projections (derived calendar data)

- Chronos reads the [`dated-items`](../concepts/projections.md#dated-items-version-1) projection from providers it was granted (since v0.1.0: `lyra`, `mnemosyne`). Each provider is a **source** with Chronos-owned settings: enabled, color, show completed.
- Reconciliation: for the window −90 … +400 days, Chronos fetches every page; only after a complete, successful fetch does it replace that source's derived entries. Failures keep entries and mark the source stale with the error. Reconciliation runs every 15 minutes, on demand, and shortly after hint events (`lyra.dated-items.changed`, `mnemosyne.resource.changed`).
- Derived entries keep the owner's reference, kind, certainty and status, and are visually distinct (provenance mark, "date may change" for tentative/estimated). Opening one leads to the owner. "Plan in calendar" creates a native Event with a `chronos.about` relation.
- **Lyra**: what is shared is chosen in Lyra ([Lyra](lyra.md#calendar-sharing)); Chronos decides whether the source is shown.
- **Mnemosyne Task deadlines**: **disabled by default**, explicit opt-in in Chronos settings. Shown as all-day deadline entries with the Workspace as context, completion reflected, never converted into Chronos Events. Mnemosyne remains the owner of Tasks, deadlines and reminder intent.

### Enceladus (Planned; not implemented)

[Enceladus](enceladus.md) owns training planning rules, weekly schedules, date exceptions, sessions and history. Chronos initially presents a read-only, date-scoped projection with deep links to the owner; it neither edits nor independently expands training rules. Exact `dated-items` mapping and occurrence identity remain Open. Enceladus retains simple schedule/date editing and full history without Chronos. Source registration, scope grants and deep-link navigation still require implementation; this source is not part of Chronos v0.1.1.

## Integrations

- **Lyra**, **Mnemosyne**: implemented projections above.
- **Enceladus**: planned read-only training-date projection above; all training edits stay in Enceladus.
- **Meridian**: birthdays and name days as projections (Planned; needs date-precision support in `dated-items`).
- **Atlas**, **Argus**, **Hermes**: references to Events (e.g. `argus:photo related_to chronos:event`) through the generic mechanism.
- **Nexus**: notifications for reminders (Planned; reminders are not implemented).

## Module contract (v0.1.1)

Identity `chronos`, port 15333, capabilities `ui.application`, `storage.data`, `network.egress`, `events.subscribe`, `references.create`, `references.read`, `references.resolve`, `projections.read`; Entity References v2 with enumeration; backup v1 (SQLite snapshot); health at `/health`.

## Implementation status (0.1.1, 2026-11-05)

0.1.0 was implemented and verified as described above, with Nexus 0.1.5, Lyra 0.1.0 and Mnemosyne 1.2.0 in a Docker Compose installation: native events, import, a real public subscription, Lyra and Mnemosyne projections, planning with references, backlinks and reconciliation. 0.1.1 declares the application icon and requires Nexus 0.1.8 or later; the repository documentation records no other 0.1.1 change, and this documentation pass did not rerun the suite. Not implemented: reminders, two-way sync, CalDAV, `.ics` export, "this and following" edits, Meridian dates.

## Established decisions

- Chronos may project module-owned dates but never becomes their authoritative owner ([ADR 0011](../decisions/0011-projection-queries.md)).
- The event model, read-only external calendars and provenance rules above ([ADR 0013](../decisions/0013-chronos-and-lyra-v1-boundaries.md)).
- A Mnemosyne deadline never automatically becomes a Chronos Event; the projection is opt-in.

## Planned direction

- Reminders delivered through Nexus notifications.
- Meridian date projections; Enceladus training-date projections with owner deep links; timeline views spanning the ecosystem.
- Export of native calendars as `.ics`.

## Open questions

- Two-way synchronization and CalDAV server/client support.
- "This and following" recurrence edits; per-occurrence edits of native series.
- Historical and uncertain dates (e.g. "year unknown").
- Whether cross-ecosystem timelines belong in Chronos or Nexus. Module-specific timelines remain views of their owner's records ([meridian](meridian.md#timeline)).
- Deep links from a projected entry into the owner's resource: the Nexus bridge can currently switch applications but not open a module path.
