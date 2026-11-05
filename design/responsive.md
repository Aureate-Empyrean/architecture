# Responsive behaviour

The ecosystem is desktop-first. Current applications implement responsive layouts with module-local media queries; there are no shared breakpoint tokens. This document records the breakpoints that actually exist, the navigation patterns at narrow widths, and the rules a new module must satisfy.

**Terminology:** "implemented" means the media query and layout rule exist in the named stylesheet. It does not mean every screen was re-verified at that width in this documentation pass. The [implementation status](../implementation-status.md) records which suites and browser checks each repository reports.

## 1. Shared intent vs implemented breakpoints

**DOCUMENTED** ([design-system.md](../concepts/design-system.md#interaction-principles)):

- Desktop-first.
- Below **900 px**, secondary panes collapse into drawers.
- Below **600 px**, navigation collapses.
- No horizontal page scroll.

**IMPLEMENTED** breakpoints differ by application:

| Application | Breakpoints | Primary navigation behaviour |
|---|---|---|
| Nexus | `max-width: 1100px`, `max-width: 860px` | No collapse; the horizontal command bar remains, frame/bar shrink and content padding reduces. The drawer spans the viewport at 860 px. |
| Mnemosyne | `max-width: 1180px`, `max-width: 1000px`, `max-width: 760px` | At 1000 px the sidebar becomes a 64 px icon rail; at 760 px it becomes a horizontal bar, brand hidden, active item gets a fill. |
| Chronos | `max-width: 1100px`, `max-width: 960px`, `max-width: 640px` | At 960 px the 284 px sidebar becomes an off-canvas panel (`min(300px, 84vw)`) toggled by a menu button; at 640 px the top bar wraps. |
| Lyra | `max-width: 1100px`, `max-width: 900px`, `max-width: 640px` | At 900 px the 248 px nav becomes an off-canvas panel (`min(290px, 84vw)`) toggled by a menu button; at 640 px the top bar and page tighten. |
| Meridian | `min-width: 1100px`, `min-width: 1760px`, `min-width: 2200px`; `max-width: 1099px`, `max-width: 960px`, `max-width: 760px`, `max-width: 640px` | At 960 px the 232 px rail becomes a 64 px icon rail; at 640 px it becomes a horizontal bar with a scrollable 40 px-item nav. |
| Argus | `max-width: 1099px`, `max-width: 760px` | At 760 px the 220 px rail becomes a horizontal scrollable bar; the brand name hides. |
| Janus Web Vault | `max-width: 1120px`, `max-width: 760px` | Standalone; at 1120 px the rail becomes a horizontal strip; at 760 px a single column. |

**Conclusion.** The documented 900/600 rule is not the implemented rule. Do not cite 600 px as the ecosystem's navigation breakpoint. Treat 960/900 px as the common content-module threshold for collapsing a rail into an off-canvas panel or icon rail, and 640/760 px as the phone threshold.

## 2. Nexus

**IMPLEMENTED** (`empyrean-nexus/frontend/style.css`):

| Condition | Change |
|---|---|
| `≤ 1100px` | Overview sections stack vertically; the divider changes from vertical to horizontal |
| `≤ 860px` | `--frame: 8px`, `--bar: 56px`; masthead radius 16 px; content padding `20px 12px 60px`; hero stacks and its action group moves to the top right; overview/module/settings padding reduces; drawer starts below the bar and spans the viewport |
| Application mode | The command bar is fixed and reduced to a 5 px gold edge; hover/focus slides it down. The module iframe is `100dvh` and the Nexus atmosphere is hidden |

**Gap.** Nexus has no mobile navigation pattern. The words Overview / Modules / Activity remain in the command bar at 860 px and below. At very narrow widths the bar's left cluster, navigation and right cluster can exceed the viewport. This has not been verified as a no-horizontal-overflow layout in this documentation pass. **RECOMMENDED:** contribute a Nexus mobile navigation treatment (overflow menu or compact icon row) before claiming phone parity.

## 3. Content modules

### Chronos

- `≤ 1100px`: search field narrows to 200 px.
- `≤ 960px`: `.app` becomes one column; `.sidebar` is fixed off-canvas, `top/bottom/left: var(--frame)`, width `min(300px, 84vw)`, translated off-screen, revealed by `.app.nav-open`; `.menu-toggle` appears. Escape closes the nav. There is no scrim element for the nav, so the page behind remains visible and pointer-active; this is a gap, not a pattern to copy.
- `≤ 640px`: `--frame: 8px`, `--gutter: 46px`, `--hour: 50px`; the top bar wraps, the title takes a full row, search takes a full row, and the view switcher becomes full-width; the month grid keeps six rows at a 76 px minimum.

### Lyra

- `≤ 1100px`: the title body becomes one column.
- `≤ 900px`: one-column app; off-canvas nav (`min(290px, 84vw)`), menu toggle appears; the spotlight drops its poster column. Escape closes the nav; no scrim.
- `≤ 640px`: `--poster-w: 124px`, `--frame: 8px`; top bar 52 px; search flexes; page padding reduces; spotlight/hero radius and type reduce; title 32 px.

### Meridian

- `≥ 1100px`: dashboard two columns (`minmax(360px, 5fr) / minmax(0, 7fr)`, 48 px gap); profile content + 360 px context; context sticky.
- `≥ 1760px`: context 380 px, page padding 56 px; a rich profile flows into two CSS columns.
- `≥ 2200px`: page max 2040 px, context 420 px, type one step up (per `meridian/docs/DESIGN.md`).
- `≤ 1099px`: profile header reduces to two columns and actions move below; dashboard one column.
- `≤ 960px`: rail becomes 64 px icons; labels, counts and captions hide; nav items 46 px square.
- `≤ 760px`: directory rows drop the time column and move contact details under the name; import decisions become a full-width row; trash actions wrap.
- `≤ 640px`: one-column app with a horizontal rail; brand hidden; nav is a scrollable row of 40 px items; quick search hidden; sheet becomes non-flex, `min-height: calc(100vh - 140px)`, `overflow: visible`; body overflow returns to `auto`; page padding 24/16/60; display 28 px; the side panel becomes a near-full-viewport sheet (top 44 px, left/right/bottom frame insets).
- The comparison table stacks into labelled blocks below 640 px; no horizontal page scroll is documented and tested for the Stage B screens.

### Argus

- `≤ 1099px`: photo layout becomes one column; stage image max-height 62vh.
- `≤ 760px`: one-column app with horizontal rail; brand name hides; nav items auto width; sheet `overflow: visible`; page padding 18/14/56; display reduces to 26 px; grid min tile 104 px; facts labels 96 px.
- The rail scrolls horizontally; this is the module's intended phone navigation.

### Mnemosyne

- `≤ 1180px`: home/overview grids become one column; project row drops the due column.
- `≤ 1000px`: sidebar 64 px icon rail; brand text and labels hide; split view becomes 250 px list + content.
- `≤ 760px`: body overflow `auto`; sidebar becomes a horizontal bar; brand, daily link and apps menu hide; nav ink hides and the active item gets a solid accent fill; sidebar footer moves to the right; split panes stack; list/tree panes cap at 38vh; drawer full width; capture field wraps.

## 4. Overflow rules

**IMPLEMENTED:**

- `foundation.css` sets `img, svg { max-width: 100% }` and `box-sizing: border-box` globally.
- Chronos, Lyra and Meridian lock `html, body { overflow: hidden }` at desktop and scroll an inner view/sheet. Meridian releases it at 640 px; Mnemosyne releases it at 760 px.
- Meridian values use `overflow-wrap: anywhere`; list rows use `text-overflow: ellipsis` with `min-width: 0`.
- Lyra poster rails scroll horizontally inside their own strip with a mask; the page itself does not.
- Meridian tabs and the phone rail scroll inside their own strip.
- Long tables: Meridian stacks its comparison table at narrow widths rather than widening the page.

**RECOMMENDED floor for a new module:**

1. No horizontal page scroll at 1440, 1024, 768 and 390 px.
2. Every scroll container has `min-width: 0` / `min-height: 0` where it is a grid/flex child, or it will refuse to shrink.
3. Long unbroken values (URLs, IDs, names) get `overflow-wrap: anywhere` or an ellipsis with a title/accessible full value.
4. A horizontally scrolling strip (poster rail, tabs, phone nav) is labelled and keyboard-scrollable; it does not make the page scroll.
5. Fixed-height panes cap their height and scroll internally; they do not clip actions.
6. The primary action remains reachable when the header wraps.

## 5. Touch and pointer differences

**IMPLEMENTED:**

- Meridian reveals row actions at full opacity under `@media (hover: none)`; otherwise they are 50% opacity until hover/focus.
- Argus photo captions show on hover and `:focus-visible`; the tile is a button, so keyboard focus reveals the caption.
- Chronos/Lyra show some icon buttons only on row hover; check `:focus-visible` coverage when adding this pattern.

**RECOMMENDED.** Never hide the only affordance behind hover. On touch, controls must be visible or discoverable by focus. Minimum touch target 34–40 px; `--control-sm` 28 px is a desktop density choice, not a phone target.

## 6. Test matrix

| Width | What to check |
|---|---|
| 1440 px | Default desktop composition; rail/toolbar; content max-width; no unexpected wrapping |
| 1024 px | Rail collapse threshold; table/stack behaviour; header actions |
| 768 px | Off-canvas or icon rail; drawer/panel behaviour; tap targets |
| 390 px | Horizontal nav strip or menu; no horizontal page scroll; title wrapping; dialogs/drawers fit; primary action reachable |

Also test: 200% browser zoom, a long localized string, a long resource name, an empty state, an error state and a loading state at 390 px.

## 7. Gaps and future work

- No shared breakpoint tokens or documented breakpoint scale.
- Nexus has no mobile navigation treatment.
- Chronos and Lyra off-canvas navigation has no scrim or focus trap; Escape closes it, but background content remains reachable.
- The documented 900/600 rule does not match the implemented 960/900 and 640/760 thresholds.
- Meridian's 2200 px type step and Argus's 1600 px page max are module-specific, not ecosystem rules.
- No automated visual regression suite is referenced by this documentation pass; responsive claims come from each repository's own browser checks and CSS.
