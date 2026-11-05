# Design System and Visual Identity

The shared visual language of the ecosystem, the rule for how individual modules carry their own accent identity, and the shared foundation every official application builds on. Canonical files: [`design/tokens.css`](../design/tokens.css) (token set v2) and [`design/foundation.css`](../design/foundation.css) (foundation v1). Contributor guide: [`design/README.md`](../design/README.md). Decisions: [ADR 0008](../decisions/0008-shared-design-tokens.md), [ADR 0012](../decisions/0012-design-system-v2.md).

## Concept

Three separate things:

- **Shared design language**: the dark foundation, surface hierarchy, typography, spacing, materials, motion, restraint and quality bar that every Aureate Empyrean application shares.
- **Shared foundation primitives**: buttons, fields, menus, dialogs, drawers, toasts, states, implemented once in `foundation.css`.
- **Module identity**: one accent family plus each application's own layout, navigation and workflows.

```
Aureate Empyrean / Nexus  → shared foundation → gold identity
Mnemosyne                 → shared foundation → violet identity
Chronos                   → shared foundation → celestial blue identity
Lyra                      → shared foundation → orchid identity
Janus                     → shared foundation → teal identity
```

Shared language does not mean identical interaction design. A calendar, a media library and a notes application share how things *feel*, not how they are laid out.

## Established decisions

