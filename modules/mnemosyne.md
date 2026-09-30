# Mnemosyne

## Purpose

Mnemosyne is the personal knowledge, memory, organization and work module: where the user maintains knowledge, ideas, research, plans, tasks and active personal/work organization. It is not merely a notes application and not an Obsidian/Notion clone; its value lies in Workspaces, tasks and projects living beside knowledge, and in participating in the ecosystem's references.

Its long-term conceptual model centers on **Workspaces, Notes, Tasks, Projects, Collections and Canvases**, with cross-cutting capabilities: Daily Notes, Inbox/Quick Capture, Today/Upcoming/Overdue, boards, tags, references, backlinks, attachments, search, history, reminders, recurrence, integrations and customization.

Mnemosyne exposes **workflows, not its database schema**: it must not present itself as a generic CRUD/resource manager (see [Interaction model](#interaction-model)).

The long-term model below is the architecture. The [V1 boundary](#v1-boundary) is an implementation subset of it, not a redefinition; deferred items are deferred, not rejected.

**Relationship to Documents.** Mnemosyne is primarily knowledge the user maintains and thinks/works in. [Documents](documents.md) is primarily artifacts created for reading, sending, publishing or export. The boundary is not absolute; resources may reference one another.

## Owns

- Workspaces, Notes, Tasks, Projects, Collections and Canvases (planned) and their relationships.
- Daily Notes, Inbox/Quick Capture, tags, Mnemosyne-side attachment relationships, reminders' semantic intent, Mnemosyne search, content/version history, archive and trash.
- Mnemosyne integration configuration and connected-account scoping (per Workspace).
- Mnemosyne's own application navigation and UX.

## Does not own

- Knowledge specifically about people, organizations or groups ([Meridian](meridian.md) facts, stories, quotes, interactions). Mnemosyne resources may reference Meridian entities.
- Communications ([Hermes](hermes.md)); Mnemosyne is not a communications archive.
- Places ([Atlas](atlas.md)), calendar events and calendar projection ([Chronos](chronos.md)), media ([Argus](argus.md)), music ([Lyra](lyra.md)).
- Physical file/blob storage (shared storage; see [storage-and-files](../concepts/storage-and-files.md)). Mnemosyne owns the relationship/context around an attachment.
- Notification delivery (Nexus notification infrastructure).
- External resources that integrations reference (e.g. GitHub Issues/PRs, Slack items).
- Credentials for connected accounts (Janus/Nexus secret infrastructure).
- Artifact-oriented document authoring ([Documents](documents.md)).

## Integrations

- **Cross-module references** ([cross-module-references](../concepts/cross-module-references.md)): Mnemosyne participates fully. Illustrative reference targets: another Note/Task/Project/Canvas, a Meridian Person/Organization/Group, an Atlas place, a Hermes conversation, a Lyra resource, an external resource such as a GitHub Issue. An **embed is a presentation of the referenced resource, not a copy**; ownership stays with the owning module/system. Unresolved, deleted or unavailable references follow the existing lifecycle principles and are not silently destroyed.
- **Backlinks**: notes link to resources, and resources can discover the notes that mention them, subject to permissions.
- **Shared storage**: attachments reference shared blobs.
- **Nexus**: authentication, permissions, routing, notifications (delivery of reminders), events, updates, references/backlinks. Application boundary: a full application at its own path, see [architecture](../architecture.md#application-boundary).
- **Chronos**: possible calendar/time projections. A Task deadline does **not** automatically become a Chronos event unless the architecture explicitly defines that behavior.
- **Hermes**: communication resources (e.g. a conversation or Slack message) are referenced or used as a source; they are not archived into Mnemosyne.
- **Design system**: Mnemosyne uses the shared design language with a purple/violet module accent ([design-system](../concepts/design-system.md)).

## Long-term model

Everything in this section is Established at the conceptual level unless labeled Planned or Open. Exact schemas, editor libraries and algorithms are Open.

### Workspaces

A Workspace is a first-class logical boundary for Mnemosyne resources and integrations. It is not a tag, filter or cosmetic context. Users define arbitrary Workspaces (Personal, Work, University, Aureate Empyrean, Client X). The purpose is to prevent personal and professional identities/data from being mixed by accident.

- By default the **active Workspace scopes**: Notes, Tasks, Projects, Collections, Canvases, Inbox, Today, Search, Quick Capture, resource pickers, integrations and connected external accounts.
- Connected accounts/integration configuration belong to a Workspace, and a Workspace may have several connected accounts for the same integration (e.g. personal GitHub in Personal; company GitHub and company Slack in Work).
- Cross-workspace views ("All Workspaces") may exist but are explicit, never the default. Cross-workspace moves/copies are deliberate user actions. Cross-workspace search is explicit.
- **Logical isolation is Established. Physical isolation (separate databases/storage) is not**; it is an implementation/Open question.

### Notes

Simple to create and use; no unnecessary metadata required before writing. A Note may have: title, content, workspace, collections, tags, icon, accent/color, pinned/favorite state, timestamps, references, attachments, history.

- Long-term editing direction is rich, portable **Markdown-oriented content**, not a proprietary block database. Expected capabilities: headings, emphasis, highlighting, lists, numbered lists, checklists, quotes/callouts, code and code blocks, tables, links, images/files, horizontal rules, collapsible sections; footnotes/math possibly later. Slash-command insertion is possible; editor UX/library is not an architecture decision.
- **A checkbox/checklist item in a Note is not automatically a Task.** Lightweight checklists do not pollute the task system. An item or text selection may be explicitly converted to a Task; a real Task may be embedded/referenced from a Note.
- Notes support autosave conceptually.
- Note-to-Note links and backlinks are convenient and duplicate nothing.

### Attachments

Notes and other appropriate resources reference attachments. Mnemosyne owns the relationship/context; shared storage owns the blob. Inline image display, file cards and paste/drop are UX concerns, not reasons to duplicate storage.

### Daily Notes

A first-class workflow: a Workspace may have a Daily Note associated with a calendar date, openable/creatable immediately as low-friction capture for thoughts, links, lightweight checklists, references and tasks. Daily Notes are normal Mnemosyne knowledge resources, not an incompatible special format. Content may later be explicitly extracted into a Note or Task. Purpose: capture first, organize later.

### Tasks

First-class resources. Long-term properties: title, description, workspace, lifecycle status, priority, start date, due/deadline, completion timestamp, reminders, recurrence, subtasks/checklists, tags, color/icon, project/organizational relationships, related Notes, references, attachments, timestamps/history. Only the minimum is mandatory.

- **Priority**: None, Low, Medium, High, Urgent. Independent of user-selected color.
- **Lifecycle** distinguishes at least Open, Completed, Cancelled. **Kanban/board columns are organizational presentation and are not task lifecycle status.**
- Start date, due/deadline and completion date are distinct. Date-only deadlines are representable without fake times such as 23:59.
- A Task need not belong to a Project or Collection; quick tasks enter the Inbox.
- Subtasks/checklists are supported conceptually without designing around unbounded nesting. Dependencies/blocking are desirable long-term; not designed.
- Tasks may reference arbitrary Empyrean resources.

### Reminders and recurrence

- **Deadline and reminder are separate.** A resource may have a deadline without a reminder, one reminder, or several. Notes may have reminders without becoming Tasks.
- Mnemosyne owns the semantic intent ("this resource should remind me at X"). Nexus/shared notification infrastructure delivers notifications. Chronos may later provide calendar/time projections.
- Recurring tasks preserve occurrence/completion history. The model distinguishes fixed-schedule recurrence (every Monday), completion-relative recurrence (14 days after completion) and custom recurrence. No storage algorithm is chosen.

### Projects

Something the user is actively doing toward a goal, with a lifecycle and an end. Not merely a folder. A Project may organize Tasks, Notes, Canvases, references, external resources and views such as boards. Metadata: title, description, workspace, icon/accent, status, start/due dates, tags, linked resources. Lifecycle: Active, On Hold, Completed, Archived. Completing or archiving a Project does not destroy its resources or history. Jira-style complexity (sprints, story points, managers) is not imported without a real requirement.

### Collections

Ongoing organization of knowledge, distinct from Projects (Project = something done and possibly completed; Collection = ongoing organization). Primarily organize Notes; may be hierarchical; a Note may belong to several Collections without duplication. Not filesystem folders.

### Canvas (Planned resource)

An infinite/spatial workspace for thinking and arranging resources. Owns: title, workspace, icon/accent, node placement/layout, visual connections, groups/frames, viewport/layout state, references, timestamps/history. Possible nodes: plain text card, Note, Task, Project, image/file, web link, another Canvas, generic Empyrean resource, external resource (GitHub Issue/PR).

- **A Canvas stores layout, presentation and references only, never independent copies.** A Task on a Canvas is the same Task.
- **Connections are visual, user-authored relationships by default and do not silently create semantic relationships in other modules.** An arrow between a Meridian Person and Organization does not create an Employment relationship. Explicit user actions might someday create semantic relationships from connections; that is separate functionality.
- Quick plain-text cards need not be Notes; a card may later be explicitly converted to a Note or Task.

### Search and discovery

Search is scoped to the active Workspace by default. Long-term scope: Notes, note contents, Tasks, Projects, Collections, Canvases, attachment metadata where appropriate, integration resources, and referenced Empyrean resources where permissions permit. Filters may include type, workspace, project, collection, tag, date, status, priority, source/integration. A command palette/quick navigation is desirable long-term; its UX is not architecture. Ecosystem-wide search remains a separate Open question.

### Inbox, Quick Capture, Today

Quick Capture records something before deciding where it belongs; the Inbox is the unorganized landing area. **Today / Upcoming / Overdue are derived views over Tasks, not copies.** Home is a working surface, described under [Interaction model](#interaction-model).

### History, archive and trash

- **Archive**: the resource stays valid, is hidden from normal active workflows, remains discoverable where appropriate, and references keep working.
- **Trash**: a complete recoverable-deletion model, defined under [Trash](#trash).
- Deleting a referenced resource does not silently destroy incoming references.
- Long-term content/version history supports viewing and restoring previous versions and potentially diffs.
- **Content/version history is distinct from user/resource activity history.** Mnemosyne Activity is domain/user-work activity; Nexus Activity is ecosystem/system operational activity. Individual keystrokes are not meaningful activity.

### Trash

Moving a resource to Trash is a reversible action, distinct from permanent deletion.

- **Move to Trash** is reversible and does not generally require a destructive confirmation dialog. A short-lived Undo after moving to Trash is a desirable UX direction.
- Trash supports: viewing trashed resources with their deletion timestamp; restoring resources; permanently deleting individual resources; multi-select where useful, with restore and permanent delete of the selection; and Empty Trash.
- **Permanent deletion and Empty Trash are destructive and require explicit confirmation.**
- **Automatic permanent deletion** is based on time in Trash and is configurable. **Default retention: 30 days** (Established). The policy direction includes at least Never, 7, 14, 30, 60 and 90 days (Planned; the configuration surface is not specified). Retention is calculated from `trashed_at`, not from creation or last-edit time. With Never, nothing is deleted automatically. The UI may communicate remaining retention where useful ("Deleted 4 days ago · permanently deleted in 26 days").
- **Restore** returns the resource to its original Workspace and preserves its valid relationships where possible.
- Moving to Trash does not silently destroy references to the resource, and the resource stays recoverable during retention. After permanent deletion, incoming cross-module references follow the reference lifecycle in [cross-module-references](../concepts/cross-module-references.md) (unresolved, not silently removed).
- Permanent deletion follows shared-storage ownership/reference rules and never blindly deletes a blob still referenced elsewhere ([storage-and-files](../concepts/storage-and-files.md)).
- The mechanism that schedules and runs automatic cleanup is an implementation detail, but behavior must be deterministic and testable.

### Integrations and connectors

An open connector/integration model. Connected accounts are scoped to Workspaces. **GitHub is the first planned integration**, for repositories, Issues and Pull Requests. Example: Workspace "Aureate Empyrean" with a connected GitHub identity, Project "Empyrean Nexus" linked to a repository.

- Mnemosyne does not own GitHub Issues/PRs. It may reference, display, link them to Projects, place them on a Canvas, reference them from Notes, and create a Mnemosyne Task from one while preserving source/reference provenance. The Task is owned by Mnemosyne; the Issue/PR by GitHub.
- Synchronization behavior (e.g. completing a Task when an Issue closes) is explicit and Open, not assumed.
- Slack is a possible future integration under the same model (a Work Workspace connecting a company Slack and explicitly creating Tasks/Notes from Slack resources). Hermes owns communication-domain concepts.
- Credentials/tokens for connected accounts belong to Janus/Nexus secret infrastructure according to the eventual security architecture, not to a Mnemosyne-invented vault.
- Whether connectors are implemented by Mnemosyne, shared Nexus connector infrastructure, Hermes where appropriate, or a broader plugin system is Open.

### Customization and templates

Resources may support icon, accent/color and pin/favorite. These are user resource customization, separate from the module accent ([design-system](../concepts/design-system.md)); color has no hidden semantics unless the user gives it one. Templates are desirable long-term for Notes and appropriate workflows, as starting structures rather than new resource types.

### Offline / local-first direction

Mnemosyne is a strong candidate for future offline-capable/local-first clients, since knowledge and tasks may need capture without connectivity. No synchronization protocol is designed, and not every module must be local-first. Keep the possibility architecturally open, following [local-first-and-sync](../concepts/local-first-and-sync.md).

## Interaction model

Interaction semantics and product principles. Layouts, components, icons and labels named here are illustrative directions, not pixel-level requirements.

### Core principle

**Shared data primitives do not imply shared interaction models.** A Note behaves like a Note, a Task like a Task, a Project like a Project, a Collection like a Collection. Resources may share underlying capabilities (tags, references, attachments, icons, accents, timestamps, archive/trash semantics) but are not all exposed through one generic editor or form. Mnemosyne exposes workflows, not its schema. **Progressive disclosure** is preferred: the information and actions relevant to the current workflow come first; other properties stay accessible without dominating.

### Navigation

Primary navigation is small and stable, centered on **Home, Notes, Tasks, Projects, Collections**. Workspace switching is prominent; the Daily Note is immediately reachable; Search is available everywhere within the active Workspace; switching back to Nexus/other modules stays available. Today, Inbox, Upcoming, Overdue, Completed and Trash are **not** required to be permanent top-level destinations: task views belong inside Tasks, and Trash is secondary management functionality.

### Home

A practical working surface, not an analytics dashboard. It answers: what needs my attention now, what can I quickly capture, what was I recently working with. It may surface quick capture, today's Tasks, overdue Tasks, today's Daily Note and recent resources. No meaningless statistics, resource-count cards or decorative dashboards; it optimizes for doing work.

### Tasks

Tasks are first-class actionable resources, not Notes with a Task icon.

- An open Task's primary representation includes an actionable completion control (such as a checkbox). Activating it completes the Task immediately; completing must not require opening the Task, finding a lifecycle property, selecting Completed and saving.
- Creation is low-friction: add task → enter title → Enter. The Task exists immediately with sensible defaults. Deadline, priority and Project may optionally be supplied during quick creation without a mandatory form.
- Task lists show useful information only: completion state, title, and deadline, priority and Project/context when relevant. They do not expose the whole schema.
- Detailed editing uses progressive disclosure. Primary: title, description, checklist/subtasks, deadline, priority, Project, tags. Secondary: attachments, references/backlinks, resource identity, icon/accent, other metadata. A detail may be a panel/drawer or other efficient pattern; a Task need not feel like a full document editor.
- Lifecycle stays Open/Completed/Cancelled; board column stays distinct from lifecycle.
- **Task views** live inside the Tasks area: Inbox, Today, Upcoming, Completed, and All Tasks where useful. Overdue Tasks are clearly visible and actionable, e.g. prominently within Today/Tasks, without a permanent top-level item. These are derived views over the same Task resources, never duplicate objects.

### Notes

Optimized for rapid reading, switching and writing. Opening Notes makes existing Notes immediately browsable and easy to move between. A list/detail (multi-pane) model on desktop is a preferred direction: navigation/list on one side, the active Note/editor on the other. Creating a Note puts the user directly into writing. The editor and content are primary; metadata and shared resource capabilities are secondary; formatting controls stay visually subordinate to writing. Presenting Note editing as a database-record form, or requiring notes → generic resource list → open row → separate CRUD editor for ordinary note-taking, is explicitly discouraged.

### Projects

Active work contexts, not generic resources or folders. A Project makes its relevant work immediately understandable. Possible views: Overview, Board, Tasks, Notes. The simple Kanban direction remains, and cards represent the same Task resources, never copies. Lifecycle stays Active/On Hold/Completed/Archived. No Jira-style complexity.

### Collections

Knowledge organization, not Projects and not filesystem folders. Interaction emphasizes hierarchy, browsing and Note membership; a tree/navigation representation is a natural direction. Opening a Collection primarily reveals the Notes/resources organized in it, not a generic metadata editor. A Note can remain in several Collections without duplication.

### Workspace UX

Workspace stays a visible first-class context. Switching is understandable, without hidden or ambiguous controls. Management exposes create, rename, archive and switch without relying on unexplained UI (e.g. unlabeled ellipsis menus) for essential behavior. All isolation/scoping decisions are unchanged.

### Information density and copy

Mnemosyne makes productive use of desktop space. Avoid: giant cards holding little information, huge empty dashboard areas, one-resource-per-screen CRUD patterns where unnecessary, excessive card nesting, exposing all metadata at once, decorative resource counts. Prefer compact actionable lists, list/detail layouts where appropriate, useful grouping, strong hierarchy, progressive disclosure, and enough density to scan many Notes/Tasks, without being cramped. UI text communicates state, identifies content, explains non-obvious actions or warns about consequences; it avoids filler such as "1 resource in this Workspace" unless the count helps the current workflow, and avoids exposing implementation terminology unnecessarily. Mnemosyne does not imitate another application; familiar patterns are referenced conceptually only. See also [design-system](../concepts/design-system.md).

## V1 boundary

**Planned** implementation scope: a deliberately small but genuinely usable real module, intended to replace the Example Module as the first meaningful everyday test of Nexus. A subset of the long-term vision, not a redefinition.

**Included**

- **Workspaces**: create, rename, switch, archive; strict default scoping.
- **Notes**: rich Markdown-oriented editing, autosave, title, tags, Collections, icon/accent, pin/favorite, Note-to-Note links, backlinks, attachments.
- **Trash** (for V1 resources): recoverable deletion as defined under [Trash](#trash): restore, permanent delete with confirmation, Empty Trash, and configurable retention (default 30 days).
- **Tasks**: quick create, Inbox, title, description, Open/Completed/Cancelled, priority, start date, deadline, completion date, subtasks/basic checklist, tags, related Note, Today (with Overdue clearly visible), Upcoming, Completed, All Tasks; direct completion control.
- **Interaction model**: the workflows described under [Interaction model](#interaction-model) (small stable navigation, working-surface Home, list/detail Notes, progressive-disclosure Task detail) rather than generic resource CRUD.
- **Projects**: title, description, icon/accent, Active/On Hold/Completed/Archived, related Tasks and Notes, simple Kanban view.
- **Collections**: hierarchical; a Note may belong to several.
- **Daily Notes**: one per Workspace/date, immediate open/create, consistent with normal Notes.
- **Search**: Notes, note content, Tasks, Projects, Collections; active-Workspace scoped by default.
- **Empyrean integration**: Module Protocol participation, full-page application boundary, Nexus authentication and permissions, generic cross-module references and backlinks where the protocol requires, appropriate activity/events, shared infrastructure, application/module switching as the architecture requires.

**Deferred (not rejected):** Canvas, GitHub integration, Slack integration, recurring tasks, advanced reminders, full content version history/diff UI, templates, advanced external embeds, offline/local-first synchronization, advanced command palette, natural-language date parsing. An existing architectural dependency may pull a deferred item in.

## Established decisions

- Mnemosyne is the personal knowledge, memory, organization and work module; not merely a notes app or an Obsidian/Notion clone.
- Workspace is a first-class logical boundary (not a tag/filter/cosmetic context); the active Workspace scopes resources and integrations by default; cross-workspace views, moves and search are explicit. Logical isolation is established; physical isolation is not.
- Connected accounts/integration configuration belong to a Workspace.
- Notes are simple; content direction is portable Markdown-oriented, not a proprietary block database. A checklist item is not automatically a Task.
- Embeds are presentations of referenced resources, never copies; unresolved references are not silently destroyed.
- Mnemosyne owns attachment context; shared storage owns blobs.
- Daily Notes are normal knowledge resources.
- Tasks are first-class; lifecycle status is distinct from board columns; priority is independent of color; start, deadline and completion are distinct; date-only deadlines are representable.
- Deadline and reminder are distinct; Mnemosyne owns reminder intent; Nexus delivers notifications; a Task deadline does not automatically become a Chronos event.
- Project is not a folder; Collection is distinct from Project; Collections are not filesystem folders; a Note may be in several Collections.
- A Canvas stores layout/presentation/references, never copies, and its connections do not create semantic relationships elsewhere.
- Today/Upcoming/Overdue are derived views.
- Archive and Trash are distinct; deleting a referenced resource does not destroy incoming references.
- Shared data primitives do not imply shared interaction models; Mnemosyne exposes workflows, not its schema, and is not a generic CRUD/resource manager. Progressive disclosure is preferred.
- Primary navigation is small and stable; Today/Inbox/Upcoming/Overdue/Completed/Trash are not required as top-level destinations.
- Home is a working surface, not an analytics dashboard.
- Open Tasks have a direct completion control; quick creation is low-friction; Task views are derived views inside Tasks.
- Creating a Note leads directly into writing; the editor is primary.
- Kanban cards are the same Task resources, never copies.
- Move to Trash is reversible without destructive confirmation; permanent deletion and Empty Trash require explicit confirmation. Default Trash retention is 30 days, calculated from `trashed_at`; Never means no automatic deletion. Restore returns the resource to its original Workspace. Permanent deletion never blindly deletes a shared blob still referenced elsewhere.
- Content/version history is distinct from activity history; Mnemosyne Activity is distinct from Nexus Activity.
- Mnemosyne does not own external integration resources (e.g. GitHub Issues/PRs); a Task created from one is owned by Mnemosyne and preserves provenance. Mnemosyne is not a communications archive.
- Mnemosyne is a full application, not a page inside the Nexus sidebar.
- Resource colors are separate from the module accent; Mnemosyne's identity is purple/violet on the shared foundation.
- Mnemosyne is knowledge the user works in; Documents is artifacts for reading, sending, publishing or export; the boundary is not absolute.

## Planned direction

- Canvas as a first-class resource.
- GitHub integration (repositories, Issues, Pull Requests), then possibly Slack and other connectors under the same Workspace-scoped model.
- Recurring tasks with occurrence history; advanced reminders.
- Full content version history with restore and diff.
- Templates.
- Advanced command palette; broader search scope including integration and referenced resources.
- Task dependencies/blocking.
- Offline-capable/local-first clients.
- Reminders on Notes.
- Trash retention options Never, 7, 14, 30, 60 and 90 days (default 30 is Established).

## Open questions

- Storage model: database-owned notes vs. plain files on disk (interop with existing Markdown tools); physical isolation between Workspaces.
- Reference syntax inside Markdown and exact resource/reference types.
- How Workspaces relate to Nexus permissions and multi-user, and whether references/backlinks from other modules respect Workspace boundaries (Workspaces are a logical boundary within Mnemosyne; the owning module enforces scoping).
- Exact task, recurrence and reminder models; task dependencies.
- Whether connectors live in Mnemosyne, shared Nexus connector infrastructure, Hermes, or a broader plugin system; connected-account credential handling via Janus/Nexus secrets.
- Integration synchronization semantics (e.g. Issue closes → Task).
- Whether any Task/deadline projection into Chronos is ever defined.
- Whether Meridian person notes stay in Meridian or are Mnemosyne notes referencing the person ([meridian](meridian.md)).
- Ecosystem-wide search: which module or Nexus service owns it.
- Local-first/offline design and sync ([local-first-and-sync](../concepts/local-first-and-sync.md)).
- Exact boundary with [Documents](documents.md), including content model sharing.
- Trash: configuration surface and exact retention options beyond the default, how references to a trashed (not yet deleted) resource resolve for other modules, and the cleanup scheduling mechanism (behavior must be deterministic and testable).
- Exact layouts, components and labels (illustrative in this document); where the module accent tokens are centralized.
- Template model.
- Whether the Example Module is retired once Mnemosyne V1 exists.
