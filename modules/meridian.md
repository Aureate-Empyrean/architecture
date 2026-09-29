# Meridian

## Purpose

A comprehensive, private information system about people, organizations and the relationships between them. Conceptually: a private-knowledge counterpart to a social profile, combined with a much more powerful MonicaHQ-style personal relationship manager.

It is not an OSINT harvesting or covert surveillance tool. It stores what the user knows and chooses to record.

## Owns

- **Person** and **Organization** as first-class entities.
- Information about them, including (expected areas): identity, aliases/nicknames/former names/usernames, profile photos, birth date/place, name day and other important dates, nationality/languages, contact information, accounts/social identities, addresses/places, education, employment, interests, preferences, life history, relationships, facts, interactions, timeline, notes, quotes, sources/provenance, attachments, custom/extensible data.
- Relationships: Person↔Person, Person↔Organization, Organization↔Organization, with optional history/time ranges.
- Facts (see below).

## Does not own

- Messages, conversations or communication identities' content ([Hermes](hermes.md)). Meridian is referenced *from* them.
- Photos, video, recognition results ([Argus](argus.md)).
- Place records and location history ([Atlas](atlas.md)).
- Calendar events and reminders ([Chronos](chronos.md)), though Meridian is authoritative for personal dates such as birthdays.
- Notes/knowledge not specifically about a person ([Mnemosyne](mnemosyne.md)).
- Physical file storage ([storage-and-files](../concepts/storage-and-files.md)).

Meridian must not duplicate module-owned data; it links via references.

## Integrations

Via [cross-module references](../concepts/cross-module-references.md); Meridian person is a common target:

- Hermes: communication identities/conversations/messages ↔ person.
- Argus: photos depicting the person (backlinks).
- Atlas: places/visits associated with the person.
- Chronos: events involving the person; birthday/name-day projection.
- Documents/Mnemosyne: mentions of the person.

Meridian discovers related resources through backlinks, not by copying them.

## Established decisions

- Person and Organization are first-class entities.
- The data model must **not** be a single Person table with hundreds of fixed columns; it must be extensible.
- Knowledge about people is representable as **facts** that capture changing knowledge over time. A fact conceptually has: subject, key/type, value, source, date learned, validity period, current/historical state, confidence, note.
- Relationships may have history/time ranges.
- Meridian does not duplicate underlying messages, photos or other module-owned data.
- Meridian is authoritative for personal data such as birthdays; other modules may project it.

## Planned direction

- Rich coverage of the information areas listed above, built incrementally rather than all at once.
- Integration with other modules through references and backlinks.
- Provenance/source tracking for facts.

## Open questions

- Concrete data model: how facts, typed structured data (addresses, employment, education) and custom fields relate.
- Extensibility mechanism for custom data (user-defined types, module-contributed types).
- Confidence semantics and how conflicting facts are presented.
- Relationship type vocabulary: fixed, user-defined, or both; directionality and inverse relationships.
- Birthday/date ownership boundary with [Chronos](chronos.md).
- Merging/deduplicating people (e.g. when several communication identities turn out to be the same person) and how references follow a merge.
- Privacy controls within Meridian (sensitive facts, visibility) once multi-user exists.
- Import from existing tools (e.g. contact formats, MonicaHQ).