- The visual identity is celestial, black/charcoal, premium, contemporary and serious, with restrained aureate gold. Not cyberpunk, not crypto, not gaming-launcher, not generic SaaS or admin-panel; no excessive glow or gradients.
- **Liquid glass over an atmosphere** ([ADR 0014](../decisions/0014-liquid-glass-surfaces.md), superseding 0012's floating-only rule): every application has one fixed atmosphere layer (accent and `--accent-2` light over a deep gradient). Navigation, toolbars, content panels and cards may be elevated glass (`ae-surface`, ≥ 66 % dark tint, 22 px blur); transient layers use the stronger `--glass-2`. Text never sits on lighter glass than `--surface-glass` and keeps ≥ 10:1 contrast. At most three translucent layers overlap. Solid fallbacks without `backdrop-filter` or with `prefers-reduced-transparency`.
- **Every major module has its own recognizable accent identity** while remaining recognizably part of Aureate Empyrean. Gold stays part of the broader identity but does not compete with a module's accent inside that module.
- The shared tokens (neutrals, surfaces, text, materials, elevation, type, spacing, radii, controls, focus, layers, motion) are common to all applications.
- User-selectable resource colors (a Note, Project, Calendar or Category set to red, blue, green…) are **separate** from the module accent and from semantic state colors. Color carries no hidden meaning unless the user gives it one.
- Accent use is deliberate and restrained: active navigation, selected states, primary actions, focus, important interactive highlights, the module mark, subtle progress. Not: accent backgrounds everywhere, neon glow, accent borders on every card, decoration overwhelming content.
- Interfaces favor information density with restraint: compact, useful, hierarchical, without decorative filler cards, meaningless counts or empty dashboards. UI copy states, identifies, explains a non-obvious action or warns about consequences, and does not expose implementation terminology needlessly. Modules do not imitate another product's look or interaction design.
- Every interface implements loading, empty and error states, visible keyboard focus and reduced-motion behavior.

## Tokens (v2)

Neutrals and text (unchanged from v1):

| Name | Token | Hex |
|---|---|---|
| Void Black | `--void` / `--canvas` | `#090A0C` |
| Obsidian | `--obsidian` / `--layer-1` | `#101216` |
| Graphite | `--graphite` / `--layer-2` | `#191C22` |
| Elevated | `--elevated` / `--layer-3` | `#222630` |
| Primary Text | `--text` | `#F2F0EA` |
| Secondary Text | `--text-2` | `#A7A9AF` |
| Muted Text | `--text-3` | `#70747D` |

Surface hierarchy: **canvas** (application background) → **layer-1** (panes, sidebars, inputs) → **layer-2** (content blocks, controls) → **layer-3** (raised: menus, popovers, opaque fallback for glass). Hierarchy comes primarily from spacing and typography; surfaces and hairlines (`--line`, `--line-2`) support it.

Added in v2.1 (ADR 0014): `--accent-2` (secondary atmosphere hue, accent family), `--accent-glow`, `--surface-glass`, `--surface-glass-strong`, `--surface-solid`, `--edge`, `--edge-strong`, `--edge-highlight`, `--blur-surface`, `--shadow-glow`, `--shadow-float`, `--text-display`, `--weight-light`. Foundation 1.1 adds `ae-atmosphere`, `ae-surface`, `ae-gradient-text` and modernised controls.

Added in v2: materials (`--glass-1` bars, `--glass-2` floating panels, `--glass-line`, `--glass-highlight`, `--blur-1` 14 px, `--blur-2` 26 px), elevation `--shadow-1…3`, type scale `--text-xs…3xl` (11–36 px, 14 px base), weights, tracking, 4 px spacing `--space-1…8`, radii `--radius-xs` 5, `--radius-sm` 7, `--radius` 10, `--radius-lg` 14, `--radius-xl` 20, pill, controls 28/34/40 px, `--focus-ring`, z-layers, `--motion-spatial` 240 ms. See the file for exact values.

Aureate ecosystem identity (Nexus primary accent): Aureate Gold `#D6AD60`, Solar Gold `#F0D58A`, Deep Bronze `#8C6734`.

## Module accents

| Module | Accent | Light (`--accent-hi`, focus) | Deep | Status |
|---|---|---|---|---|
| Nexus | `#D6AD60` (atmosphere `--accent-2` `#8C6734`, cool counter-light) | `#F0D58A` | `#8C6734` | Established (composition: Nexus 0.1.7) |
| Mnemosyne | `#8B7CF6` (atmosphere `--accent-2` `#C99A6B`, warm lamp) | `#B7AEFF` | `#5B4FC4` | Established (Mnemosyne 1.2.2) |
| Janus | `#4FB8B0` | `#8AD8D1` | `#2E7F79` | Established ([ADR 0009](../decisions/0009-janus-client-delivery.md)) |
| Chronos | `#7AA7EB` (atmosphere `--accent-2` `#8F86F0`) | `#B9D2F7` | `#3E68A8` | Established ([ADR 0012](../decisions/0012-design-system-v2.md)) |
| Lyra | `#C98BE0` (atmosphere `--accent-2` `#6C63E8`) | `#E6C2F1` | `#8A4FA3` | Established ([ADR 0012](../decisions/0012-design-system-v2.md)) |

Contrast (WCAG 2.x) against `--layer-1`: gold 8.9, Mnemosyne 5.6, Janus 7.9, Chronos 7.6, Lyra 7.4; `--void` text on each accent ≥ 5.9. The deep shades are ≈ 3.3:1 and are for non-text use only (fills behind accent text, rules). The translucent derivatives (`--accent-hover`, `-soft`, `-sel`, `-line`) are computed from the accent at 9/14/18/45 % opacity.

Chronos's celestial blue reads as time, sky and clarity; Lyra's orchid reads as the Lyra constellation and stage light. Both sit on either side of Mnemosyne's violet and stay distinguishable in the application switcher.

## Foundation primitives (`foundation.css`)

Prefixed `ae-`, token names only, no layout:

- Base: canvas, text, font smoothing, selection, scrollbars, `:focus-visible` ring.
- Text helpers: `ae-eyebrow`, `ae-meta`, `ae-mono`, `ae-num`, `ae-kbd`, `ae-visually-hidden`.
- Materials: `ae-glass` (floating panels), `ae-glass-bar` (sticky bars), `ae-ambient`.
- Controls: `ae-btn` (`--primary` one per surface, `--quiet`, `--danger`, `--sm`, `--lg`, `--icon`, `.is-busy`), `ae-field`/`ae-label`/`ae-hint`/`ae-error-text`, `ae-input`, `ae-select`, `ae-textarea`, `ae-check`, `ae-switch`, `ae-seg`, `ae-chip`, `ae-pill` (`--accent`, `--good`, `--warn`, `--danger`).
- Floating: `ae-menu`/`ae-menu-item`/`ae-menu-sep`, `ae-popover`, `ae-dialog` (+ head/body/foot, `--wide`), `ae-drawer` + `ae-scrim`, `ae-toasts`/`ae-toast`, `[data-tip]` tooltips.
- States: `ae-banner` (`--info`, `--warn`, `--danger`, `--good`), `ae-empty`, `ae-spinner`, `ae-skeleton`, `ae-progress`.
- Motion: shared keyframes; `prefers-reduced-motion` removes movement while keeping spinners readable.

## Interaction principles

Implementation-level guidance is in each application; the shared rules are:

- Workflows, not schemas ([principles](../principles.md)). One dominant action per surface; rare and destructive actions in menus with confirmation.
- Progressive disclosure: summary first, details in drawers or inspectors with spatial continuity.
- Immediate and truthful: state shown is state confirmed; pending work is labelled; errors keep their real message and offer recovery.
- Keyboard first-class: arrow keys in lists and grids, Enter opens, Escape closes the top-most layer and restores focus.
- Motion ≈ 120 ms micro, 180 ms ordinary, 240 ms spatial; ease-out entering, ease-in leaving; never delays the action.
- Responsive: desktop-first; below 900 px secondary panes collapse into drawers; below 600 px navigation collapses; no horizontal page scroll.

## Token distribution

- Applications vendor `tokens.css` (as `frontend/aureate-tokens.css` or equivalent) and, from foundation v1, `foundation.css` (as `frontend/aureate-foundation.css`) unchanged, load tokens → foundation → own styles, and override only the accent family (`--accent*`, `--focus`) and `--font-display`.
- Roboto is the shared UI face and is bundled by each application, including sandboxed module applications (embedded as a `data:` font).
- A vendored copy that differs from the canonical version it names is a bug.

## Migration of existing applications

Status (2026-11-03): Nexus 0.1.6 and Mnemosyne 1.2.1 completed steps 1 and 4. Both vendor token set 2.1 and carry a "design system 2.1 composition" layer at the end of their own stylesheet: an atmosphere, floating glass navigation and panels, solid fallbacks without `backdrop-filter` and under `prefers-reduced-transparency`. Nexus is the "command center" (gold status read off the atmosphere, one structural panel per screen, a lens command bar, switcher, drawer and dialogs); Mnemosyne is "lamplight" (violet dusk with one warm amber lamp, a floating rail and toolbar, the work on an elevated, nearly opaque sheet so long-form reading and writing stay quiet). Nexus 0.1.7 and Mnemosyne 1.2.2 refine both layers to two materials (quiet structural panels for content, lens glass only for navigation and floating layers), no cards inside surfaces, and one radius scale (controls 9, groups 12, panels 16, floating 18 px). Rule learned there: floating glass must never be a descendant of another element with `backdrop-filter`, `filter`, `mask` or `opacity` below 1, because that element becomes its backdrop root and the blur then sees only it; a bar that contains menus keeps its own glass on a pseudo-element. Steps 2 and 3 (vendoring `foundation.css` and replacing local controls with `ae-` primitives) remain Planned; the local component styles are kept and restyled, so markup, workflows and tests are unchanged.

The original migration path:

1. **Re-vendor tokens v2.** No visual change: v2 is a strict superset of v1.
2. **Vendor `foundation.css`.** No visual change while no `ae-` class is used, apart from the base element rules (selection, scrollbars, focus ring), which must be checked screen by screen.
3. **Migrate components** one surface at a time: replace local button/field/menu/dialog styles with `ae-` primitives; delete the local duplicates; keep layouts and workflows.
4. **Adopt materials** for floating layers only, then review against the translucency rules above.
5. Run each application's browser QA at 1440 px and 390 px after every step.

Gymless and the standalone book-library prototype are visual-quality references; Gymless is the starting point for [Enceladus](../modules/enceladus.md), while books belong to [Lyra](../modules/lyra.md#books-physical-and-digital). Foundation adoption remains deferred; neither prototype is claimed integrated.

## Open questions

- Light theme, if any.
- How community modules adopt the shared tokens and foundation (decided for official applications only), and whether they must.
- Icon and logo system per module; a shared icon set is not chosen (applications draw their own inline SVG icons).
- Atlas's accent and map style ([atlas](../modules/atlas.md#map-presentation)).
- Whether `foundation.css` should later be split (e.g. floating layers vs controls) as it grows.
