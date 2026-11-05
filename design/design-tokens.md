# Design tokens

Canonical source: [`tokens.css`](tokens.css), version 2.1. Decisions: [ADR 0008](../decisions/0008-shared-design-tokens.md), [ADR 0012](../decisions/0012-design-system-v2.md), [ADR 0014](../decisions/0014-liquid-glass-surfaces.md). The token file is vendored unchanged by each application and loaded before its own styles.

**IMPLEMENTED.** Every current public web application in the ecosystem baseline vendors token set 2.1. Nexus, Mnemosyne, Chronos, Lyra, Meridian, Argus and the Janus Web Vault carry byte-identical copies of `tokens.css`; the Janus desktop client carries an older trimmed copy. A token change is made here and re-vendored; editing a vendored copy is a bug.

**Rule.** Use token names in components. Do not copy raw hex values into module CSS when a token exists. The exceptions are module-owned identity values (accent family, atmosphere motif, resource colours) and module-local layout tokens.

## 1. Colour

### Neutrals and text

| Role | Token | Value | Status |
|---|---|---|---|
| Void / canvas | `--void`, `--canvas` | `#090A0C` | IMPLEMENTED |
| Obsidian / layer 1 | `--obsidian`, `--layer-1` | `#101216` | IMPLEMENTED |
| Graphite / layer 2 | `--graphite`, `--layer-2` | `#191C22` | IMPLEMENTED |
| Elevated / layer 3 | `--elevated`, `--layer-3` | `#222630` | IMPLEMENTED |
| Legacy surface 1 | `--surface-1` | `#0D0F12` | IMPLEMENTED, legacy |
| Legacy surface 2 | `--surface-2` | `#14161B` | IMPLEMENTED, legacy |
| Primary text | `--text` | `#F2F0EA` | IMPLEMENTED |
| Secondary text | `--text-2` | `#A7A9AF` | IMPLEMENTED |
| Muted text | `--text-3` | `#70747D` | IMPLEMENTED |
| Metadata text | `--text-meta` | `color-mix(in srgb, var(--text-2) 45%, var(--text-3))` | IMPLEMENTED |
| Hairline | `--line` | `rgba(255,255,255,.075)` | IMPLEMENTED |
| Strong hairline | `--line-2` | `rgba(255,255,255,.12)` | IMPLEMENTED |
| Hover wash | `--hover` | `rgba(255,255,255,.045)` | IMPLEMENTED |
| Press wash | `--press` | `rgba(255,255,255,.07)` | IMPLEMENTED |

Surface hierarchy: **canvas** (application background) → **layer 1** (panes, sidebars, inputs) → **layer 2** (content blocks, controls) → **layer 3** (raised: menus, popovers, opaque fallback for glass). Hierarchy comes primarily from spacing and typography; surfaces and hairlines support it.

`--text-3` is a muted label colour, not a body-text colour. It is used for metadata, placeholders and inactive icons. **RECOMMENDED:** body copy and essential instructions use `--text` or `--text-2`; verify contrast on the surface actually behind the text, especially over glass and atmosphere gradients.

### Aureate ecosystem identity and default accent

| Role | Token | Value | Status |
|---|---|---|---|
| Aureate Gold | `--aureate` | `#D6AD60` | IMPLEMENTED |
| Solar Gold | `--aureate-bright` | `#F0D58A` | IMPLEMENTED |
| Deep Bronze | `--aureate-deep` | `#8C6734` | IMPLEMENTED |
| Accent | `--accent` | `var(--aureate)` | IMPLEMENTED |
| Accent light / focus | `--accent-hi` | `var(--aureate-bright)` | IMPLEMENTED |
| Accent deep | `--accent-deep` | `var(--aureate-deep)` | IMPLEMENTED |
| Accent hover | `--accent-hover` | `rgba(214,173,96,.09)` | IMPLEMENTED |
| Accent soft | `--accent-soft` | `rgba(214,173,96,.14)` | IMPLEMENTED |
| Accent selection | `--accent-sel` | `rgba(214,173,96,.18)` | IMPLEMENTED |
| Accent line | `--accent-line` | `rgba(214,173,96,.45)` | IMPLEMENTED |
| Focus | `--focus` | `var(--accent-hi)` | IMPLEMENTED |
| Secondary atmosphere hue | `--accent-2` | `var(--aureate-deep)` | IMPLEMENTED (v2.1) |
| Accent glow | `--accent-glow` | `color-mix(in srgb, var(--accent) 36%, transparent)` | IMPLEMENTED (v2.1) |

