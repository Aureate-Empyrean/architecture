# 0010. Outbound network access is an explicit, reviewable module capability

- Status: Accepted (2026-11-02)

## Context

Nexus runs every module on its own Docker network created with `internal=True`; module containers have no route to the Internet ([Nexus security model](https://github.com/Aureate-Empyrean/empyrean-nexus), `docs/security-model.md`). That was the right default for the first modules, which only needed Nexus.

Chronos must fetch external iCalendar subscriptions and Lyra must query an external metadata provider (TMDB). Both are explicit user-configured integrations, so they need outbound HTTPS from the module's server process. The options considered:

- **Module egress capability.** A new capability that the owner reviews at install/update. The broker attaches the module to a second, per-module, non-internal network. Small, enforceable at the container boundary, no new service.
- **Nexus-mediated fetch.** Nexus fetches URLs on a module's behalf against a per-module host allowlist. Tighter control over destinations, but it makes Nexus an HTTP proxy for arbitrary domain integrations, adds bodies, streaming and timeouts to the gateway, and puts every integration's traffic through the control plane.
- **No egress.** Defers the features, which the product brief requires now.

## Decision

- Module Protocol v1 gains the capability **`network.egress`** (Nexus 0.1.5). A manifest using it must exclude Nexus 0.1.4 and earlier, which reject it.
- When it is granted, the broker creates a per-module bridge network `<installation>-egress-<module-id>` **without** `internal`, labelled as owned by the installation, and connects only that module's container to it. The internal network (and its `nexus` alias) is unchanged. The Nexus gateway is never attached to an egress network. Disable and uninstall remove the egress network. Without the capability, the runtime is exactly as before.
- The capability means "this module may open outbound connections". It is **not a destination allowlist**. Docker cannot restrict destinations without a proxy, so the install review says so plainly.
- Each module that holds the capability is responsible for **SSRF protection** for user-supplied destinations: allowed schemes and ports, DNS resolution with rejection of loopback, private, link-local, CGNAT, multicast, reserved and unspecified addresses (IPv4 and IPv6, including mapped forms), pinning the connection to the vetted address, re-validating every redirect, timeouts and response-size limits, and never logging URLs or credentials.
- A module requests egress only for a declared integration it documents (Chronos: calendar subscriptions; Lyra: its metadata provider). Requesting it is visible in the update capability diff like any other capability.

## Consequences

- Owners see, at install, that Chronos and Lyra can reach the Internet. Installed still does not mean trusted: a malicious module with egress can exfiltrate whatever it holds, so egress widens the consequence of trusting a module. The security model must state this.
- The egress network does not isolate a module from the host's other reachable interfaces. In-module SSRF checks are a code-level control, not a host firewall. Strict deployments can add host firewall rules for egress bridges.
- A Nexus-mediated fetch with destination allowlists remains possible later. If it is introduced, `network.egress` can be narrowed or superseded by a new ADR.
- Affects: [principals-and-permissions](../concepts/principals-and-permissions.md), [module-contract](../concepts/module-contract.md), [Chronos](../modules/chronos.md), [Lyra](../modules/lyra.md), Nexus `docs/module-protocol.md` and `docs/security-model.md`.
