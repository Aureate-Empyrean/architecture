# Implementation status

Where each part of the ecosystem actually stands. The status labels in the [README](README.md) classify decisions; this page reports implementation. **Implementation repositories are authoritative for details**, and this page should be updated when a version or status changes.

Snapshot: 2026-11-05. "Verified" means re-checked in that snapshot; other entries come from each repository's own documentation and were not re-run.

| Label | Meaning |
|---|---|
| Implemented and tested | Exists, with passing automated tests or recorded browser checks |
| Implemented, unverified | Exists, but has not been exercised end to end |
| Work in progress | Partly built, or designed and under way |
| Planned | Intended, not built |

**How to read this page.** *Local development* is source in this workspace, including uncommitted work; it is not a release. *Tested functionality* cites recorded suites or browser evidence from the implementation repositories; this documentation pass did not rerun them. *Production deployment* means only the rows under [Deployments](#deployments), and those come from deployment records that were not re-checked. *Unfinished* features have partial or no code; *Planned* features have none. Acceptance of a design decision does not imply implementation, and passing tests do not authorize activation or deployment.

## Modules

| Module | Folder | Version | Port | Status | Start with |
|---|---|---|---|---|---|
| Nexus | `empyrean-nexus/` | 0.1.9 | 12333 | Implemented and tested; a pre-release, not security-audited. A default-off local navigation-history pilot is documented; it is not enabled by the checked-in Compose file and not deployed | `README.md`, `docs/` |
| Mnemosyne | `mnemosyne/` | 1.2.3 | 14333 | Implemented and tested; has the pilot navigation adapter | `README.md`, `docs/` |
| Chronos | `chronos/` | 0.1.1 | 15333 | Implemented and tested; 0.1.1 declares the application icon and requires Nexus ≥ 0.1.8 | `README.md` |
| Lyra | `lyra/` | 0.1.1 | 16333 | Implemented and tested for films and series; 0.1.1 declares the application icon. Music and books are Planned | `README.md` |
| Meridian | `meridian/` | 0.3.1 | 17333 | Implemented and tested (verified: 99 Python and 16 graph tests). Profile images are Work in progress | [status](modules/meridian.md#implementation-status), `README.md` |
| Argus | `argus/` | 0.1.0 | 18333 | Work in progress: the module works alone (verified: 22 tests), with one known defect. No integration yet | [status](modules/argus.md#implementation-status-2026-11-05), `docs/STATUS.md` |
| Janus | `janus/` | Development build | none | Work in progress: the cryptographic design is unreviewed, and it must not hold real secrets. Desktop client and local Web Vault milestone 1 exist; sync, pairing and extension are Planned. Independent of Nexus | `README.md`, `docs/WEB_VAULT_MILESTONE_1.md` |
| Enceladus, Hermes, Atlas, Documents, Astra | none | | | Planned; no implementation in this workspace | module documents |

Port registry: `empyrean-nexus/docs/ports.md`.

### Private development tooling

Zeus and Dev Orchestrator are private internal tooling, not public modules. Their
implementation status, topology, operating policy, security evidence and known gaps are
maintained only in restricted private repositories and are intentionally not reproduced
here. Neither is production-ready or deployed to Exodus; both are separate from Astra. See
the public boundary note in [Zeus and Dev Orchestrator](modules/zeus.md).

## Deployments

| Installation | Nexus | Mnemosyne | Chronos | Lyra | Meridian | Argus | Source |
|---|---|---|---|---|---|---|---|
| Local test (`aetest`) | 0.1.9 | 1.2.3 | 0.1.1 | 0.1.1 | 0.3.1 | 0.1.0 | Verified |
| Production | 0.1.9 | 1.2.3 | 0.1.1 | 0.1.1 | 0.3.0 | not installed | Deployment records of 2026-11-03 and 2026-11-05; not re-checked |

## Cross-module integrations

| Integration | Mechanism | Status |
|---|---|---|
| Chronos reads Lyra and Mnemosyne dates | `dated-items` projection | Implemented and tested; the Mnemosyne source was off on production at its last record |
| Meridian shows Argus photos | `photo-library`, `photo-renditions`, reference `meridian.representative-image` | Work in progress: Argus projections exist and are tested directly; no Meridian consumer, reference registration or end-to-end flow exists |
| Meridian opens Argus to upload and returns | Bridge `navigate` with a module path | Planned; the bridge takes only a module ID today |

## Deviations recorded

| Deviation | Reason | Where |
|---|---|---|
| Argus media in its own volume, not shared blobs | The blob primitive is too limited | [ADR 0019](decisions/0019-argus-interim-media-storage.md) |
| SQLite, not the default PostgreSQL, in server modules | Module Protocol v1 cannot run a database per module | [ADR 0013](decisions/0013-chronos-and-lyra-v1-boundaries.md), [Meridian](modules/meridian.md#implementation-status) |
| Meridian Life Events without time of day, place or attachments | Smallest useful first form | [Meridian](modules/meridian.md#implementation-status) |

## Superseded

| Earlier position | Replaced by |
|---|---|
| Argus wrapping Immich, Lyra building on Jellyfin as runtime backends | [ADR 0005](decisions/0005-official-modules-own-product-implementations.md) |
| Modules as disposable containers without persistent data ("Module Protocol v1" limitations) | Nexus application infrastructure extension: `storage.data`, `ui.application` |
| Meridian graph shapes and edge styling in its `DESIGN.md` § 21 | § 22, Celestial Graph |

## Not settled

ADR 0016 (Janus web client delivery) remains Proposed. Open questions are listed in each document.