The defaults are the ecosystem gold, which is Nexus's accent. Gold remains part of the ecosystem identity in every application, but a module's own accent does not compete with it inside that module.

**IMPLEMENTED module accents.** These are applied in each module's own `:root`, not in `tokens.css`:

| Application | `--accent` | `--accent-hi` | `--accent-deep` | `--accent-2` |
|---|---|---|---|---|
| Nexus | `#D6AD60` (default) | `#F0D58A` (default) | `#8C6734` (default) | `#8C6734` (local `:root` in style.css) |
| Mnemosyne | `#8B7CF6` | `#B7AEFF` | `#5B4FC4` | `#C99A6B` (added in the 2.1 composition layer) |
| Chronos | `#7AA7EB` | `#B9D2F7` | `#3E68A8` | `#8F86F0` |
| Lyra | `#C98BE0` | `#E6C2F1` | `#8A4FA3` | `#6C63E8` |
| Meridian | `#C48663` | `#E3B497` | `#8C5638` | `#5D7389` |
| Argus | `#C48663` | `#E3B497` | `#8C5638` | `#5D7389` |
| Janus Web Vault | `#4FB8B0` | `#8AD8D1` | `#2E7F79` | `#26386E` |

**DOCUMENTED contrast.** The design-system concept records WCAG 2.x contrast against `--layer-1` as: gold 8.9, Mnemosyne 5.6, Janus 7.9, Chronos 7.6, Lyra 7.4; `--void` text on each accent is at least 5.9. The deep shades are about 3.3:1 and are for non-text use only. These figures come from the architecture documentation and were not recalculated for this guide. Meridian's design document separately states that its text levels are at least 5.5:1 and copper text at least 4.5:1 on band and raised surfaces (`meridian/docs/DESIGN.md`). Treat all figures as claims to re-check when colours change.

**RECOMMENDED.** A new accent must reach at least 4.5:1 as text on `--layer-1` and `--surface-glass`, and `--void` text on the accent must remain readable. Do not use a deep accent shade for text.

### Semantic states

| Role | Token | Value | Status |
|---|---|---|---|
| Success / healthy | `--good` | `#8FC2A9` | IMPLEMENTED |
| Success wash | `--good-soft` | `rgba(143,194,169,.12)` | IMPLEMENTED |
| Danger text | `--danger` | `#EC9DA3` | IMPLEMENTED |
| Danger wash | `--danger-soft` | `rgba(236,157,163,.1)` | IMPLEMENTED |
| Danger strong fill | `--danger-strong` | `#B8454F` | IMPLEMENTED |
| Warning | `--warn` | `#E8B36F` | IMPLEMENTED |
| Warning wash | `--warn-soft` | `rgba(232,179,111,.12)` | IMPLEMENTED |

Semantic colours are identical in every application. They are separate from the module accent and from user-chosen resource colours. **RECOMMENDED:** do not use semantic colours as decoration, and never carry meaning by colour alone.

### Materials and translucency

| Role | Token | Value | Status |
|---|---|---|---|
| Floating glass, bars | `--glass-1` | `rgba(18,20,25,.74)` | IMPLEMENTED |
| Floating glass, panels | `--glass-2` | `rgba(30,33,41,.8)` | IMPLEMENTED |
| Glass hairline | `--glass-line` | `rgba(255,255,255,.09)` | IMPLEMENTED |
| Glass top highlight | `--glass-highlight` | `inset 0 1px 0 rgba(255,255,255,.055)` | IMPLEMENTED |
| Bar blur | `--blur-1` | `14px` | IMPLEMENTED |
| Floating blur | `--blur-2` | `26px` | IMPLEMENTED |
| Glass saturation | `--glass-saturate` | `135%` | IMPLEMENTED |
| Content glass | `--surface-glass` | `rgba(14,16,21,.58)` | IMPLEMENTED (v2.1) |
| Strong content glass | `--surface-glass-strong` | `rgba(19,21,27,.82)` | IMPLEMENTED (v2.1) |
| Solid fallback | `--surface-solid` | `#111318` | IMPLEMENTED (v2.1) |
| Surface hover | `--surface-hover` | `rgba(255,255,255,.045)` | IMPLEMENTED (v2.1) |
| Surface edge | `--edge` | `rgba(255,255,255,.085)` | IMPLEMENTED (v2.1) |
| Strong edge | `--edge-strong` | `rgba(255,255,255,.14)` | IMPLEMENTED (v2.1) |
| Edge highlight | `--edge-highlight` | `inset 0 1px 0 rgba(255,255,255,.07)` | IMPLEMENTED (v2.1) |
| Content blur | `--blur-surface` | `22px` | IMPLEMENTED (v2.1) |
| Ambient strength | `--ambient-strength` | `10%` | IMPLEMENTED |

