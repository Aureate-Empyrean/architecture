# Secrets

Two distinct categories of secret exist in the ecosystem. They have different custodians and different runtime requirements, and are not interchangeable. See [ADR 0004](../decisions/0004-janus-custody-and-secret-classes.md).

## A. User vault secrets

Passwords, recovery codes, TOTP seeds, payment-card secrets, personal API keys the user intentionally keeps in the vault, sensitive identity material.

- Belong to [Janus](../modules/janus.md) and follow its custody and security model: user-custodied, usable only where the user's vault is unlocked, never plaintext at Nexus.
- Are not a runtime source for unattended server processes.

## B. Service / integration credentials

OAuth refresh tokens, API credentials, connector credentials, machine credentials required for unattended synchronization (e.g. a Mnemosyne GitHub integration, Hermes connectors, Nexus's own operational secrets).

- May need to be usable by Nexus or the owning service **while the human user is absent and Janus is locked**. They therefore cannot depend on an unlocked user vault as their runtime source.
- Must still be protected at rest and must not leak through logs, events, URLs, reference metadata, notifications, search indexes or backups in plaintext.
- Are scoped by capability: a module or connector receives only the credentials its declared function requires ([principals-and-permissions](principals-and-permissions.md)).

## Established decisions

- The two categories are distinct. "Janus/Nexus secrets" is not a single store.
- Integration and connector credentials are category B, not Janus vault contents. A user may additionally keep a personal copy of a credential in Janus, but Janus is not its runtime source for services.
- Category A never becomes usable by unattended services as a side effect of integration.

## Open questions

- Where category B credentials are held (Nexus, the owning module, or both) and their storage and at-rest protection. Cryptography and the concrete secret store are not designed here.
- Rotation, revocation and re-authorization flows for connectors.
- Backup and restore of category B credentials.
- Whether any user-mediated flow may move a category A secret into category B, and under what explicit approval.
