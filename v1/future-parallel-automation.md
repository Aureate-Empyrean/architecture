# Dormant future design — not active

The user explicitly deferred queue-state controllers, leases, fencing and Actions orchestration. This archived baseline is retained for future review only. AI_AGENT_INSTRUCTIONS.md and live Issues define the current simple workflow. Do not implement or activate this design without a new explicit request.

# Reusable setup and implementation plan

## Target layout and ownership

Use `Aureate-Empyrean/architecture` as the central issue host, subject to access verification. Implementation stays in each module repository. Use stable `AEV1-*`, existing `WP*`, and `FND-*` IDs; an issue may affect several repositories but should split implementation by owning repository when feasible. One canonical issue per deliverable. Link existing issues instead of recreating them. An optional GitHub Project displays labels and links; it does not own state or claims.

Issues own task description, scope, priority, acceptance and dependency definitions. A small dedicated `queue-state` branch in the same repository owns machine coordination metadata only: issue IDs, accepted metadata digest, workflow state/generation, leases, resource locks, idempotency receipts and evidence references. It contains no duplicate product descriptions, code, user data or secrets. Issue labels, comments, assignees and Project columns are reconciled views. The branch is required because ordinary issue label/comment/assignee updates are not an atomic claim protocol.

The controller reads reviewed issue metadata, validates it, accepts a digest/revision into the ledger, and mirrors transitions back to Issues. Editing scope/dependencies invalidates availability until revalidated; it cannot silently alter a live claim. Reject missing dependencies, cycles and unknown task IDs. A human marking an issue closed is not completion evidence; reconcile or flag drift. Queue-owned fields should be changed through the controller.

## State and evidence contract

| State | Entry condition / actor | Exit and dependency meaning |
|---|---|---|
| Draft | Imported proposal or unclear scope | No claim until bounded acceptance, dependencies and ownership are reviewed. |
| Available | Accepted metadata, dependencies satisfied, execution authorized, no blocker | Controller may grant one claim. Eligibility is recomputed transactionally. |
| Claimed | Atomic lease grant | Agent works only in isolated checkout; heartbeat/release/submit requires current token. |
| Implemented | Accepted submission names exact commits, changed scope and test evidence | Does not imply validation; implementation lease ends, review hold remains. |
| Validating | Validator holds a separate lease for the submitted candidate digest | Evidence must match candidate and required matrix. |
| Validated | Required checks pass for exact candidate; known limits explicit | Waiting for owner integration approval. Approval alone is not integration. |
| IntegrationReady | Owner approved exact candidate/base/target | Integrator must claim target-repository locks and recheck base/evidence. |
| Integrated | Intended changes present in designated integration branch; commit recorded | Requires owner-authorized integration, actual resulting SHA and relevant validation. |
| Deployed | Separately approved environment, image digest, schema and deployment/health evidence | Test rollout and production are separate environment records; do not infer one from the other. |
| Blocked | Explicit reason, evidence, owner and unblock condition | Not claimable; preserve last completed milestone as a separate field. |

Validation failure returns to Claimed only under a newly acquired implementation lease, or Blocked when external evidence/decision is required. Candidate changes invalidate validation and approvals. A changed integration result requires revalidation. A closed-as-duplicate/deferred issue is a terminal disposition, not successful completion. Defer only with an accepted scope decision. Reopened completed work creates a new generation and invalidates stale claims/results; preserve history.

Default issue closure: close after Integrated for code/docs tasks; keep release/deployment tasks open until their own environment-specific DoD. Record required terminal state per task. A V1 release is an aggregate gate, never an inference from the number of closed issues.

Suggested labels: `ae:v1`, `state:<state>`, `priority:p0..p4`, `area:<module>`, `kind:implementation|validation|integration|release|scope|foundation`, `evidence:local|recorded|unknown`, `scope:required|proposed|conditional|deferred`. Milestone: AE V1. Apply one state and priority; views can lag, so claim via controller only.

## Concurrency-safe protocol

Implement one deterministic controller library plus a CLI and a GitHub Actions adapter. No LLM runs in Actions. Durable requests are typed command comments on canonical issues, or workflow-dispatch requests with stable request IDs. CLI can post a request and await its receipt; Actions can process comment events. A reconciliation run scans unacknowledged requests so cancellation, event delivery duplication and lost responses do not lose work. The proposed interface below is a specification, not a command installed by this package.

`queue next --capabilities <set>` reads candidates; `queue claim --next --session <uuid> --request-id <uuid>` requests a grant. `queue heartbeat`, `queue submit`, `queue validate`, `queue release` require task ID, lease ID and fencing epoch. Owner-only operations accept scope, change priority/dependencies, approve integration, approve deployment and recover an expired claim. Return typed JSON receipts and human-readable issue comments.

