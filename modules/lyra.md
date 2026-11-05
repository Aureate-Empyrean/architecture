# Lyra

Lyra's role and ownership boundary are established. Its first **implemented** domain is screen media, Movies and Television (`lyra/`, v0.1.1, Nexus ≥ 0.1.8; [ADR 0013](../decisions/0013-chronos-and-lyra-v1-boundaries.md)); the Music product specification is not yet written. Status labels in this document classify decisions.

Lyra is Aureate Empyrean's **multimedia platform**: the user's personal library for media they consume. Its long-term purpose: **multimedia library and playback: music first; movies, TV, books, audiobooks later.** **Music** is its first and currently most-developed domain *architecturally*; Movies and Television are implemented first because they could be delivered without playback infrastructure. This does not reorder the long-term model. The name has a dual association: the lyre as an instrument, and Lyra as a constellation, which fits the celestial identity of the ecosystem.

## Purpose

A self-hosted library, organization and playback/consumption module for media the user collects, consumes, reads, watches or listens to: the Aureate Empyrean alternative to relying on hosted services for the user's own collection.

Long-term domains include at least **Music, Movies, Television/series, Books/ebooks and Audiobooks**. Other consumed-media types may be added later without a separate module for every medium. Music is developed architecturally; Movies/TV tracking is implemented. Books and Audiobooks remain Planned direction (see [Domains](#domains)).

**Music domain** (intended scope, not a v1 requirement list): artists, albums, tracks, playlists, music metadata, library organization, playback, queue, listening history, favorites, discovery/recommendations, importing music/library metadata, matching unidentified or externally referenced music to source candidates, and acquisition/review workflows.

## Owns

- Consumed-media domain records, by domain (see [Domains](#domains)).
- **Music**: Artist, Album, Track, Playlist, music metadata, library relationships; playback domain and playback/listening state where appropriate (queue, history, favorites); music-specific import, matching and review workflows.

Illustrative referenceable resources (syntax and types are not established; see [cross-module-references](../concepts/cross-module-references.md)):

```
lyra://artist/<id>   lyra://album/<id>   lyra://track/<id>   lyra://playlist/<id>
```

## Does not own

- Generic physical file/blob storage (shared storage; see [storage-and-files](../concepts/storage-and-files.md)).
- The user's own captured/imported life media, such as personal photos and videos ([Argus](argus.md)); see [Boundary with Argus](#boundary-with-argus).
- People and contact profiles ([Meridian](meridian.md)).
- Generic communication ([Hermes](hermes.md)).
- Generic documents/notes ([Documents](documents.md), [Mnemosyne](mnemosyne.md)).
- Module lifecycle and interoperability (Nexus). Nexus does not become a media service.
- Secrets. User vault secrets belong to [Janus](janus.md); credentials an integration needs while the user is absent are service/integration credentials ([secrets](../concepts/secrets.md)). Lyra does not build its own vault.

## Integrations

- **Storage**: a Lyra Track owns the music meaning (artist, album, duration, tags, library state) and references a shared audio blob; shared storage owns the blob where that architecture applies. The blob API is not frozen here.
- **Meridian**: Meridian may hold a fact such as "Person X considers Track Y a favorite" as a reference to `lyra://track/…` or `lyra://artist/…`. Meridian owns the preference; Lyra owns the music. This uses the generic reference mechanism with no special Meridian–Lyra coupling. Lyra resources may eventually discover Meridian references to them through backlinks, subject to normal permission rules. Lyra does not duplicate Meridian's Person model.
- **Secrets**: integration credentials follow the service/integration category ([secrets](../concepts/secrets.md)).
- **Argus**: see [Boundary with Argus](#boundary-with-argus).

## Domains

**Music** is the first and most developed domain; its architecture is described throughout this document (including the TrackSwipe-informed workflow below).

**Movies** and **Television/series** have an implemented tracking design ([Screen media](#screen-media-movies-and-television-v01)); their playback semantics remain Planned. The other domains below are **Planned direction only**. They establish semantic direction and are not designs; no schemas, protocols, codecs, transcoding, readers, clients or provider integrations are specified.

- **Movies**: library, watch history and release tracking (implemented); playback and watch-progress semantics (Planned).
- **Television/series**: shows, seasons and episodes with episode-level tracking (implemented); playback (Planned).
- **Books/ebooks**: authors, series, editions; reading progress and appropriate reading/library semantics.
- **Audiobooks**: book/library metadata combined with audio playback and progress semantics.
- Other consumed-media types may follow.

**Workflows, not schemas.** Lyra may share library/media primitives across domains, but shared primitives do not imply one generic media interaction model (see [principles](../principles.md), "Expose workflows, not schemas"). Music has artists/albums/tracks/playlists with playback and queue semantics; movies have watch progress; TV has shows/seasons/episodes; books have reading progress; audiobooks combine both. A generic "Media Resource" CRUD interface is explicitly not the intent. Interaction models for the new domains are designed when each domain is designed.

## Books: physical and digital

### Established boundaries

- Books belong within Lyra's consumed-media domain, not a separate module. Physical bookshelf tracking is a first-class use case alongside digital ebooks; neither requires a reader implementation.
- Distinguish bibliographic identity (a work and its editions) from the user's owned or accessible copies/formats. Multiple physical or digital copies of one title must relate to the same work/appropriate edition rather than becoming disconnected duplicate books.
- Lyra owns bibliographic metadata, library/copy relationships and reading state; file bytes belong to [shared storage](../concepts/storage-and-files.md), with Lyra holding references. This does not claim a deployed blob service or a completed file migration.
- [Mnemosyne](mnemosyne.md) continues to own general notes and knowledge. Book references can provide context for notes; Lyra must not absorb notes or become a catalog for unrelated documents. A PDF is not automatically a book merely because of its format.

### Planned direction (not implemented)

- Work/edition metadata such as title, authors and series, with user copies linked to the relevant edition.
- Physical copies: ownership, bookshelf/shelf or other location, reading status and progress, without requiring a digital file.
- Digital copies: format, file reference and availability, reading status and progress. PDF, EPUB and other ebook formats are intended candidates; no supported-format matrix or reader support is established.
- Multiple formats or copies of a title can coexist without fragmenting its bibliographic identity. A temporarily unavailable file must not erase the book or its reading history.
- Audiobooks may relate to the same bibliographic identity while retaining audio-specific edition, copy and listening-progress semantics; those details remain Open.

### Open implementation questions

- Exact work/edition/copy schema and UUID/reference types, edition matching, merging and metadata providers.
- Whether reading status/history belongs to a work, edition, copy or a separate reading activity; how progress maps across formats, editions, rereads and audio.
- Owned versus wishlist versus borrowed/access-only states, including whether a wishlist item has no copy and how loans are tracked. Ownership and availability must not be conflated.
- Supported ebook/audio formats, reader or player implementation, file availability rules, import/export and optional sync. No ebook reader, DRM bypass, format support, synchronization or import is claimed implemented or committed by this document.

## Screen media: Movies and Television (v0.1)

The first implemented domain. Its purpose is a personal media tracker that answers: what have I watched, what am I watching, what do I want to watch, which episodes have I seen, what comes next and when, what is coming out soon. Playback is not part of v0.1.

### Resources (Established)

| Type | Reference (Nexus Entity References v2) | Meaning |
|---|---|---|
| Movie | `nexus:v1:lyra:movie:<uuidv7>` | One film. |
| Series | `nexus:v1:lyra:series:<uuidv7>` | One television show. |
| Season | `nexus:v1:lyra:season:<uuidv7>` | A season of one Series (by season number). |
| Episode | `nexus:v1:lyra:episode:<uuidv7>` | An episode of one Season (by episode number). |

All four are resolvable: label, minimal summary (year or air date) and an open path, to requesters the owner granted a resolve scope. Lyra creates a resource when the user first acts on a title (adds it, rates it, marks something watched); browsing and search results are not resources. Removing a title from the library deletes the user's data for it but keeps the catalog resource and its UUID, so a later re-add is the same resource. Music types (`artist`, `album`, `track`, `playlist`) are reserved for the Music domain.

### Provider mapping (Established)

- TMDB identifiers are stored as **external mappings** (`tmdb` movie/tv/season/episode IDs, plus IMDb and TVDB IDs where TMDB supplies them). Matching a title found again by any path resolves to the existing resource: the TMDB ID first, then IMDb ID. One provider title maps to one Lyra resource; Episodes are unique per Series by (season, episode) number and per TMDB episode ID.
- **Provider metadata** (titles, overview, dates, runtime, genres, cast/crew, images, provider status) is stored apart from **user data** (library status, rating, notes, watch history, calendar preference). Refresh rewrites only provider metadata. Episodes that disappear upstream are marked missing, never deleted while they carry history.

### Watch state (Established)

- **Movie**: library status `planned` or `watched` (watched ⇔ at least one history entry); repeated viewings are separate history entries.
- **Series**: `planned`, `watching`, `completed`, `on_hold`, `dropped`. Marking the first episode watched moves `planned` → `watching`. Watching the last aired episode of an ended series offers `completed`; it is never forced.
- **Episodes**: watched ⇔ at least one history entry; mark a single episode, a whole season, or "up to here". The **next episode** is the first unwatched, aired (or next scheduled) episode after the furthest watched one, skipping specials (season 0).
- Personal ratings are 1–10; notes are plain text. History is local and deletable entry by entry.

### TMDB integration (Established)

- TMDB is an external metadata/discovery provider through its official API v3 (`api.themoviedb.org/3`), never scraped and never Lyra's database. Requests carry only the search text, provider IDs, language and region; no library or watch data is sent.
- The credential (v4 read access token preferred; v3 API key accepted) is entered in Lyra's settings, validated against TMDB, stored server-side in the module data volume (file mode 0600), never returned to the browser, logged or exported, and excluded from backups (re-enter after restore). This is the [category B](../concepts/secrets.md) credential held by the owning module; the general location of category B credentials remains Open.
- Images are fetched server-side from TMDB's image service and cached in the data volume; the sandboxed UI receives them as data URIs.
- Rate and failure handling: concurrent request cap, retry with backoff on 429 honouring `Retry-After`, 10 s timeout; when TMDB is unavailable, library, tracking and calendar features keep working from local data.
- Attribution: "This product uses the TMDB API but is not endorsed or certified by TMDB." is shown in Discover and Settings.
- Requires `network.egress` ([ADR 0010](../decisions/0010-module-network-egress.md)); outbound requests go only to the fixed TMDB API and image hosts.

### Release dates (Established)

- Episode air dates and movie release dates are **date-only** and stay date-only; no time is invented.
- Movie dates use the user's release region (settings), preferring theatrical, then digital, then physical release types. If the region has none, TMDB's primary release date is used and marked **estimated**.
- Certainty: future dates from TMDB are **tentative** (may change); past dates are **confirmed**; fallback dates are **estimated**; undated releases are **unknown** and not projected.
- Provider status `Canceled` is shown on the Series; future episodes keep their provider dates while TMDB lists them.

### Calendar sharing

Lyra decides which dates it provides to Chronos through the [`dated-items`](../concepts/projections.md#dated-items-version-1) projection ([ADR 0011](../decisions/0011-projection-queries.md)):

- Episodes from: series being watched (default), all tracked series (not dropped), only selected series, or none; optionally season premieres only.
- Movie releases: watchlist movies (default) or none.
- Per series: follow the default, always, or never.

Items: `lyra.episode-air` (title "Series · S1E3 · Name", grouped by Series) and `lyra.movie-release` (with the release type). Lyra publishes the hint event `lyra.dated-items.changed` (empty payload) when a refresh or a sharing setting changes projected dates. Chronos Events that reference a Lyra resource (`chronos.about`, shared with Lyra) appear on the title as "Planned in Chronos".

### Implementation status (0.1.1, 2026-11-05)

0.1.0 was implemented as described above and verified against a mocked TMDB and, through Nexus 0.1.5, with fixture data in a Docker Compose installation (projection to Chronos, backlinks, resolution, updates). A live TMDB request has not been verified (no credential was available). 0.1.1 declares the application icon and requires Nexus 0.1.8 or later; the repository documentation records no other 0.1.1 change, and this documentation pass did not rerun the suite. Not implemented: music, books, audiobooks, playback, acquisition, *arr, streaming providers.

### Module contract (v0.1.1)

Identity `lyra`, port 16333, capabilities `ui.application`, `storage.data`, `network.egress`, `events.publish`, `references.read`, `references.resolve`; resolve scope `chronos`/`event`; provides projection `dated-items`; Entity References v2; backup v1 (SQLite snapshot, credential excluded); health at `/health`.

## Boundary with Argus

- **Argus** primarily owns personal captured/imported media representing the user's own life and content: personal photos and videos, with their metadata and recognition.
- **Lyra** primarily owns media the user collects, consumes, reads, watches or listens to: albums, tracks, movies, TV episodes, books, audiobooks.
- The distinction is conceptual, not artificially absolute. Interoperability and references remain possible where appropriate (e.g. a personal video referenced from a Lyra-managed collection, or a photo related to a film). Edge cases, such as a home video the user also watches like any movie, are not settled here.

## Established decisions

- Lyra is a first-class module and Aureate Empyrean's multimedia platform for the user's consumed media; long-term domains include at least Music, Movies, Television/series, Books/ebooks and Audiobooks. Music is the first and most-developed domain.
- Lyra owns its media-domain resources (for Music: artists, albums, tracks, playlists). Other modules reference them rather than duplicating them.
- Shared library primitives do not imply a shared interaction model (workflows, not schemas).
- Lyra is intended to include playback, not only metadata cataloguing.
- Lyra integrates through the generic interoperability and cross-module reference mechanisms.
- Meridian may reference Lyra resources for music preferences without owning the catalog. Meridian does not require Lyra to record a textual/external music preference (e.g. "likes this song" with no Lyra track).
- Shared storage, not Lyra domain records, owns shared physical blobs where the shared-storage architecture applies.
- Lyra's matching/acquisition architecture must not be inherently Spotify-specific; Spotify is at most one input.
- Imported playlists become Lyra playlists rather than requiring a Spotify-specific internal model. Provenance may be preserved where useful.
- TrackSwipe is not a separate planned module (see below).
- Lyra owns its library and playback implementation and user experience; complete applications such as Jellyfin, Plex or Navidrome are product references, not runtime backends or required dependencies. Mature components (e.g. FFmpeg, codecs, metadata libraries) are reused where appropriate ([ADR 0005](../decisions/0005-official-modules-own-product-implementations.md)).
- Acquisition/review workflows remain part of Lyra and may use tools such as yt-dlp, spotDL or similar components. The architecture neither defines nor encourages copyright infringement; legality depends on source, content, rights and jurisdiction.

## TrackSwipe (Music domain)

TrackSwipe is an existing standalone prototype built for a Spotify → YouTube Music/local-library migration. It is **not** an Aureate Empyrean module and will not become one. This architecture requires no change to, rewrite of, or immediate integration with its code.

Ideas it proved that Lyra may evolve: importing expected track metadata; searching for and ranking candidates; preferring clean music-oriented sources over music videos where appropriate; presenting uncertain matches for human review with approve / reject / skip (rejection advancing to the next candidate); avoiding already-downloaded tracks; applying authoritative metadata; preserving playlist membership; organizing the resulting library.

Lyra's workflow is **not** a 1:1 embedding of TrackSwipe. It is intended to be source-agnostic:

```
Desired/identified music
   ↓  metadata / search / candidate discovery
   ↓  high-confidence automatic handling where safe
      OR human candidate review where uncertain (approve / reject / skip)
   ↓
library
```

Possible inputs (none committed): Spotify exports/metadata, other services' exports, playlists, albums, manually entered artist/title, URLs, existing local files with incomplete metadata, other imports/connectors.

## Planned direction

- Music library management; artist/album/track/playlist resources.
- Playback; queue, history and favorites where appropriate.
- Music import.
- Generalized candidate matching and review based on lessons from TrackSwipe.
- Source-agnostic import/acquisition workflows.
- Discovery/recommendation capabilities.
- Books/ebooks and Audiobooks as later Lyra domains, each with its own workflows; Movies and Television playback after tracking (v0.1).

## Open questions

- Music v1 scope; exact music data model; exact music resource/reference types.
- **Movies/TV playback and media-server capabilities**: tracking is implemented (above); how Lyra provides local libraries, scanning, playback, progress, streaming and transcoding is Open. Jellyfin, Plex and Navidrome are product references for the capabilities, not backends; which components (e.g. FFmpeg) are reused is not chosen.
- Optional acquisition and organisation interoperability (e.g. external *arr tooling) remains Open and unimplemented. Lyra must work without these tools; they are not required infrastructure or runtime backends. Torrent discovery and download automation are out of scope.
- Streaming-provider links and availability (e.g. open a title on a service the user subscribes to): a future provider integration layer, not service-specific code.
- Merging two Lyra resources that turn out to be the same title (redirect identity), and matching titles across metadata providers other than TMDB.
- Retention and privacy controls for watch history beyond per-entry deletion.
- Books/ebooks and audiobooks: reader/format support, library and reading-progress semantics, metadata providers, and how audiobooks share primitives with both books and audio playback.
- Which library primitives are shared across domains, and how cross-domain features (search, collections, favorites, history) work without a generic media UI.
- Where the Argus/Lyra boundary needs finer rules (e.g. home videos).
- Resource/reference types for Books, Audiobooks and Music, and how Meridian preferences reference them. Movie, Series, Season and Episode reference types are already established above.
- Playback, streaming and transcoding architecture; supported audio formats; clients (mobile/desktop/web).
- **Local-first/offline/client architecture.** Undecided: server-first vs. local-first, offline capability, synchronization. Janus's local-first requirement does not automatically apply to Lyra ([local-first-and-sync](../concepts/local-first-and-sync.md)).
- Metadata providers; recommendation/discovery design.
- Supported import sources; supported acquisition sources, tools and mechanisms, and their legal, rights and service-specific considerations.
- Automatic vs. manual matching thresholds.
- Relationship to existing local music folders; library scanning.
- Duplicate detection; metadata conflict resolution.
- Playlist interoperability/export.
- Import from existing libraries (including those managed by other applications) and interoperability with external services, if any; not as runtime backends.
- Whether any TrackSwipe code is reused, or only its product ideas.
- Listening history: retention and privacy controls.

Scope and sequencing decision: [ADR 0015](../decisions/0015-training-books-and-delivery-sequencing.md).
