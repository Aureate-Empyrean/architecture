# 0018. Nexus Remote Access: private-network enrollment owned by Nexus

- Status: Accepted (2026-11-03) as long-term direction. The capability is **Planned / long-term**; nothing here is implemented, and technology and protocol choices remain Open.
- Extends [architecture: Deployment](../architecture.md#deployment); consistent with the sequencing in [0015](0015-training-books-and-delivery-sequencing.md); preserves [0009](0009-janus-client-delivery.md), [0016](0016-janus-independent-web-client.md) and [0017](0017-janus-devices-sync-and-clients.md).

## Context

Nexus runs at home behind a router. The architecture already says remote access needs no Aureate-operated cloud and that a private overlay or VPN is the preferred direction, with Tailscale as the current recommended onboarding option. Setting that up by hand is beyond most users. The owner wants an onboarding comparable to the simplified remote access of systems such as ZimaOS, available to the whole ecosystem rather than to one module.

Two facts shape the boundary:
- A private network connection only proves that a device can reach the network. It is not a user session in Nexus and grants nothing in a module.
- [Janus](../modules/janus.md) keeps a trust boundary that Nexus must not be able to weaken (0009, 0016, 0017).

## Decision

### Intended experience

1. The user installs the Aureate Empyrean mobile application.
2. In Nexus, they open **Settings → Remote Access**.
3. Nexus shows a pairing QR code.
4. The mobile application scans it.
5. The user explicitly authorizes the enrollment.
6. The mobile application joins a private, encrypted network that includes Nexus.
7. The user reaches Nexus and the modules they are authorized for, remotely.

### Established requirements

- **Nexus owns** remote-access orchestration: enrollment, the list of enrolled devices, revocation and access policy. It is ecosystem infrastructure, usable for every authorized module, not a Janus integration.
- No manual port forwarding is required, and neither Nexus nor any module's HTTP endpoint is exposed publicly.
- **Three separate layers.** Network enrollment (the device may reach the private network), Nexus authentication (a user session) and module authorization (permissions inside Nexus) stay separate. Reaching Nexus over the private network still requires signing in, and module access still follows Nexus permissions.
- **Nexus private-network pairing and Janus vault-device pairing are separate operations,** with separate keys and separate authorization. Neither implies the other, and revoking one does not revoke the other unless the user does both.
- Remote Access must not weaken Janus's independent trust boundary. In particular, Nexus never serves, proxies or updates Janus code over the private network, and the authenticity of a Janus origin or release must therefore not depend on network names, routes or certificates that Nexus controls.
- A private network does not make any host trustworthy. A compromised host on it stays compromised, and an enrolled device is not trusted beyond what its network policy allows.
- Nexus supports device enrollment, revocation and access-policy management. Enrollment needs the user's explicit authorization; enrolled devices can be revoked from Nexus.
- No Aureate-operated cloud is required (architecture: Deployment).

### Initial technology direction (not a provider commitment)

- A Tailscale/WireGuard-style overlay network.
- Keep the option of a self-hosted control plane (Headscale or an equivalent), so that the coordination service, and the device and connection metadata it sees, can stay under the owner's control.

### Open, to investigate before implementation

- Android and iOS VPN integration: permissions, background behavior, battery, coexistence with another active VPN, platform entitlements, and distribution constraints of app stores and alternative stores.
- Whether the mobile application embeds an overlay client or delegates to an existing client application; licensing and terms of use of the chosen provider.
- Pairing protocol: QR contents, binding to this Nexus, expiry (a short-lived, single-use code is the expected direction, not yet decided), and protection against a QR code that is photographed or relayed.
- Access policy model: which ports and hosts an enrolled device reaches (for example only the Nexus entry point), per-device policies, and how policies relate to Nexus permissions.
- DNS and TLS for remote names, and how certificates are obtained without weakening the Janus requirement above.
- Where the control plane runs (hosted coordination versus self-hosted), what metadata it learns, and how a self-hosted one is backed up and restored.
- Revocation semantics: removing a device from the network, ending its Nexus sessions, and how these combine in the interface without becoming one implicit action.
- The Aureate Empyrean mobile application's own scope, release signing and distribution, consistent with 0015's sequencing.

## Consequences

- Nexus gains a future Settings area and a device-management responsibility. Neither is implemented, and no networking, mobile, Tailscale or Headscale work starts from this ADR.
- Module documentation can assume remote reachability only as a later capability, never as a substitute for authentication or authorization.
- Janus clients authenticate their synchronization and delivery independently of the network path. A Janus host reachable over the private network is reached as an independent origin, with nothing executable passing through Nexus.
- Device authentication and authorization ([principals-and-permissions](../concepts/principals-and-permissions.md)) gains a concrete first case: devices enrolled for network access.
