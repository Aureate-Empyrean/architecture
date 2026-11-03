# 0012. Design system v2: layered surfaces, restrained translucency and a shared foundation stylesheet

- Status: Accepted (2026-11-02); rule 2 superseded by [0014](0014-liquid-glass-surfaces.md)
- Extends [0008](0008-shared-design-tokens.md); the vendoring rule is unchanged.

## Context

Token set v1 ([0008](0008-shared-design-tokens.md)) unified names and values, but only defined colours, type faces, motion and two radii. Every application still invents its own spacing scale, type scale, elevation, control sizes and component styles. The current applications differ visibly in quality; the separately developed Gymless and the standalone book-library prototype set a more modern, polished bar than Nexus and Mnemosyne. Chronos and Lyra are the first applications built after this observation and need a foundation that reaches that bar without each inventing one.

The established identity ([design-system](../concepts/design-system.md)) says "avoid excessive glow, gradients and glass". The new direction keeps that limit but uses **controlled** translucency where it improves the interface.

## Decision

1. **Token set version 2** in [`design/tokens.css`](../design/tokens.css) is a strict superset of v1: every v1 name keeps its v1 value. It adds a surface hierarchy (`--canvas`, `--layer-1…3`), translucent materials (`--glass-1/2`, `--glass-line`, `--glass-highlight`, `--blur-1/2`), elevation (`--shadow-1…3`), a type scale, weights and tracking, a 4 px spacing scale, radii, control sizes, a focus ring, z-layers, and one spatial motion duration.
2. **Translucency rules.** Glass is a material for **floating layers above content** only: sticky headers over scrolling content, menus, popovers, drawers, dialogs, toasts. It is never used for primary reading surfaces, lists, forms or text blocks, never stacked more than two layers deep, and always has an opaque fallback when `backdrop-filter` is unsupported or `prefers-reduced-transparency: reduce` is set. At most one ambient accent glow per screen, at low strength, behind content.
3. **Foundation stylesheet.** A new canonical file, [`design/foundation.css`](../design/foundation.css), defines base element styling and a small set of prefixed (`ae-`) primitives: buttons, inputs, selects, switches, checkboxes, segmented controls, chips, menus, popovers, dialogs, drawers, toasts, tooltips, banners, empty, loading and skeleton states, focus and reduced-motion behaviour. It uses token names only. Applications vendor it unchanged, like the tokens, loading it after the tokens and before their own styles. It contains **no layouts, navigation shells or workflows**: those stay per application so applications do not look or behave identically.
4. **Module accents** are assigned for the two new applications, on the same rule as before (accent family overridden in the application's own `:root`):
   - **Chronos, celestial blue**: `#7AA7EB` / light `#B9D2F7` / deep `#3E68A8`.
   - **Lyra, orchid**: `#C98BE0` / light `#E6C2F1` / deep `#8A4FA3`.

   Chronos (hue ≈ 215°) and Lyra (≈ 285°) sit on either side of Mnemosyne's violet (≈ 248°) and away from Nexus gold and Janus teal. All five accents reach at least 4.5:1 against `--layer-1` for text and focus use.
5. **Migration.** Nexus and Mnemosyne keep their vendored v1 copies for now; v1 copies are not a bug because they name version 1. Adopting v2 is: re-vendor `tokens.css` (no visual change, since it is a superset), then vendor `foundation.css` and migrate components screen by screen. See [design-system](../concepts/design-system.md#migration-of-existing-applications).

## Consequences

- One stylesheet pair defines what "Aureate quality" means at the component level. Changes are versioned here and re-vendored.
- `foundation.css` is a second vendored file (~500 lines), still far short of a component framework. If it grows beyond primitives, split it rather than adding layouts.
- The architecture summary's "avoid glass" becomes "glass only as a floating material, with fallbacks".
- Gymless and the standalone book-library prototype remain references only; their code is not read or changed.
