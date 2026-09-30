# Lyra

**Status: Planned.** Lyra's role and ownership boundary are established now; its product specification and v1 scope are not.

Lyra is Aureate Empyrean's **multimedia platform**: the user's personal library for media they consume. **Music** is its first and currently most-developed domain. The name has a dual association: the lyre as an instrument, and Lyra as a constellation, which fits the celestial identity of the ecosystem.

## Purpose

A self-hosted library, organization and playback/consumption module for media the user collects, consumes, reads, watches or listens to: the Aureate Empyrean alternative to relying on hosted services for the user's own collection.

Long-term domains include at least **Music, Movies, Television/series, Books/ebooks and Audiobooks**. Other consumed-media types may be added later without a separate module for every medium. Only Music is developed so far; the other domains are Planned direction (see [Domains](#domains)).

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
- Passwords, API credentials and other external-account secrets ([Janus](janus.md)). If an integration needs credentials, Janus/Nexus secret infrastructure should be used per the eventual security architecture; Lyra does not build its own vault.

## Integrations

- **Storage**: a Lyra Track owns the music meaning (artist, album, duration, tags, library state) and references a shared audio blob; shared storage owns the blob where that architecture applies. The blob API is not frozen here.
- **Meridian**: Meridian may hold a fact such as "Person X considers Track Y a favorite" as a reference to `lyra://track/…` or `lyra://artist/…`. Meridian owns the preference; Lyra owns the music. This uses the generic reference mechanism with no special Meridian–Lyra coupling. Lyra resources may eventually discover Meridian references to them through backlinks, subject to normal permission rules. Lyra does not duplicate Meridian's Person model.
- **Janus / Nexus**: credentials for any external integration.
- **Argus**: see [Boundary with Argus](#boundary-with-argus).

## Domains

**Music** is the first and most developed domain; its architecture is described throughout this document (including the TrackSwipe-informed workflow below).

The domains below are **Planned direction only**. They establish semantic direction and are not designs; no schemas, protocols, codecs, transcoding, readers, clients or provider integrations are specified.

- **Movies**: playback and watch-progress semantics.
- **Television/series**: shows, seasons and episodes.
- **Books/ebooks**: authors, series, editions; reading progress and appropriate reading/library semantics.
- **Audiobooks**: book/library metadata combined with audio playback and progress semantics.
- Other consumed-media types may follow.

**Workflows, not schemas.** Lyra may share library/media primitives across domains, but shared primitives do not imply one generic media interaction model (see [principles](../principles.md), "Expose workflows, not schemas"). Music has artists/albums/tracks/playlists with playback and queue semantics; movies have watch progress; TV has shows/seasons/episodes; books have reading progress; audiobooks combine both. A generic "Media Resource" CRUD interface is explicitly not the intent. Interaction models for the new domains are designed when each domain is designed.

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
- Movies, Television/series, Books/ebooks and Audiobooks as later Lyra domains, each with its own workflows. This does not change the ecosystem's current implementation priorities.

## Open questions

- v1 scope; exact music data model; exact resource/reference types.
- **Movies/TV and media-server capabilities**: whether Lyra integrates with, reuses parts of, forks, or independently implements an approach comparable to Jellyfin is Open. Jellyfin integration/reuse is a future option worth investigating when that domain is designed. It is not decided that Lyra forks or depends on Jellyfin, and no transcoding/streaming architecture is chosen.
- Books/ebooks and audiobooks: reader/format support, library and reading-progress semantics, metadata providers, and how audiobooks share primitives with both books and audio playback.
- Which library primitives are shared across domains, and how cross-domain features (search, collections, favorites, history) work without a generic media UI.
- Where the Argus/Lyra boundary needs finer rules (e.g. home videos).
- Resource/reference types for non-Music domains (e.g. movies, episodes, books), and how Meridian preferences for films/TV/books reference them.
- Playback, streaming and transcoding architecture; supported audio formats; clients (mobile/desktop/web).
- **Local-first/offline/client architecture.** Undecided: server-first vs. local-first, offline capability, synchronization. Janus's local-first requirement does not automatically apply to Lyra ([local-first-and-sync](../concepts/local-first-and-sync.md)).
- Metadata providers; recommendation/discovery design.
- Supported import sources; supported acquisition sources and mechanisms, including any legal or service-specific considerations.
- Automatic vs. manual matching thresholds.
- Relationship to existing local music folders; library scanning.
- Duplicate detection; metadata conflict resolution.
- Playlist interoperability/export.
- Integration with external music servers/services, if any.
- Whether any TrackSwipe code is reused, or only its product ideas.
- Listening history: retention and privacy controls.
