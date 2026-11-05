# 0016. Janus web client on an independent origin

- Status: Proposed (2026-11-03) — awaiting owner acceptance
- Amends [0009](0009-janus-client-delivery.md); preserves [0004](0004-janus-custody-and-secret-classes.md).
- See also [0017](0017-janus-devices-sync-and-clients.md) (Accepted): the owner confirmed the Web Vault as a planned client that Nexus never serves, proxies or updates, with its delivery risk documented. The hosting, signing and authentication specifics below remain Proposed.

## Context

The owner decided that Janus will also become a web application integrated with Nexus, while
keeping its own trust boundary. ADR 0009 rejected a *Nexus-served* web application because a
compromised Nexus could deliver code that captures the master password. A feasibility study
(`janus/docs/WEB_FOUNDATION_REPORT.md`) showed that the existing Rust core runs unchanged in
Chromium, Firefox and WebKit through WebAssembly, interoperates with desktop vaults, and keeps
Argon2id and XChaCha20-Poly1305. It also showed that a separate port or hostname on the Nexus
host does not isolate Janus: the Nexus broker holds the Docker socket, and cookies are shared
across ports.

## Decision (proposed)

- A Janus web client may exist, served from **its own origin on a deployment unit Nexus cannot
  control**: no shared host root, Docker daemon, reverse proxy, TLS key, storage or backup
  credentials. Nexus never serves, proxies, embeds or updates Janus code (0009 unchanged on
  this point).
- Janus web code is a static bundle built from reviewed source, signed with a Janus release
  key kept off servers, and verified on the Janus host before activation. No automatic or
  Nexus-triggered updates.
- The Janus server stores ciphertext snapshots only, with revision-checked writes; its
  authentication is independent of Nexus and of the master password.
- The vault runs in the browser through the same Rust core (WebAssembly) in a dedicated
  worker; the vault format stays shared with the desktop client.
- Nexus integration: a launcher link to the exact Janus origin (top-level, `noopener`), and
  optionally owner-authorized, non-secret status metadata. Nothing executable or secret flows
  between them.
- The desktop client remains the high-assurance client.

## Consequences

- Compromising Nexus alone no longer reaches Janus code; compromising the Janus host still
  does. The owner accepts or limits that residual risk explicitly.
- Release signing (Open in 0009) becomes a prerequisite for the web client too.
- Hosting, certificate, signing and authentication choices are listed as open decisions in
  the report and must be made before implementation.

## Implementation note (2026-11-05)

Status remains Proposed; this note does not accept the delivery model. A local Web Vault
Milestone 1 exists in the Janus working tree as a separate static UI and Rust/WASM worker with
encrypted IndexedDB and six item kinds. It is not hosted, signed, verified or deployed, and the
service worker/cache pipeline is not a signed release mechanism. The local client does not
satisfy this ADR's origin, signing, authentication or storage-service requirements. See
[Janus architecture](https://github.com/Aureate-Empyrean/janus/blob/main/docs/ARCHITECTURE.md), [operations](https://github.com/Aureate-Empyrean/janus/blob/main/docs/OPERATIONS.md)
and [handoff](https://github.com/Aureate-Empyrean/janus/blob/main/docs/HANDOFF_2026-11-05.md).
