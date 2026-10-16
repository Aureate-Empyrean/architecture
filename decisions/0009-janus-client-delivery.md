# 0009. Janus clients are independently released standalone applications

- Status: Accepted (2026-10-16)

## Context

Janus must be usable with no Nexus at all ([janus](../modules/janus.md)), and its security design names the **client delivery threat**: if Nexus serves or updates executable Janus client code, a compromised Nexus can deliver modified code that captures the master password during unlock, regardless of whether Nexus ever stores plaintext. Every other official module is delivered as a Nexus-hosted application, so Janus needs an explicit exception.

The owner's primary machine is Windows; the development server is Linux. Android is a later target. The client must share the ecosystem design language ([ADR 0008](0008-shared-design-tokens.md)).

Options considered:

- **Nexus-served web application.** Rejected: it is exactly the delivery threat.
- **Browser extension first.** Deferred: autofill needs an extension eventually, but an extension as the vault itself ties custody to browser stores and their update channels.
- **Electron.** Rejected: a large runtime and attack surface for a vault.
- **Native per-platform (.NET/WinUI).** Rejected: no path to Android or the shared web-based design language.
- **Tauri 2 with a Rust core.** Chosen.

## Decision

- Janus clients are **standalone applications released independently of Nexus**. Nexus never serves, embeds or updates Janus client code. If Nexus synchronization is added later, Nexus handles only ciphertext.
- Stack: **Tauri 2** desktop shell with a **Rust core** (`janus-core`) holding vault logic and cryptography. The interface is plain HTML/CSS/JS that vendors the shared tokens and bundles Roboto locally.
- **Windows desktop is the first target.** Builds and tests run on GitHub Actions (`windows-latest`, plus Linux checks). Android is a later target through the same core.
- **No automatic updater.** Updates are user-initiated installs of a published release, consistent with inspectable updates ([updates](../concepts/updates.md)).
- Release signing is required before Janus is offered for real use. The mechanism (Authenticode certificate, update/release signing key, reproducible builds) is **Open**.
- Janus's accent is **teal**: `#4FB8B0`, light `#8AD8D1`, deep `#2E7F79` ([design-system](../concepts/design-system.md)).

## Consequences

- Janus does not appear as a Nexus-hosted application. How a standalone client relates to the Nexus registry and lifecycle stays Open ([nexus](../modules/nexus.md)).
- The cryptographic design remains Open and needs review ([janus-security-design](../modules/janus-security-design.md)). Until then, builds are development builds that must say so and must not be used for real secrets.
- Release engineering (signing, provenance, distribution channel) becomes part of Janus's threat model.
