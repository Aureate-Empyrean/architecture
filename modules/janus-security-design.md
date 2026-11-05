# Janus Security Design

**Status: no reviewed design. This document is a placeholder and status boundary. It contains no cryptographic design.** An unreviewed implementation draft exists in the Janus repository (`janus/docs/SECURITY_DESIGN_DRAFT.md`, construction in `crates/janus-core`). It is the author's own work, has had no independent review, and is not recorded here until it has been designed and reviewed under the rules below.

[Janus](janus.md) requires a dedicated security/cryptographic design and independent review before any implementation is considered production-safe. Nothing in the architecture repository selects, or should be read as selecting, an algorithm, parameter, key hierarchy, file format or protocol. The properties Janus must satisfy are recorded in [janus](janus.md#security-and-custody-model); this document is where the design that satisfies them will be recorded once it exists and has been reviewed.

**Current implementation pointers (2026-11-05).** A local Web Vault milestone and an unsigned desktop build exist. Their code and format descriptions are unreviewed development material, not accepted design: [Janus architecture](https://github.com/Aureate-Empyrean/janus/blob/main/docs/ARCHITECTURE.md), [operations](https://github.com/Aureate-Empyrean/janus/blob/main/docs/OPERATIONS.md), [vault format](https://github.com/Aureate-Empyrean/janus/blob/main/docs/VAULT_FORMAT.md), [security draft](https://github.com/Aureate-Empyrean/janus/blob/main/docs/SECURITY_DESIGN_DRAFT.md) and [handoff](https://github.com/Aureate-Empyrean/janus/blob/main/docs/HANDOFF_2026-11-05.md). Review must include browser staged-persistence and worker lifecycle races, native concurrent-write durability and Windows filesystem behavior, plaintext import-source handling, and code delivery. Presence in the source tree does not promote any algorithm or parameter into this document.

## Rules for this document

- A choice is recorded here only after it has been designed and reviewed, together with its rationale and the threat model it addresses.
- Established cryptographic primitives and reviewed libraries are used; no home-grown cryptography or custom random-number generator.
- Nothing here claims "zero knowledge", "military grade", "unbreakable" or similar. Terminology is precise and justified by the reviewed design.
- Until the design is reviewed, Janus implementations are not to be presented as production-safe for real secrets.

## Areas that need design (all Open)

- Threat model, including the **client delivery threat**: if Nexus serves or updates executable Janus client code, a compromised Nexus could deliver modified code that captures secrets during unlock ([janus](janus.md#security-and-custody-model)). Delivery is decided by [ADR 0009](../decisions/0009-janus-client-delivery.md): independently released clients that Nexus never serves, proxies or updates. Release signing remains Open for every client.
- **Web Vault delivery trust model** ([ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md); proposed model in [ADR 0016](../decisions/0016-janus-independent-web-client.md)): hosting unit, signed static bundle and its verification, storage-service authentication. Residual risk to state, not design away: control of Web Vault delivery is control of the code that receives the master password.
- Cryptographic primitives; KDF and parameters
- Key hierarchy; master-password handling
- Vault encryption format; per-item vs. vault-level encryption
- Attachment encryption
- Metadata confidentiality
- Secure local storage; decrypted-memory lifecycle
- Clipboard handling/clearing
- TOTP secret handling
- Passkey architecture
- Biometric and PIN unlock: platform key protection, attempt limits, and why a PIN is never a high-entropy key
- Recovery-key design; key-loss behavior; recovery semantics
- Device pairing protocol (QR binding, authenticated end-to-end encrypted key transfer, explicit approval on a trusted device); device authorization and revocation, including snapshots a revoked device already holds
- Encrypted snapshot synchronization ([ADR 0017](../decisions/0017-janus-devices-sync-and-clients.md)): versioning, protection against silent overwrites, conflict preservation and resolution, rollback protection, metadata visible to the synchronization server, and where that server runs
- Backup/restore cryptographic interaction
- Secure deletion limitations on modern storage
- Encrypted transfer format; documented portable encrypted export format readable by independent tooling
- Janus local storage technology
- Import handling of plaintext source exports
- Browser-extension threat model, and how the extension holds or reaches vault data
- Interaction with Nexus Remote Access ([ADR 0018](../decisions/0018-nexus-remote-access.md)): network reachability must never stand in for Janus authentication, and Janus origin or release authenticity must not depend on names, routes or certificates Nexus controls
- Android autofill threat model
- Breach-check privacy protocol
- Security audit/review process (who reviews, when, and what gates production use)