### Atomic grant

1. Controller authenticates caller using GitHub event identity/permission, not a claimed agent name. Separate session UUIDs distinguish two agents using the same owner GitHub login. A session ID is attribution, not an authorization credential.
2. Fetch exact remote `queue-state` head H into an isolated controller checkout. Initialize this branch once through an explicitly reviewed setup; never touch release refs.
3. Read reviewed issue definitions and accepted metadata digests. Confirm task generation, execution authorization, required milestone dependencies, resource locks, active lease/hold and capability requirements. Any mismatch fails closed. Issue-definition edits become effective only when accepted into this ledger transaction; an edit request is not an immediate revocation.
4. Pick highest-priority eligible task, then oldest eligible timestamp, then stable ID. Never auto-pick a blocked task. Record a random lease ID, increasing per-task epoch, claimant/session, phase, expiry, candidate/base SHA and resource set. Preserve the epoch across expiry/release/reopen; never recycle it.
5. Create commit C with H as its sole parent and publish ONLY `refs/heads/queue-state` using an explicit expected-H lease. Proposed Git operation: `git push --force-with-lease=refs/heads/queue-state:<H> origin <C>:refs/heads/queue-state`. Do not use shorthand lease or force on application branches. C must always descend directly from H; no history rewrite is intended.
6. Two contenders on H cannot both update that ref. The loser fetches new state, recomputes eligibility and retries with bounded backoff. On repeated contention return retryable failure, never local success. If the response is lost, query the stable request ID before retrying. The authoritative grant receipt is the committed ledger entry, not the subsequent issue comment.
7. Mirror labels/claim comments/optional assignee after commit. A crash between commit and mirror cannot create a second owner. Reconciler repairs the mirror idempotently. Store pending mirror operations in the ledger, acknowledging them with another CAS transaction.

This design uses Git's explicit expected-value lease behavior; see [Git push documentation](https://git-scm.com/docs/git-push). It still needs implementation and remote integration tests; this document is not a claim that an installed system is race-safe.

### Expiry and fencing

Proposed initial policy: lease 120 minutes; heartbeat every 20 minutes while working; expiry evaluated by controller runner UTC. All checks use one controller clock per transaction with a small documented skew allowance. If clock integrity is uncertain, stop reclaim rather than grant conflicting ownership. Heartbeat cannot revive an expired/revoked epoch. Check lease before writing, before commit/PR submission and before every shared transition. Local work after expiry can be preserved in its isolated checkout, but cannot be submitted under the stale epoch.

Expired leases enter Blocked/expired-review, retaining resource holds. Do not auto-reassign overlapping work just because a laptop missed a heartbeat. The next agent may inspect/recover the checkpoint under a recovery lease; it must locate existing work/PRs, record disposition and then explicitly supersede the old epoch before a fresh implementation grant. This exceptional recovery is deterministic where the old result is durable; ask the owner only when work cannot be located or disposition is ambiguous. Routine task selection needs no manual assignment.

All submit/heartbeat/release/validation/integration operations compare exact task generation, lease ID, epoch, phase, holder and candidate digest in the SAME ledger CAS transaction. An expired agent cannot release a replacement's lock or publish an accepted result. This fences queue transitions; it cannot physically prevent a privileged user bypassing the protocol or editing another checkout. Agents therefore use isolated branches and no direct merge/deploy privileges through the queue.

### Resource and dependency rules

Start conservatively with an exclusive implementation/review hold per affected repository, acquired atomically with the task. Multi-repository tasks acquire the complete resource set in one ledger update; no partial acquisition/deadlock. Tasks explicitly proven independent can later use reviewed narrower resources (for example `chronos:query` versus `chronos:month-ui`), with global namespace resources for shared SDK/schema/contracts. Integration always uses an exclusive target-repository lock. Existing unqueued/Claude work is registered as an external hold, released only on handoff.

Keep the resource hold through Implemented/Validated until Integrated or an explicit safe handoff/rebase disposition. This reduces parallelism but prevents silent overlap with unmerged work. Unrelated module work continues. Validators can use isolated disposable candidates under separate validation leases without taking implementation ownership.

Dependencies are typed edges: `requires_implemented`, `requires_validated`, `requires_integrated`, `requires_decision`. Default for code built on earlier work is Integrated. A reviewed stacked candidate may use Implemented plus exact ancestor SHA; it remains a combined integration chain. Priority is not a dependency. Soft preferences do not block claims. Closing an issue as not planned never satisfies an implementation prerequisite. Superseded work must name the replacement and coverage evidence.

WP02a already contains WP03 despite the old blueprint's preferred opposite order. WP02b already contains WP01. WP04c contains Argus gallery. Validate/integrate these chains as actual ancestry, retaining each deliverable's traceability. Do not cherry-pick ancestors twice. Reconcile Nexus `1e1fa31` with `1b40d96`; neither is assumed to contain the other.

