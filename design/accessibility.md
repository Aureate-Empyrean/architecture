# Accessibility

The accessibility floor for an Aureate Empyrean application: keyboard operability, visible focus, semantic structure, readable contrast, reduced motion and reduced transparency. Some of this is implemented consistently; some is documented intent or a known gap. This document names which is which.

There is no shared accessibility test suite in this repository. Claims below come from source inspection and from each module's own documentation. Re-test in a browser when changing behaviour.

## 1. Keyboard support

### Shared requirements

**DOCUMENTED** ([design-system.md](../concepts/design-system.md#interaction-principles), Nexus `docs/ui.md`):

- Every action is reachable by keyboard.
- Arrow keys move within lists, grids and menus.
- Enter opens the focused item; Escape closes the top-most layer and restores focus.
- Focus is always visible.
- The main switcher has a global shortcut.

### Implemented shortcuts

| Application | Shortcut | Behaviour | Status |
|---|---|---|---|
| Nexus | `Ctrl/⌘ K` | Opens the application switcher | IMPLEMENTED |
| Nexus | Arrow Up/Down | Moves between module rows; Enter opens details | IMPLEMENTED |
| Nexus | Escape | Closes the open popover (focus returns to trigger), then the drawer | IMPLEMENTED |
| Chronos | `t` | Go to today | IMPLEMENTED |
| Chronos | `m` / `w` / `d` / `a` | Month / week / day / agenda | IMPLEMENTED |
| Chronos | `n` | New event | IMPLEMENTED |
| Chronos | Left/Right, `j` / `k` | Previous / next period | IMPLEMENTED |
| Chronos | `/` | Focus search | IMPLEMENTED |
| Lyra | `/` | Focus search | IMPLEMENTED |
| Lyra | Escape | Close menu; close nav; go back from a title | IMPLEMENTED |
| Meridian | `/` | Focus the visible search field | IMPLEMENTED |
| Meridian | Escape | Close graph filters, menu, panel or dialog in order | IMPLEMENTED |
| Meridian | Ctrl/⌘ + Enter | Save the open side panel | IMPLEMENTED |
| Meridian graph | Arrows, `+`, `−`, `0`, Enter, Escape | Pan, zoom, fit, open selection, clear selection | IMPLEMENTED (documented in `meridian/docs/DESIGN.md`) |
| Argus | Enter on a focused tile | Open the photo | IMPLEMENTED via button semantics |

**Caution.** Shortcuts are document-level and do not cross the iframe boundary. A module must not assume a Nexus shortcut is available inside its frame, and Nexus shortcuts must not fire while a module has focus. Modules suppress single-key shortcuts inside inputs, textareas, selects and `contenteditable` (Chronos, Lyra, Meridian).

**RECOMMENDED.** Keep global single-key shortcuts few and discoverable. Do not bind a bare letter that conflicts with typing. Provide a visible or documented way to reach the same action.

## 2. Focus management

**IMPLEMENTED:**

- `foundation.css` removes the default outline and applies `--focus-ring` on `:focus-visible` (`0 0 0 2px var(--canvas), 0 0 0 4px var(--focus)`). Controls that draw their own shadows force the ring with `!important`.
- Nexus uses `outline: 2px solid var(--focus); outline-offset: 2px` for its local focus style.
- Menus focus the first item on open; Arrow Up/Down cycle; Escape closes and returns focus to the anchor (Chronos, Lyra, Nexus; Meridian has its own menu helper).
- Nexus drawers focus the heading on open and return focus to the module row on close.
- Chronos event drawer focuses the close button and returns focus to the selected event on close (`state.selected.returnFocus`).
- Native `<dialog>` + `showModal()` provides browser-level focus containment and restoration for modal dialogs in Chronos, Lyra, Meridian, Argus and Nexus.
- Meridian's side panel is a custom modal: `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, `#app.inert = true`, a Tab/Shift+Tab trap, Escape, Ctrl/⌘+Enter and focus return to the invoker.

**Known gaps:**

- Chronos and Lyra use `.ae-drawer` with `role="dialog" aria-modal="true"` but do not implement a focus trap or mark the page inert. Focus is moved to the close button, but Tab can leave the drawer. Treat this as a defect to fix, not a pattern.
- Nexus's module-details drawer toggles `aria-hidden` but does not set `inert` on the shell; keyboard focus is returned explicitly.
- Chronos and Lyra off-canvas navigation has no focus trap and no scrim; background content remains focusable.
- Programmatic focus on a page heading after navigation intentionally has no ring in Meridian; do not remove the ring from interactive controls.

**RECOMMENDED.** Use native `<dialog>` where possible. For a custom drawer/panel: label it, move focus in, trap Tab, make the rest inert, close on Escape, and return focus to the invoker. Do not rely on `aria-modal` alone.

## 3. Semantics and structure

**IMPLEMENTED:**

- Landmarks: Nexus has a `main` content area; Chronos has `<aside aria-label="Calendars">` and `<main>`; Lyra uses `<nav aria-label="Lyra">`; Meridian and Argus use labelled `<aside>` and `<main>`/`<nav>`.
- Navigation uses real links or buttons, with `aria-current="page"` on the active destination (Nexus links, Lyra buttons, Meridian buttons, Argus buttons).
- Headings follow a visible hierarchy; page headings receive programmatic focus after navigation (`tabindex="-1"`) in Nexus, Meridian and Argus.
- Form controls have associated `<label>` elements; icon-only buttons have `aria-label`; decorative icons are `aria-hidden="true"`.
- Menus use `role="menu"` / `role="menuitem"`; separators use `role="separator"`.
- Toasts: the page container is `role="status" aria-live="polite"` in Chronos, Lyra, Meridian and Argus. Nexus changes the single toast to `role="status"` for success and `role="alert"` for errors.
- Meridian exposes live counts and tallies (`aria-live="polite"`), form errors (`role="alert"`), a dirty-state label, and a graph live region describing the selection.
- Argus uploads use `aria-live="polite"`.
- Meridian's comparison table uses `<caption>`, `<thead>`, `<th scope="col">` and `<th scope="row">`; below 640 px it becomes stacked blocks with visible labels.

**Gaps:**

- No skip link is implemented in the current shells.
- `<html lang="en">` is the static document language. Chronos, Lyra and Meridian update `document.documentElement.lang` from the active locale; Nexus and Argus do not.
- Chronos's month grid uses `role="grid"`; full ARIA grid keyboard semantics (row/cell navigation) are not implemented across every cell.
- Some custom controls use `<div>`/`<span>` plus click handlers where a native button would be preferable. Prefer native elements in new work.

**RECOMMENDED.**

- One `<main>` per document; `<nav aria-label>` for each navigation region; `<aside aria-label>` for a complementary rail.
- Use native `<button>` and `<a href>`; add roles only when native semantics cannot express the widget.
- Label every input; use `aria-describedby` for hints and errors; set `aria-invalid` on failure.
- Mark the current page with `aria-current="page"`; mark the current step with `aria-current="step"`.
- Keep one polite live region for status and one assertive region for errors; do not put interactive content inside a live region.
- Update `<html lang>` when the interface language changes.

## 4. Contrast

**DOCUMENTED, not recalculated here:**

- The design-system concept records accent contrast against `--layer-1`: gold 8.9, Mnemosyne 5.6, Janus 7.9, Chronos 7.6, Lyra 7.4; `--void` text on each accent at least 5.9. Deep shades are about 3.3:1 and are non-text only.
- Meridian's design document states text levels at least 5.5:1 and copper text at least 4.5:1 on band and raised surfaces.
- ADR 0014 requires body text to keep at least 10:1 contrast over the brightest atmosphere colour when placed on `--surface-glass`.

**IMPLEMENTED safeguards:** atmosphere and glass are dark enough for light text; `--text` is near-white; `--text-2` is the normal secondary; `--text-3` is muted metadata. State is not carried by colour alone in the patterns that matter: status pills pair a dot with a word, Meridian warnings pair colour with an icon and words, graph kinds use shape and line style.

**RECOMMENDED.** Do not use `--text-3` for body copy or essential instructions. Re-check contrast on the actual background: glass over a bright artwork or atmosphere is not the same as glass over `--canvas`. Interactive states must remain distinguishable in greyscale. Focus must be visible on both light artwork and dark glass; use the two-tone `--focus-ring`, not a single accent outline on an accent fill.

## 5. Reduced motion

**IMPLEMENTED:**

- `foundation.css` has a global `prefers-reduced-motion: reduce` block: all animations and transitions drop to 1 ms, iteration count 1, smooth scrolling disabled. Spinners and button busy indicators keep a slower (`1.6s`) rotation so progress remains legible.
- Nexus `motion.js` and its stylesheet remove movement; `prefers-reduced-motion` is handled in `style.css`.
- Meridian documents that its animation helper does nothing, view transitions are skipped and `document.getAnimations()` is empty after navigation, profile open, tab switch and panel open. Its CSS also disables specific image/caption transitions.
- Argus disables tile image and caption transitions.
- Chronos and Lyra inherit the foundation rule.

**RECOMMENDED.** Reduced motion removes movement, not information. Keep a static progress indicator. Do not use an infinite decorative animation. Test with the OS setting, not only by reading CSS.

## 6. Reduced transparency

**IMPLEMENTED:**

- `foundation.css` turns `.ae-glass` and `.ae-glass-bar` into solid `--layer-1`/`--layer-3` and removes `backdrop-filter` under `prefers-reduced-transparency: reduce`; `.ae-surface` becomes `--surface-solid`.
- `@supports not (backdrop-filter…)` provides the same solid fallbacks when blur is unsupported.
- Nexus, Mnemosyne, Meridian and Argus add their own reduced-transparency blocks for their local materials.
- Chronos and Lyra use `ae-surface` / `ae-glass`, so they inherit the foundation fallback.

**RECOMMENDED.** Any new glass recipe must include both fallbacks. A translucent layer without a solid fallback is incomplete.

## 7. Accessibility checklist for a module

1. All navigation and actions work with Tab/Shift+Tab, Enter/Space and arrow keys where expected.
2. Focus is visible on every interactive element, including over artwork and glass.
3. A modal traps focus, is labelled, closes on Escape and returns focus.
4. Menus open with focus in the first item and close with focus on the anchor.
5. The current destination has `aria-current="page"`.
6. Every input has a label; errors are announced and associated with the field.
7. Status and errors use a live region; errors keep the real message.
8. Text meets the module's contrast target on the actual surface; no meaning is colour-only.
9. `prefers-reduced-motion` removes movement but keeps progress readable.
10. `prefers-reduced-transparency` and missing `backdrop-filter` produce solid surfaces.
11. The page works at 390 px and 200% zoom with no horizontal page scroll.
12. The document language is set and updated when the interface language changes.

## 8. Future work

- Shared focus-trap / inert helpers for drawers and panels.
- Skip links in Nexus and module shells.
- Consistent `lang` updates across modules.
- Full ARIA grid semantics for the Chronos calendar, or a simpler semantic table alternative.
- Automated axe or equivalent checks in each module's test suite.
- A documented contrast token policy that distinguishes body, secondary, metadata and disabled text.
