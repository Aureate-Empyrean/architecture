# 0017. Janus product direction: independent devices, encrypted snapshot sync and a family of clients

- Status: Accepted (2026-11-03) as product direction. Mechanisms marked Open below are not decided and require the reviewed security design.
- Amends [0009](0009-janus-client-delivery.md) (client family); confirms the product direction of [0016](0016-janus-independent-web-client.md), whose delivery specifics remain Proposed; preserves [0004](0004-janus-custody-and-secret-classes.md).

## Context

ADR 0009 made Janus a set of independently released standalone clients, Windows desktop first, and rejected a Nexus-served web application because a compromised Nexus could deliver code that captures the master password. ADR 0016 (Proposed) described a web client on an origin Nexus cannot control, after a feasibility study showed that the existing Rust core runs unchanged in Chromium, Firefox and WebKit through WebAssembly (`janus/docs/WEB_FOUNDATION_REPORT.md`).

The owner has since confirmed how Janus should behave across devices, how devices join and recover, how they unlock, and which clients the product includes. Those decisions constrain the vault format, synchronization, the security design and several documents, so they are recorded here. This ADR does not design or review any cryptographic mechanism.

## Decision

### Established product requirements

**Independent, local-first devices**
- Janus is a standalone application, independent of Nexus.
- Each authorized device keeps its own locally encrypted vault and can unlock and use it on its own, including offline. No device depends on another device being present or online: the phone is not needed to unlock Janus on a PC, and a mobile client need not be online for desktop access.
- Offline changes synchronize when connectivity is available.

**Encrypted synchronization**
- The initial implementation direction is **encrypted vault snapshots**. Per-item change synchronization may be reconsidered later; it is not the initial direction.
- The synchronization server may persist encrypted snapshots, for devices that are offline and for recovery.
- The server never requires plaintext vault contents, the master password or vault-decryption keys.
- Snapshots are versioned, and writes are protected against silent overwrites.
- Conflicting offline changes are preserved and resolved safely; no device's changes are discarded silently.

**Device pairing and recovery**
- QR-code pairing is the preferred convenience workflow for enrolling a new device.
- Pairing requires an authenticated, end-to-end encrypted key transfer and explicit authorization from an existing trusted device. **Scanning a QR code is not authentication.**
- A separate **recovery key** is a planned recovery mechanism.
- Recovery never depends on Nexus, or any Aureate Empyrean infrastructure, holding a universal decryption secret (0004 unchanged).

**Unlock**
- Master-password unlock is the foundation.
- Optional biometric and PIN unlock are planned for supported devices. They protect locally stored key material through the platform's security mechanisms (for example a hardware-backed keystore). **A short PIN is not equivalent to a high-entropy key** and must not be used as one; a PIN only gates key material that the platform protects, for example with attempt limits.
- Platform capabilities and limits are documented per platform.
- Manual locking, automatic locking and protection of decrypted material in memory remain mandatory.

**Clients**

All clients share compatible vault formats and synchronization behavior.

| Client | Decision | Status |
|---|---|---|
| Desktop | The existing Rust core and Tauri 2 direction, independent release signing, offline operation and local custody (0009). Not discarded or replaced because a web vault is planned; it remains the higher-assurance client. | Development build exists; unsigned, unreviewed |
| Web Vault | A standalone browser vault built on the existing Rust/WebAssembly core, with local encrypted storage and offline use where the browser supports it. **Not served, proxied, embedded or updated by Nexus.** It needs its own deployment and client-delivery trust model; 0016 proposes one. | Planned; a feasibility proof of concept exists |
| Browser Extension | An early high-priority client for autofill, password generation, login capture and convenient vault access. It is installed independently from a verified package and must never expose vault contents indiscriminately to web pages. | Planned |
| Mobile | Native clients for the long term: secure local storage, platform-assisted unlock, system autofill and device pairing where the platform supports them. | Long-term |

**Web Vault residual risk (recorded, not mitigated away).** Whoever controls the delivery of Web Vault code — its server, files, TLS key or build pipeline — can deliver malicious code that captures the master password at the next unlock, even though the vault is encrypted locally. Isolating the Web Vault from Nexus removes Nexus as one path to this; it does not remove the risk. **The Web Vault does not provide the trust guarantees of a signed native client,** and no document may claim it does.

### Proposed implementation direction (not yet accepted in detail)

- Web Vault delivery as in 0016: its own origin on a deployment unit Nexus cannot control, a static bundle signed with a release key kept off servers and verified before activation, no automatic or Nexus-triggered updates, a ciphertext-only storage service with revision-checked writes and its own authentication.
- Snapshot sync with compare-and-swap on a revision, snapshot history on the server, and local persistence of the last known snapshot on each device.

### Awaiting design and review (Open)

Key hierarchy; pairing protocol and how a QR code binds the two devices; recovery-key construction; device authorization and revocation, and what revocation means for snapshots a revoked device already holds; rollback protection (a server presenting an older valid snapshot); conflict representation and resolution; metadata visible to the synchronization server (vault identifier, revision, size and timing of saves); PIN and biometric key protection per platform; the Web Vault delivery trust model and release signing; the browser-extension threat model and how the extension relates to the other clients (its own vault replica, or a connection to an installed client); where the synchronization server runs (a ciphertext store hosted by Nexus, which 0009 allows, or an independent Janus host as 0016 proposes); and recovery semantics. The independent security-review requirement remains in force.

### Existing implementation versus future work

Exists as unreviewed development work: `janus-core` (vault create, unlock, lock, master-password change, Login and Secure Note items, password generator, vault format v2, documented encrypted export), the Tauri 2 Windows desktop development build, and the browser feasibility proof of concept. Not implemented: synchronization, pairing, recovery key, biometric or PIN unlock, the Web Vault product, the browser extension and mobile clients. Janus remains unsuitable for real secrets until a reviewed design exists.

## Consequences

- The vault format must allow independent per-device unlock, encrypted snapshots, revision history and conflict preservation. Format changes go through the security design.
- Release signing (Open in 0009) gates the desktop client, the Web Vault and the browser extension alike.
- 0009's rejection of a Nexus-served web application stands. Its deferral of "browser extension first" concerned an extension as the vault itself; this ADR plans the extension as an early client, while how it holds or reaches vault data stays Open.
- [Nexus Remote Access](0018-nexus-remote-access.md) is a separate mechanism. Enrolling a device in a Nexus private network never enrolls it in a Janus vault, and network reachability grants nothing in Janus.
- Pairing, recovery and sync work cannot begin as production features before the security design covers them. Prototypes use synthetic data only.

## Implementation addendum (2026-11-05)

This does not amend the accepted product decision above. The "Existing implementation versus future work" section predates the local Web Vault milestone. Current source state: `janus-core` supports six item kinds, and Web Vault Milestone 1 exists as a local development application (separate static UI and Rust/WASM worker, encrypted IndexedDB, locking, search, encrypted backup/replace, previewed imports). It is not the earlier `poc/` prototype and is not hosted, signed or approved for real secrets. Synchronization, device pairing, recovery keys, server authentication, a signed delivery pipeline, the browser extension and mobile clients remain unimplemented. Cryptography and formats remain unreviewed; this addendum does not change the security-design requirement. See [Janus architecture](https://github.com/Aureate-Empyrean/janus/blob/main/docs/ARCHITECTURE.md) and [handoff](https://github.com/Aureate-Empyrean/janus/blob/main/docs/HANDOFF_2026-11-05.md).
