# Meridian

## Purpose

Meridian is the comprehensive private information system for people, organizations, groups, relationships and what the user knows about them. Conceptually: a private-knowledge counterpart to a social profile, combined with a much more powerful MonicaHQ-style personal relationship manager.

Its breadth is intentional. The long-term model below is the architecture; the [V1 boundary](#v1-boundary) is an implementation subset of it, not a redefinition. It holds detailed, evolving information about known people and organizations while preserving history, provenance, uncertainty and the relationships between pieces of information. It stores what the user knows and chooses to record. It is not an OSINT harvesting system and is not designed for covert or non-consensual surveillance.

It must support rich structured information without becoming one giant Person table of hundreds of nullable columns.

## Owns

- **Person**, **Organization** and **Group** as first-class entities.
- Rich domain records around them: identity, contact information, online accounts/identities, appearance, place associations, education, employment/professional activity, relationships, group memberships, interests/preferences, personal characteristics, life events, interactions (the human meaning/context), stories, quotes, personal dates, notes and custom/extensible data. See [Information model](#information-model).
- Knowledge with history, sources/provenance and confidence: facts, claims/evidence and observations (conceptual; see [Knowledge](#knowledge-claims-observations-and-provenance)).
- The Person-facing **timeline** as a derived view (see [Timeline](#timeline)).
- Selective Person document export: choosing which Meridian information goes into a generated document, and the Meridian-specific semantics of building a Person profile or CV from Person records (see [Selective PDF export](#selective-pdf-export)).

Not yet designed beyond boundaries: attachments (beyond existing module boundaries), general observations beyond the conceptual model below, exact schemas.

## Does not own

- Communications themselves: messages, conversations, calls, communication identities' content ([Hermes](hermes.md)). Meridian is referenced *from* them and owns only the person/relationship meaning it adds.
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

**Meridian in the ecosystem.** Meridian must work independently for manually entered Meridian-owned data. It is also intentionally designed to become substantially richer as the rest of Aureate Empyrean exists: deep integration is a core property, not an afterthought. A Person, Organization or Group profile may aggregate views from several modules (Hermes communications, Argus media, Atlas places, Chronos events, Lyra music, Documents/Mnemosyne knowledge, Janus relationships without secret access) while preserving ownership boundaries. Integration does not mean centralization: a module owns its data, Nexus owns interoperability, and modules cooperate through references, backlinks, observations and provenance.

## Information model

Conceptual content of the model, not required fields and not a schema. Every item below is optional. Exact schemas, enums and storage are Open. Cross-cutting rules:

- **One source of truth, multiple views.** A record is not duplicated because it appears in several views. Education may appear in the profile, the education section, the timeline and a CV. Employment: profile, Organization view, timeline, CV. A Relationship: profile, relationship graph, timeline. Group membership: profile, Group page, timeline/history. An Interaction: Person interaction history, Group context, timeline/feed, and as source/provenance for a fact. A Lyra-linked preference: preferences, profile, timeline if temporal, backlink views. Views derive from authoritative records.
- **Temporal information preserves change** rather than overwriting history.
- **Source, date learned and confidence** matter and must distinguish, e.g., "person told me" from "my estimate" (see [Knowledge](#knowledge-claims-observations-and-provenance)).
- **Temporal precision and uncertainty are preserved** (see [Date precision](#date-precision-and-uncertainty)).
- **Extensible:** custom types/categories remain possible; closed enums are avoided.

### Entities

Meridian has three first-class entities: **Person**, **Organization** and **Group**. All follow the same philosophy: a common structured core, appropriate structured domain records, and facts/custom extensibility. An Organization is not "a Person but a company" (see [Organizations](#organizations)). A **Group** is a meaningful collection of people that is not necessarily an organization, and is deliberately not an Organization subtype: four friends are not an Organization (see [Groups](#groups)).

### Person identity

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
- No native numeric pseudo-psychological scores (trust 73%, closeness 91%, toxicity 48%). A user may record their own note. (Real domain quantities, such as an ownership percentage, are unaffected.)
- Relationships involving Organizations are covered under [Organizations](#organizations); Group membership under [Groups](#groups).

### Organizations

A broad first-class entity, not "Person but company". Organization-specific attributes are not forced on every Organization; different types need different data.

- **Types** are extensible and an Organization may have several: company/business, school/university, government/public institution, nonprofit/charity, club/association, sports organization/team, band/artist organization where appropriate, religious, political, healthcare institution, online community where organizational semantics make sense, criminal organization where legitimately known/recorded, custom.
- **Identity** may include: legal name, display/common name, former names, aliases, abbreviations, logo (reference), type(s), founded/dissolved dates, status, registration and tax/VAT identifiers, other external identifiers, websites, languages, notes. Historical changes are preserved where relevant.
- **Structure** (Organization↔Organization): parent/subsidiary, part-of (faculty→university, department→faculty, division), branch, owns/owned-by, member-of, affiliated-with, predecessor/successor, partner, acquired/acquired-by, custom. Examples: University → Faculty → Department; group company → subsidiary; band → label → management agency. A hierarchy does not imply legal ownership. These relationships may be directional, temporal, sourced, confidence-aware and contextual. Real domain quantities such as ownership percentage are allowed.
- **Places:** Atlas owns Places; Meridian owns Organization↔Place associations (registered office, headquarters, branch, warehouse, campus, venue, former headquarters, custom), which may be temporal. Location is not one address string.
- **Contact and online presence:** phone numbers, email addresses, websites, mailing/contact addresses, online/social accounts, GitHub organizations/accounts, community/platform identities, with the same temporal/provenance principles as for people. Hermes may own communication identities/channels/messages; Meridian owns the knowledge that an identity/account belongs to the Organization.
- **People↔Organizations** is broader than Employment: employee, founder, owner, board member, volunteer, student, teacher, member, contractor, client, manager, custom. Employment and Education records remain authoritative for their richer data; an Organization's related People are derived from these records/relationships rather than stored as duplicate lists.
- **Facts, events, timeline:** Organizations may have generic Facts with provenance/history/confidence and participate in Life Events (founded, dissolved, opened/closed branch, acquisition, merger, rebrand, leadership change, award, custom), without duplicate events where another authoritative record already expresses it. The timeline is derived; Organization timeline support is Planned/Open and not specified.

### Groups

A Group is a meaningful collection of people that is not necessarily an organization: friend group, classmates, gym group, household, family branch, former classmates, informal team, recurring social group, custom. It is lighter-weight than an Organization. May include: name, aliases/former names, description, type/context, members, member roles/context, formed/dissolved dates where meaningful, related places, relationships to People, Organizations and other Groups, facts, events, sources, notes. Schema is Open.

- **Membership is many-to-many.** A Person may belong to any number of Groups at once (e.g. a friend group, a gym group, a family group, former classmates), and a Group has any number of People. It is never `Person.group_id` or any single-group ownership. Membership is a record with its own metadata: Person, Group, role/context, joined, left, current/former, notes, source, confidence. It is temporal, and historical membership is preserved.
- **Co-membership does not imply a relationship.** If Peter and Jana belong to the same Group, Meridian may know they share membership. It must not infer `friend_of` or any other relationship unless that is separately known and recorded.
- **Group in interactions and events:** Group is context, not a shortcut that replaces known participants (see [Interactions](#interactions)).
- Generic Group↔Group and Organization↔Group relationships may be supported; not over-designed here (Open).

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

A Life Event is a meaningful event in the history of a Person, Organization or Group that is not already sufficiently represented by an existing authoritative domain record. Types are not a closed enum; examples: birth, death, wedding, engagement, divorce, graduation, moving, funeral, burial, cremation, accident, award, legal event, joining/leaving an organization, major purchase, trip, hospitalization where legitimately recorded, ceremony, milestone, custom.

May include: subject(s), type, title, description, start/end date-time, date precision/uncertainty, place (Atlas reference), involved People and Organizations, participant roles, source, date learned, confidence, notes, attachments/references. Exact schema is future work.

**One event, many participants.** A real-world event is stored once and appears on every participant's timeline (a wedding with Peter and Jana as spouses; a birth with Anna as child/subject, Peter as father, Jana as mother). Participant roles are extensible.

**Event vs. state/domain record.** Wedding = event; Married to Jana = relationship state. Started job = event; Employment at Company X = Employment record. Moved to Brno = event; Residence in Brno = place association. Graduation = event; Education at University X = Education record. Users are not required to duplicate information to make it appear on a timeline: an Employment record dated 2022-04-01 → 2024-01-31 can yield "Started working at Company X" and "Left Company X" without separately stored LifeEvents. A stored LifeEvent is appropriate when the event carries meaning beyond an existing authoritative record.

### Interactions

An Interaction is a Meridian-owned record of the human meaning/context of contact or activity between people/entities. **Hermes owns the communication itself.**

- Types are extensible, not a closed enum. Examples: in-person meeting, phone/video call, conversation, message or email exchange, activity together, visit, introduction/first meeting, gift, argument/conflict, help/favor, custom.
- Not restricted to user↔Person. An Interaction may involve several participants, with roles/context where useful.
- May include: participants, type, date/time or uncertain temporal information, place (Atlas reference), summary, topics, notes/context, related resources, source/provenance, related Hermes communication, related Chronos event, other generic references.
- **No duplication.** Meridian does not copy a Hermes call, message, conversation or exchange merely to display it. A Meridian Interaction exists only when Meridian has additional person/relationship/contextual meaning to own; otherwise the Hermes resource references the Meridian Person and appears through backlinks/views. Examples: manually recording "had coffee with Peter today" is a Meridian Interaction; an Android call collected into Hermes is a Hermes Call referencing the Person, with no Interaction required; contextual notes the user adds about that call may be a Meridian Interaction referencing the Hermes Call.
- **Interaction vs. Life Event.** An Interaction is contact/activity between participants; a Life Event is something meaningful in the history of a Person/Organization/Group. Coffee together and a phone call are Interactions; a wedding is a Life Event; starting a job is a domain transition or Life Event where useful; a shared vacation may be a Life Event with participants that also contains or relates to Interactions. Edge cases are not forced into one category; they may reference each other.
- **Group context.** "The FI group met for drinks" is one Interaction involving the People who took part, optionally referencing the Group, and possibly a Group-related Life Event if significant. A Group reference does not stand in for participants whose identities are known.
- **Person interaction history is a view.** It may combine Meridian Interactions, Hermes backlinks, and relevant Chronos, Atlas and other referenced resources while preserving ownership boundaries. It is not duplicated storage.

### Stories and quotes

**Stories/anecdotes** preserve human narrative that does not fit key/value Facts ("At school Peter once stole the school bell and hid it in the teacher's car"). May include: title, narrative/content, involved People/Organizations/Groups, exact or approximate time, place (Atlas reference), source/provenance, related Interaction, related Life Event, notes, attachments/references. A story is not automatically decomposed into Facts; it may appear on profiles and timelines while remaining its own information.

**Quotes** are first-class knowledge: quoted Person/entity, quote text, date/time or temporal uncertainty, context, source/provenance (e.g. `hermes://message/9182` or a Meridian Interaction), related Interaction/Hermes resource, notes. The quote is Meridian's record; the source message is referenced, not copied. Exact schema is Open.

### Date precision and uncertainty

Not every fact or event has an exact timestamp. The model must be able to represent: exact date; month known/day unknown; year known; approximate date; date/time range; unknown date; natural descriptions ("summer 2022"). Examples: `2024-08-17`, `2024-08`, `2024`, "approximately August 2024", "May 2023 → July 2023", "summer 2022", unknown. Precision and uncertainty must not be destroyed by forcing a precise datetime. This applies to temporal Meridian records generally, not only Life Events. The storage representation is Open.

### Timeline

**The timeline is a view, not an authoritative copy of temporal data.** Anything temporal may participate: Life Events, Interactions, Stories, Education, Employment, professional activity, Relationships, Group memberships, place associations, appearance changes, preference changes, personal dates and other temporal facts/records. No duplicate Timeline records are created to display them. This enables a full Person timeline, category-filtered timelines, relationship history, time-range views and, potentially later, Organization timelines. Query/indexing/materialization is Open.

**Planned: graphical Person timeline.** Nodes on a time axis (e.g. birth, school, moved, wedding) representing temporal information from any of the sources above; hovering/selecting shows a compact preview and can open the authoritative record; category/type may be visually distinguished. Orientation, colors, components and interaction are not specified. It uses no second data model.

**Open/future: Meridian-wide multi-entity timeline** across several People/Organizations (vertical chronology, swimlanes per Person, filtered sets, zoomable views, relationship-oriented views are examples only; none is chosen). The temporal model should not unnecessarily prevent it, and is not over-engineered for it.

**Open: Life Periods/Eras** (user-defined, e.g. "Brno years" 2020–2023). Undecided whether this is a first-class primitive or a saved timeline grouping/view.

### Knowledge, claims, observations and provenance

A Fact is **not unquestionable objective truth**. Meridian preserves how the user came to know something, conflicting information, historical knowledge and uncertainty. Three conceptual distinctions, useful where relevant; whether they become separate storage primitives, and their final terminology, are Open:

- **Fact / current knowledge state**: what Meridian currently records about an entity.
- **Claim / evidence**: a specific assertion from a source that supports, contradicts, qualifies or updates a Fact or domain record.
- **Observation**: something directly observed by the user or by another module/source.

Example: Peter's employment may have evidence that (a) Peter said "I work at ACME", (b) Jana later said Peter already left ACME, (c) an imported public profile says ACME 2022 → present, (d) the user observed Peter discussing a new job. Meridian must preserve all of these without silently deleting contradictory claims or treating one as automatically objective. A conflict may remain unresolved, and the user can control/override the knowledge state.

**Sources are first-class** and are not limited to a text string such as "Peter". A source may be: a Meridian Person, Interaction or other record; a Hermes message, call or conversation; a Documents or Mnemosyne resource; an Argus photo/media resource; a Chronos or Atlas resource; another module's resource; an imported file; an external source with recorded metadata; direct user observation; manual entry; custom/future types. Cross-module sources use generic Empyrean references rather than copies (`Fact: Peter works at ACME` / `source: hermes://message/9182`). If the referenced module is disabled, unavailable or uninstalled, the provenance reference stays as an unresolved/unavailable reference and does not disappear, per [cross-module-references](../concepts/cross-module-references.md). One Fact or domain record may have multiple sources.

Three separate notions, not to be conflated:

- **Provenance**: where the information came from.
- **Confidence**: how reliable the user considers it.
- **Temporal precision**: how precisely its date/time is known.

A direct source may give an uncertain date; a precise claim is not automatically trustworthy. Confidence is not AI scoring and uses no numeric truth scores (no "73.4%"). A small human-readable vocabulary such as unknown/low/medium/high is the conceptual direction; exact semantics are Open. Automated imports may record what they observed, but that does not make it objectively true.

Facts conceptually carry: subject, key/type, value, source(s), date learned, validity period, current/historical state, confidence, note. Rich domain objects above are **not** forced into a generic key/value Fact. The boundary between generic Facts, structured domain records and custom fields is Open.

### Cross-module observations

Modules are authoritative for their own domain meaning, not over each other. Meridian does not choose a global "winner" just because two modules hold the same real-world value.

Example: Meridian may know Person Natália has an Instagram account with current handle `@newname` and previous handle `@oldname`. Hermes may know an Instagram communication identity with platform account ID `123456`, observed handle `@newname`, historically observed handle `@oldname`, and its messages. These are not necessarily invalid duplicates:

- Meridian owns the knowledge that the online identity belongs to a Person, and the known identity/profile history in Meridian's domain.
- Hermes owns communication identities and the source-observed metadata needed to preserve/import communication correctly.
- Hermes may observe a handle change and expose it; Meridian may use that as provenance/evidence for its own account history.
- Meridian may know that several communication identities belong to one Person, letting Hermes present them as related through references.

The real Open question is how observations and changes to shared real-world attributes propagate between modules while preserving each module's domain ownership, provenance and user control. No synchronization engine is designed here; see [interoperability](../concepts/interoperability.md).

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

## V1 boundary

**Planned** implementation scope, not a redefinition of the product. Everything outside V1 remains part of Meridian's intended architecture ("not in V1" never means "not part of Meridian"). The cut aims at the smallest coherent Meridian that proves rich entity modeling, history, provenance, relationships, Groups, interoperability, and one-source-of-truth/multiple-views. It can be revised.

| Layer | Content |
|---|---|
| **Established long-term architecture** | Everything in this document marked Established. |
| **V1 implementation scope (Planned)** | Person, Organization and Group entities; basic relationships (Person↔Person, Person↔Organization, Organization↔Organization) with temporal validity; many-to-many temporal Group membership; extensible Facts/custom information; source/provenance foundation (multiple sources per record, generic references as sources with unresolved handling, human-readable confidence vocabulary, contradictory information not silently deleted); basic contact information; online accounts as records; basic Education and Employment; basic Interests/Preferences (extensible, reference-or-text target); Life Events; Interactions (manual, optionally referencing other modules' resources); temporal/history concepts including date precision; outbound cross-module references with unresolved handling; a derived Person timeline (chronological view, not necessarily graphical); search and basic navigation sufficient to use the model. |
| **Planned later** | Appearance; personal characteristics; contact preferences; full death/disposition detail; Stories and Quotes; graphical timeline; Organization and Group timelines; richer claim/evidence workflows; selective PDF/CV export; rich Organization type-specific records; deep backlink-driven ecosystem views as other modules appear. |
| **Open** | See [Open questions](#open-questions). |

V1 must work with manually entered data alone; it does not require any other module to exist.

## Established decisions

- Person, Organization and **Group** are first-class entities. Group is not an Organization subtype. Meridian is intentionally capable of rich, comprehensive profiles.
- Group membership is many-to-many, temporal and historical; co-membership never implies another relationship.
- Interactions are Meridian-owned human context; Hermes owns the communication itself. Meridian does not copy Hermes communications, and a Meridian Interaction exists only to own additional meaning. Person interaction history is a view. Interactions are extensible in type and may have several participants.
- Interaction and Life Event are distinct concepts and may reference each other.
- Facts are not objective truth. Contradictory claims are preserved, may remain unresolved, and the user controls the knowledge state. Sources are first-class (not text strings), may be cross-module references, and may be multiple per record; provenance, confidence and temporal precision are separate notions. No numeric truth scores.
- Stories/anecdotes and quotes are first-class knowledge, not decomposed into Facts.
- Modules are authoritative for their own domain meaning, not over each other; Meridian and Hermes may both hold an online identity's handle without either being authoritative over the other.
- Organizations are a broad first-class entity with extensible types, structure/hierarchy relationships (not implying legal ownership), place associations and online presence; People↔Organization relationships are broader than Employment.
- Meridian works independently for manually entered data and is intentionally richer with ecosystem integration; integration does not mean centralization.
- The data model must **not** be a single Person table with hundreds of fixed columns; it must be extensible.
- Historical change is preserved where relevant; provenance, source and confidence matter.
- Knowledge about people is representable as **facts** that capture changing knowledge over time (subject, key/type, value, source, date learned, validity period, current/historical state, confidence, note).
- Identity may include aliases, former names, titles, languages and similar.
- Online accounts are richer records, not username strings.
- Contact information may be temporal.
- Atlas owns Places; Meridian owns Person/Organization/Group relationships to Places. Hermes owns communications; Meridian may link an online account to a Hermes identity. Janus owns secret material. Argus owns the photo/media library and recognition. Lyra owns music. Chronos owns calendar/reminder projection. Meridian never duplicates their data.
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

- Rich Person identity, contact, account and appearance capabilities based on the concepts above, built incrementally rather than all at once, starting with the [V1 boundary](#v1-boundary).
- Life Event and Interaction support.
- Broad support for historical appearance, preferences, relationships and similar temporal data.
- Graphical Person-specific timeline derived from temporal records; timeline filtering/category views. Organization timeline support (Planned/Open).
- Integration with other modules through references and backlinks; profiles aggregating views across modules.
- Provenance/source tracking, including claims/evidence, for facts and domain records.
- Stories and quotes.

## Open questions

- Exact schemas for all of the above, and the boundary between generic Facts, structured domain records and custom fields.
- Extensibility mechanism for custom data (user-defined types, module-contributed types).
- Exact inverse-relationship implementation; relationship type vocabulary details (the vocabulary is extensible; how custom types and inverses are defined is not).
- Exact temporal uncertainty representation and how it interacts with [Chronos](chronos.md) date handling.
- Exact preference target/rank/qualifier schema; exact language-proficiency schema.
- Exact funeral/burial/cremation split between the death record and Life Events.
- Exact online-account ↔ Hermes communication identity linkage model.
- **How observations and changes to shared real-world attributes propagate between modules** while preserving each module's domain ownership, provenance and user control (not "which module wins"). No synchronization engine is designed.
- Whether claims/evidence/observations become separate storage primitives, their terminology, and confidence semantics; how unresolved conflicts are presented. Whether provenance/evidence becomes an ecosystem-wide concept (e.g. for Hermes raw-source data) rather than Meridian-specific is undecided and not assumed.
- Exact Interaction, Story and Quote schemas; participant-role vocabulary; when an Interaction wrapper is warranted vs. a plain Hermes backlink.
- Exact Group schema; Group↔Group and Organization↔Group relationship support; Group timeline/history views.
- Organization type-specific records, hierarchy semantics and type vocabulary; Organization timeline.
- Life Period/Era as first-class primitive vs. saved/grouped view.
- Meridian-wide multi-Person/Organization timeline UX and visualization.
- Storage, indexing and materialization of timeline views.
- Mechanism for Chronos projection of personal dates (Chronos-side question; see [Chronos](chronos.md)).
- Merging/deduplicating people (e.g. when several communication identities turn out to be the same person) and how references follow a merge.
- Privacy controls within Meridian: handling of especially sensitive categories (health/medical, beliefs, appearance, death details, personal characteristics) and visibility once multi-user exists.
- Attachments beyond existing module boundaries.
- Search architecture.
- Final V1 cut (the [V1 boundary](#v1-boundary) is Planned and revisable).
- Import from existing tools (e.g. contact formats, MonicaHQ).