## Delivery sequence

1. FND-01: restore AE read access; inventory existing open/closed issues, PRs, workflow policy, default branch, repository plan/permissions and current remote heads. Match by stable task ID and scope; paginate all results. No automatic issue creation.
2. FND-02: review scope/priority/state/metadata contract and inventory delta; freeze proposed import manifest with checksum. Preserve dirty architecture changes and private screenshots. Decide where reviewed architecture decisions are committed without copying whole checkouts.
3. FND-03: implement controller, ledger CAS and command parser in an isolated architecture branch. Test locally against a disposable bare Git remote and mocked GitHub API. Add authorization/idempotency/fencing/resource tests before enabling claims.
4. FND-04: add trusted Actions adapter and small CLI, reconciler, issue template and proposed AGENTS instructions. Use `contents:write` only for ledger/controller job; `issues:write` for mirrors; default read permissions elsewhere. No deployment credentials, no application merge permissions. Current cross-repository PR verification may need narrowly scoped read access; establish that during FND-01 rather than inventing an existing token.
5. FND-05: after explicit publication request, idempotently create/update reviewed issues/labels/milestone and stable-ID→issue-number mapping. Dry-run diff first, deduplicate against all issue states, keep dependencies by stable ID until numbers resolve. A rerun after partial failure must not duplicate issues or overwrite human decisions. Projects setup is optional.
6. FND-06: owner-authorized sandbox exercise with two distinct sessions, races/crashes/expiry and one synthetic task lifecycle. Only then enable the production backlog queue. This is queue activation, not application deployment.
7. FND-07: reconcile completed branches and newer module heads, attach commit-specific validation evidence, and produce one integration proposal at a time. Owner approves exact target/base/candidate. Actual integration and test/production rollout remain separate tasks/approvals.
8. FND-08: verify coverage and final V1 acceptance; no release claim while required scope or safety gates are unresolved.

## Controller acceptance tests

- At least 20 simultaneous requests for one task: exactly one active lease; losers have explicit receipts. Multiple eligible tasks selected in deterministic priority/tie order; no duplicate task/resource owners.
- Two tasks touching the same resource; multi-repository lock acquisition; held external work; release/handoff; integration lock collision. Unrelated repositories remain eligible.
- Duplicate event/request, lost grant response, controller crash before/after CAS/before mirror, mirror API failure, canceled workflow and retry: replay returns same result and reconciliation restores Issues without regranting.
- Heartbeat versus reclaim race, stale submit/release/validation after a new epoch, reopen with old token, invalid session, forged claimant, revoked caller and wrong phase: rejected without altering current ownership.
- Missing/cyclic dependency, metadata edit mid-claim, closed-as-duplicate prerequisite, unreviewed scope, failed validation, moved PR head/base and changed candidate invalidate eligibility/approval correctly.
- Controller never executes issue text, branch names or PR code. Reject unsupported commands/oversized payloads. Run workflow-injection fixtures. Trusted code comes only from reviewed default branch, actions pinned to reviewed commit SHAs. See [GitHub script injection guidance](https://docs.github.com/en/actions/concepts/security/script-injections).
- Do not rely on one Actions concurrency group to preserve requests: default pending runs can be replaced; `queue:max` supports up to 100 pending runs and ordering is based on waiting time. Durable requests plus CAS/reconciliation remain necessary. See [current GitHub concurrency documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/control-workflow-concurrency).
- Permission denied/rate limit/unavailable GitHub/missing ledger branch/clock uncertainty: no false grant, no fallback to labels. Show a useful retry reason. Controller health failures notify once; do not require routine owner triage.
- Validation evidence records actual SHA, command, platform, pass/fail/skip, artifacts and limitations. Owner approval for exact SHAs cannot be replayed onto a new head. No auto-close from PR keywords before integration policy is met.

## Operations and rollback

No always-on server is required. Comment events trigger work; a modest scheduled/manual reconciliation catches missed requests, and CLI heartbeat runs only while an agent is active. Actions minutes are still consumed, even with zero extra LLM quota; measure them during pilot. Avoid polling every few seconds or full-suite reruns per heartbeat.

Keep append-only ledger commits, versioned schema, controller release SHA and downloadable evidence. Recovering coordination state must increment a global recovery generation and invalidate all old leases; do not restore an old state file and resurrect expired tokens. Disable new grants before maintenance; preserve existing checkpoints and pending receipts. If controller is disabled, backlog remains readable but parallel claiming stops. Recovery uses a reviewed newer ledger commit, never resets release branches. Owner approval and access controls are the authority boundary; branch protection availability must be verified, not assumed from historical plan notes.
