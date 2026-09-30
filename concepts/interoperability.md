# Interoperability

How independent modules, Nexus, collectors and third-party software work together without sharing internals.

## Established decisions

- A module owns its domain data; Nexus owns interoperability.
- Modules **never access each other's databases**.
- Modules interact only through: **documented APIs**, **events**, **shared primitives** (identity, permissions, storage/blobs, notifications), and **cross-module references** ([cross-module-references.md](cross-module-references.md)).
- Nexus does not understand domain concepts, so it cannot become the place where cross-module domain logic lives.
- A reference to a resource never grants access to its contents; discovery via references is permission-aware ([cross-module-references.md](cross-module-references.md)).
- Nexus may synchronize local-first modules' state without owning it; for sensitive modules (Janus) the intended property is that Nexus does not need plaintext ([local-first-and-sync.md](local-first-and-sync.md)).
- **Modules are authoritative for their own domain meaning, not over each other.** Two modules holding the same real-world value (e.g. an Instagram handle known to both Hermes and Meridian) is not an invalid duplicate, and no global "winner" is chosen. Each module keeps its own meaning, provenance and user control; one module's observation may serve as provenance/evidence for another's records. See [Meridian](../modules/meridian.md#cross-module-observations).
- **Sensitive plaintext must not leak into Nexus-visible channels.** For sensitive modules (Janus), secret material must not appear in Nexus metadata, logs, events, search indexes, notifications, URLs or reference metadata, and interoperability must not turn Nexus into a plaintext secret index ([Janus](../modules/janus.md#security-and-custody-model)).
- **Projection is not ownership.** A module may present another module's resources through references/backlinks (e.g. Atlas projecting Meridian or Argus resources on a map) without owning their content; the owning module stays authoritative ([Atlas](../modules/atlas.md#cross-module-geographic-projection)). Derived caches are allowed under the rule below.
- Third-party/community modules must be able to participate through the same mechanisms without being hard-coded into Nexus or official modules. Installed is not trusted: they receive only declared capabilities, never direct access to another module's database or storage, and enforced isolation is required before untrusted modules are treated as safely isolated ([principals-and-permissions](principals-and-permissions.md)).
- Every connected module provides the minimal [module contract](module-contract.md).
- Data is portable and APIs are open ([principles](../principles.md)).
- Module trust categories: Official, Verified, Community. Verified means reviewed, not guaranteed safe.

## Derived data and projection

"Do not copy another module's truth" does not prohibit useful derived data. A module may maintain data derived from another, authoritative module when the derived representation is (Established):

- explicitly **non-authoritative**;
- **rebuildable** from the owning source;
- **not independently edited** as truth;
- **refreshable or invalidatable** when the source changes;
- subject to the same **access/capability rules** as the source ([principals-and-permissions](principals-and-permissions.md)).

Practical cases: Atlas displaying large numbers of Argus geographic resources; Chronos aggregating dated resources; search indexes; projections; performance caches. Projection does not transfer ownership, and a derived copy never becomes an alternative source of truth.

A module-declared projection/query capability (e.g. "dated items in a range", "geographic items in an area") is Planned; its protocol is Open ([module-contract](module-contract.md#planned)).

## Channels (conceptual)

| Channel | Use | Status |
|---|---|---|
| Documented, versioned APIs | Direct request/response between modules and clients | Planned |
| Lightweight system events | Notify others that something changed, without sharing data. Not to be confused with Chronos calendar Events or Meridian Life Events. | Planned |
| Shared primitives | Auth, permissions, blobs, notifications, config | Planned |
| References | Link resources across modules; backlinks | Established requirement; design Open |
| Optional sync for local-first modules | Keep client state usable offline and synchronized | Planned; see [local-first-and-sync.md](local-first-and-sync.md) |
| Connectors / plugins | Bring external services/sources in | Planned; see [Hermes](../modules/hermes.md) |
| Collectors | Device-side ingestion | Planned; see [collectors.md](collectors.md) |
| Locale preference and module localization metadata | Nexus communicates preferred locale; modules declare supported locales | Planned; see [localization.md](localization.md) |

## Guidance for module authors

- Expose what other modules legitimately need through an API or references; do not expect anyone to read your storage.
- Store references to other modules' resources, not copies of their data. Derived caches are allowed only under the [derived-data rule](#derived-data-and-projection).
- Never treat holding a reference as permission to read the target; a module that references a sensitive resource does not gain its contents.
- Treat referenced resources as possibly unavailable or forbidden; render unresolved references gracefully.
- Respect permissions on every cross-module access.

## Open questions

- How observations and changes to shared real-world attributes propagate between modules while preserving each module's domain ownership, provenance and user control (no synchronization engine is designed).
- Module manifest/registration contract and API versioning/compatibility policy, including the manifest comparison used for pre-update review ([updates.md](updates.md)).
- Event model: schema, naming, delivery guarantees, who may subscribe under which permissions.
- Projection/query capability protocol and how derived data is invalidated.
- Where cross-module orchestration lives when it is neither Nexus (domain-ignorant) nor a single module (e.g. "show everything about this person").
- Enforcement of community-module isolation and the trust categories (required before untrusted third-party modules are treated as safely isolated).
- Verified-module review process.
- Standard external protocols to offer for outside tools (e.g. CalDAV, CardDAV, WebDAV, IMAP): none decided.
- Extension of existing modules' data by other modules (e.g. a module adding a custom Meridian data type), if at all.
- How modules mark data as opaque to Nexus (e.g. encrypted state) and what interoperability features remain available for it.
- Localization-related module metadata (supported/fallback locales, translation pack compatibility): fields undefined; see [localization.md](localization.md).
- Import/export interchange formats between ecosystem modules and the outside world.
