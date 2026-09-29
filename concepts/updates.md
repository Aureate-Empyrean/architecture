# Updates

How Nexus and modules are updated. Rule: **updates are inspectable before they are applied.**

This document defines the transparency and safety semantics of updating. It does not define an updater, a metadata schema, or a release/distribution system.

## Concept

For normal interactive updates:

```
Update discovered → Update available → Review update
      → Explicit user approval → Apply update → Verify result
```

"Update available" is a notice, not an action. The primary action leads to a review; nothing in the installation changes until the user approves.

### Pre-update review

The user can inspect, where the information exists: current and target version; a human-readable release summary (features, changes, fixes, known breaking changes); compatibility requirements; whether a restart or downtime is expected; declared data/schema migrations; declared configuration changes; capability/permission changes; links to full release notes. Not every release has every category, and the review shows only what exists rather than empty sections.

Illustrative only (wording and layout are not specified):

```
Update available   v0.1.0 -> v0.2.0        [Review update]

Nexus v0.2.0: What's new / Changed / Fixed
System impact:  Database migration: required · Restart: required · Downtime: ~10 s
Permissions:    No new permissions requested.
[Full release notes]                        [Cancel]  [Update to v0.2.0]
```

### Human notes vs. machine-readable metadata

- **Human release notes** are written by the release/module author ("Added relationship history").
- **Machine-readable update metadata** is what Nexus can inspect or verify programmatically: version, minimum/maximum compatible Nexus version, module protocol compatibility, required restart, declared migrations, capability/permission requirements, dependency requirements, artifact identity/digest, and similar. The schema is not defined.

Prose release notes must not be the only carrier of security-relevant changes.

### Manifest and permission diff

For a module update, Nexus compares the installed module manifest with the proposed one before installation and surfaces security-relevant differences prominently:

```
Permissions / capabilities
+ files.read
+ references.resolve
- notifications.publish
```

(Capability names are examples; the capability model is Open, see [nexus](../modules/nexus.md).) A release note saying "minor update" does not substitute for the diff. Increased privilege requires explicit awareness/approval; reduced privilege may be shown informationally.

### Compatibility

Where reasonably possible, the review detects compatibility before anything is mutated, e.g. "Meridian v1.3.0 requires Nexus >= 0.2.0", a module-protocol change (v1 → v2), a required migration, a required restart. A known unmet compatibility requirement blocks the normal update flow rather than being installed knowingly.

### Migrations

Declared data/schema migrations are shown before approval (e.g. "Data migration required: relationship schema; backup recommended"). Destructive or irreversible migrations should eventually get stronger warnings and backup/restore safeguards. See [backups](backups.md).

### Result

After applying, Nexus verifies enough state to distinguish: **successful**, **completed but module unhealthy**, **failed**, and **rollback/recovery required**. "Updated successfully" is not shown merely because an artifact was downloaded; health verification participates where appropriate.

### Nexus itself

The same principle applies to Nexus updates: installed/target version, release notes, compatibility implications, migrations, restart/downtime expectations and important configuration changes. Updating the control plane deserves at least the transparency of updating a module.

## Established decisions

- Updates are inspectable before they are applied.
- Clicking "Update available" does not itself apply an update; normal interactive updates require explicit approval after review.
- Human release notes and machine-readable update metadata are distinct; security-relevant changes do not rely on prose alone.
- Nexus compares module manifests/capabilities before updating.
- Newly requested privileges are never silently granted, including because an older version was already installed.
- Known incompatible updates are not knowingly applied through the normal update flow.
- Declared migrations and meaningful system impact are surfaced before the update.
- Nexus updates follow the same transparency principle.
- Update success is not defined solely by downloading/installing an artifact.
- Automatic unattended updates are **not** the default.
- Any future automatic-update mode must preserve auditability and must not silently grant newly requested permissions.

## Planned direction

- Structured update metadata.
- Manifest diff presentation.
- Compatibility checks before mutation.
- Post-update health verification.
- Migration awareness.
- Release-note presentation.
- Integration with future release discovery.
- Keeping the design compatible with later verification of provenance, signatures, checksums/digests, trusted publishers and Official / Verified / Community status, which the review may eventually surface.

## Open questions

- Update metadata schema (and its relationship to the module manifest, see [interoperability](interoperability.md)).
- Release discovery mechanism.
- Rollback mechanism.
- Migration execution and sandboxing; arbitrary migration scripts must not run without controls, but the controls are undefined.
- Automatic, scheduled, security-only and staged update policies; update channels.
- Dependency resolution.
- Backup-before-update policy (recommended vs. required, for which updates).
- Release signing/provenance and publisher trust.
- Exact post-update verification rules.
- Approval flow for increased privilege.
- Updates of local-first clients that run without Nexus ([local-first-and-sync](local-first-and-sync.md)), which Nexus cannot review or apply.
