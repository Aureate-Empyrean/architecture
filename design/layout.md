# Layout

Two layouts exist in the ecosystem: the **Nexus shell** (the control plane) and **module application shells** (full-window applications hosted in a Nexus frame). They share materials and tokens but not composition. This document separates what Nexus owns from what a module owns.

Sources: `empyrean-nexus/frontend/style.css`, `empyrean-nexus/frontend/app.js`, `empyrean-nexus/docs/ui.md`, `empyrean-nexus/docs/design-language.md`, and each module's `frontend/style.css`. See [references/README.md](references/README.md) for exact paths.

## 1. Nexus shell

**IMPLEMENTED** (Nexus 0.1.9).

```
┌────────────────────────────────────────────────────────────────────┐
│  atmosphere (fixed, behind everything; hidden in application mode) │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │ masthead (sticky / fixed; 60 px; floating lens; 18 px radius)│  │
│  │ [mark + EMPYREAN NEXUS] [Overview Modules Activity]          │  │
│  │                         [update] [bell] [settings] [account] │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                    │
│         content (max 1180 px; padding 34 / 32 / 80)                │
│         ┌──────────────────────────────────────────────────────┐   │
│         │ page heading: eyebrow, h1 32 px, sub, actions         │   │
│         │ one structural panel or view-specific content        │   │
│         └──────────────────────────────────────────────────────┘   │
│                                                                    │
│  drawer (module details; 440 px; overlays or pushes content)       │
└────────────────────────────────────────────────────────────────────┘
```

| Part | Implementation |
|---|---|
| Page frame | `--frame: 12px`; masthead margin `12px 12px 0`; content is centred |
| Command bar | `.masthead`, height `--bar: 60px`, sticky at `top: var(--frame)`; radius 18 px; glass lives on `::before`, not on the bar |
| Brand / launcher | Nexus mark + `EMPYREAN NEXUS` wordmark + current product name; opens the application switcher (`Ctrl/⌘ K`) |
| Primary navigation | Words: Overview, Modules, Activity; one gold indicator (`.nav-ink`) under the current page |
| Right cluster | Update notice only when a real update is reported, notifications, Settings, account menu |
| Content | `.content`, `max-width: 1180px`, `padding: 34px 32px 80px` |
| Page heading | `.page-heading`: h1 32 px, `page-sub` metadata, optional actions |
| Structural panel | Overview modules/activity and the Modules list share one panel divided by hairlines; no card per row |
| Module details | `.drawer`, 440 px, opened beside the Modules list; Escape closes and returns focus to the row |
| Dialogs | Native `<dialog>` with the Nexus lens treatment; `aria-labelledby`; Escape closes |
| Atmosphere | Fixed `.atmosphere` with gold light from above, a cool counter-light and faint orbit rings |

**DOCUMENTED behaviour** (`empyrean-nexus/docs/ui.md`): the masthead is the only persistent chrome; modules are never appended to the primary navigation; the launcher lists Nexus, every enabled module with status/version, then "Manage modules" and release information.

**IMPLEMENTED navigation routes:** `/app/overview`, `/app/modules`, `/app/activity`, `/app/settings`, `/app/notifications`, `/app/modules/<id>`. The router uses normal links, `pushState` and `popstate` (`frontend/router.js`).

