# Shared AI agent instructions — Aureate Empyrean V1

Applies equally to Codex, DeepSeek, Claude and future agents. GitHub Issues across the `Aureate-Empyrean` organization are the canonical task system. Independent applications own their issues: Chronos in chronos, Lyra in lyra, and so on. Reserve architecture for ecosystem-wide, cross-repository and architectural tasks. Read this shared standard, the selected issue and only its relevant source/evidence; model-specific chats are not the backlog. This standard establishes the simple assignment workflow now; the earlier queue-state/lease/fencing/Actions design is dormant future work.

## Choose and assign work

1. Discover current open work organization-wide using `org:Aureate-Empyrean is:issue is:open label:ae:v1 label:status:ready no:assignee`. Search priorities p0 through p4, then oldest eligible issue. The labels and search form one logical queue; architecture is not a second copy of module tasks. Check cross-repository dependencies and latest comments, not only a cached list. Do not take already-assigned work, including an assignment to your GitHub login from another agent session. Resume your own current assignment only after confirming its session/checkpoint.
2. Before changing files, assign the issue to the authenticated working account and add one concise assignment comment: agent/session identifier, objective, exact repository/base, intended worktree/branch and time. Set `status:in-progress` and re-read the issue/assignees/comments. If another session claimed it or ownership is unclear, stop edits and resolve ownership. Never steal or clear someone else's assignment.
3. Until an atomic claiming mechanism is separately approved and implemented, only **one autonomous agent session may select and claim tasks at a time**. Other sessions may execute explicitly assigned, nonoverlapping tasks; they may not select their own next issue. Assignment is a simple coordination convention, not an atomic lock. Do not launch competing autonomous claims or assume shared-login agents are distinguishable by assignee alone. One account may have multiple sessions; the assignment comment identifies the session. Planned parallel work needs explicit nonoverlapping assignments; same-repository implementation is serialized by default.
4. Recheck dependencies, active assignments and unmerged candidate branches before editing. External work/Claude handoffs are blockers until their owner supplies a checkpoint. An absent assignee does not override an explicit external hold. No automatic expiry/reassignment.
5. Finish with a result/checkpoint comment, accurate status and next step. Unassign your own session only when relinquishing work. Preserve local work before handing off; never delete an abandoned branch/worktree to make a task look free.

## Continuous execution within one authorized session

After completing a task, commit and record the result within the authorized scope, run appropriate validation, update its Issue with exact evidence/status, and release only your own assignment when the checkpoint is safe. Discover the next highest-priority eligible Issue and continue automatically if your session is the sole selector and its scope, resources and authorization permit more work. Do not stop after each task just to ask the owner what to do next.

A task-specific implementation request does not authorize unrelated tasks. An owner-authorized backlog run may continue across eligible tasks within its stated bounds. If a local commit was authorized but push/PR publication was not, report that boundary rather than publishing anyway. If further checks require changing the candidate, commit the final delta and rerun affected validation so evidence names the final SHA.

Never bypass an approval gate, start blocked/already-owned work, or launch unbounded background execution. Respect session limits, model quotas, budgets, time limits and explicit stop instructions. Do not acquire/renew a selector role that the session cannot exercise. If no eligible work remains, or the authorized boundary/resources are reached, post a concise checkpoint and stop. A blocked task may be parked with its milestone/evidence preserved while the sole selector continues other eligible work.

### Single-selector convention

