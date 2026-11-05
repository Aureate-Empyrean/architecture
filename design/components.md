# Components

Reusable interface patterns in the current implementations. `foundation.css` defines the shared `ae-` primitives; Nexus and Mnemosyne keep local component styles from before the foundation and restyle them. The newer modules (Chronos, Lyra, Meridian, Argus) use the `ae-` primitives and add module-specific compositions.

Status labels are defined in [README.md](README.md#how-to-read-the-status-labels). "Binding" means required for a Nexus-connected application; "visual" means a default or recommendation. See [module-ui-contract.md](module-ui-contract.md) for the binding list.

## Component inventory

| Pattern | Shared implementation | Status | Notes |
|---|---|---|---|
| Buttons | `.ae-btn` variants | IMPLEMENTED in foundation; used by Chronos, Lyra, Meridian, Argus | Nexus and Mnemosyne use local button classes |
| Text fields, selects, textareas | `.ae-field`, `.ae-input`, `.ae-select`, `.ae-textarea` | IMPLEMENTED in foundation; used by newer modules | Nexus and Mnemosyne have local input styles |
| Checkbox / switch | `.ae-check`, `.ae-switch` | IMPLEMENTED in foundation; used by newer modules | Native inputs with `appearance: none` |
| Segmented control | `.ae-seg` | IMPLEMENTED in foundation; used by Chronos, Lyra, Argus | Meridian uses its own `.seg` / `.seg-btn` |
| Chips / pills | `.ae-chip`, `.ae-pill` | IMPLEMENTED in foundation; used by newer modules | Status pills and filters |
| Cards | `.ae-surface` | IMPLEMENTED as a material, not a card API | Lyra `.card`, Argus `.tile`, Meridian `.person-tile` are module patterns |
| Lists / tables | — | Not in foundation | Meridian `.list` / `.vrow` / `.cmp-table`; Nexus `.module-row`; Argus `.facts` |
| Menus | `.ae-menu`, `.ae-menu-item`, `.ae-menu-sep` | IMPLEMENTED in foundation; used by newer modules | Each app adds positioning and keyboard handling |
| Dialogs | `.ae-dialog` | IMPLEMENTED in foundation; used by newer modules | Native `<dialog>`; Nexus has a local dialog |
| Drawers | `.ae-drawer`, `.ae-scrim` | IMPLEMENTED in foundation; used by Chronos, Meridian | Nexus has a local drawer |
| Toasts | `.ae-toasts`, `.ae-toast` | IMPLEMENTED in foundation; used by newer modules | One live region per page |
| Banners | `.ae-banner` | IMPLEMENTED in foundation; used sparingly | Meridian uses `.notice` for the same role |
| Empty states | `.ae-empty` | IMPLEMENTED in foundation; overridden by Chronos, Lyra, Meridian, Argus | Module treatments differ |
| Loading | `.ae-spinner`, `.ae-skeleton`, `.ae-progress` | IMPLEMENTED in foundation; used by newer modules | Meridian adds `.skeleton` |
| Tooltips | `[data-tip]` | IMPLEMENTED in foundation | Hover/focus, delayed |
| Tabs | — | Not in foundation | Meridian uses ARIA tabs; Lyra/Chronos use navigation/views |
| Avatar | — | Not in foundation | Meridian only |
| Table | — | Not in foundation | Meridian comparison table only |

**RECOMMENDED.** Prefer an existing `ae-` primitive. Add a module-local class only for composition (layout, media, identity), not to re-implement a button, field or dialog. If a primitive is missing, build it locally and record the gap; do not add it to `foundation.css` without an ADR-level decision.

## 1. Buttons

**IMPLEMENTED** (`foundation.css`):

- `.ae-btn` — default glass-secondary button, height `--control` (34 px), radius `--radius-sm` (7 px), 16 px icons.
- `.ae-btn--primary` — accent gradient with dark text and `--shadow-glow`; the one-primary-per-surface rule is a convention, not enforced by CSS.
- `.ae-btn--quiet` — transparent, secondary text; section actions and row actions.
- `.ae-btn--danger` — danger text on a danger wash; destructive confirmation actions.
- `.ae-btn--sm` / `.ae-btn--lg` / `.ae-btn--icon`.
- `.is-busy` — hides the label and shows a spinner while keeping the button size; pair with `disabled` and `aria-busy="true"`.

Example from Chronos:

```html
<button class="ae-btn ae-btn--primary" data-act="plan">
  <svg class="i" aria-hidden="true">…</svg><span>Plan in my calendar</span>
</button>
<button class="ae-btn ae-btn--quiet ae-btn--icon" aria-label="More actions" aria-haspopup="menu">
  <svg class="i" aria-hidden="true">…</svg>
</button>
```

**IMPLEMENTED deviations.** Nexus uses local `button`, `.primary`, `.quiet`, `.danger` classes with `--r-control` (9 px) and its own gradient. Mnemosyne has its own button styles. They are visually close but are not the foundation primitive; do not copy their markup into a new foundation-based module.

**RECOMMENDED.**

- One primary button per surface or region. Put rare and destructive actions in an overflow menu with a confirmation.
- A destructive button names the action ("Delete permanently"), not "OK".
- A busy button keeps its width and label space; do not replace it with a global spinner.
- Icon-only buttons need an `aria-label` and a visible focus ring.
- Minimum hit area: `--control-sm` is 28 px; for touch, prefer 34–40 px.

**OPTIONAL.** A module may restyle button radii, gradients and hover treatment through its own stylesheet. It must preserve the focus ring, disabled state, busy state and one-primary discipline.

## 2. Forms

**IMPLEMENTED** (`foundation.css`):

- `.ae-field` — grid with a 6 px gap; label, control, hint and error.
- `.ae-label`, `.ae-hint`, `.ae-error-text`.
- `.ae-input`, `.ae-select`, `.ae-textarea` — 34 px control, 7 px radius, inset shadow, accent focus ring.
- `.ae-check` — 18 px checkbox; accent fill with a dark check.
- `.ae-switch` — 34 × 20 px switch for immediate settings.
- `.ae-seg` — segmented control; buttons use `aria-pressed`.
- `.ae-chip` — filter chip; `aria-pressed` for toggles.
- `aria-invalid="true"` turns the border danger-coloured.

Example from Meridian:

```html
<div class="ae-field">
  <label class="ae-label" for="np-given">Given name</label>
  <input class="ae-input" id="np-given" maxlength="120" data-focus autocomplete="off">
</div>
<p class="form-error" id="form-error" role="alert" hidden></p>
```

**IMPLEMENTED.** Chronos sets `aria-invalid` on missing required fields. Meridian uses `role="alert"` for form errors and an `aria-live="polite"` dirty-state label. Lyra uses `.ae-field`, `.ae-hint`, `.ae-error-text` and a password field with `autocomplete="off"`.

**RECOMMENDED.**

- Every control has a visible label. Placeholder text is not a label.
- Group related fields; use a `fieldset` with a legend for radio/checkbox groups.
- Show errors next to the field and in a summary only when the form is long. Keep the server's real message.
- Use `aria-describedby` for hints and error text; set `aria-invalid` on the control.
- Do not use a switch for a destructive or irreversible action.
- Keep forms keyboard-operable and preserve values after a failed submit.

**OPTIONAL.** Two-column form layouts, side panels and inline editing are module choices. Meridian's side panel is the most complete pattern: `role="dialog"`, `aria-modal="true"`, `aria-labelledby`, page `inert`, focus trap, Escape and Ctrl/⌘+Enter, dirty guard and focus return.

## 3. Cards, lists and tables

### Cards

**IMPLEMENTED.** `foundation.css` has no card component. `.ae-surface` is a material (glass, edge, float shadow). Module card patterns:

- Lyra `.card`: artwork-first poster with a separate caption block; the card itself is transparent, the poster carries radius and hover depth.
- Argus `.tile`: square photo tile, 12 px radius, inset hairline, caption gradient on hover/focus.
- Meridian `.person-tile`: raised `--m-raised` tile, 14 px radius, inset hairline, hover lifts to `--m-raised-hover`; no coloured border or glow.
- Chronos event blocks and pills inside the calendar grid; not a generic card.

**RECOMMENDED.** Use a card only when the item is a self-contained object with a preview (media, person, module). Do not wrap every record in a card. A card inside a panel or another card is an anti-pattern. Cards need a hover/focus state that is more than colour alone.

### Lists

**IMPLEMENTED.** Meridian `.list` is the reference record container: raised surface, 14 px radius, hairline edge, rows separated by `--m-line`; `.vrow` uses label/value/actions columns (132 px label, min-height 54 px); `.row` is the interactive variant (min-height 60 px). Nexus uses `.module-row` inside one structural panel. Chronos uses agenda rows and calendar cells. Argus uses a photo grid and a facts list.

**RECOMMENDED.** One list per section; hairlines between rows; metadata in a secondary text token; row actions visible on hover/focus and always visible on touch (`@media (hover: none)`).

### Tables

**IMPLEMENTED.** Only Meridian has a real data table: `.cmp-table` in the import comparison, with `<caption>` (visually hidden), `<thead>`, `<th scope="col">`, `<th scope="row">` and a stacked layout at narrow widths. There is no shared table primitive.

**RECOMMENDED table conventions for a new module:**

- Use a real `<table>` with a `<caption>` (visually hidden if the heading already names it).
- Use `<th scope="col">` and `<th scope="row">`.
- Keep numeric columns right-aligned with tabular numerals.
- On narrow screens, either allow horizontal scrolling inside a labelled region or stack rows with visible labels; do not let the page scroll horizontally.
- Do not use a table for layout.

## 4. Dialogs, drawers and menus

### Dialogs

**IMPLEMENTED.** `foundation.css` styles `.ae-dialog` (max 520 px, `--radius-xl`), `.ae-dialog--wide` (760 px), head/body/foot, `::backdrop` blur, and a rise animation. Newer modules use a native `<dialog class="ae-dialog ae-glass">` with `showModal()`.

```js
const d = document.querySelector("#dialog");
d.className = "ae-dialog ae-glass";
d.innerHTML = html;
if (!d.open) d.showModal();
document.querySelector("[autofocus]", d)?.focus();
```

Nexus uses its own `<dialog id="dialog">`, sets `aria-labelledby`, closes on Escape and outside click, and restores focus through the browser's native dialog behavior.

**RECOMMENDED.**

- Use native `<dialog>` + `showModal()` for modal confirmation.
- Label the dialog with `aria-labelledby`; use `aria-describedby` for the consequence text.
- Move focus to the first field or the safest action (Cancel for destructive confirmations).
- Escape closes the top-most layer; the invoker regains focus.
- Do not stack dialogs; if a flow needs several steps, use one dialog with steps or a panel.

### Drawers

**IMPLEMENTED.** `.ae-drawer` is fixed to the right (440 px, inset 12 px), with `.ae-scrim`. Chronos uses it for event details: `role="dialog" aria-modal="true" aria-labelledby="details-title"`, focus to the close button, Escape closes. Nexus has a local module-details drawer with a 440 px width, `aria-hidden` toggling and focus return to the module row. Meridian uses a custom right-side `.panel` with a focus trap and `inert` page.

**RECOMMENDED.** A drawer is for inspecting or editing one item while keeping the list visible. It needs a labelled close control, Escape handling, focus return and a scrollable body with a fixed foot. It is not a second page.

### Menus and popovers

**IMPLEMENTED.** `.ae-menu` / `.ae-menu-item` / `.ae-menu-sep`, `role="menu"` / `role="menuitem"`, first item focused, ArrowUp/ArrowDown cycling, Escape closes and returns focus to the anchor. Chronos and Lyra implement this locally over the foundation classes. Nexus has its own `.popover` / `.menu` implementation with the same keyboard model.

**RECOMMENDED.** Use a menu for contextual and rare actions, not primary navigation. Keep destructive items last and danger-coloured. Do not nest submenus; use a dialog or a second level if the action needs explanation.

## 5. Feedback and states

### Empty states

**IMPLEMENTED.** `.ae-empty` centres an icon, heading, text and optional actions. Chronos and Lyra override its surface with a glass/radius treatment; Meridian uses `.empty-state` with a 52 px icon tile and at most two actions; Argus uses `.empty`. Nexus uses `.empty-state` / `.empty-compact`.

**RECOMMENDED.** An empty state says what is missing, why, and the one useful next action. Distinguish "nothing yet" from "no matches" and from an error. Keep it within a 420–520 px measure; do not fill the screen with a large illustration.

### Loading

**IMPLEMENTED.** `.ae-spinner` (18 px), `.ae-skeleton` (shimmer), `.ae-progress` (4 px bar). Modules use skeletons for content-shaped loading; buttons use `.is-busy`; Nexus uses a skeleton while a panel loads. Splash screens are plain text (`Opening Chronos…`, `Opening Meridian…`) or a Nexus mark.

**RECOMMENDED.** Use a skeleton only when the shape is known and loading is likely to exceed a moment; otherwise use a quiet spinner or a text status. Do not show a skeleton forever on error. Preserve layout to avoid jumps. Under reduced motion, keep a readable spinner but remove shimmer movement.

### Toasts and banners

**IMPLEMENTED.** `.ae-toasts` is a fixed bottom-centre region, `role="status" aria-live="polite"`; `.ae-toast` is a glass row, `.ae-toast--danger` for failure. Nexus uses `role="status"` for success and `role="alert"` for errors. Meridian adds a leave transition and optional follow-up action.

**RECOMMENDED.** Toasts confirm a completed action and never carry the only copy of important information. Errors keep the real message and a recovery path. Use a banner for persistent page-level conditions; use a toast for transient confirmation.

### Status pills and chips

**IMPLEMENTED.** `.ae-pill` (20 px) with `--accent`, `--good`, `--warn`, `--danger` variants; `.ae-chip` (24 px) for filters, with `aria-pressed` / `.is-active`. Status is usually a dot plus a word, not colour alone.

**RECOMMENDED.** Use a pill for a state, a chip for a filter. Keep the label to one or two words. Do not use a pill as a button unless it is a real toggle with `aria-pressed`.

## 6. Tooltips, icons and identity

**IMPLEMENTED.** `[data-tip]` renders a tooltip on hover/focus after 350 ms; it is text-only and not interactive. Icons are inline SVG, 20 × 20 viewBox, 1.6 px stroke, `aria-hidden="true"`, 18 px display (Nexus 16 px). See [design-tokens.md](design-tokens.md#6-iconography).

**RECOMMENDED.** A tooltip supplements a visible label or an `aria-label`; it never replaces the accessible name. Do not put essential instructions in a tooltip. Use the module's own application icon for identity, not a generic icon font.

## 7. Missing shared components (future work)

The following are **not** in `foundation.css` and should be treated as future work, not implemented functionality:

- Tabs (ARIA tab pattern), toolbar, breadcrumb, pagination.
- Table, card, avatar, badge (beyond pill/chip), tree, split pane.
- A drawer/dialog behavior module (focus trap, scroll lock, return focus) shared across applications.
- A toast queue with deduplication and action support.
- A form validation summary and error-focus helper.
- A theme or density switch.
