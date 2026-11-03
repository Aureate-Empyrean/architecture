# Enceladus

## Purpose

A self-hosted, privacy-first module for physical training, strength and progress. Enceladus is the established ecosystem name for the former Gymless concept; it is not a claim that the existing application has been renamed or integrated.

## Current starting point (2026-11-03)

Gymless is an existing React PWA with a server-side SQLite database and an optional Android APK. Its codebase is a starting point for evaluation and hardening, not a deployed Enceladus module. No Nexus registration, projection contract, ecosystem migration or rename is claimed here. The APK was a one-off convenience for a friend unfamiliar with self-hosting. Further APK/mobile development is explicitly not a current priority; see [delivery sequencing](../architecture.md#strategic-sequencing).

### Confirmed audit blockers before integration

The current Gymless audit has confirmed the following blockers (audit findings supplied for this architecture update, not re-verified here):

- A dangerous destructive import can replace existing data with an incomplete import.
- Imports can introduce invalid foreign-key relationships.
- An unbounded invalid-date query can hang.
- The API lacks authentication required before wider exposure.

Backup/restore coverage for records and media, and local persistence reliability, also need hardening. These findings are unresolved integration prerequisites, not fixed capabilities. Import validation and safe failure must precede destructive replacement; date queries need validated, bounded ranges. Wider exposure must wait for appropriate API authentication and authorization, including the standalone deployment boundary. A Nexus gateway alone does not repair unsafe module endpoints.

## Established decisions

- Enceladus owns training planning rules and records; [Chronos](chronos.md) owns calendar presentation and derived projections.
- Enceladus must remain useful without Chronos: simple weekly schedule and date-exception editing, session recording and full training history stay available in Enceladus.
- No shared application database or direct cross-module database access. [Nexus](nexus.md) handles registration, permissions and integration infrastructure, never workout data or training logic ([ADR 0001](../decisions/0001-module-ownership-and-domain-ignorant-nexus.md)).
- Privacy and explicit user control apply to measurements and optional progress photos as well as workouts. Photos are optional, not a requirement for tracking progress.
- The MVP excludes an AI coach, nutrition tracking and wearable integrations. Gamification is optional and must not delay a useful training workflow.

## Owns (planned product scope)

These responsibilities define the intended module; this list is not an inventory of implemented Gymless features.

- Exercise catalog and user-defined/custom exercises.
- Workout templates, weekly schedules and date-specific exceptions.
- Workout sessions, sets, RPE (rating of perceived exertion), supersets and rest timers.
- Personal records (PRs), training history and progress views.
- Body measurements and optional progress-photo metadata and references.

## Does not own

- Cross-module calendar presentation, Chronos calendars or native calendar Events.
- Nexus registration, permission infrastructure or shared file/blob mechanics.
- General notes and knowledge ([Mnemosyne](mnemosyne.md)), nutrition tracking or wearable ingestion in the MVP.

## Planned integration

- First harden the starting application, then design the rename/migration and [module contract](../concepts/module-contract.md). Existing data must survive a reviewed, validated migration. Persistent first-class resources need stable UUIDs; database-local IDs must not become public references ([ADR 0002](../decisions/0002-uuid-resource-identity.md)).
- Initially provide a read-only, date-scoped projection to Chronos through the existing [projection mechanism](../concepts/projections.md), with owner-controlled disclosure and deep links back to Enceladus. Chronos must not edit schedules, expand Enceladus planning rules itself, or become a second workout record store.
- Evaluate `dated-items` v1 for concrete occurrences expanded by Enceladus within a validated bounded range. Exact resource types, stable occurrence identity, kinds, status mapping and disclosure settings remain Open. No recurrence or workout-specific fields are added to Nexus.
- Chronos entries are derived, permission-aware and rebuildable; only a complete successful fetch replaces cached entries. Enceladus remains authoritative when Chronos is absent, disabled or stale. Deep links are intended behavior; the current Nexus bridge limitation is recorded in [Chronos open questions](chronos.md#open-questions).
- Progress-photo bytes follow [shared storage](../concepts/storage-and-files.md); Enceladus owns their training meaning and references. Shared storage is not claimed implemented. The transition from existing local media and consistent record/media [backup and restore](../concepts/backups.md) needs design and verification.

## Open questions

- Practical MVP subset and migration order after the audit blockers are resolved.
- Exact catalog/template/session/measurement resource model, referenceable types, history preservation and PR calculations.
- Weekly schedule/date-exception semantics, stable projected occurrence identity and timed versus date-only training plans.
- Standalone authentication and persistence/backup design; photo permissions, retention and deletion behavior.
- Database choice for the integrated module: existing SQLite is a starting point, not a new ecosystem default. Follow [ADR 0007](../decisions/0007-postgresql-default-database.md) and actual deployment constraints.
- Any later offline/sync behavior, gamification or mobile distribution; none is an integration prerequisite.

Scope and sequencing decision: [ADR 0015](../decisions/0015-training-books-and-delivery-sequencing.md).
