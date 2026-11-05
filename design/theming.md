# Theming

How the default look is produced, what a module may change today, and what would require new implementation. There is **no runtime theme API** in the current ecosystem. Theming is a source-level stylesheet concern.

Canonical decisions: [ADR 0008](../decisions/0008-shared-design-tokens.md) (vendoring and accent override), [ADR 0012](../decisions/0012-design-system-v2.md) (token set v2 and foundation), [ADR 0014](../decisions/0014-liquid-glass-surfaces.md) (atmosphere and elevated glass). Canonical files: [`tokens.css`](tokens.css), [`foundation.css`](foundation.css).

## 1. The default theme

**IMPLEMENTED.** The default Aureate Empyrean theme is dark, gold-accented and Roboto-based:

- `--canvas` / `--void` `#090A0C` behind everything.
- A fixed atmosphere layer with gold light from above and a deep bronze counter-light; Nexus adds faint orbit rings.
- A floating lens command bar, a structural content panel, and lens glass for menus, the drawer and dialogs.
- Gold (`--accent` `#D6AD60`) for active navigation, selection, focus, primary actions and key status.
- Roboto for all functional text; optional Greater Theory only for fixed uppercase brand marks when installed locally.
- Semantic green/amber/red for state, separate from the accent.
- Dark-only: `color-scheme: dark` in every token copy. No light palette.

The default accent family is also the Nexus accent. Nexus adds a local `--accent-2: #8C6734` and a local lens recipe in its stylesheet; these are not in `tokens.css`.

