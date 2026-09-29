# Meridian

## Purpose

Meridian is the comprehensive private information system for people, organizations, relationships and what the user knows about them. Conceptually: a private-knowledge counterpart to a social profile, combined with a much more powerful MonicaHQ-style personal relationship manager.

Its breadth is intentional. It holds detailed, evolving information about known people and organizations while preserving history, provenance, uncertainty and the relationships between pieces of information. It stores what the user knows and chooses to record. It is not an OSINT harvesting system and is not designed for covert or non-consensual surveillance.

It must support rich structured information without becoming one giant Person table of hundreds of nullable columns.

## Owns

- **Person** and **Organization** as first-class entities.
- Rich domain records around them: identity, contact information, online accounts/identities, appearance, place associations, education, employment/professional activity, relationships, interests/preferences, personal characteristics, life events, personal dates, notes and custom/extensible data. See [Information model](#information-model).
- Facts, with history, provenance and confidence.
- The Person-facing **timeline** as a derived view (see [Timeline](#timeline)).
- Selective Person document export: choosing which Meridian information goes into a generated document, and the Meridian-specific semantics of building a Person profile or CV from Person records (see [Selective PDF export](#selective-pdf-export)).

Expected areas that are **not yet designed**: interactions (user↔Person history), stories/anecdotes, quotes, general observations, sources/provenance beyond the existing fact direction, attachments beyond existing module boundaries, Organization taxonomy.

## Does not own

- Messages, conversations or communication identities' content ([Hermes](hermes.md)). Meridian is referenced *from* them.
- Photos, video, media library, biometric/recognition representations ([Argus](argus.md)). Meridian may hold a representative/profile image reference, not exact facial geometry, fingerprints, iris templates or similar.
- Place records and location history ([Atlas](atlas.md)).
- Calendar events, reminders and calendar projection of dates ([Chronos](chronos.md)).
- Music catalog: artists, albums, tracks ([Lyra](lyra.md)).
- Credentials and secret material, including passwords, session cookies and tokens ([Janus](janus.md)).
- Notes/knowledge not specifically about a person ([Mnemosyne](mnemosyne.md)).
- Physical file storage ([storage-and-files](../concepts/storage-and-files.md)).

Meridian must not duplicate module-owned data; it links via references. Meridian owns the *knowledge about the person* ("Peter lived in Brno 2020–2023"); other modules own the referenced thing (what Brno is).

| Meridian knows | The other module owns |
|---|---|
| Peter lived in Brno 2020–2023 | Atlas: the Place |
| Peter's favorite song is Track X | Lyra: Track X |
| Peter owns Instagram account @example | Hermes: messages/conversations of the communication identity |
| Peter's birthday is 17 May | Chronos: calendar/reminder projection |
| Peter has a profile image | Argus: photo library, media metadata, recognition |
| Peter has a GitHub account | Janus: the credential, if legitimately stored |

## Integrations

Via [cross-module references](../concepts/cross-module-references.md); a Meridian person is a common target:

- Hermes: communication identities/conversations/messages ↔ person; an online account in Meridian may reference the corresponding Hermes communication identity.
- Argus: photos depicting the person (backlinks); representative image reference.
- Atlas: places associated with the person, birth/death/funeral/resting places, addresses.
- Chronos: events involving the person; birthday/name-day projection; reminders derived from or referencing Meridian's personal dates.
- Janus: a credential may belong to a person or organization (`janus://… belongs_to -> meridian://person/123`). This gives Meridian no access to secret material, and Meridian must not automatically be able to discover that a person has credentials in Janus without explicit permission.
- Lyra: music preferences may reference `lyra://…` resources. Meridian owns the preference; Lyra owns the music. See [Lyra](lyra.md).
- Documents/Mnemosyne: mentions of the person.

Meridian discovers related resources through backlinks, not by copying them.

## Information model

Conceptual content of the model, not required fields and not a schema. Every item below is optional. Exact schemas, enums and storage are Open. Cross-cutting rules:

- **One source of truth, multiple views.** An Education record may appear in the profile, the education section, the timeline and a generated CV without four copies. The same holds for Employment, Relationships and Lyra-linked preferences. Views derive from authoritative records.
- **Temporal information preserves change** rather than overwriting history.
- **Source, date learned and confidence** matter and must distinguish, e.g., "person told me" from "my estimate" (see [Facts](#facts-structured-records-and-custom-fields)).
- **Temporal precision and uncertainty are preserved** (see [Date precision](#date-precision-and-uncertainty)).
- **Extensible:** custom types/categories remain possible; closed enums are avoided.

### Identity

May include: full/legal name, preferred name, first/middle/last names, former names, nicknames, aliases, profile/representative photos, gender, pronouns, nationality, citizenship, languages, academic/professional/honorific titles, birthplace, hometown, internal Meridian identifier, external identifiers where legitimately known. Names and identity information can change over time. Languages may carry detail (native, understands, speaks, writes, proficiency); no schema is fixed.

Online usernames/accounts are **not** string fields on Person; see below.

### Personal dates, birth and death

Meridian is authoritative for personal facts/dates.

- **Birth:** date, optional time, place (Atlas reference).
- **Name day** and custom personally significant dates are legitimate personal dates. Arbitrary dated life history ("moved to Brno") is not a personal date; it is a life event or a place association.
- **Death:** date, optional time, place of death (Atlas reference), circumstances/cause where legitimately recorded, notes, source, confidence.
- Place of death, funeral place and resting place are **distinct** and are not collapsed into one field. Example: dies in a hospital in Košice, funeral in Humenné, buried in Snina.
- Disposition (burial, cremation, donated, other) may carry cemetery/resting place (Atlas reference), grave/plot, burial or cremation date, ashes location (Atlas reference), notes. Funeral, burial and cremation may be modeled as life events instead of death-record fields; the exact boundary is Open.

Chronos owns calendar projection and reminders. Example: Meridian holds "birthday = 2001-05-17"; Chronos holds a recurring event/reminder derived from or referencing that fact. There must be no duplicate authoritative truth.

### Contact information

- **Phone numbers and email addresses:** value, type (personal/work/other/custom), valid from/until, primary status, notes, source.
- **Websites:** URL, type, notes.
- **Contact addresses:** address/place (Atlas reference where appropriate), type (home/work/mailing/other/custom), valid from/until, current status, source.

"This is a contact/residential address of the Person" is distinct from "this Person is associated with this Place." A school, a favorite bar or a frequently visited place is not automatically a contact address.

### Online accounts and identities

An account is a richer record, not a username string, because one person may have several accounts on a service, old or deleted accounts, and changing usernames, display names and avatars. May include: service/platform, current handle and handle history, display-name history, profile URL, service account ID if known, creation date if known, state (active/inactive/deleted/unknown), profile/avatar history, notes, source, and a reference to the corresponding Hermes communication identity where appropriate. A Person may have multiple accounts on one service; historical and deleted accounts remain representable.

Boundary: Meridian knows "this Instagram account belongs to Peter." Hermes knows "these messages came from this communication identity." They are linked by generic references, without special database coupling and without copying Hermes data into Meridian. Secrets are never stored here.

### Contact preferences

May include: preferred channel, preferred phone/email/account, best time to contact, do-not-contact channels, notes. This is not an automation or rules engine.

### Appearance

Legitimately known or observed information, potentially temporal. Categories: physical basics (height, weight, build, posture); hair (color, natural color, length, style, texture, facial hair); face/eyes (eye color, glasses/contacts, notable features); distinguishing features (tattoos, piercings, scars, birthmarks); style/presentation (clothing style, colors, accessories, jewelry).

History is preserved (natural hair brown; 2022–2024 black; 2024–2025 blonde; brown from 2025; started wearing glasses in 2023; tattoo acquired in 2024). Observed and estimated values are distinguished by the general source/confidence model ("168 cm, person told me, high" vs "≈170 cm, my estimate, low").

Meridian is not a biometric database. Argus may use technical recognition representations internally; those are not Meridian Person knowledge.

### Places

Meridian owns knowledge about the relationship between a Person and a Place; Atlas owns the Place. An association may include: place (Atlas reference), relationship/type (residence, former residence, hometown, workplace, school, frequently visited, owns property, family home, significant place, custom), valid from/until, current status, notes, source, confidence. The Atlas Place is never duplicated into Meridian.

### Education

Covers more than universities: primary/secondary school, university, courses, certifications, training, other/custom. May include: organization (Meridian Organization), campus/place (Atlas reference), school/faculty/department, program, field of study, degree/qualification, level, start/end dates, status (studying/graduated/dropped out/interrupted/unknown), achievements, notes, source. Enums are not frozen. Education records are temporal domain records that participate in timeline views without duplicate timeline copies.

### Employment and professional activity

Employment may include: organization (Meridian Organization), position/title, department/team, type, start, end, current status, workplace (Atlas reference), manager (Meridian Person), colleagues (Meridian People), notes, source. Professional activity is not limited to conventional employment: self-employment, founder/owner, board/member roles, volunteering, freelancing and custom roles must be representable. Not every Person↔Organization relationship is Employment. Employment/Education are rich domain records; the relationship graph provides the broader connection; neither is a duplicate authority for the other.

### Relationships

Relationships are first-class records, not a field on Person, between Person↔Person, Person↔Organization and Organization↔Organization.

- Types are **not a closed enum**; custom types are required. Illustrative families: family (parent, child, sibling, cousin…), partnership (dating, partner, spouse, ex-partner…), social (friend, acquaintance, classmate, roommate, neighbor…), professional (colleague, manager, client, teacher, student…).
- One pair may have several simultaneous relationships (friends, colleagues and former classmates).
- A record may include: entity A, entity B, type, valid from/until, current/former state, source, date learned, confidence, notes/context.
- **Directional relationships support inverse semantics without two independently authoritative facts that can diverge** (`Peter parent_of Jana` implies `Jana child_of Peter`). The mechanism is Open.
- Relationships are temporal and history is not overwritten (2019–2020 classmates; 2020–2022 friends; 2022–2024 partners; 2024 former partners; 2025 friends).
- **State vs. event:** "married to Jana" is a relationship state; "wedding on 2024-08-17" is an event.
- Human context labels ("best friend", "gym friend", "maternal grandmother") must be possible without turning every phrase into a global relationship type.
- No native numeric pseudo-psychological scores (trust 73%, closeness 91%, toxicity 48%). A user may record their own note.

### Interests and preferences

Modeled as extensible records, not columns such as `favorite_movie`, `favorite_food`, `favorite_song`. Categories may include hobbies, sports, music, films/TV, books, games, art, technology, science, history, travel, fashion, food/cooking, vehicles, collecting, organizations/communities, topics and custom. A record may include: subject/topic/value/reference, category, strength where useful, valid from/until, current/historical state, notes, source, confidence.

Sentiment/state (loves, likes, neutral, dislikes, hates, favorite, preferred, avoids) stays extensible. "Favorite" is a property/state, not a column, and multiple favorites are possible. Preferences may have context ("likes wine with dinner; dislikes alcohol at parties"; "prefers text normally, phone when urgent") without becoming a rules engine. History is preserved (favorite band X 2019–2021, Y from 2022).

**Music and Lyra.** Preferences may target a genre, artist, album or track. Where a Lyra resource exists, the target is a generic cross-module reference:

```
Person preference: category = Music, kind = Track, target = lyra://track/…, sentiment = favorite
```

Meridian owns the fact that the Person considers it a favorite; Lyra owns the Track. Meridian must not require Lyra: a textual/external value stays possible for music not in Lyra. The target/kind model is extensible, not a permanently closed enum. Rank/qualifier (second favorite, favorite song by Artist X) may be added later; not designed.

### Personal characteristics

Related to, but distinct from, preferences. Legitimately known information about personality traits, habits, routines, mannerisms, skills, abilities, talents, strengths, weaknesses, fears, goals, ambitions, values, beliefs, aversions that are not mere preferences, communication style, sense of humor, recurring behaviors. These need particularly strong source/provenance/confidence semantics, distinguishing self-described ("describes themselves as introverted") from user observation ("I consider them introverted").

Meridian does **not infer** medical, psychiatric or psychological diagnoses from behavior or messages. A legitimately known medical or psychological fact may be recorded with provenance if the user chooses; inference is not the purpose.

### Life Events

A Life Event is a meaningful event in the history of a Person or other Meridian entity that is not already sufficiently represented by an existing authoritative domain record. Types are not a closed enum; examples: birth, death, wedding, engagement, divorce, graduation, moving, funeral, burial, cremation, accident, award, legal event, joining/leaving an organization, major purchase, trip, hospitalization where legitimately recorded, ceremony, milestone, custom.

May include: subject(s), type, title, description, start/end date-time, date precision/uncertainty, place (Atlas reference), involved People and Organizations, participant roles, source, date learned, confidence, notes, attachments/references. Exact schema is future work.

**One event, many participants.** A real-world event is stored once and appears on every participant's timeline (a wedding with Peter and Jana as spouses; a birth with Anna as child/subject, Peter as father, Jana as mother). Participant roles are extensible.

**Event vs. state/domain record.** Wedding = event; Married to Jana = relationship state. Started job = event; Employment at Company X = Employment record. Moved to Brno = event; Residence in Brno = place association. Graduation = event; Education at University X = Education record. Users are not required to duplicate information to make it appear on a timeline: an Employment record dated 2022-04-01 → 2024-01-31 can yield "Started working at Company X" and "Left Company X" without separately stored LifeEvents. A stored LifeEvent is appropriate when the event carries meaning beyond an existing authoritative record.

### Date precision and uncertainty

Not every fact or event has an exact timestamp. The model must be able to represent: exact date; month known/day unknown; year known; approximate date; date/time range; unknown date; natural descriptions ("summer 2022"). Examples: `2024-08-17`, `2024-08`, `2024`, "approximately August 2024", "May 2023 → July 2023", "summer 2022", unknown. Precision and uncertainty must not be destroyed by forcing a precise datetime. This applies to temporal Meridian records generally, not only Life Events. The storage representation is Open.

### Timeline

**The timeline is a view, not an authoritative copy of temporal data.** Anything temporal may participate: Life Events, Education, Employment, professional activity, Relationships, place associations, appearance changes, preference changes, personal dates and other temporal facts/records. No duplicate Timeline records are created to display them. This enables a full Person timeline, category-filtered timelines, relationship history, time-range views and, potentially later, Organization timelines. Query/indexing/materialization is Open.

**Planned: graphical Person timeline.** Nodes on a time axis (e.g. birth, school, moved, wedding) representing temporal information from any of the sources above; hovering/selecting shows a compact preview and can open the authoritative record; category/type may be visually distinguished. Orientation, colors, components and interaction are not specified. It uses no second data model.

**Open/future: Meridian-wide multi-entity timeline** across several People/Organizations (vertical chronology, swimlanes per Person, filtered sets, zoomable views, relationship-oriented views are examples only; none is chosen). The temporal model should not unnecessarily prevent it, and is not over-engineered for it.

**Open: Life Periods/Eras** (user-defined, e.g. "Brno years" 2020–2023). Undecided whether this is a first-class primitive or a saved timeline grouping/view.

### Facts, structured records and custom fields

Facts conceptually carry: subject, key/type, value, source, date learned, validity period, current/historical state, confidence, note. Rich domain objects above are **not** forced into a generic key/value Fact. The boundary between generic Facts, structured domain records and custom fields is Open.

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
- **The PDF contains only information explicitly selected for that export.** The exporter must not silently add information because it seems useful. A CV must not unexpectedly include relationships, private notes, unrelated timeline events, sensitive facts, old addresses or arbitrary data unless selected. Templates may offer default selections, but the final content stays visible and controllable before generation. This applies equally to the categories described in the Information model (appearance, personal characteristics, death details, etc.): none is included unless explicitly selected.
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

- Person and Organization are first-class entities. Meridian is intentionally capable of rich, comprehensive profiles.
- The data model must **not** be a single Person table with hundreds of fixed columns; it must be extensible.
- Historical change is preserved where relevant; provenance, source and confidence matter.
- Knowledge about people is representable as **facts** that capture changing knowledge over time (subject, key/type, value, source, date learned, validity period, current/historical state, confidence, note).
- Identity may include aliases, former names, titles, languages and similar.
- Online accounts are richer records, not username strings.
- Contact information may be temporal.
- Atlas owns Places; Meridian owns Person/Organization relationships to Places. Hermes owns communications; Meridian may link an online account to a Hermes identity. Janus owns secret material. Argus owns the photo/media library and recognition. Lyra owns music. Chronos owns calendar/reminder projection. Meridian never duplicates their data.
- Meridian is authoritative for personal facts/dates such as birthdays; Chronos projects them, and there is no duplicate authoritative truth.
- Meridian must never receive secret material merely because a Janus credential references a Meridian person or organization.
- Education and Employment/professional activity are rich temporal domain records; professional activity is not limited to employment.
- Relationships are first-class, temporal, multi-type and extensible (not a closed enum); one pair may have several; directional relationships have inverse semantics without diverging duplicates. There are no native numeric pseudo-psychological scores.
- Interests/preferences are extensible records, not fixed columns, and may be temporal and contextual.
- Meridian owns a Person's music preference; Lyra owns the referenced music; Meridian does not require Lyra to represent a music preference.
- Personal characteristics distinguish self-description from observation. Meridian does not infer diagnoses from behavior or messages.
- Meridian is not a biometric database.
- Life Events exist as a concept; one event may involve multiple participants and is stored once.
- Events and ongoing states/domain records are distinct concepts.
- Temporal precision and uncertainty must be preserved.
- **The timeline is a derived view over authoritative temporal records**; authoritative data is not duplicated for timeline display. One source of truth may feed multiple views.
- Selective Person PDF/CV uses authoritative Meridian records and includes only explicitly selected information.

## Planned direction

- Rich Person identity, contact, account and appearance capabilities based on the concepts above, built incrementally rather than all at once.
- Life Event support.
- Broad support for historical appearance, preferences, relationships and similar temporal data.
- Graphical Person-specific timeline derived from temporal records; timeline filtering/category views.
- Integration with other modules through references and backlinks.
- Provenance/source tracking for facts.

## Open questions

- Exact schemas for all of the above, and the boundary between generic Facts, structured domain records and custom fields.
- Extensibility mechanism for custom data (user-defined types, module-contributed types).
- Confidence semantics and how conflicting facts are presented.
- Exact inverse-relationship implementation; relationship type vocabulary details (the vocabulary is extensible; how custom types and inverses are defined is not).
- Exact temporal uncertainty representation and how it interacts with [Chronos](chronos.md) date handling.
- Exact preference target/rank/qualifier schema; exact language-proficiency schema.
- Exact funeral/burial/cremation split between the death record and Life Events.
- Exact online-account ↔ Hermes communication identity linkage model, and which module is authoritative for a handle that both know about.
- Life Period/Era as first-class primitive vs. saved/grouped view.
- Meridian-wide multi-Person/Organization timeline UX and visualization.
- Storage, indexing and materialization of timeline views.
- Mechanism for Chronos projection of personal dates (Chronos-side question; see [Chronos](chronos.md)).
- Merging/deduplicating people (e.g. when several communication identities turn out to be the same person) and how references follow a merge.
- Privacy controls within Meridian: handling of especially sensitive categories (health/medical, beliefs, appearance, death details, personal characteristics) and visibility once multi-user exists.
- Interactions, quotes, stories/observations, source/provenance architecture, attachments, Organization taxonomy: not yet designed.
- Search architecture.
- Import from existing tools (e.g. contact formats, MonicaHQ).
