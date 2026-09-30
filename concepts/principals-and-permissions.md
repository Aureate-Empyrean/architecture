# Principals and Permissions

What "permission-aware" means in the current single-user architecture. Multi-user authorization is not designed here.

## Principals

Parties that access resources or act in the ecosystem:

- **The human user**, through an authenticated user session.
- **Module code**: each installed module, official or not.
- **Devices**: phones, computers and clients connecting to Nexus.
- **Collectors and connectors**: device-side ingestion and service integrations ([collectors](collectors.md)).
- **Third-party/community modules** (future): module code from outside the official project.
- **Additional users** (future; multi-user is Open).

## Established decisions

- **One human owner does not mean every component sees everything.** Permissions and capabilities constrain software components and integrations, not only future users.
- **Least privilege.** A module, device, collector or connector receives only the capabilities and data its declared function requires ([module-contract](module-contract.md)).
- **Holding a reference is not access** ([cross-module-references](cross-module-references.md)). Resolving and discovering references is authorized against the calling principal.
- **Installed is not trusted.** A community/third-party module is not trusted merely because it is installed. Architectural convention ("modules never access each other's databases") is not a security boundary by itself.
- Long-term intent for community modules: least privilege, declared capabilities, no direct access to another module's database or storage, interoperability only through approved Nexus/module interfaces, and network/storage isolation appropriate to the deployment model.
- **Concrete enforcement is required before untrusted third-party modules are treated as safely isolated.** Until that security architecture exists, installing a community module means trusting its code.
- **Mnemosyne Workspaces are not principals and not access-control boundaries.** They are organizational ([mnemosyne](../modules/mnemosyne.md#workspaces)). Cross-module interoperability may carry Workspace context for presentation and filtering, but Workspace membership is not an authorization boundary unless a future decision says so.
- Derived data held by one module about another module's resources is subject to the same capability rules as the source ([interoperability](interoperability.md#derived-data-and-projection)).

## Open questions

- Capability vocabulary, granularity and grant/revocation flow.
- Enforcement mechanisms: network isolation, per-module storage credentials, sandboxing, process isolation for the Docker Compose deployment.
- Device authentication and authorization.
- Multi-user authorization and how it relates to module capabilities.
- Verified-module review process ([architecture](../architecture.md#module-trust-categories)).
