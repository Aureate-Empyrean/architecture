# Provider-agnostic AE prompt templates

Use one template, replace brackets with actual facts and link the [canonical standard](../AI_AGENT_INSTRUCTIONS.md). Unknowns remain unknown. Do not invent approval, context or validation. Prompts state outcomes; agents choose ordinary technical details within established contracts. These are reusable templates, not current execution requests. No verified DeepSeek-specific method is claimed; optional provider-specific notes belong separately and cannot weaken shared rules.

## Planning brief

> **Outcome:** [owner-approved product result or bounded specification to prepare].
> **Authority:** [owner approval/reference]; authorized now: [proposal only / publish scoped Issues]. Execution, merge and deployment: [explicit boundary].
> **Context:** [relevant canonical docs/Issues and verified facts]; unresolved product decisions: [list or none].
> **Scope:** [modules, intended behavior, exclusions and dependencies]. Preserve established decisions and avoid unrelated expansion.
> **Work:** inspect relevant source and existing open/closed Issues/PRs first. Reuse duplicates. Propose/publish independently executable tasks in their owning repositories, with priority, module/kind/status labels, required dependency milestones and clear acceptance/tests. Unapproved ideas stay outside the execution backlog.
> **Acceptance/report:** [concrete completeness criteria]; return brief, approval boundaries, issue links/proposed text and only material unresolved decisions. Do not implement product code under a planning-only brief.

## Implementation brief

> **Outcome:** complete [canonical Issue URL] or [bounded approved change]. Follow the shared standard and inspect completed work before changing it.
> **Verified context:** [repository/base/candidate SHAs and relevant docs/evidence links]; [reported/unknown limits].
> **Scope/constraints:** [permitted behavior/interfaces/files, exclusions, compatibility/privacy requirements and dependency milestones].
> **Authority/autonomy:** [allowed edits/commit/push/PR/test scope]; [single assigned task / sole-selector backlog run, scope and session limits]. [Explicit merge/deploy/admin/destructive boundaries]. Make reasonable technical choices; ask only for a materially blocking decision.
> **Execution/testing:** confirm assignment/session, eligible dependencies and dirty state; use an isolated branch/worktree, preserve ancestors, make the smallest complete change. Run [focused checks] plus relevant regression/failure/recovery tests using synthetic isolated fixtures; no shared/live environment unless authorized.
> **Acceptance/report:** [checkable results and terminal milestone]. Record final base→candidate SHA, behavior/paths, commands/environment/pass-fail-skip, limitations and rollback; update the Issue accurately. Continue eligible work only if the current session is authorized as sole selector; otherwise hand off/stop safely.

## Review/integration brief

> **Outcome:** inspect existing [Issue/candidate URL and exact SHA] and prepare [review / validation evidence / integration proposal / specifically approved integration]. Do not reimplement completed work.
> **Context/dependencies:** [repository, target/base/candidate ancestry, prior report links and required milestones]. Distinguish source existence, reported tests and current results.
> **Scope/authority:** [review-only paths and allowed synthetic checks]; [exact integration approval or explicitly proposal-only]. Merge/deployment/admin/destructive actions require their own authorization. Preserve protected work and external ownership.
> **Work/tests:** inspect diff/ancestry, compatibility, actual test provenance and migration/rollback. Run only meaningful authorized checks against the exact candidate; list unrun platforms/gaps. Prepare the concrete next-step manifest/patch where needed.
> **Acceptance/report:** [bounded review criteria]; report findings, exact SHAs, test evidence/limits and next eligible milestone/blocker. Approval alone is not Integrated/Deployed. A changed candidate needs reassessed validation. Continue only within current authorized session bounds.
