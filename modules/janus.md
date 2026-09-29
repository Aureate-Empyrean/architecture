# Janus

Janus is the credentials, secrets and password-management module. The name is established. It is named after the Roman god of gates, doors and transitions: Janus guards access.

## Purpose

A local-first password manager and secrets vault. Janus must work as a fully useful standalone application on a device without Empyrean Nexus or any server connection:

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

## Owns

- Credentials/secrets and their secure local representation.
- Secret material.

Intended domain breadth (not every type is required in v1):

- website/application logins, usernames/passwords
- passkeys
- TOTP secrets
- recovery codes
- API keys, authentication tokens
- SSH keys, certificates/private keys
- secure notes
- custom secret types

A credential may conceptually include: type, service/application, username/identifier, secret material, associated URLs, notes, attachments where appropriate, history, metadata, and references to other Empyrean resources. The concrete schema is not defined.

## Does not own

- People or organizations ([Meridian](meridian.md)). A credential *references* the person or organization it belongs to; Janus does not store that identity independently.
- Plaintext access for other components. Neither Nexus nor Meridian gains access to secret material by synchronizing, backing up, or being referenced by Janus data.
- Non-secret identity facts such as usernames or social accounts as knowledge about a person (Meridian's domain); the boundary is Open.

## Integrations

- **Meridian**: `janus://credential/… belongs_to -> meridian://person/123` (or `meridian://organization/42`). See the security rule below.
- **Nexus** (optional):
  - Synchronization infrastructure for encrypted vault state.
  - Backup infrastructure that may store and transport Janus state. For both sync and backup, Nexus should not need plaintext vault contents.
  - Reference model: Janus participates in cross-module references, but the mechanism for *sensitive* Janus references is Open (see below). It is not established that Janus relations are published to the normal Nexus reference index.

**Vault contents vs. reference metadata.** These are separate concerns.
- *Vault contents* (passwords, TOTP secrets, API tokens, SSH private keys, recovery codes) are secret material owned and protected by Janus.
- *Reference metadata* (e.g. "credential X belongs_to Meridian person Y") is not the secret itself, but its existence can reveal information and may be sensitive.
- "Nexus does not need plaintext vault contents" therefore does not answer what reference metadata Nexus may know. That question is Open.
- **Backups**: see [backups](../concepts/backups.md).
- Other modules may be referenced from credentials (e.g. a document or note), subject to the same reference rules.

## Established decisions

- The module name is Janus, and it is the credentials/secrets/password-management domain.
- **A Janus client must remain useful when Nexus is unavailable**: Nexus offline, no network, out of reach of a self-hosted server, or the user chooses never to use Nexus. Locally available vault data stays accessible according to the device's normal authentication/security policy.
- Nexus is optional for Janus synchronization.
- Changes made offline are eligible for later synchronization if the user has configured Nexus sync.
- Secret material remains owned by Janus.
- Janus participates in cross-module references.
- **A reference to a Janus resource does not grant access to its secret data.** Referencing a Meridian Person does not give Meridian any secret material, and Meridian must never receive secret material merely because a credential references a person.
- Janus may represent credentials belonging to, or entrusted to the user by, another Meridian Person or Organization (for example, a relative who asks the user to maintain unique passwords for them).
- **Janus is a custody/storage system, not a credential acquisition system.** It must not be designed to steal, extract or covertly acquire other people's passwords, session cookies, authentication tokens or other credentials. Credentials legitimately created, owned, shared with, or entrusted to the user may be stored.
- **Intended security property:** Nexus should not need plaintext access to Janus vault contents in order to synchronize them.

```
Device A ──decrypts locally
   │  encrypted synchronization data
   ▼
 Nexus
   │  encrypted synchronization data
   ▼
Device B ──decrypts locally
```

- "Encrypt the database" is not a sufficient security design. The properties above are goals that require a dedicated security design and review; they are not claims about any existing implementation.

## Planned direction

Product scope, not requirements for a first release; UI is deliberately unspecified.

- Multi-device encrypted synchronization through Nexus.
- Breadth of secret types listed under Owns.
- Password generation.
- Mobile/desktop autofill.
- Biometric/device-assisted unlock where the platform supports it.
- Clipboard timeout/clearing.
- TOTP generation.
- Credential history.
- Secure notes.
- Device pairing and revocation.
- Import/export from common password managers.
- Backup/recovery integration.

## Open questions

All of the following require dedicated security design; none is decided.

**Cryptography and keys**
- Cryptographic protocol and key hierarchy.
- KDF and encryption choices.
- Recovery/key-loss design. Recovery is especially important for a password manager but must not undermine vault confidentiality; this tension is explicitly unresolved.

**Devices and sync**
- Device pairing protocol.
- Sync protocol and conflict resolution.
- How Nexus stores and versions encrypted sync state.
- Secure sharing between users/devices.

**Ownership and references**
- Exact ownership/custody model. Conceptual ownership values: user/self, Meridian Person, Meridian Organization, multiple/shared, unknown. Conceptual custody/context: mine, entrusted to me, shared with me. Vocabulary and data model are not established.
- Exact permission model for secret backlinks. The existence of a secret may itself be sensitive; see [cross-module-references](../concepts/cross-module-references.md).
- Whether Janus publishes sensitive relations to the normal Nexus reference index at all, given that the index itself could reveal that a credential exists; whether some sensitive references are not centrally indexed; whether a stricter protected indexing/discovery mechanism is needed; and what metadata Nexus may know about Janus references.
- Boundary with Meridian's "accounts/social identities".

**Platform**
- Passkey implementation details.
- Browser integration.
- Platform-specific autofill architecture.
- How a standalone Janus client (which has no Nexus) relates to the module registry/lifecycle in [Nexus](nexus.md).
- Whether Nexus's own operational secrets/configuration are related to Janus in any way (default assumption: they are not).
