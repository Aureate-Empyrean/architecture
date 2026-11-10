# Owner planning and agent execution procedures

The owner decides what AE should become. ChatGPT helps develop those decisions. GitHub Issues records approved work. Implementation agents execute it. No special ChatGPT integration/API, dashboard, service or extra orchestrator is required.

Read [shared instructions](../AI_AGENT_INSTRUCTIONS.md), [prompt templates](PROMPT_TEMPLATES.md), and the [current V1 inventory](README.md). Module tasks live in their application repositories; architecture owns ecosystem/cross-repository/architectural tasks. Live Issues override stale progress snapshots; every original capability ID and legacy draft alias has an explicit disposition in [BACKLOG_MAP.json](BACKLOG_MAP.json). Historical audits remain evidence, not active queues. Future/undecided Issues cannot enter the V1 execution query; clear their owner decisions and record accepted scope before relabeling.

## Start autonomous backlog execution

1. Owner authorizes a bounded run, for example: “Continue approved AE V1 ready work in this session. Act as the only task selector; use the canonical standard, preserve existing candidates, and continue until the session limit or no eligible task remains. No merge, deployment, administrative change or destructive operation.” This example is a template, not approval issued by this document.
2. Agent opens the root AGENTS.md, retrieves the canonical standard using authenticated GitHub access, and reads the setup issue’s latest selector START/STOP/HANDOFF comments. If another selector is active, do only explicitly assigned nonoverlapping work; do not select a task.
3. With sole-selector authorization and no conflict, add a short START comment to [architecture #4](https://github.com/Aureate-Empyrean/architecture/issues/4): session ID, agent, authorization reference, scope, limits and start time. Re-read before selecting. The closed setup issue is only an existing comment location, not a new task/database/service.
4. Query ready/unassigned Issues organization-wide, priority p0→p4, then oldest eligible. Inspect dependency milestones, scope approval, ownership/external holds and candidate access. Assign explicitly with the required task comment; then inspect/create the isolated worktree.
5. Implement or review according to that Issue, validate appropriately, commit within authorized scope, post exact evidence/status and release only safe ownership. Continue to the next eligible task without asking for routine assignment. Never bypass gates or session limits; never start unbounded background execution.

Only one autonomous selector is permitted. Assignment/comments are not atomic locks; simultaneous selector bootstrap is unsupported. Conflicting/ambiguous selector records mean no new claims until resolved. Other agents can work concurrently only on already explicitly assigned, nonoverlapping work. A planning conversation need not own the selector role.

## Submit an owner-approved planning brief

1. Owner and ChatGPT discuss vision/features/UX/architecture and settle the intended change. Capture a concise brief using the planning template, including approval reference, boundaries and unresolved decisions.
2. Clearly identify whether the current agent may prepare proposed Issues, publish them, or execute them. A speculative chat or agreement that an idea is interesting is not publication/execution permission. Existing explicit authorization is reusable; do not request it again.
3. Agent checks relevant docs and open/closed Issues/PRs across affected repositories; preserves architecture and completed work; reuses duplicates and separates independently owned tasks. Publish only the approved scope in the correct repositories, with acceptance, dependencies, labels and accurate ready/blocked state. Otherwise return concrete proposed text for the one missing approval.
4. Return links plus material decisions/blockers. The implementation selector discovers eligible approved work from Issues; the owner need not distribute tasks or resend the whole conversation.

## Check current V1 progress

Use [all open V1 Issues](https://github.com/issues?q=org%3AAureate-Empyrean%20is%3Aissue%20is%3Aopen%20label%3Aae%3Av1) or [ready and unassigned](https://github.com/issues?q=org%3AAureate-Empyrean%20is%3Aissue%20is%3Aopen%20label%3Aae%3Av1%20label%3Astatus%3Aready%20no%3Aassignee). Add `label:priority:p0` (through p4), or `label:status:blocked|backlog|in-progress|review` with one actual status at a time. Implementation/validation/integration/deployment are separate Issue evidence milestones. Search exact labels, not the literal pipe-separated example. Closed completed work is queried separately with `is:closed`; not-planned/duplicate closures are not successful implementation.

For CLI access, a read-only query is:

```text
gh search issues --owner Aureate-Empyrean --state open --label ae:v1 --label status:ready --no-assignee --sort created --order asc --limit 100 --json repository,number,title,url,labels,assignees
```

The count of open/closed Issues is not a V1 readiness certificate. Read candidate-specific milestones, recovery/platform evidence, remaining scope decisions and the checklist dispositions. Ready means eligible work, not “all product gates complete.” Report what is measured versus inherited reports. Avoid repeated full audit/log dumps.

## Stop or hand off safely

Respect explicit stop/pause instructions immediately; start no new task. If a bounded write is already in flight, record its observed outcome, then stop. Do not continue a test run or publication merely to finish the task after an explicit stop.

Leave a short issue checkpoint: session, exact branch/worktree/base/candidate, changed/pending files, commands/results, unrun checks, current milestone and next action. Commit only if still authorized; never discard uncommitted work. If another session must resume that same task, record the explicit handoff and receiving session; release/reassign only your own ownership when safe. Otherwise keep blocked/owned work visibly held rather than pretending it is ready.

For the sole selector, add STOP or HANDOFF on architecture #4 after the checkpoint. HANDOFF names the receiving session and bounded authority; it acknowledges before selecting. A stale record is not permission to steal ownership; locate the existing checkpoint/session or ask only for the unresolved ownership decision. A crashed session can leave a stale record; no automatic expiry/reclaim exists. If no eligible work or resources remain, provide a concise summary and stop.

## Local discovery without sibling-path assumptions

Each repository has a short root AGENTS.md linking to the canonical standard. Other provider-specific instruction files, if present, remain in force within their scope and must be preserved. An agent that does not auto-read AGENTS.md should open it explicitly.

From any independently cloned AE repository, retrieve the private standard using existing GitHub authentication:

```text
gh api -H 'Accept: application/vnd.github.raw+json' 'repos/Aureate-Empyrean/architecture/contents/AI_AGENT_INSTRUCTIONS.md?ref=main'
```

This prints Markdown, not JSON; do not add a JSON-only parser. Read the canonical standard and only linked sections relevant to the selected task. A web/connector read of the authenticated canonical URL is an equivalent fallback. No assumption is made that ../architecture exists. If authenticated access is unavailable, stop new execution and report that specific blocker; do not make repositories public or copy an invented standard.

## Current limits

There is no atomic claiming, selector lock service, lease/fencing mechanism or Actions orchestration. Single-selector discipline and explicit assignment are required. Existing local-only candidates may be unavailable on another machine; report candidate access as a blocker rather than recreating completed work. Scope/security/environment approvals remain human decisions. This system does not run after a session ends and consumes only the resources the active agent is already authorized to use.
