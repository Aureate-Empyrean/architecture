# Principles

Durable principles for the whole ecosystem. These are intentionally free of implementation detail. All are **Established** unless marked otherwise.

## User ownership and privacy

1. **User data belongs to the user.** The user can always see, export, correct and delete what the system holds.
2. **Self-hosting is first-class.** It is the primary deployment model, not a fallback.
3. **No mandatory cloud.** No feature of the core ecosystem may require a hosted service operated by the project.
4. **No data harvesting.** No telemetry or analytics leaves the installation without explicit, informed user opt-in.
5. **No artificial paywalls around core functionality.**
6. **Privacy and explicit user control are architectural requirements**, not features. They constrain design; they are not added afterwards.
7. **The system remembers what the user chooses to give it. It does not observe.** Any collection of data (device collectors, connectors, imports, recognition) must be opt-in, transparent, locally controlled and revocable. No covert monitoring. The ecosystem may hold extremely detailed information about the user's own life, communications, location, media and known people; that is intentional. It must not be designed for covert or non-consensual surveillance. Collection features must operate on the user's own devices and data, or information the user legitimately provides, with explicit user control, visibility and revocability.
8. **Personal, not adversarial.** Features operate on the user's own data and known people. Meridian is not an OSINT tool; Argus is not for identifying strangers.

## Openness and portability

9. **Open APIs and portable data.** Data must be exportable in documented, open formats. Leaving must be possible.
10. **Restore is as important as backup.** A backup that cannot be reliably restored is not a backup.
11. **Community participation is a design requirement.** Third-party modules must be able to participate without being hard-coded into Nexus or official modules.

## Structure

12. **Ownership follows domains.** A module owns its domain data. Nexus owns interoperability. See [architecture.md](architecture.md).
13. **No back doors between modules.** Modules never access each other's databases; they use documented APIs, events, shared primitives and references. A reference to a resource never grants access to its contents.
14. **Modules stay independently understandable** while participating in one coherent ecosystem.
15. **Nexus stays small.** It provides infrastructure and does not learn domain concepts.
16. **Do not force everything into a module.** Some capabilities belong in Nexus as services.
17. **References survive absence.** Data referring to something that is unavailable degrades to an unresolved reference; it is not silently destroyed.

## Technology posture

18. **Prefer boring, maintainable technology** over unnecessary infrastructure. Introduce a new moving part only when a concrete need justifies it.
19. **Reuse strong open-source work** (e.g. Immich for photos) where appropriate rather than rebuilding it.
20. **Use established formats and components** (e.g. tar, zstd, standard encryption) rather than inventing proprietary ones.
21. **Local-first where practical.** Modules where offline operation matters may keep authoritative usable state on the device; Nexus is not a mandatory runtime dependency for them. See [concepts/local-first-and-sync.md](concepts/local-first-and-sync.md).
22. **Do not build speculative machinery.** No graph databases, inference engines, or distributed identity systems until a concrete requirement demands them.
23. **Single-user first.** Multi-user may come later; do not make single-user harder to run in order to prepare for it.
24. **Localization is an ecosystem concern.** Locale controls presentation only; changing language never rewrites or duplicates user data. Use established i18n standards rather than inventing a translation syntax. See [concepts/localization.md](concepts/localization.md).
25. **Updates are inspectable before they are applied.** No update changes the installation without review and explicit approval, and new privileges are never silently granted. See [concepts/updates.md](concepts/updates.md).

26. **Expose workflows, not schemas.** Shared data primitives do not imply shared interaction models; a module's interface should follow the user's task, with progressive disclosure, rather than exposing its storage model as generic CRUD. See [modules/mnemosyne.md](modules/mnemosyne.md#interaction-model) for the first worked example.

## Documentation

27. Architecture lives in this repository, not only in conversations or code. Distinguish decided, planned and open clearly.