**DOCUMENTED identity.** Restrained, celestial, premium, serious; no cyberpunk, crypto, gaming-launcher, generic SaaS or admin-panel look; no excessive glow or gradients. Accent is deliberate and restrained, not an accent background everywhere. See [design-system.md](../concepts/design-system.md#established-decisions).

## 2. What a module may override

**Supported by ADR 0008 and implemented in every module:**

- The accent family: `--accent`, `--accent-hi`, `--accent-deep`, `--accent-hover`, `--accent-soft`, `--accent-sel`, `--accent-line`, `--focus`.
- `--accent-2`, the secondary atmosphere hue (v2.1), and optionally `--accent-glow` if the derived value is unsuitable.
- `--font-display`, if the module has a display face. It must remain optional and must not break the fallback.

**Everything else is a module-local design decision, not a supported token override.** A module may define its own layout tokens, surfaces and component styles in its own stylesheet, as Meridian and Argus do. That is different from changing the shared token meanings.

**Critical implementation detail.** The translucent accent derivatives in `tokens.css` are literal gold values:

```css
--accent-hover: rgba(214, 173, 96, 0.09);
--accent-soft:  rgba(214, 173, 96, 0.14);
--accent-sel:   rgba(214, 173, 96, 0.18);
--accent-line:  rgba(214, 173, 96, 0.45);
```

Changing only `--accent` leaves these gold. A correct module override sets **all** of them, as Chronos, Lyra, Meridian and Argus do. `--focus` should alias `--accent-hi` so the focus ring follows the accent.

## 3. Complete accent override

**IMPLEMENTED pattern** (from `chronos/frontend/style.css`; the same shape appears in Lyra, Meridian and Argus):

```css
:root {
  --accent: #7AA7EB;
  --accent-hi: #B9D2F7;
  --accent-deep: #3E68A8;
  --accent-2: #8F86F0;

  --accent-hover: rgba(122, 167, 235, 0.09);
  --accent-soft:  rgba(122, 167, 235, 0.14);
  --accent-sel:   rgba(122, 167, 235, 0.20);
  --accent-line:  rgba(122, 167, 235, 0.45);

  --focus: var(--accent-hi);
}
```

Then load order:

```html
<link rel="stylesheet" href="aureate-tokens.css">   <!-- canonical, unchanged -->
<link rel="stylesheet" href="aureate-foundation.css"><!-- canonical, unchanged -->
<link rel="stylesheet" href="style.css">            <!-- module accent + composition -->
```

**RECOMMENDED.** Choose an accent at least 4.5:1 against `--layer-1` and `--surface-glass`; ensure `--void` text on a solid accent fill is readable; keep the deep shade for fills and rules, not text. Use a secondary hue in the same family rather than an unrelated colour. Give the accent a role: it marks place, selection, primary action and focus, not every icon and heading.

**OPTIONAL.** The accent hue, the secondary atmosphere hue, the module mark, the atmosphere motif (Chronos starfield, Lyra projector beams, Nexus orbits) and the display face. These are identity choices.

## 4. Material recipes in use

**IMPLEMENTED.** These are module-level recipes, not shared tokens. Reproduce one only when you want that module's specific look.

| Application | Navigation / floating | Content surface | Notes |
|---|---|---|---|
| Nexus | `--lens-tint` `rgba(17,16,14,.7)` (strong `.84`), blur `32px saturate(165%) brightness(.78)`, sheen + radial accent light, multi-layer edge shadow | `--panel-bg` `rgba(13,14,17,.74)` with a top sheen and hairline; no blur | Command bar glass on `::before`; application mode uses a stronger tint |
| Mnemosyne | Sidebar/topbar `rgba(19,17,27,.58)`, blur `26px saturate(160%)`; floating layers `--lens-tint` `.7` / strong `.86`, blur `30px` | `--sheet` `rgba(15,14,21,.78)`; inside the sheet no glass | "Lamplight": violet dusk plus one warm `--accent-2` lamp |
| Chronos | `ae-surface` (rail, topbar, view): `--surface-glass` + `--blur-surface` (22 px) | Same `ae-surface` on the calendar view | Starfield fixed layer; solid fallback |
| Lyra | `ae-surface` on rail and topbar; `ae-glass` on menus/dialogs/toasts | Glass panels (`ae-surface`, `episode-card`, `spotlight`, `hero`) | Projector-light fixed layer; artwork carries colour |
| Meridian | Rail/toolbar `rgba(24,21,19,.66)`, blur `24px saturate(150%)`; panels/menus `rgba(30,26,23,.94)`, blur 28 px | `--m-sheet` `#151210`; `--m-raised` lists/tiles; no blur on content | Copper + warm graphite; depth from tone steps and hairlines |
| Argus | Rail/sheet `color-mix(in srgb, var(--m-sheet) 92%, transparent)` + hairline; body atmosphere gradients | Sheet and raised tiles | Copper + warm graphite, copied `--m-*` layer |

**Rule.** A module that uses `ae-surface` gets the canonical glass recipe and its fallbacks for free. A module that writes its own recipe must also write the `@supports not (backdrop-filter…)` and `prefers-reduced-transparency` fallbacks. **RECOMMENDED:** start with `ae-surface` / `ae-glass`; customize only if the module's composition needs it.

## 5. Alternative themes

**There is no runtime theme API.** The current application context does not include a theme, theme name, density or user appearance preference. Nexus does not serve theme bundles. A user cannot select a theme in any current interface. Do not build against an imagined API.

**What a contributor can do today:**

1. **Fork or vendor the module and change its tokens/styles at build time.** This is the only supported route. Keep the canonical token names and values unless the theme intentionally changes them.
2. **Add a downstream stylesheet after the module stylesheet** that overrides `:root` token values and specific `ae-` component properties. This is a source-level override, not a runtime plugin.
3. **Ship a separate module build** with a different accent family and/or material recipe.

Example of a source-level alternate appearance (RECOMMENDED pattern, not an implemented feature):

```css
/* Loaded after the module's own stylesheet. Still a source change. */
:root {
  --canvas: #07080B;
  --layer-1: #0D1015;
  --layer-2: #151A21;
  --layer-3: #1D232C;
  --accent: #9BB7D4;
  --accent-hi: #D6E4F0;
  --accent-deep: #4E6B88;
  --accent-hover: rgba(155, 183, 212, 0.09);
  --accent-soft:  rgba(155, 183, 212, 0.14);
  --accent-sel:   rgba(155, 183, 212, 0.20);
  --accent-line:  rgba(155, 183, 212, 0.45);
  --focus: var(--accent-hi);
}
```

**Limitations to state honestly:**

- Foundation and module stylesheets contain literal `rgba(255,255,255,…)` washes, gradients, shadows and lens recipes. Changing only the neutral tokens will not re-theme those; they must be overridden or the theme will look half-applied.
- Application icons are separate SVG files; a theme does not recolour them.
- The theme must be re-applied after upgrading a vendored module.
- There is no persistence, no user setting, no per-user sync and no Nexus UI for themes.
- A theme that reduces contrast, removes focus rings or disables reduced-motion fallbacks is not acceptable.

## 6. Light/dark behaviour

**IMPLEMENTED:** dark only.

- Every `tokens.css` copy sets `color-scheme: dark`.
- `foundation.css` styles form controls, scrollbars, selection and `input[type=date]` for a dark scheme.
- No stylesheet contains a `prefers-color-scheme` branch or a `[data-theme]` selector.
- Several components assume light text on dark surfaces with literal white-alpha washes and dark shadows.

**DOCUMENTED:** a light theme is an Open question in the [design-system concept](../concepts/design-system.md#open-questions). It is not planned in an ADR and has no token set.

**RECOMMENDED future work:** a light theme is not a token swap. It needs a light neutral scale, light semantic washes, adjusted glass recipes and an audit of every component's literal colours, shadows and selection states. It also needs a decision on whether the atmosphere survives in light mode and how module accents remain distinguishable. Record that decision before implementing.

## 7. Safe overrides

| Change | Status | Guidance |
|---|---|---|
| Accent family in the module's `:root` | Supported | Set all eight accent values; verify contrast |
| `--accent-2` atmosphere hue | Supported | Keep it in the accent family; do not make it an interactive colour |
| `--font-display` | Supported | Optional; keep the UI fallback; use only for fixed brand/display strings |
| Module-local layout tokens | Module-scoped | `--rail`, `--frame`, `--sidebar`, `--topbar`, `--content` are not shared API |
| Module-local component styles after foundation | Module-scoped | Keep the `ae-` class names and expected states; restyle, do not fork behaviour |
| Override `ae-btn`, `ae-input`, `ae-dialog` in a module stylesheet | Allowed with care | Preserve focus, disabled, busy, error and reduced-motion behaviour |
| Edit `aureate-tokens.css` / `aureate-foundation.css` | Forbidden | Change the canonical file and re-vendor |
| Override `--text`, `--layer-*`, `--good`/`--warn`/`--danger` for a theme | Risky | Possible in a source-level theme; requires a full contrast and state audit |
| Add a `theme` or `data-theme` attribute and expect Nexus to set it | Not implemented | No host support exists |
| Use a CSS framework or CDN font | Not compatible with the sandbox | Bundle styles and fonts; the gateway CSP blocks external loads |

## 8. Resource colours are not theming

**IMPLEMENTED.** User-selected colours for a Note, Project, Calendar, Category or avatar tone are data, not accent tokens. They are rendered as inline custom properties or local classes, and they carry no semantic meaning. Do not derive the module accent from user data and do not use resource colours for status.

## 9. Verification checklist for a theme

1. Load order is tokens → foundation → module styles → theme override.
2. Vendored files are unchanged; the module's accent block sets every translucent derivative.
3. Text contrast on `--layer-1`, `--surface-glass` and the brightest atmosphere area meets the module's stated target.
4. Focus rings are visible on every interactive control, including inside glass and over artwork.
5. Hover, pressed, selected, disabled, busy and error states still read.
6. `prefers-reduced-transparency: reduce` makes every glass surface solid and readable.
7. `prefers-reduced-motion: reduce` removes movement but keeps spinners readable.
8. Empty, loading and error states still look intentional.
9. The application icon remains legible at 32, 38 and 48 px.
10. The theme is documented as a source-level customization, not a runtime feature.