**IMPLEMENTED responsive behaviour:** two breakpoints, `1100px` and `860px`. At 860 px the frame becomes 8 px, the bar 56 px, content padding 20/12/60, the hero stacks and the drawer spans the viewport. The primary navigation is not collapsed into a menu. See [responsive.md](responsive.md#2-nexus).

## 2. Application chrome and application mode

**IMPLEMENTED.** A module with the `ui.application` capability takes the full window at `/app/modules/<id>`.

| State | Behaviour |
|---|---|
| Normal module view | Nexus page heading + a sandboxed `<iframe>` (`sandbox="allow-scripts"`) inside `.module-frame`; height `clamp(360px, 58vh, 720px)`; optional API explorer below |
| Application mode | Full-window `<iframe id="module-application" sandbox="allow-scripts allow-downloads">`, `height: 100dvh`; Nexus content max-width removed and padding 0; Nexus atmosphere hidden |
| Nexus command bar in application mode | Fixed at the top, translated up so only a 5 px gold edge remains; hovering or moving keyboard focus into it slides the full bar down |
| Return to Nexus | The launcher or a module bridge `navigate` request; the module never owns the top-level URL |
| Failure | A module that is missing, stopped or incompatible gets a Nexus empty state with the relevant action, not an empty frame |

**Binding boundary** ([architecture](../architecture.md#application-boundary), [ADR 0001](../decisions/0001-module-ownership-and-domain-ignorant-nexus.md)):

- Nexus owns the top-level browser URL, History API writes and the application frame.
- A module owns its internal routes, rendering, save behavior and navigation UX.
- Module internal routes below `/modules/<id>` are data to Nexus, not routes Nexus interprets or proxies.
- Nexus never appends module domain navigation to its own sidebar or command bar.

**DOCUMENTED, not fully implemented:** [architecture.md](../architecture.md#application-boundary) still describes the application/module switcher design as Open and says no general switcher exists. Nexus 0.1.9 **does** implement a launcher/switcher. Treat the implementation as evidence of the current UI and the architecture text as needing a documentation update; do not infer a broader navigation API from it.

## 3. Module application shells

**IMPLEMENTED.** The newer modules share a family resemblance: a floating navigation rail or sidebar, a floating top bar or toolbar, and a workspace surface. The composition is intentionally not identical.

| Application | Shell | Navigation width | Top bar / toolbar | Workspace | Frame |
|---|---|---|---|---|---|
| Chronos | Two-column grid: calendar sidebar + main column | `--sidebar: 284px` | Floating `.topbar`, 64 px, radius 20 px | `.view` scrolls; radius 24 px | 14 px |
| Lyra | Two-column grid: navigation + scrolling main | `--nav: 248px` | Sticky `.topbar` with a 60 px glass inner bar | `.page`, max 1280 px, padding 24/32/48 | 14 px |
| Meridian | Two-column grid: rail + main column | `--rail: 232px` | `.toolbar`, 56 px, radius 14 px | `.sheet` scrolls; page max 1760 px, padding 36/40/72 | 12 px |
| Argus | Two-column grid: rail + sheet | 220 px (literal) | No separate toolbar; the page head carries actions | `.sheet` scrolls; page max 1600 px, padding 28/36/72 | 12 px |
| Mnemosyne | Two-column grid: sidebar + main | `--sidebar: 236px` | `.topbar` | `.view` / writing surface; sidebar and toolbar float | 12 px (composition) |
| Nexus | Single column with a horizontal command bar | — | 60 px masthead | `.content` max 1180 px | 12 px |

**IMPLEMENTED common pattern in Chronos, Lyra and Meridian:**

1. The rail/toolbar is glass and floats above the atmosphere.
2. The work sits on a quieter, more opaque surface (Chronos view, Lyra page background, Meridian sheet).
3. The rail holds a brand mark, destinations and a footer action. Meridian can collapse to icons; Argus and Mnemosyne switch to a horizontal bar on phones.
4. The top bar holds the page title, search, view controls and one primary action.
5. Content uses typography, whitespace and hairlines before boxes.

**OPTIONAL.** Rail vs sidebar vs horizontal bar, width, radius, atmosphere motif and whether the workspace is a distinct sheet are module choices. The new module should not copy a shell wholesale if its content does not need that structure.

**RECOMMENDED.** A content module with more than three destinations uses a persistent rail at desktop widths. A module with one or two destinations may use a toolbar-only layout. Keep the rail and toolbar out of the scrolling content so navigation and the primary action remain available.

## 4. Content layout

**IMPLEMENTED patterns:**

- **Page heading.** A page title (26–40 px depending on module), an optional eyebrow, one factual metadata line and at most one primary action in the heading. Nexus uses 32 px; Chronos 26 px; Lyra 40 px; Meridian 34 px.
- **Sections.** A section heading (13–22 px) with an optional "Manage"/"All" link; content below. Meridian and Argus use banded screens for identity headers.
- **Lists before cards.** Nexus module rows, Meridian `.list`, Argus photo grid and Lyra poster grid are the primary containers. Cards are used for media and people, not for every record.
- **One structural panel per screen.** Nexus groups modules and activity in one panel; Meridian groups records in `.list`; Chronos groups the calendar in one view. Avoid card-inside-card and a dashboard of equal-weight widgets.
- **Reading measure.** Long-form text is constrained (Lyra about/notes max 62ch; Meridian `.measure` 920 px; Mnemosyne writing surface). Do not let prose span a 1760 px page.
- **Empty space is allowed.** Do not add filler widgets to balance a sparse screen.

**RECOMMENDED.** New module page:

```
page
├── page header (eyebrow, title, one-line meta, one primary action)
├── optional notice/banner
├── section (label + list/grid + empty state)
└── optional secondary section
```

Use a single content max-width (1180–1760 px depending on density) and a single page padding scale. Do not mix a centred 1120 px column with a full-bleed 1760 px section without a clear reason.

## 5. Navigation boundaries

| Concern | Owner | Rule |
|---|---|---|
| Top-level URL and History API | Nexus | Module must not write the parent URL directly |
| Application frame and sandbox | Nexus | Module runs in an opaque-origin iframe |
| Module switcher | Nexus | Launcher lists enabled applications; modules do not add themselves to Nexus navigation |
| Internal routes, breadcrumbs, tabs, drawers | Module | Module owns its own navigation and state |
| Cross-module navigation | Module → Nexus bridge | `navigate` takes a module ID or `nexus`; it does not carry an internal route today |
| Deep links / browser Back–Forward inside a module | Optional pilot | Default-off `empyrean-navigation-v1`; only Mnemosyne has an adapter |
| External links | Module → Nexus bridge | `external` asks Nexus to open a reviewed origin or `mailto:` |

**IMPLEMENTED** bridge actions: `context`, `request`, `navigate`, `external`. `context` returns enabled application names/IDs, the Nexus locale and navigation support. `navigate` switches module ID only and does not reply on a successful switch. See [module-ui-contract.md](module-ui-contract.md#4-the-nexus-bridge).

**RECOMMENDED.** A module keeps a visible in-module location indicator (rail selection, tab, breadcrumb or title) because Nexus chrome does not show the module's internal page. Do not rely on the browser Back button for in-module navigation unless the navigation-history pilot is enabled and the module has an adapter.

## 6. Layout anti-patterns

Avoid these; each is visible in at least one historical implementation and should not be copied:

- A full-height opaque sidebar plus a flat hairline-divided content area with no atmosphere. This was the pre-2.1 Mnemosyne/Nexus composition and is superseded.
- A card for every record, or a card inside a panel/card.
- A second primary button in the same region.
- Module pages appended to the Nexus primary navigation.
- A module that assumes Nexus chrome, cookies or storage.
- Fixed-height content that clips at 390 px.
- A top bar that contains a `backdrop-filter` element which in turn contains menus; the bar becomes the backdrop root and the menus lose the page behind them. Use a `::before` layer for the bar's glass.