**Documented translucency rules** ([ADR 0014](../decisions/0014-liquid-glass-surfaces.md)):

- One fixed atmosphere layer per application.
- Navigation, toolbars, content panels and cards may use elevated glass; transient layers use the stronger glass.
- Text never sits on glass lighter than `--surface-glass`; body text keeps at least 10:1 contrast over the brightest atmosphere colour.
- At most three translucent layers overlap.
- Solid fallbacks are mandatory without `backdrop-filter` and under `prefers-reduced-transparency: reduce`.
- Floating glass must not be a descendant of another element with `backdrop-filter`, `filter`, `mask` or `opacity` below 1. The command bar therefore keeps its blur on `::before`.

These are binding design decisions. The implementation uses them in Nexus, Mnemosyne, Chronos, Lyra, Meridian and Argus, but the exact material recipes differ per application; those variations are documented in [theming.md](theming.md#4-material-recipes-in-use).

### Colour values that are not tokens

**IMPLEMENTED, module-scoped:**

- User-selected resource colours (Mnemosyne categories/projects, Chronos calendars, Meridian avatar tones) and Chronos's event palette. These are data, not theme tokens.
- Meridian's avatar tones and graph colours (`meridian/docs/DESIGN.md` § 2 and § 22).
- Hard-coded local values for atmosphere gradients, brand marks and glass recipes in each stylesheet.

**RECOMMENDED:** keep data colours out of the token layer. A resource colour must not silently acquire a semantic meaning.

## 2. Typography

| Token | Value | Status |
|---|---|---|
| `--font-ui` | `"Roboto", "Noto Sans", "Segoe UI", "Helvetica Neue", system-ui, sans-serif` | IMPLEMENTED |
| `--font-display` | `var(--font-ui)` by default; modules may override | IMPLEMENTED |
| `--font-mono` | `"Roboto Mono", ui-monospace, "SFMono-Regular", Menlo, Consolas, monospace` | IMPLEMENTED |
| `--text-xs` | `11px` | IMPLEMENTED |
| `--text-sm` | `12.5px` | IMPLEMENTED |
| `--text-base` | `14px` | IMPLEMENTED |
| `--text-md` | `15px` | IMPLEMENTED |
| `--text-lg` | `18px` | IMPLEMENTED |
| `--text-xl` | `22px` | IMPLEMENTED |
| `--text-2xl` | `28px` | IMPLEMENTED |
| `--text-3xl` | `36px` | IMPLEMENTED |
| `--text-display` | `44px` | IMPLEMENTED (v2.1, available; not used by every module) |
| `--leading-tight` / `--leading` | `1.2` / `1.5` | IMPLEMENTED |
| `--weight-light` | `300` | IMPLEMENTED (v2.1) |
| `--weight-regular` / `--weight-medium` / `--weight-semibold` / `--weight-bold` | `400` / `500` / `600` / `700` | IMPLEMENTED |
| `--tracking-tight` / `--tracking-caps` | `-0.012em` / `0.08em` | IMPLEMENTED |

**IMPLEMENTED.** Roboto is the functional face. Nexus serves a variable Roboto subset from `/assets/fonts/Roboto.woff2`. Chronos, Lyra, Meridian and Argus inline a base64 `data:font/woff2` face when serving their sandboxed page. Janus serves its own font files. A module that relies on an installed system Roboto is still readable through the fallback stack but is not following the bundling rule.

**IMPLEMENTED module choices.** The shared scale is a starting point, not a cap:

| Application | Body | Page heading | Notable choices |
|---|---|---|---|
| Nexus | 14 px | 32 px (hero 38 px) | Optional local "Greater Theory" poster face for fixed `EMPYREAN NEXUS` / `AUREATE EMPYREAN` marks only |
| Mnemosyne | 14 px | — | Optional local poster face for fixed brand strings only |
| Chronos | 14 px | 26 px | Compact calendar hierarchy |
| Lyra | 14 px | 40 px | Cinematic display type; section headings 22 px |
| Meridian | 15 px | 34 px (`--m-fs-display`), person name 38 px | Nothing readable below 12 px; 12 px only for uppercase labels |
| Argus | 15 px | 34 px (`--m-fs-display`) | Follows Meridian's type scale |

**RECOMMENDED.** New content modules use the token scale and keep body text at `--text-base` or `--text-md` (14–15 px). Do not introduce a second body size for the same role. Use `--font-mono` for identifiers, code and tabular technical values, and the `.ae-num` helper or `font-variant-numeric: tabular-nums` for changing numbers. Uppercase labels use `--text-xs` with `--tracking-caps`.

**OPTIONAL.** A module may define its own display scale for its identity (Lyra's 40 px page title, Meridian's 38 px person name) as long as body hierarchy, wrapping and contrast remain sound.

## 3. Spacing, radii, controls and elevation

| Group | Tokens | Values | Status |
|---|---|---|---|
| Spacing | `--space-1` … `--space-8` | 4, 8, 12, 16, 20, 24, 32, 48 px | IMPLEMENTED |
| Radii | `--radius-xs` | 5 px | IMPLEMENTED |
| | `--radius-sm` | 7 px | IMPLEMENTED (v1 name/value) |
| | `--radius` | 10 px | IMPLEMENTED (v1 name/value) |
| | `--radius-lg` | 14 px | IMPLEMENTED |
| | `--radius-xl` | 20 px | IMPLEMENTED |
| | `--radius-pill` | 999 px | IMPLEMENTED |
| Controls | `--control-sm` / `--control` / `--control-lg` | 28 / 34 / 40 px | IMPLEMENTED |
| Elevation | `--shadow-1` | `0 1px 2px rgba(0,0,0,.4)` | IMPLEMENTED |
| | `--shadow-2` | `0 10px 28px rgba(0,0,0,.42), 0 1px 3px rgba(0,0,0,.35)` | IMPLEMENTED |
| | `--shadow-3` | alias of legacy `--shadow` | IMPLEMENTED |
| | `--shadow-glow` | `0 10px 30px -6px var(--accent-glow)` | IMPLEMENTED (v2.1) |
| | `--shadow-float` | `0 24px 60px -20px rgba(0,0,0,.75), 0 2px 6px rgba(0,0,0,.35)` | IMPLEMENTED (v2.1) |
| Focus | `--focus-ring` | `0 0 0 2px var(--canvas), 0 0 0 4px var(--focus)` | IMPLEMENTED |

**Foundation defaults.** Buttons and inputs use `--radius-sm` (7 px); menus use `--radius` (10 px); dialogs, drawers and `ae-surface` use `--radius-xl` (20 px). This is the canonical primitive scale, not the whole ecosystem's radius scale.

**IMPLEMENTED module radius scales.** These are local tokens and values, not shared tokens:

| Application | Control | Group / list | Panel / surface | Floating |
|---|---|---|---|---|
| Nexus | 9 px (`--r-control`) | 12 px | 16 px (`--r-panel`) | 18 px (`--r-float`) |
| Mnemosyne | 9 px (`--r-control`) | 12 px (`--r-group`) | 18 px (`--r-surface`) | — |
| Chronos | 12–14 px | 16 px | 20 px top bar / 24 px rail and view | 18 px |
| Lyra | 14 px | 20 px top bar | 20–30 px | — |
| Meridian | 9 px (`--m-r-control`) | 12 px (`--m-r-group`), 14 px list | 16 px (`--m-r-surface`) | — |
| Argus | 9 px (`--m-r-control`) | 12 px (`--m-r-group`), 14 px list | 16 px (`--m-r-surface`) | — |

**Rule.** Do not describe one radius scale as shared. The foundation's 7/10/20 px scale and the newer modules' 9/12/16/18 px scale both exist. **RECOMMENDED:** pick one scale for a new module and apply it consistently to controls, groups, panels and floating layers. Do not mix 7 px foundation controls with 16 px module panels unless the contrast is intentional.

**Borders.** Hairlines are `--line` / `--line-2`; glass edges use `--edge` / `--edge-strong` plus `--edge-highlight`. Coloured borders are reserved for state (accent selection, danger error, warning notice), not for decoration. **RECOMMENDED:** use one hairline weight per hierarchy level.

**Elevation.** Depth comes from tone steps and hairlines first, shadows second. `ae-surface` combines an edge highlight with `--shadow-float`. `ae-glass` uses `--glass-highlight` plus `--shadow-2`. Avoid a shadow on flat list rows and inside a surface.

## 4. Motion

| Token | Value | Status |
|---|---|---|
| `--motion-fast` | `120ms` | IMPLEMENTED |
| `--motion-base` | `180ms` | IMPLEMENTED |
| `--motion-slow` | `230ms` | IMPLEMENTED |
| `--motion-spatial` | `240ms` | IMPLEMENTED (v2) |
| `--ease-out` | `cubic-bezier(.2,.8,.2,1)` | IMPLEMENTED |
| `--ease-in` | `cubic-bezier(.4,0,1,1)` | IMPLEMENTED |
| `--ease-spring` | `cubic-bezier(.34,1.4,.64,1)` | IMPLEMENTED |

Micro feedback uses `--motion-fast`; ordinary transitions use `--motion-base`; drawers, page changes and other spatial moves use `--motion-slow` or `--motion-spatial`. Ease out when entering, ease in when leaving. Motion never delays an action.

**IMPLEMENTED.** `foundation.css` collapses animation and transition durations to 1 ms under `prefers-reduced-motion: reduce`, except spinners, which keep a slower rotation. Meridian's design document records that `document.getAnimations()` is empty after its tested interactions. Chronos, Lyra and Argus inherit the foundation rule.

## 5. Layers

| Token | Value | Status |
|---|---|---|
| `--z-sticky` | `10` | IMPLEMENTED |
| `--z-drawer` | `40` | IMPLEMENTED |
| `--z-dialog` | `50` | IMPLEMENTED |
| `--z-menu` | `60` | IMPLEMENTED |
| `--z-toast` | `70` | IMPLEMENTED |
| `--z-tooltip` | `80` | IMPLEMENTED |

**RECOMMENDED:** keep the order. A module that adds its own overlays should map them onto these layers or stay below `--z-drawer`; do not invent a layer above tooltips for decoration.

## 6. Iconography

**IMPLEMENTED.**

- Icons are inline SVG, drawn by each application. There is no shared icon package or sprite service.
- The content modules use a 20 × 20 `viewBox`, `fill="none"`, `stroke="currentColor"`, `stroke-width="1.6"`, round caps and joins, and `aria-hidden="true"`; display size is usually 18 × 18 px (`.i`). Nexus uses a 20 × 20 grid with 16 × 16 display size (`svg.i`).
- Application icons are separate. A module serves a square, full-bleed tile (`frontend/icon.svg`) in its own colours. Nexus applies the corner radius and edge light. Current icons use a 48 × 48 viewBox (Argus uses 64 × 64).
- Icon strokes use a dark ink colour on the accent tile (for example `#1b1408` for Nexus, `#0b1424` for Chronos, `#1a0f20` for Lyra, `#241309` for Meridian and Argus).
- Icons often carry meaning together with words. An icon-only button has an accessible name (`aria-label`).

**Binding.** The manifest icon is validated by Nexus: at most 64 KiB, square, full-bleed, static SVG (no script, `foreignObject`, `style`, image, link or animation) or a PNG/WebP with a valid signature. See [module-ui-contract.md](module-ui-contract.md#8-application-icon).

**RECOMMENDED.** Use 1.6 px strokes at 20 × 20, 18 px display size, round caps and joins; align optical weight with text. Do not mix filled and outline icons in the same toolbar. Do not use an icon as the only label for a destructive or ambiguous action.

## 7. Module-local token layers

**IMPLEMENTED, not shared.** Meridian and Argus define a parallel `--m-*` layer in their own stylesheets: surfaces (`--m-canvas`, `--m-well`, `--m-sheet`, `--m-band`, `--m-raised`, `--m-overlay`), text (`--m-text`, `--m-text-2`, `--m-text-3`, `--m-accent-text`), type (`--m-fs-*`), spacing (`--m-s1…--m-s9`), radii (`--m-r-*`), motion (`--m-fast`, `--m-hover-time`, `--m-page`, `--m-panel`, `--m-ease*`) and layout (`--rail`, `--frame`, `--m-context`, `--m-page-max`, `--m-page-pad`). Chronos, Lyra, Nexus and Mnemosyne define smaller local layout sets (`--sidebar`/`--nav`, `--topbar`, `--frame`, `--content`, `--r-*`, `--lens-*`).

These layers are legitimate module design, not candidate shared tokens. **RECOMMENDED:** a new module may add a small prefixed layer for its own layout and identity, but should use the canonical tokens for shared colour, type, spacing and motion values wherever they fit.

## 8. Do not invent

The following do not exist in the current token set and must not be presented as available:

- A light palette, `data-theme` contract or automatic theme switching.
- Breakpoint, container or grid tokens.
- Spacing values beyond the 4 px scale as shared tokens.
- A fourth elevation level, a second shadow family or an opacity scale.
- Additional semantic colours beyond good/warn/danger.
- A shared icon-name registry or icon font.
- Per-module accent tokens registered with Nexus.
