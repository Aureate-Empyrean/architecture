# Architecture

Ecosystem-level architecture and module boundaries. Per-module detail is in [modules/](modules/); shared mechanisms are in [concepts/](concepts/). Principles are in [principles.md](principles.md).

Status labels: **Established**, **Planned**, **Open** (see [README](README.md#status-vocabulary)).

## Structure

Aureate Empyrean consists of:

1. **Empyrean Nexus** — the central platform, control plane and shared infrastructure.
2. **Independent modules** — each owns a domain.

```
                 ┌──────────────────────────────┐
   user ───────► │        Empyrean Nexus        │
                 │ identity · permissions ·     │
                 │ registry · gateway · events ·│
                 │ references · storage · backup│
                 └───┬─────┬─────┬─────┬────────┘
                     │     │     │     │      (APIs, events, references)
                 Meridian Hermes Atlas Argus Janus Chronos Mnemosyne Documents … community modules

  Collectors (e.g. Android companion) ──► Nexus APIs/events ──► owning module
```

Current conceptual modules:

| Module | Domain | Document |
|---|---|---|
| Meridian | People, organizations, relationships, personal knowledge about them | [meridian](modules/meridian.md) |
| Hermes | Communications | [hermes](modules/hermes.md) |
| Atlas | Places and location history | [atlas](modules/atlas.md) |
| Argus | Photos, video, media metadata, local recognition | [argus](modules/argus.md) |
| Janus | Credentials, secrets, password management; local-first | [janus](modules/janus.md) |
| Chronos | Calendar, dates, events, time-oriented information | [chronos](modules/chronos.md) |
| Mnemosyne | Personal knowledge, notes | [mnemosyne](modules/mnemosyne.md) |
| Documents | Document creation/editing (final name undecided) | [documents](modules/documents.md) |

Some capabilities may exist as **Nexus services** rather than modules (the file explorer/shared storage is the main candidate). Not every capability must be a module.

## Established decisions

### Core ownership rule

- A module **owns its domain data**.
- Nexus **owns interoperability**.
- Modules **must not directly access each other's databases**.
- Nexus **must not become a central database** of every module's domain objects.
- Modules communicate via **documented APIs, events, shared primitives and cross-module references**.

### Nexus scope

- Nexus is infrastructure-focused and small. It **must not understand domain concepts** such as Person, Message, Photo or Place. See [nexus.md](modules/nexus.md).

### Cross-module references

- References are a first-class primitive, provided (indexed/discoverable) by Nexus, with owning modules authoritative for the data. See [cross-module-references.md](concepts/cross-module-references.md).
- Not a graph database, semantic knowledge graph, inference engine or distributed identity system.

### Deployment

- Docker Compose first.
- One public entry point; modules do not expose random user-facing ports.
- Internal port allocation follows the project's existing convention (not defined here).
- Single-user first; multi-user may come later.

### Localization

- Localization is ecosystem-wide. Nexus owns the installation/user locale preference and localization interoperability; modules own their translatable messages. Official and community translations are supported without forking module source, and standalone clients stay localizable without Nexus. See [localization.md](concepts/localization.md).

### Local-first modules

- Local-first operation is a pattern available to modules where it makes product sense; not every module is local-first. Nexus is optional synchronization/interoperability for such modules, not a mandatory runtime dependency. Janus is the first module for which this is an explicit requirement. See [local-first-and-sync.md](concepts/local-first-and-sync.md).
- Secret material remains owned by Janus. Nexus may synchronize encrypted Janus state; the intended security property is that it needs no plaintext vault access to do so. See [janus.md](modules/janus.md).

### Naming

- Parent organization: **Aureate Empyrean**.
- Central platform: **Empyrean Nexus** — intentionally the only two-word product/module-level name.
- Modules use single-word names. Future modules should follow this where a good name exists.

### Licensing

- **AGPL-3.0-or-later** for core official software.
- Separate Aureate Empyrean branding/trademark policy.
- Forking, modification and redistribution are legitimate; useful improvements are encouraged upstream. Independent forks must not falsely present themselves as official releases.
- Community modules may use their own compatible licenses.

### Module trust categories

- **Official**, **Verified**, **Community**.
- Verified means reviewed under a defined process, **not** guaranteed safe.
- The review process and enforcement mechanism are Open.

### Visual identity (summary)

Celestial, black/charcoal, restrained aureate gold; premium and serious. Not cyberpunk, crypto or gaming-launcher; avoid excessive glow, gradients, glass. Current Nexus palette:

| Name | Hex |
|---|---|
| Void Black | `#090A0C` |
| Obsidian | `#101216` |
| Graphite | `#191C22` |
| Elevated | `#222630` |
| Aureate Gold | `#D6AD60` |
| Solar Gold | `#F0D58A` |
| Deep Bronze | `#8C6734` |
| Primary Text | `#F2F0EA` |
| Secondary Text | `#A7A9AF` |
| Muted Text | `#70747D` |

## Planned direction

- Module lifecycle managed through Nexus (install / enable / disable / update / uninstall).
- Versioned APIs and a lightweight event mechanism as the standard inter-module channels.
- Collectors as an ingestion layer feeding modules through Nexus ([collectors.md](concepts/collectors.md)).
- Optional encrypted synchronization for local-first modules ([local-first-and-sync.md](concepts/local-first-and-sync.md)).
- Content-addressed shared storage ([storage-and-files.md](concepts/storage-and-files.md)).
- Portable, restorable backup/export ([backups.md](concepts/backups.md)).
- Nexus-communicated locale, module-declared supported locales and portable translation packs ([localization.md](concepts/localization.md)).
- Third-party module participation through the same public mechanisms official modules use ([interoperability.md](concepts/interoperability.md)).

## Open questions

- Authentication/permission model details, including what "capabilities" means concretely and how cross-module access is authorized.
- Module packaging and runtime contract (how a module declares itself to Nexus; what the registry stores).
- Event delivery guarantees, ordering and persistence.
- Whether/when multi-user is introduced and its effect on ownership and permissions.
- Trust-category review process for Verified modules.
- How local-first clients relate to the module registry/lifecycle and to Nexus backups when they never connect to Nexus.
- Localization details (resource format, fallback chain, translation pack trust/distribution): see [localization.md](concepts/localization.md#open-questions).
- Which capabilities become Nexus services versus modules (files/storage is the leading candidate).
