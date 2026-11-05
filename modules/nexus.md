# Empyrean Nexus

## Purpose

The shared foundation and user-facing control plane of the ecosystem. Nexus provides infrastructure and interoperability so that independent modules can form one coherent system.

Empyrean Nexus is intentionally the only two-word product/module-level name.

## Owns

Responsibilities that are established or expected eventually:

- identity/authentication
- permissions/capabilities
- module lifecycle and registry (install, enable, disable, update, uninstall); updates are reviewed before being applied ([updates](../concepts/updates.md))
- shared configuration and service/integration credentials (not user vault secrets; see [secrets](../concepts/secrets.md))
- ecosystem/default locale preference and localization interoperability (see [localization](../concepts/localization.md))
- gateway/routing
- versioned APIs
- lightweight system-event mechanism (change notifications between components; distinct from Chronos calendar Events and Meridian Life Events)
- notifications
- health/status
- shared storage primitives
- backup/export/restore infrastructure
- cross-module resource/reference infrastructure (index, resolution, backlinks)
- plugin/connector infrastructure
- projection query forwarding between modules (domain-ignorant; [projections](../concepts/projections.md))
- per-module outbound network grants (`network.egress`, [ADR 0010](../decisions/0010-module-network-egress.md))
- optional synchronization infrastructure for local-first modules (Planned; see [local-first-and-sync](../concepts/local-first-and-sync.md))
- remote-access orchestration and device management for a private network (Planned, long-term; [Remote Access](#remote-access-planned-long-term))

## Does not own

- Domain data or domain concepts. Nexus must not understand Person, Message, Photo, Place, Event, Note, etc.
- A central database of every module's domain objects.
- Document editing (that is the Documents module).
- Module business logic.
- A Person or any other domain object representing the user. The user's own Person is a Meridian resource ([meridian](meridian.md#the-distinguished-self-person-me)).
- Plaintext module secrets. In particular, Nexus should not need plaintext access to [Janus](janus.md) vault contents, or the user's Janus master password, to synchronize them, and holds no universal recovery secret for Janus vaults.

## Integrations

- Modules connected to a Nexus installation register with it and are reached through its gateway. Local-first clients such as [Janus](janus.md) also work without any Nexus connection.
- Collectors send data to modules via Nexus APIs/events ([collectors](../concepts/collectors.md)).
- Nexus indexes references between resources ([cross-module-references](../concepts/cross-module-references.md)).
- Nexus provides blob/file primitives ([storage-and-files](../concepts/storage-and-files.md)) and orchestrates backup ([backups](../concepts/backups.md)).
- [Janus](janus.md): Janus works without Nexus. Nexus may host Janus's encrypted snapshot store or opaque ciphertext backups if that option is chosen (Open, [ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md)), and may show a launcher link to a Janus Web Vault on its own origin ([ADR 0016](../decisions/0016-janus-independent-web-client.md), Proposed). It never serves, proxies, embeds or updates Janus code and never owns Janus vault data.
- [Enceladus](enceladus.md) (Planned integration): registration, permission scopes and projection forwarding only; workout rules, records and history remain in Enceladus.
- Common integration surface for community modules ([interoperability](../concepts/interoperability.md)).

## Navigation history and deep links (local pilot, 2026-11-05)

A default-off pilot exists in the Nexus and Mnemosyne working trees; it is not an ecosystem requirement or a production deployment. The Nexus server process must receive `NEXUS_NAVIGATION_HISTORY=1`; the checked-in Compose configuration does not forward it, so `.env` alone cannot enable it.

- **Ownership.** Nexus owns the top-level browser URL, History API writes and the opaque application-frame lifecycle. A participating module owns its domain route codec, normalization, rendering, save/conflict behavior and UX. Nexus treats a route reference as data and never uses it as an API or proxy fetch target.
- **Session fencing.** Frame loads are bound by challenge, session and monotonically increasing revision. Client intents are serialized and stale or superseded messages are rejected rather than automatically rebased.
- **Adoption boundary.** There is no automatic migration. A module needs its own route adapter, callbacks, save guards and real-app tests; granting `ui.application` alone does not make it supported. Mnemosyne is the only worked example, and the SDK, snapshot schema and module adoption remain pilot contracts.
- **Draft copies.** Participating modules may send bounded checkpoints to the trusted parent page. These live in the parent tab's `sessionStorage`, are scoped to origin, tab, owner and frame binding, are not encrypted, and are neither server data nor part of Nexus/module backups. They are recovery copies, not a durable-save or authorization mechanism; Janus must not use this channel.
- **Status.** Recorded local browser evidence covers Nexus, Mnemosyne and unchanged Chronos over loopback HTTP. Other browsers, physical mouse input, BFCache, container/image lifecycle, session expiry, deployment proxy/auth/CSP behavior and quota/timeout failure combinations remain open production gates.

Activation, rollback, wire messages, SDK limits and the adoption checklist are in the [Nexus navigation-history guide](https://github.com/Aureate-Empyrean/empyrean-nexus/blob/main/docs/navigation-history.md); Mnemosyne's codec and recovery behavior are in its [navigation notes](https://github.com/Aureate-Empyrean/mnemosyne/blob/main/docs/NAVIGATION_HISTORY.md).

## Established decisions

- Nexus is small and infrastructure-focused; it does not understand domain concepts.
- Modules own domain data; Nexus owns interoperability.
- Docker Compose first; one public entry point; modules do not expose random user-facing ports; internal ports follow the project's existing convention.
- Single-user first; multi-user may come later.
- Nexus does not become a giant central database.
- Nexus is not a mandatory runtime dependency for modules that operate local-first (Janus requires this).
- Nexus updates and module updates are inspectable before they are applied, with manifest/capability changes compared and new privileges never silently granted ([updates](../concepts/updates.md)).
- Nexus is the control center/homepage; installed modules are full applications with their own navigation and UX, not permanent pages inside the Nexus management sidebar ([architecture](../architecture.md#application-boundary)).
- Nexus state about modules is derived where possible: the reference index and blob usage are rebuildable from modules through the [module contract](../concepts/module-contract.md).
- Uninstalling or disabling a module never deletes its user data; destructive deletion is separate and explicit ([resource lifecycle](../concepts/resource-identity-and-lifecycle.md#destructive-operations-and-module-removal)).
- Permissions constrain components, not only users; installed modules are not trusted by default ([principals-and-permissions](../concepts/principals-and-permissions.md)).
- Nexus owns the ecosystem locale preference; modules own their translatable messages ([localization](../concepts/localization.md)).

## Remote Access (Planned, long-term)

Decided as direction in [ADR 0018](../decisions/0018-nexus-remote-access.md); not implemented.

- **Experience.** Install the Aureate Empyrean mobile application, open Settings → Remote Access in Nexus, scan the pairing QR code, authorize the enrollment explicitly, and reach Nexus and the authorized modules over a private encrypted network.
- **Ownership.** Nexus owns orchestration, enrolled devices, revocation and access policy. It is ecosystem infrastructure for all authorized modules, not a Janus integration.
- **No exposure.** No manual port forwarding; neither Nexus nor module HTTP endpoints are exposed publicly; no Aureate-operated cloud is required.
- **Separate layers.** Network enrollment, Nexus authentication and module authorization stay separate: reaching Nexus over the private network still requires signing in, and modules still follow Nexus permissions. Nexus network pairing and [Janus](janus.md) vault-device pairing are separate operations, and Remote Access must not weaken Janus's independent trust boundary.
- **No implied trust.** A private network does not make a compromised host trustworthy.
- **Technology direction.** A Tailscale/WireGuard-style overlay, keeping the option of a self-hosted control plane (Headscale or equivalent). Not a provider commitment.
- **Before implementation.** Android and iOS VPN integration, permissions and distribution limits; pairing protocol; access-policy model; DNS/TLS for remote names; control-plane placement and metadata; revocation semantics (listed in the ADR).

## Planned direction

Much of this list now exists in a first form; see [implementation status](../implementation-status.md) and the Nexus repository's documentation. Shared blob storage exists only as a bounded primitive ([storage-and-files](../concepts/storage-and-files.md#current-implementation-2026-11)).

- Module registry and lifecycle management.
- Versioned public APIs and a lightweight event mechanism.
- Reference index with backlink discovery, permission-aware.
- Shared blob storage and a user-facing file explorer (likely a Nexus capability rather than a module).
- Backup/export/restore orchestration across Nexus and modules.
- Notification and health/status services.

## Open questions

- Permission/capability model details: vocabulary, granularity, grant flow and enforcement (the minimum rule is in [principals-and-permissions](../concepts/principals-and-permissions.md)).
- Module contract wire format and manifest schema (required areas are in [module-contract](../concepts/module-contract.md)).
- Event mechanism: delivery guarantees, persistence, replay, transport.
- How the reference index stays consistent with module-owned data (push, pull, or both).
- Storage and at-rest protection of service/integration credentials ([secrets](../concepts/secrets.md#open-questions)).
- Which of the responsibilities above split into separate Nexus services versus one deployable.
- How Nexus stores and versions encrypted, opaque sync state, and how devices are authorized/revoked; for Janus, whether Nexus hosts the snapshot store at all ([ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md)).
- Remote Access design questions ([ADR 0018](../decisions/0018-nexus-remote-access.md)): mobile VPN integration, pairing protocol, access policy, control-plane placement, revocation.
- Multi-user: what changes for identity, permissions, and data ownership.
