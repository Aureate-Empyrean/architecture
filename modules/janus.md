# Janus

Janus is the local-first encrypted vault for credentials, secrets, authentication material, payment information and sensitive identity records. The name is established. It is named after the Roman god of gates, doors and transitions: Janus guards access.

Janus is broader than a password manager, although password management and authentication workflows are among its most important responsibilities. It is not a general note-taking application: [Mnemosyne](mnemosyne.md) owns general notes and knowledge, and Janus Secure Notes exist specifically for secret or sensitive information.

**Security boundary of this document.** This document establishes *required security properties and product behavior*. It does **not** define a cryptographic design, and nothing here should be read as one. Janus requires a dedicated, reviewed security/cryptographic design before any implementation is considered production-safe; see [janus-security-design](janus-security-design.md). Security correctness takes priority over feature count.

## Purpose

Janus must work as a fully useful standalone application on a device without Empyrean Nexus or any server connection:

1. install Janus on a phone or computer,
2. create a local encrypted vault,
3. use it indefinitely offline,
4. optionally connect the device to a Nexus installation later,
5. synchronize encrypted vault data between authorized devices when connectivity exists.

```
                Empyrean Nexus
                encrypted sync
                      |
          -------------------------
          |           |           |
        Phone       Laptop      Desktop
       local DB    local DB    local DB
```

Each device stays usable offline; changes synchronize when connectivity returns. Nexus synchronization is an optional enhancement, never a runtime dependency for accessing the user's own vault. See [local-first-and-sync](../concepts/local-first-and-sync.md).

