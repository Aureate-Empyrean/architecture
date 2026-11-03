# 0013. Chronos and Lyra v1 boundaries: owned resources, external providers and derived calendar data

- Status: Accepted (2026-11-02)

## Context

Chronos and Lyra are the first official modules after Mnemosyne. Both integrate external data (iCalendar files and feeds; TMDB) and both meet at the calendar. The decisions below constrain both modules and their contract with each other, so they are recorded once.

## Decision

**Chronos**

- Chronos owns calendars and native calendar Events. Every Event, and every Calendar, has a Chronos-generated UUIDv7.
- Event origin is explicit and permanent: `native`, `import` (one-time `.ics` file), `subscription` (iCalendar URL) or `projection` (dated items from another module). Projected entries are **derived data**, not Chronos Events and not referenceable Chronos resources; their identity is the owner's reference.
- External iCalendar data is **read-only** in Chronos v1. Imports and subscriptions never modify native calendars. A user may copy an external or projected entry into a new native Event. There is no two-way synchronization and no CalDAV.
- External events keep their iCalendar `UID` (+ `RECURRENCE-ID`) as provenance, mapped to a stable Chronos UUID, so repeated imports and refreshes update rather than duplicate.
- Times: timed events store UTC instants **and** their IANA zone; all-day events store dates only and are never given a time. Recurrence (RFC 5545 `RRULE`, `EXDATE`, `RECURRENCE-ID` overrides) is expanded at query time in the event's zone, using mature libraries (`icalendar` for parsing, `python-dateutil` for rules).

**Lyra**

- Lyra's first implemented domain is **screen media: Movies and Television** (Series, Season, Episode). This is an implementation priority only; Music remains Lyra's first domain in the long-term model, and its resource types are reserved.
- Lyra generates UUIDv7 identities for Movie, Series, Season and Episode. A TMDB identifier is an **external mapping**, never a Lyra identity or a cross-module identity. One provider title maps to at most one Lyra resource.
- Provider metadata and user data are stored separately; refreshing provider metadata never writes watch state, ratings, notes, history or calendar preferences.
- TMDB is an external discovery and metadata provider, explicitly invoked, never Lyra's database. Lyra works from local data when TMDB is unavailable.
- The TMDB credential is a service/integration credential held by Lyra server-side; see [secrets](../concepts/secrets.md). It is never sent to the browser, logged or included in backups.

**Between them**

- Lyra provides the `dated-items` projection ([0011](0011-projection-queries.md)) containing only what its calendar-sharing settings select. Chronos never stores Lyra media records, only derived calendar entries keyed by Lyra references.
- Lyra owns *what* is shared (which series or movies, premieres or every episode); Chronos owns *whether and how* a source is shown (enabled, colour).
- When a user plans to watch a release, Chronos creates a native Event that references the Lyra resource (`chronos.about`), shared with Lyra so Lyra can show it as a backlink.

## Consequences

- No module stores another's truth; every cross-module view is rebuildable.
- Two-way calendar sync, CalDAV, reminders, playback, acquisition, *arr integrations and streaming-provider links remain out of v1 and need their own decisions.
- Both modules use SQLite in their data volume: Module Protocol v1 cannot run a PostgreSQL service per module. This is compatible with [0007](0007-postgresql-default-database.md) ("modules whose deployment genuinely favors it") but means 0007's default is not yet reachable for Nexus-hosted modules.
