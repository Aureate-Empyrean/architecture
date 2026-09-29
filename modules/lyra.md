# Lyra

**Status: Planned.** Lyra's role and ownership boundary are established now; its product specification and v1 scope are not.

Lyra is the music module. The name has a dual association: the lyre as an instrument, and Lyra as a constellation, which fits the celestial identity of the ecosystem.

## Purpose

A self-hosted music library, organization and playback module: the Aureate Empyrean alternative to relying on a hosted service for the user's own music collection.

Intended domain (not a v1 requirement list): artists, albums, tracks, playlists, music metadata, library organization, playback, queue, listening history, favorites, discovery/recommendations, importing music/library metadata, matching unidentified or externally referenced music to source candidates, and acquisition/review workflows.

## Owns

- Music-domain records: Artist, Album, Track, Playlist, music metadata, library relationships.
- Playback domain and playback/listening state where appropriate (queue, history, favorites).
- Music-specific import, matching and review workflows.

Illustrative referenceable resources (syntax and types are not established; see [cross-module-references](../concepts/cross-module-references.md)):

```
lyra://artist/<id>   lyra://album/<id>   lyra://track/<id>   lyra://playlist/<id>
```

## Does not own

- Generic physical file/blob storage (shared storage; see [storage-and-files](../concepts/storage-and-files.md)).
- People and contact profiles ([Meridian](meridian.md)).
- Generic communication ([Hermes](hermes.md)).
- Generic documents/notes ([Documents](documents.md), [Mnemosyne](mnemosyne.md)).
- Module lifecycle and interoperability (Nexus). Nexus does not become a music service.
- Passwords, API credentials and other external-account secrets ([Janus](janus.md)). If an integration needs credentials, Janus/Nexus secret infrastructure should be used per the eventual security architecture; Lyra does not build its own vault.

## Integrations

- **Storage**: a Lyra Track owns the music meaning (artist, album, duration, tags, library state) and references a shared audio blob; shared storage owns the blob where that architecture applies. The blob API is not frozen here.
- **Meridian**: Meridian may hold a fact such as "Person X considers Track Y a favorite" as a reference to `lyra://track/…` or `lyra://artist/…`. Meridian owns the preference; Lyra owns the music. This uses the generic reference mechanism with no special Meridian–Lyra coupling. Lyra resources may eventually discover Meridian references to them through backlinks, subject to normal permission rules. Lyra does not duplicate Meridian's Person model.
- **Janus / Nexus**: credentials for any external integration.

## Established decisions

- Lyra is a first-class module for music.
- Lyra owns music-domain resources (artists, albums, tracks, playlists). Other modules reference them rather than duplicating them.
- Lyra is intended to include playback, not only metadata cataloguing.
- Lyra integrates through the generic interoperability and cross-module reference mechanisms.
- Meridian may reference Lyra resources for music preferences without owning the catalog. Meridian does not require Lyra to record a textual/external music preference (e.g. "likes this song" with no Lyra track).
- Shared storage, not Lyra domain records, owns shared physical blobs where the shared-storage architecture applies.
- Lyra's matching/acquisition architecture must not be inherently Spotify-specific; Spotify is at most one input.
- Imported playlists become Lyra playlists rather than requiring a Spotify-specific internal model. Provenance may be preserved where useful.
- TrackSwipe is not a separate planned module (see below).

## TrackSwipe

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

## Open questions

- v1 scope; exact music data model; exact resource/reference types.
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
