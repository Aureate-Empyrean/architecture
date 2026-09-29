# Interoperability

How independent modules, Nexus, collectors and third-party software work together without sharing internals.

## Established decisions

- A module owns its domain data; Nexus owns interoperability.
- Modules **never access each other's databases**.
- Modules interact only through: **documented APIs**, **events**, **shared primitives** (identity, permissions, storage/blobs, notifications), and **cross-module references** ([cross-module-references.md](cross-module-references.md)).
- Nexus does not understand domain concepts, so it cannot become the place where cross-module domain logic lives.
- A reference to a resource never grants access to its contents; discovery via references is permission-aware ([cross-module-references.md](cross-module-references.md)).
- Nexus may synchronize local-first modules' state without owning it; for sensitive modules (Janus) the intended property is that Nexus does not need plaintext ([local-first-and-sync.md](local-first-and-sync.md)).
- Third-party/community modules must be able to participate through the same mechanisms without being hard-coded into Nexus or official modules.
- Data is portable and APIs are open ([principles](../principles.md)).
- Module trust categories: Official, Verified, Community. Verified means reviewed, not guaranteed safe.

## Channels (conceptual)

| Channel | Use | Status |
|---|---|---|
| Documented, versioned APIs | Direct request/response between modules and clients | Planned |
| Lightweight events | Notify others that something changed, without sharing data | Planned |
| Shared primitives | Auth, permissions, blobs, notifications, config | Planned |
| References | Link resources across modules; backlinks | Established requirement; design Open |
| Optional sync for local-first modules | Keep client state usable offline and synchronized | Planned; see [local-first-and-sync.md](local-first-and-sync.md) |
| Connectors / plugins | Bring external services/sources in | Planned; see [Hermes](../modules/hermes.md) |
| Collectors | Device-side ingestion | Planned; see [collectors.md](collectors.md) |

## Guidance for module authors

- Expose what other modules legitimately need through an API or references; do not expect anyone to read your storage.
- Store references to other modules' resources, not copies of their data.
- Never treat holding a reference as permission to read the target; a module that references a sensitive resource does not gain its contents.
- Treat referenced resources as possibly unavailable or forbidden; render unresolved references gracefully.
- Respect permissions on every cross-module access.

## Open questions

- Module manifest/registration contract and API versioning/compatibility policy.
- Event model: schema, naming, delivery guarantees, who may subscribe under which permissions.
- Where cross-module orchestration lives when it is neither Nexus (domain-ignorant) nor a single module (e.g. "show everything about this person").
- Sandboxing/isolation of community modules and enforcement of the trust categories.
- Verified-module review process.
- Standard external protocols to offer for outside tools (e.g. CalDAV, CardDAV, WebDAV, IMAP): none decided.
- Extension of existing modules' data by other modules (e.g. a module adding a custom Meridian data type), if at all.
- How modules mark data as opaque to Nexus (e.g. encrypted state) and what interoperability features remain available for it.
- Import/export interchange formats between ecosystem modules and the outside world.
