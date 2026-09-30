# 0004. Janus custody and the user-secret/service-secret distinction

- Status: Accepted (2026-09-30)

## Context

Janus is a local-first, user-custodied vault: Nexus should not need plaintext vault contents or the master password, and no universal recovery secret exists. At the same time, integrations (a Mnemosyne GitHub connector, Hermes sync) must run unattended while the user is absent and Janus is locked. Earlier documents said integration credentials belong to "Janus/Nexus secret infrastructure", which silently implied either that connectors cannot run or that vault secrets are also usable by the server.

## Decision

- User vault secrets belong to Janus and follow its custody model. They are not a runtime source for unattended services.
- Service/integration credentials are a separate category, usable by Nexus or the owning service without the user present, protected at rest and never leaked through logs, events, URLs, reference metadata or notifications.
- Janus's cryptographic design remains Open and requires dedicated review ([janus-security-design](../modules/janus-security-design.md)).

See [secrets](../concepts/secrets.md) and [janus](../modules/janus.md).

## Consequences

- Connectors can be designed without weakening Janus.
- The service-credential store is a distinct security design task (Open).
- Users see honestly which credentials a server can use on its own.