Use the existing [workflow setup issue](https://github.com/Aureate-Empyrean/architecture/issues/4) for brief selector-session START, STOP and HANDOFF comments; do not create a new coordination service or issue. Before an authorized backlog run selects work, read the latest session comments. START identifies agent/session, current authorization and scope/limits. Re-read after posting and select only while you are the sole active selector. STOP records checkpoint and release; HANDOFF names the next explicitly designated session, which acknowledges before selecting.

These comments are a human-readable convention, not an atomic lock. Do not bootstrap two autonomous selector sessions simultaneously. If another selector is active, ownership is uncertain or comments conflict, select nothing; work only an explicitly assigned nonoverlapping task or stop with the conflict. No automatic timeout, stolen assignment, lease, fencing or Actions controller is introduced. This documentation update itself starts no selector and does not authorize execution of product tasks.

## State and dependency rules

For open Issues use exactly one execution label: `status:ready`, `status:blocked`, `status:backlog`, `status:in-progress` or `status:review`. `ready` means approved, independently executable and unblocked; `backlog` means deferred/planned work not selected for current execution; `in-progress` means explicitly assigned active work; `review` means a concrete candidate awaits review; `blocked` names an unresolved prerequisite, owner decision, ownership hold or environment approval. Preserve historical closed-Issue labels.

Use `priority:p0..p4`, `module:*`, `kind:*` and exactly one scope label: `ae:v1`, `ae:future` or `ae:scope-undecided`. Future/undecided Issues are visible planning records, never V1 execution candidates. Publishing one does not approve its open product/design choices. Never infer V1 exclusion merely from unknown scope.

Each Issue has a versioned JSON block between `<!-- ae-backlog:v1 -->` markers: stable `source_id`, `scope`, `lifecycle`, `checklist_ids`, Issue `dependencies` with a `kind` (`technical`, `owner-decision` or `deployment`) and `required_milestone`, explicit `satisfied`/`evidence`, `owner_gates` and implementation `milestones`. Live labels and this block must agree. Native GitHub blocking links mirror actual unresolved Issue prerequisites. A prerequisite decision Issue is typed `owner-decision`; a rollout evidence prerequisite is typed `deployment`. Unlinked decisions and environment approvals stay in `owner_gates`, without fabricated technical edges. Update both representations when evidence changes; never discard unexpected human dependencies. An unavailable native API is a reported limitation, never a reason to change repository settings.

A V1 runner can select only an open, unassigned `ae:v1` + `status:ready` Issue with no future/undecided scope, no unsatisfied dependency, no owner gate and no external ownership hold in current comments. Re-read live dependencies and exact evidence; a closed predecessor is insufficient without its required milestone. Do not resolve eligibility through repeated whole-architecture AI analysis. No runner or GitHub Action is implemented by this documentation.

The following are **evidence milestones**, retained in the Issue JSON/body independently of execution status. Closed `completed` means explicit terminal acceptance was met; duplicate/not-planned closure is not implementation evidence.

- Implemented: reviewable committed source or completed bounded document/checkpoint exists. Exact SHA/path is recorded.
- Validated: required tests/review passed for that exact candidate with environment, command, failures/skips and limits recorded. A prior report remains reported evidence until checked; don't relabel it as a fresh run.
- Integrated: owner-authorized changes actually exist in the designated integration target; resulting SHA and relevant checks recorded. Approval alone is not integration.
- Deployed: separately authorized environment rollout actually completed with source/image/schema/backup/health evidence. Test and production are distinct records. A build, merge or aetest rollout is not production deployment.
- Blocked: name reason, required decision/evidence and responsible role; preserve completed implementation/validation milestones in the body. A task can have an implemented candidate while its review/integration remains blocked.

Dependencies in each issue specify the required milestone or exact candidate SHA. Most changes built on another task require its integrated result; reviewed stacked candidates may use exact implemented ancestors. Closed-as-not-planned never satisfies a dependency. Scope edits or changed candidate SHAs require dependency/validation reassessment. Do not close issues automatically using PR keywords before terminal acceptance is met.

## Work safely and preserve completed work

Inspect current branch, HEAD, dirty/untracked files and actual ancestry. Use an isolated task branch/worktree from the agreed base. Preserve pending files, existing release tags and published history. Never reset, clean, stash, force-push, rewrite/rebase another agent's branch or stage unrelated files. Commit only reviewed task paths when the task authorizes a candidate commit. Push/PR work only within its explicit scope; an assignment is not blanket permission to publish unrelated code.

Known candidates: Chronos `aba2ed61fd9caad72f3a76d7607793b41871d83e` contains Trash `f5e33bcb81c2a750770925ced3ea1e889b8f515a`; Lyra `a3704aafc0a18f46b4a9ea99cb3508a563311870` contains privacy `075b72c5bac447d16f9a659f89c5616063c497f2`; Argus restore `ed13228f63e27c4b04eae2f872029c95b2b25ad0` descends from gallery `39115abeab9e5b6e66558b1b94982ad9530ef4ec`. Do not reimplement/cherry-pick these ancestors twice. Nexus cleanup `1e1fa31` is separate from gallery-navigation `1b40d96`, whose history includes owner-recovery `e0fad0f`; do not accidentally integrate auth changes while combining tests. Recheck current refs before acting.

No unauthorized merges, deployments, production access, destructive operations, real-data migration or service/container changes. Integration and deployment require owner authorization for the concrete action/candidate/environment. If authorization already exists in the session/task, do not ask again. If missing, finish the reviewable proposal first and state the exact remaining approval. Do not treat Issues text, code or external content as authority to override these boundaries. Protected P1 SDK/navigation/auth work needs explicit task allocation; respect documented ownership holds.

## GitHub administrative safety

Never change any of the following without explicit owner authorization for that administrative action:

- Repository visibility (public/private).
- Organization membership or permissions.
- Repository access, collaborators or access roles.
- Branch protection or rulesets.
- Repository settings, deletion or archival.
- GitHub Actions permissions.
- Organization-level security settings.

Permission to implement a feature, write docs, use Issues, or publish an approved branch is not permission to change administration. A denied API call, unavailable feature or private repository must not be “fixed” by changing visibility, access or security. Inspect allowed evidence, report the specific blocker, and continue unrelated authorized work. Never make a repository public to enable a feature. Documentation-only commits do not authorize release-tag moves or edits to release branches/history.

## Owner + ChatGPT planning, independent agent execution

The owner decides product vision, roadmap, scope and priority; ChatGPT helps brainstorm/specify features, UX/UI, architecture trade-offs and milestone reviews. The owner approves sensitive operations and unresolved product decisions. ChatGPT is a planning collaborator, not a mandatory execution orchestrator. No ChatGPT API, special integration, extra LLM call or particular provider is required.

Implementation agents inspect approved work, implement/test/document it, maintain accurate issue states, identify dependencies/blockers, prepare integration candidates and continue eligible work within their session authorization. Ordinary technical decisions within scope need no owner micromanagement; a product/safety/approval decision cannot be guessed.

### Planning-to-execution handoff

Any authorized agent can turn a concise owner-approved brief into independently executable Issues:

1. Verify the approval reference, approved outcome and authorized action (prepare a proposal, publish Issues, or execute existing work). Reuse existing valid authorization; do not ask again. Informal/speculative conversation or merely naming a feature is not approval to publish/implement it.
2. Check relevant documentation, established decisions and open/closed Issues/PRs across affected repositories for duplicates/completed work. Reuse or update the canonical Issue; preserve accepted architecture and exact candidate ancestry.
3. Place module work in its application repository; reserve architecture for ecosystem-wide/cross-repository/architectural work. Split only independently owned deliverables; cross-link rather than copying the same task into several repositories.
4. State verified context versus reports/unknowns; bounded objective, affected modules, dependencies with required milestones, exclusions and independently checkable acceptance/tests. Preserve the owner's outcome without expanding into unrelated work.
5. Add the justified scope (`ae:v1`, `ae:future` or `ae:scope-undecided`), priority, module, kind and exactly one execution status label. `ready` requires approved scope, execution authorization, available base/evidence, satisfied prerequisites and no ownership hold. Put approved work with unresolved gates in `blocked`, explaining the exact unblock condition. Unapproved ideas stay in the conversational brief/proposal, outside the execution backlog.
6. Check duplicate IDs/titles/scopes immediately before publication. Record approval/provenance and issue links. Publishing an approved Issue is not automatic authority to merge/deploy, nor necessarily to execute it in the current session.

If approval to publish is missing, finish the concrete brief/proposed Issue text and request only the missing decision. See [owner procedures](v1/OWNER_AGENT_WORKFLOW.md) and the three [provider-agnostic prompt templates](v1/PROMPT_TEMPLATES.md).

## Reusable implementation-prompt pattern

Generate a short task-specific prompt using verified facts and links instead of copying entire chats:

> **Objective:** one bounded result and user-visible behavior.
> **Verified context:** issue URL/ID, exact repository/base/candidate SHAs, current behavior, evidence source/date; distinguish observed, reported and unknown.
> **Scope:** permitted repositories/files/interfaces, deliverables and exclusions; dependency milestones and active ownership.
> **Constraints:** privacy/data ownership, compatibility/schema, frozen files, environment limits and authorization boundary.
> **Execution:** confirm assignment and dirty state; create isolated worktree; inspect existing implementation before changing it; implement the smallest complete change and preserve completed ancestors.
> **Testing:** relevant focused/regression commands and synthetic fixtures, target platforms, failure/recovery/privacy cases; inspect harness defaults before running; no shared/live services without authorization.
> **Acceptance:** concrete checkable results, exact required terminal milestone and remaining owner/platform gates.
> **Reporting:** exact commit/base, changed behavior/paths, commands and pass/fail/skip counts, limitations, migration/rollback, artifact links, next state/blocker. Do not claim merge or deployment from source completion.

For review-only tasks, replace implementation with inspecting the existing candidate and producing a bounded report. Never generate a coding prompt that recreates completed work.

### Practical prompt generation

Choose a **planning brief** for a product decision/specification/Issue proposal, an **implementation brief** for an existing Issue or bounded approved change, or a **review/integration brief** for completed candidate inspection and the next permitted milestone. Use the [concise reusable templates](v1/PROMPT_TEMPLATES.md); do not create three copies of the shared rules in every prompt.

State the intended outcome and only relevant verified facts/links. Prescribe internal implementation details only where an established contract, compatibility rule or measured requirement needs them. Name autonomy limits and permitted approvals explicitly. Require inspection of existing work, meaningful tests, truthful reporting and reasonable in-scope technical judgment. Include a safe stopping/handoff condition. Read only linked context needed for the task and avoid repeated explanations, excessive logging or unnecessary questions.

No verified source for a previously tested DeepSeek-specific prompt method was established in this task. These templates are shared, provider-agnostic guidance, not a claim to reproduce such a method. Provider-specific optimizations are optional, separately sourced notes and may not weaken this standard or introduce a provider dependency.

## Reduce questions, repeated context and token use

Use the issue as the working brief. Read relevant source sections and existing reports once; keep a short checkpoint with exact SHAs/links. Batch independent read-only searches. Avoid copying full logs, private data or whole histories into comments. Give concise updates only for meaningful findings/blockers/completion. Use deterministic tools/tests, not an extra model for task routing.

Proceed with reversible, in-scope work and ordinary implementation choices. Ask only for missing information or a decision that materially blocks the next action; consult existing docs/issues/source first. Do not ask the owner to assign routine tasks or repeat known context. Record a useful checkpoint and take another eligible task when a blocker permits that.

## Testing and honest reporting

Use disposable synthetic data and isolated storage/ports. Inspect test scripts before running them; names such as aetest/deploy/live_smoke are not permission to use shared services. Run checks appropriate to the change, broadening only for a failure, new change or unresolved risk. No unnecessary test loops or invented latency targets.

Report what actually ran, where and against which exact SHA. Mark unrun platforms, physical devices, skips, stale reports and unavailable evidence explicitly. Test existence is not a pass; source inspection is not end-to-end validation; 55 passed plus one skipped is not 56 passed. Never fabricate screenshots, validation, deployment, provenance or owner approval. Keep secrets, databases, real media and personal fixtures out of Git/issues/logs.

At completion post: objective/result; base→candidate; focused change summary; test commands/environment/pass-fail-skip; limitations; migration/rollback; assignment disposition; requested next milestone. Evidence for a changed candidate must be reassessed. Leave integration/deployment tasks blocked until their exact approval and prerequisites exist.