The long-term model below is the architecture. The [V1 boundary](#v1-boundary) is an implementation subset, not a redefinition; later capabilities are deferred, not rejected.

## Owns

- Credentials, secrets, authentication material, payment information and sensitive identity records, and their secure local representation.
- Secret material.
- Typed vault items (see [Item types](#item-types)), organization of vault items, secret history, locking behavior.
- Janus-side handling of import, export and portable transfer of vault items.

## Does not own

- People or organizations ([Meridian](meridian.md)). A credential *references* the person or organization it belongs to; Janus does not store that identity independently. Storing a sensitive identity *document* (passport, ID) in Janus does not make Janus the owner of the Person profile.
- General notes and knowledge ([Mnemosyne](mnemosyne.md)).
- Plaintext access for other components. Neither Nexus nor Meridian gains access to secret material by synchronizing, backing up, or being referenced by Janus data.
- Non-secret identity facts such as usernames or social accounts as knowledge about a person (Meridian's domain); the boundary is Open.
- Shared blob storage mechanics ([storage-and-files](../concepts/storage-and-files.md)); how encrypted attachments use it is Open.

## Integrations

- **Meridian**: `janus://… belongs_to -> meridian://person/123` (or `meridian://organization/42`). See [References and context](#references-and-context).
- **Nexus** (optional):
  - Synchronization infrastructure for encrypted vault state.
  - Backup infrastructure that may store and transport Janus state. For both sync and backup, Nexus should not need plaintext vault contents.
  - Reference model: Janus participates in cross-module references, but the mechanism for *sensitive* Janus references is Open. It is not established that Janus relations are published to the normal Nexus reference index.
- **Shared storage**: encrypted attachments; the confidentiality boundary must be preserved.
- **Backups**: see [backups](../concepts/backups.md).
- Other modules may be referenced from credentials (e.g. a document or note), subject to the same reference rules.

**Vault contents vs. reference metadata.** These are separate concerns.
- *Vault contents* (passwords, TOTP secrets, API tokens, SSH private keys, recovery codes, card numbers) are secret material owned and protected by Janus.
- *Reference metadata* (e.g. "credential X belongs_to Meridian person Y") is not the secret itself, but its existence or label can reveal information and may be sensitive.
- "Nexus does not need plaintext vault contents" therefore does not answer what reference metadata Nexus may know. That question is Open.

## Long-term model

Established at the level of product boundaries and required properties unless labeled Planned or Open. Cryptographic and protocol details are Open.

### Security and custody model

Required properties (Established):

- Janus is **local-first**. The user's usable vault exists on authorized client devices.
- Janus is usable **standalone** without Nexus.
- Nexus may later synchronize **encrypted** Janus state (ciphertext) without needing plaintext vault contents, and **does not need the user's master password**.
- Aureate Empyrean infrastructure **must not possess a universal recovery secret or backdoor** capable of decrypting user vaults.
- Loss of all valid unlock and recovery mechanisms may make the vault **permanently unrecoverable**. This is preferred to a hidden server-side recovery path.
- Sensitive plaintext is exposed **only where necessary for an explicit user workflow**.
- Secrets must not leak into normal Nexus metadata, logs, events, search indexes, notifications, URLs or reference metadata.
- Cross-module interoperability must not turn Nexus into a plaintext secret index.
- "Encrypt the database" is not a sufficient security design.

No security marketing claims ("zero knowledge", "military grade", "unbreakable", "industry-standard encryption" and similar) are made. Precise terminology waits for a reviewed design.

### Master password and unlock

Janus supports a **master-password-based unlock model**. The master password unlocks the user's vault. It is not an export/share password, not by definition a Nexus account password, not something disclosed to Nexus, and not a universal password reused for other Janus workflows.

Future unlock methods (Planned) may include device/platform mechanisms such as biometric or system-credential unlock, provided they preserve the vault security model. Derivation and key-wrapping mechanics are Open.

### Recovery model

Intended long-term philosophy (Planned; mechanisms Open):

- **Recovery key**: when establishing a vault, Janus should support generating separate recovery material that the user stores independently of the vault. It must not require Nexus/Aureate Empyrean to retain a plaintext master secret or universal decryption backdoor.
- **Trusted-device recovery**: an already-authorized trusted device should eventually be able to participate in recovering access or authorizing a new device, without the server possessing plaintext vault contents.
- **Failure case**: if the user has lost master-password access, recovery material and all recovery-capable trusted devices, the vault may be cryptographically unrecoverable. The UX should eventually communicate this clearly and without fearmongering.

Recovery representation, constructions and protocols require security review. The tension between recovery and confidentiality is explicitly not resolved by this document.

### Item types

Janus uses purpose-specific item types and workflows rather than treating every secret as a generic record. The list expresses intended domain breadth and does not imply every type exists in V1. The item model remains extensible for future credential types. Fields named below are potential, not a schema.

- **Login**: username/email, password, one or more URLs/domains, TOTP association, custom fields, credential-specific notes, ownership/context references, password history, autofill metadata.
- **Payment Card**: cardholder, number, expiration, security code, PIN if the user chooses to store it, billing/context information, custom sensitive fields. A card is a card record, not a renamed Login.
- **Identity / Sensitive Document**: passport, national ID, driver's licence and similar; document number, name, issuing authority/country, issue/expiration dates, relevant structured fields, encrypted attachments/scans. Jurisdiction-specific schemas are not enumerated. Janus may store these because its domain includes sensitive identity records; Meridian still owns the general Person profile.
- **Wi-Fi Credential**: SSID, password/key, security type, connection/context fields.
- **TOTP / authentication seed**: secure storage of TOTP material and local generation of current codes, with standards-compatible behavior. A TOTP secret is secret material; copying/autofilling generated codes is Planned.
- **Passkey** (long-term): belongs to the authentication domain. Storage, synchronization and export are not trivial; platform integration, standards, portability and cryptographic handling are Open.
- **API Secret**: API keys, access tokens, client secrets, other application/service credentials.
- **SSH Key**: private/public key material and metadata. No key-generation algorithms or formats are chosen here.
- **Certificate**: certificates and associated private material where applicable.
- **Recovery Codes**: structured storage, including consumed/unused state where appropriate.
- **Secure Note**: encrypted freeform information whose purpose is secrecy/sensitivity. It is not a replacement for Mnemosyne.
- **Custom Secret**: extensible sensitive item for cases that fit no standard type.

### Organization

Folders/collections or equivalent explicit organization, tags, favorites, search, and useful type-based views. Organization exists to find and use secrets efficiently; Janus is not a knowledge-management hierarchy.

### References and context

A Janus item may optionally reference its owner, subject or relevant context with generic Empyrean references (credential belongs to a Meridian Person or Organization; a Wi-Fi credential associated with someone's home; a service credential belonging to an Aureate Empyrean project context).

- **Meridian must never receive secret material merely because a Janus item references a Meridian resource.**
- References/backlinks involving Janus need special sensitivity consideration: even the existence or label of a credential can be sensitive.
- Whether and how Janus publishes references into the normal Nexus backlink/reference indexes remains **Open**; it is not resolved here. See [cross-module-references](../concepts/cross-module-references.md).

### Autofill

An important product capability. Autofill must not expose the entire vault to arbitrary webpages or apps.

- **Browser autofill** (Planned; high priority soon after the initial vault): a browser integration that identifies relevant credentials by domain/context and fills them securely. Extension security and protocol internals are Open.
- **Android/mobile autofill** (Planned): integration with OS autofill mechanisms, including Android/GrapheneOS-compatible ones where platform APIs permit. Platform implementation is Open.

### Password generation and strength

- **Generation** is a core workflow: configurable length, character classes/options, passphrases, secure random generation, and use directly in Login workflows. Generation must use reviewed platform/cryptographic facilities; no custom random-number generator is designed.
- **Strength assessment** is local and may show a qualitative assessment and, where meaningful, estimated entropy/search-space in bits. Estimated entropy for human-created passwords is an *estimate*, not a cryptographic guarantee, and fake precision is avoided; generated passwords/passphrases permit more meaningful reasoning from their generation process. The estimator is Open.

### Credential health and breach checking

Long-term (Planned): locally useful checks such as reused passwords, weak passwords, stale credentials where useful, and compromised-credential detection where safely possible. Findings are actionable and tied to items; there is no gamified security score.

**Breach checking** is desired, but privacy is mandatory (Established): **Janus never sends plaintext passwords or secrets to an external breach-check service**, and requires a privacy-preserving approach that minimizes what is disclosed about a credential. Sending an ordinary unsalted full password hash to an online service is not assumed acceptable. Provider/protocol, self-hosted or offline datasets, caching and privacy properties are Open and need security review.

### Encrypted attachments

Items may carry encrypted attachments where useful (recovery PDF, certificate/key file, document scan, recovery-code file). Integration with shared storage must preserve Janus's confidentiality boundary: shared blob storage must not imply that Janus attachments are stored in plaintext because other modules use generic blob storage. Whether Janus encrypts payloads before shared storage, uses a special encrypted-storage capability, or another reviewed mechanism is Open. See [storage-and-files](../concepts/storage-and-files.md).

### Secret history

Encrypted history for sensitive values where useful (e.g. a previous Login password). History has the same confidentiality expectations as current secrets; the user can inspect relevant previous values and delete history; retention is controllable where appropriate. Secret history is never exposed through generic Nexus activity/history.

### Locking

Manual "Lock now", configurable automatic locking after inactivity, locking on appropriate application/session/device events, clear locked/unlocked state, and (Planned) biometric/system-credential-assisted unlock where safe. **Locking must actually protect access to decrypted vault material, not merely hide UI elements.** Timeout defaults and platform behavior are product/Open decisions.

### Deletion and Trash

Janus needs a safe, recoverable deletion workflow: a stray click must not silently lose an item. Retention, history and recovery of deleted secrets have security implications, so **Mnemosyne's Trash semantics (including its 30-day default) are not copied**. Retention defaults, secure permanent deletion, synchronized deletion, and interaction with backups and history are Open and need security consideration.

### Portable secure item transfer (Planned)

Hosted one-time secret links are not a current requirement. The intended direction is portable encrypted item transfer:

- **Sender**: select one or more items, use a dedicated secure-transfer/export workflow, choose a **transfer password separate from the Janus master password**, and produce a portable encrypted file/package.
- **Recipient**: open/import the package locally, enter the transfer password, decrypt locally, then inspect and optionally import into their own vault.
- The transfer password is not the sender's master password, and the UX should explicitly recommend using a different one ("We strongly recommend using a password different from your Janus master password").
- The format should eventually be documented, versioned, interoperable where reasonably possible, and independent of Aureate Empyrean servers.
- The file extension is Open (`.aep` was considered but conflicts with a common After Effects project extension). The cryptographic container is Open and requires security review.

### Import, export and migration

- **Import/migration** from common ecosystems over time (Planned): Bitwarden, KeePass, 1Password, browser password exports and other standard/structured vault formats. Compatibility with every product is not promised in V1. Import is inspectable and maps source item types into Janus item types without silently discarding important fields. Handling of plaintext source exports is a security question (Open).
- **Export rule (Established): Janus does not provide an ordinary plaintext vault export as its normal export mechanism.** Vault export is encrypted/password-protected; there is no convenient unencrypted CSV/JSON "Export everything" path. Portable encrypted backup/export is the intended normal path. Any future plaintext export for compatibility would require a separate explicit architecture/security decision, which is not made here.

### Backups

Janus participates in the ecosystem backup architecture without weakening its confidentiality model. A general Nexus/Empyrean backup must not require decrypting Janus vault contents, and Janus ciphertext plus the required metadata/recovery information should round-trip through backup and restore. The exact backup/key interaction is Open pending security design. See [backups](../concepts/backups.md).

### Nexus sync

Janus clients may synchronize encrypted state through Nexus (Planned). Required property: Nexus performs the synchronization role without plaintext vault contents. Multi-device use, conflict resolution, device authorization and revocation, trusted-device enrollment, offline edits, ciphertext/version synchronization and interaction with recovery are Planned/Open pending dedicated design. No synchronization cryptographic protocol is designed here.

### Interaction model

**Expose workflows, not schemas** ([principles](../principles.md)). Janus looks and behaves like a secure vault, not a generic CRUD database. A reasonable structure may include All Items, Logins, Cards, Identities, TOTP/Authentication, Keys/Secrets, Secure Notes, Favorites and Trash; navigation is not established. Search is prominent. Opening a Login exposes credential workflows; a Card, card workflows; an Identity, identity/document workflows; an SSH key, key workflows. Item types are not rendered as one generic property form. Sensitive fields support appropriate reveal/copy behavior, and the UI minimizes accidental exposure, especially on overview/list screens: plaintext passwords, card numbers, private keys and TOTP seeds are not shown unnecessarily.

## V1 boundary

**Planned** implementation scope; revisable. **Security correctness takes priority over feature count**, and the cut may shrink if security dependencies make an item inappropriate for a first implementation.

**V1 core**

- Standalone, local-first encrypted vault with master-password unlock. **A reviewed cryptographic design is required before the implementation is considered production-safe.**
- Login, Payment Card, Identity/sensitive document, Wi-Fi credential, API/custom secret and Secure Note items; TOTP storage and local code generation.
- Password generator; password-strength assessment.
- Folders/organization, tags, favorites, search.
- Secret/password history.
- Manual lock and configurable auto-lock.
- Encrypted/password-protected vault export/backup (no plaintext export).
- Basic import/migration from useful common formats (initial sources to be selected).

**V1 conditional on the reviewed security design**

- Encrypted attachments (if the reviewed storage design is ready).
- Recovery key generation.
- Generic references, only where they can be supported without leaking sensitive metadata.
- Recoverable deletion/Trash semantics (needs the security decisions noted above).

**Early follow-up (high priority, not tied to release numbers)**

- **Browser autofill**, treated as an early follow-up rather than an indefinite distant idea if it cannot safely fit V1.
- Android/mobile autofill.
- Expanded migration/import.
- Credential health; privacy-preserving breach checking.
- Trusted-device enrollment/recovery.
- Nexus ciphertext sync.

**Later**

- Passkeys; mature multi-device synchronization; richer platform autofill; privacy-preserving breach databases; portable secure item transfer; additional credential/document types; device management/revocation; advanced migration; certificate/SSH workflows; possibly hardware-backed/platform-key integrations after review.

V1 is separable from autofill, sync and passkeys: none of them is required for a usable standalone vault.

## Established decisions

- Janus is the local-first encrypted vault for credentials, secrets, authentication material, payment information and sensitive identity records; broader than a password manager.
- Janus is not a general notes app; Secure Notes exist for secret/sensitive information, and Mnemosyne owns general notes.
- **A Janus client must remain useful when Nexus is unavailable**: Nexus offline, no network, out of reach of a self-hosted server, or the user chooses never to use Nexus. Locally available vault data stays accessible according to the device's normal authentication/security policy.
- Nexus is optional for Janus synchronization; offline changes are eligible for later synchronization if the user configures Nexus sync.
- Nexus should not need plaintext vault contents, or the user's master password, to synchronize Janus.
- Aureate Empyrean infrastructure holds no universal recovery secret or backdoor; the vault may be unrecoverable if all unlock/recovery means are lost.
- Sensitive plaintext is exposed only where an explicit user workflow needs it; secrets do not leak into Nexus metadata, logs, events, search indexes, notifications, URLs or reference metadata.
- Secret material remains owned by Janus.
- Janus participates in cross-module references. **A reference to a Janus resource does not grant access to its secret data**; Meridian must never receive secret material merely because an item references a person or organization. The existence or label of a credential may itself be sensitive.
- Janus may represent credentials belonging to, or entrusted to the user by, another Meridian Person or Organization (for example, a relative who asks the user to maintain unique passwords for them).
- **Janus is a custody/storage system, not a credential acquisition system.** It must not be designed to steal, extract or covertly acquire other people's passwords, session cookies, authentication tokens or other credentials. Credentials legitimately created, owned, shared with, or entrusted to the user may be stored.
- Janus uses purpose-specific typed items with type-specific workflows; sensitive documents and cards have their own workflows.
- The master password is for unlocking the vault only.
- Janus never sends plaintext passwords/secrets to an external breach-check service.
- Vault export is encrypted/password-protected; there is no ordinary plaintext export as the normal mechanism. A transfer password is conceptually distinct from the master password.
- Locking must protect decrypted vault material, not only hide the UI.
- Password-strength/entropy figures are estimates; random generation uses reviewed facilities, not a custom RNG.
- Janus needs recoverable deletion; Mnemosyne's Trash semantics are not copied.
- Janus attachments must not be stored in plaintext merely because shared blob storage exists.
- No cryptographic algorithm, KDF, key hierarchy, file format, sync protocol, recovery construction or library is established. A dedicated security/cryptographic design and review is required before production use.
- Janus exposes workflows, not schemas.

## Planned direction

Product scope; UI is deliberately unspecified.

- Multi-device encrypted synchronization through Nexus; device pairing and revocation; trusted-device enrollment and recovery.
- Recovery key generation.
- Breadth of item types listed above (passkeys later).
- Browser autofill (early follow-up); Android/mobile autofill.
- Password generation and strength assessment; credential health; privacy-preserving breach checking.
- Biometric/system-credential-assisted unlock where safe; clipboard timeout/clearing.
- Secret history; encrypted attachments.
- Portable secure item transfer.
- Import/migration from common password managers and browsers; encrypted export.
- Backup/recovery integration.

## Open questions

All of the following require dedicated security design; none is decided. The full list and process boundary live in [janus-security-design](janus-security-design.md).

**Cryptography and keys**
- Cryptographic protocol, primitives, key hierarchy, KDF and parameters, vault encryption format, per-item vs. vault-level encryption, metadata confidentiality.
- Recovery-key design and recovery/key-loss design; trusted-device recovery protocol.

**Devices and sync**
- Device pairing/enrollment/revocation protocol; sync protocol and conflict resolution.
- How Nexus stores and versions encrypted sync state.
- Secure sharing between users/devices.

**Ownership and references**
- Exact ownership/custody model. Conceptual ownership values: user/self, Meridian Person, Meridian Organization, multiple/shared, unknown. Conceptual custody/context: mine, entrusted to me, shared with me. Vocabulary and data model are not established.
- Exact permission model for secret backlinks. The existence of a secret may itself be sensitive; see [cross-module-references](../concepts/cross-module-references.md).
- Whether Janus publishes sensitive relations to the normal Nexus reference index at all, given that the index itself could reveal that a credential exists; whether some sensitive references are not centrally indexed; whether a stricter protected indexing/discovery mechanism is needed; and what metadata Nexus may know about Janus references.
- Boundary with Meridian's "accounts/social identities" and with sensitive identifiers held in Meridian.

**Storage, backup, deletion**
- Encrypted attachment storage and its interaction with shared blob storage, including deduplication.
- Backup/restore cryptographic interaction; Trash retention, secure permanent deletion (including limits on modern storage), synchronized deletion and interaction with history and backups.

**Platform and features**
- Passkey architecture; biometric/system unlock; TOTP secret handling; decrypted-memory lifecycle; clipboard handling/clearing; secure local storage.
- Browser-extension and Android-autofill threat models and architecture.
- Breach-check privacy protocol and provider/dataset.
- Encrypted transfer format and file extension; import handling of plaintext source exports; password-strength estimator.
- How a standalone Janus client (which has no Nexus) relates to the module registry/lifecycle in [Nexus](nexus.md).
- Whether Nexus's own operational secrets/configuration are related to Janus in any way (default assumption: they are not).
