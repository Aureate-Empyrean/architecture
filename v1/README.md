# AE V1 organization-wide task queue

[All open V1 tasks](https://github.com/issues?q=org%3AAureate-Empyrean%20is%3Aissue%20is%3Aopen%20label%3Aae%3Av1) · [Ready and unassigned](https://github.com/issues?q=org%3AAureate-Empyrean%20is%3Aissue%20is%3Aopen%20label%3Aae%3Av1%20label%3Astatus%3Aready%20no%3Aassignee) · [Shared agent standard](../AI_AGENT_INSTRUCTIONS.md)

GitHub Issues are canonical across independent application repositories. Architecture holds ecosystem/cross-repository/architectural tasks only. This page and local JSON are publication snapshots; current issue labels, assignees, dependencies, evidence and comments determine live state. No controller, leases, fencing or Actions orchestration is installed.

29 newly published issues replace the original 43 proposals; the pre-existing Nexus defect is also labelled into the same queue (30 total, one completed setup). Completed Chronos/Lyra/Argus work is preserved as candidate-review tasks, not assigned again for implementation. [All 162 original capability rows](CAPABILITY_CHECKLIST.csv) remain coverage evidence; legacy IDs map via [draft disposition](DRAFT_DISPOSITION.md). The [old parallel design](future-parallel-automation.md) is dormant.

## Planning and execution entrypoints

- [Workflow verification and exact documentation commits](WORKFLOW_VERIFICATION_2026-11-10.md).
- [Shared AI agent instructions](../AI_AGENT_INSTRUCTIONS.md): sole-selector execution, administration safety, approval boundaries and evidence rules.
- [Owner planning and agent procedures](OWNER_AGENT_WORKFLOW.md): conversational planning handoff, autonomous start, live progress and safe stop/handoff.
- [Three concise prompt templates](PROMPT_TEMPLATES.md): planning, implementation and review/integration; provider-agnostic.

Root AGENTS.md files point to the authenticated canonical standard from independently cloned repositories. No orchestration/controller/Actions system was added. Informal ideas are not permission to publish/execute Issues. No product task was started by this documentation update.

## Next three eligible tasks

- [WP00: Establish transport and release evidence without changing services](https://github.com/Aureate-Empyrean/architecture/issues/6) — Aureate-Empyrean/architecture; P0; ready and unassigned at activation. Recheck live state before assignment.
- [WP04a: Define complete, private recoverable sets](https://github.com/Aureate-Empyrean/architecture/issues/7) — Aureate-Empyrean/architecture; P0; ready and unassigned at activation. Recheck live state before assignment.
- [REVIEW-CHR: Review the completed Chronos 0.7.0 concurrency and Trash candidate](https://github.com/Aureate-Empyrean/chronos/issues/1) — Aureate-Empyrean/chronos; P0; ready and unassigned at activation. Recheck live state before assignment.

These are review/evidence tasks and do not authorize shared-environment operations. Candidate reviews need access to the existing local commits. Also ready: Lyra candidate review, Argus candidate review and historical build-hygiene recheck. Product coding tasks wait for their documented scope/base/handoff gates.

## Ownership and dependencies

Use `ae:v1`, `priority:p0..p4`, `status:*`, `module:*` and `kind:*` consistently. Do not claim assigned work; record agent/session in an explicit assignment comment. Assignment is a convention, not an atomic lock: concurrent autonomous claiming is not supported. Cross-repository issue links state the required milestone; no automatic dependency scheduler is running.

## Published inventory

| ID | Repository | Initial status | Issue |
|---|---|---|---|
| AEV1-SETUP | Aureate-Empyrean/architecture | completed setup | [Activate the simple V1 Issues backlog and shared agent standard](https://github.com/Aureate-Empyrean/architecture/issues/4) |
| SCOPE-01 | Aureate-Empyrean/architecture | blocked | [Confirm V1 inclusion and required acceptance gates](https://github.com/Aureate-Empyrean/architecture/issues/5) |
| WP00 | Aureate-Empyrean/architecture | ready | [Establish transport and release evidence without changing services](https://github.com/Aureate-Empyrean/architecture/issues/6) |
| WP04a | Aureate-Empyrean/architecture | ready | [Define complete, private recoverable sets](https://github.com/Aureate-Empyrean/architecture/issues/7) |
| REVIEW-CHR | Aureate-Empyrean/chronos | ready | [Review the completed Chronos 0.7.0 concurrency and Trash candidate](https://github.com/Aureate-Empyrean/chronos/issues/1) |
| REVIEW-LYR | Aureate-Empyrean/lyra | ready | [Review the completed Lyra privacy and concurrency candidate](https://github.com/Aureate-Empyrean/lyra/issues/1) |
| REVIEW-ARG | Aureate-Empyrean/argus | ready | [Review completed Argus gallery and restore-integrity candidate](https://github.com/Aureate-Empyrean/argus/issues/1) |
| WP13 | Aureate-Empyrean/architecture | blocked | [Reconcile final Meridian and Atlas handoff and residual V1 gaps](https://github.com/Aureate-Empyrean/architecture/issues/8) |
| NEX-CLEANUP | Aureate-Empyrean/empyrean-nexus | blocked | [Reconcile Nexus cleanup with gallery navigation successor](https://github.com/Aureate-Empyrean/empyrean-nexus/issues/4) |
| WP04b | Aureate-Empyrean/architecture | blocked | [Verify isolated seven-component recovery and revocation](https://github.com/Aureate-Empyrean/architecture/issues/9) |
| WP05 | Aureate-Empyrean/argus | blocked | [Honest and recoverable Argus import status](https://github.com/Aureate-Empyrean/argus/issues/2) |
| WP06a | Aureate-Empyrean/mnemosyne | blocked | [Accent-tolerant Mnemosyne workspace search](https://github.com/Aureate-Empyrean/mnemosyne/issues/3) |
| WP06b | Aureate-Empyrean/chronos | blocked | [Consistent Chronos local search matching](https://github.com/Aureate-Empyrean/chronos/issues/2) |
| WP06c | Aureate-Empyrean/lyra | blocked | [Lyra local search matching after privacy separation](https://github.com/Aureate-Empyrean/lyra/issues/2) |
| WP07 | Aureate-Empyrean/argus | blocked | [Bounded text discovery for the existing Argus photo grid](https://github.com/Aureate-Empyrean/argus/issues/3) |
| WP08 | Aureate-Empyrean/chronos | blocked | [Accessible Chronos month date structure](https://github.com/Aureate-Empyrean/chronos/issues/3) |
| WP09a | Aureate-Empyrean/mnemosyne | blocked | [Truthful, portable Mnemosyne workspace export](https://github.com/Aureate-Empyrean/mnemosyne/issues/4) |
| WP09b | Aureate-Empyrean/chronos | blocked | [Native Chronos calendar ICS export](https://github.com/Aureate-Empyrean/chronos/issues/4) |
| WP09c | Aureate-Empyrean/lyra | blocked | [Lyra personal-library data export](https://github.com/Aureate-Empyrean/lyra/issues/3) |
| WP09d | Aureate-Empyrean/argus | blocked | [Argus selected-original and metadata export contract](https://github.com/Aureate-Empyrean/argus/issues/4) |
| WP10a | Aureate-Empyrean/lyra | blocked | [Exact Lyra season and episode destinations, after navigation clearance](https://github.com/Aureate-Empyrean/lyra/issues/4) |
| WP10b | Aureate-Empyrean/architecture | blocked | [Additive lifecycle and disclosure contract design](https://github.com/Aureate-Empyrean/architecture/issues/10) |
| WP11 | Aureate-Empyrean/janus | blocked | [Independent Janus real-secret readiness gates](https://github.com/Aureate-Empyrean/janus/issues/8) |
| WP14 | Aureate-Empyrean/architecture | blocked | [Decide Enceladus V1 scope before touching its starting application](https://github.com/Aureate-Empyrean/architecture/issues/11) |
| INT-CAND | Aureate-Empyrean/architecture | blocked | [Prepare an exact combined integration proposal; integrate only after owner approval](https://github.com/Aureate-Empyrean/architecture/issues/12) |
| WP12 | Aureate-Empyrean/architecture | blocked | [Complete candidate-specific V1 acceptance and 162-row coverage disposition](https://github.com/Aureate-Empyrean/architecture/issues/13) |
| REL-TEST | Aureate-Empyrean/architecture | blocked | [Roll out and verify approved test candidate](https://github.com/Aureate-Empyrean/architecture/issues/14) |
| REL-PROD | Aureate-Empyrean/architecture | blocked | [Owner-approved V1 production release and acceptance](https://github.com/Aureate-Empyrean/architecture/issues/15) |
| HYG-01 | Aureate-Empyrean/architecture | ready | [Recheck recorded Nexus CI and clean-build hygiene gaps](https://github.com/Aureate-Empyrean/architecture/issues/16) |

## Confirmed and blocked

GitHub CLI access and write permissions were confirmed; the separate connector still returns 404 for AE. All organization issues/PRs/branches were reconciled before creation. One pre-existing open defect remains canonical: [Nexus backup SemVer/PEP 440 mismatch](https://github.com/Aureate-Empyrean/empyrean-nexus/issues/1). It is linked from recovery work and labelled into the same queue; no duplicate was created. Its V1 triage explicitly blocks implementation pending current-source reproduction and a reviewed range-policy decision. That older issue includes historical statements requiring recheck against current source.

Remote main ancestry and local candidate SHAs are recorded in [candidate reconciliation](CANDIDATE_RECONCILIATION.json). Chronos, Lyra, Argus, Meridian/Atlas and Nexus candidates are not in their current remote main branches. Local application branches/tags/files were preserved. No application candidate was pushed, merged or deployed by backlog activation. Tests in the prior reports were not rerun.

Blocked: V1 scope decisions; Meridian/Atlas final handoff; Nexus cleanup versus auth-containing successor allocation; final combined validation/recovery; protected navigation work; Janus independent security/delivery review; Enceladus scope/access; integration and environment approvals. Acceptance gates must be expanded when additional capabilities are included, and dependency cycles checked. Unknown scope is not an accepted deferral.

The current user authorized publishing the backlog and shared documentation. Application integration/deployment still need separate exact-candidate approval. No GitHub Project, milestone fan-out or additional orchestration service is required.
