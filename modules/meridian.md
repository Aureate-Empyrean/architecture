# Meridian

## Purpose

A comprehensive, private information system about people, organizations and the relationships between them. Conceptually: a private-knowledge counterpart to a social profile, combined with a much more powerful MonicaHQ-style personal relationship manager.

It is not an OSINT harvesting or covert surveillance tool. It stores what the user knows and chooses to record.

## Owns

- **Person** and **Organization** as first-class entities.
- Information about them, including (expected areas): identity, aliases/nicknames/former names/usernames, profile photos, birth date/place, name day and other important dates, nationality/languages, contact information, accounts/social identities, addresses/places, education, employment, interests, preferences, life history, relationships, facts, interactions, timeline, notes, quotes, sources/provenance, attachments, custom/extensible data.
- Relationships: Person↔Person, Person↔Organization, Organization↔Organization, with optional history/time ranges.
- Facts (see below).
- Selective Person document export: choosing which Meridian information goes into a generated document, and the Meridian-specific semantics of building a Person profile or CV from Person records (see [Selective PDF export](#selective-pdf-export)).

## Does not own

- Messages, conversations or communication identities' content ([Hermes](hermes.md)). Meridian is referenced *from* them.
- Photos, video, recognition results ([Argus](argus.md)).
- Place records and location history ([Atlas](atlas.md)).
- Calendar events and reminders ([Chronos](chronos.md)), though Meridian is authoritative for personal dates such as birthdays.
- Notes/knowledge not specifically about a person ([Mnemosyne](mnemosyne.md)).
- Credentials and secret material ([Janus](janus.md)).
- Physical file storage ([storage-and-files](../concepts/storage-and-files.md)).

Meridian must not duplicate module-owned data; it links via references.

## Integrations

Via [cross-module references](../concepts/cross-module-references.md); Meridian person is a common target:

- Hermes: communication identities/conversations/messages ↔ person.
- Argus: photos depicting the person (backlinks).
- Atlas: places/visits associated with the person.
- Chronos: events involving the person; birthday/name-day projection.
- Janus: a credential may belong to a person or organization (`janus://… belongs_to -> meridian://person/123`). This gives Meridian no access to secret material, and Meridian must not automatically be able to discover that a person has credentials in Janus without explicit permission.
- Lyra: music preferences (favorite track/artist) may reference `lyra://…` resources. Meridian owns the preference; Lyra owns the music. A preference must remain representable as textual/external information when no Lyra resource exists, so Meridian does not require Lyra. See [Lyra](lyra.md).
- Documents/Mnemosyne: mentions of the person.

Meridian discovers related resources through backlinks, not by copying them.

## Selective PDF export

Meridian can generate a PDF from the information stored about a single Person. This is a selective document builder, not a raw database export: Meridian may know far more about a person than belongs in any one document. A primary use case is generating a CV/resume directly from a Person.

Conceptual flow (a multi-step, category-based selection, not a UI specification):

```
Person -> Create PDF -> choose document/template
       -> Identity -> Contact -> Education -> Employment -> Languages/qualifications -> …
       -> Preview -> Generate PDF
```

Selection is per record within each category. Selecting a category does not include all of its records:

```
Education                          Employment
[x] University A — Program X       [x] Company A — Role
[ ] Old training course            [ ] Irrelevant old position
[x] Certification Y
```

### Established

- Meridian supports selective PDF generation for a Person; CV/resume generation is a first-class use case.
- The user selects which categories **and individual records** are included.
- **The PDF contains only information explicitly selected for that export.** The exporter must not silently add information because it seems useful. A CV must not unexpectedly include relationships, private notes, unrelated timeline events, sensitive facts, old addresses or arbitrary data unless selected. Templates may offer default selections, but the final content stays visible and controllable before generation.
- The flow includes review/preview before final generation.
- Export is generated from the authoritative Meridian records. There is no separate CV-specific data model. One record (e.g. an employment record) serves the profile, the timeline and optionally a CV, rather than being copied for each. Profile, timeline and document views derive from authoritative records.
- Different document/export templates are supported conceptually. At minimum: CV/resume, general Person profile, custom selective export.
- Meridian exports information it owns. A cross-module reference does not grant export permission, and referenced data from other modules is not automatically included.

### Planned

- CV/resume template that produces a normal, usable CV (e.g. education and employment rendered chronologically), not a data dump.
- General Person profile template and custom selective export (potentially a much broader subset).
- Multi-step category-based selection with preview.
- Sensible per-template default selections.
- Convenience actions such as select all / clear all (not an architectural requirement).

### Open

- Template format/system, visual customization/theming, and whether users can create/save custom templates or reusable selection presets.
- PDF rendering technology, and whether generic rendering belongs to [Documents](documents.md), Meridian, or shared infrastructure. Meridian is not assumed to own rendering infrastructure permanently.
- How explicitly selected cross-module information (subject to permissions) could participate in documents.
- Whether generated PDFs become managed files/Documents resources or are simply downloaded.
- Digital signing or document verification, if ever needed.

## Established decisions

- Person and Organization are first-class entities.
- The data model must **not** be a single Person table with hundreds of fixed columns; it must be extensible.
- Knowledge about people is representable as **facts** that capture changing knowledge over time. A fact conceptually has: subject, key/type, value, source, date learned, validity period, current/historical state, confidence, note.
- Relationships may have history/time ranges.
- Meridian does not duplicate underlying messages, photos or other module-owned data.
- Meridian must never receive secret material merely because a Janus credential references a Meridian person or organization.
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
- Boundary between Meridian "accounts/social identities" and [Janus](janus.md) credentials (identity/username facts vs. secrets).
- Import from existing tools (e.g. contact formats, MonicaHQ).
