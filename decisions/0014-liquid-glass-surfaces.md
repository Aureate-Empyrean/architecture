# 0014. Liquid-glass surfaces and per-application atmosphere

- Status: Accepted (2026-11-02)
- Supersedes rule 2 ("Translucency rules") of [0012](0012-design-system-v2.md); the rest of 0012 stands.

## Context

Chronos and Lyra 0.1.0 were built on token set v2 and foundation v1, yet their rendered interfaces still read as the old Mnemosyne design. The cause was structural, not chromatic:

- 0012 allowed glass only on transient floating layers. Over a flat black canvas that glass is indistinguishable from an opaque panel, so the materials were defined but invisible.
- Nothing gave the canvas light or depth, so there was nothing for translucency to reveal.
- The application shells reused the Mnemosyne layout (full-height opaque sidebar, hairline-divided flat content).
- Foundation v1 shipped opaque legacy controls and no surface or atmosphere primitive.

The product direction asks for a premium, modern interface with meaningful liquid-glass layering, atmospheric gradients and depth, while keeping content readable.

## Decision

1. **Atmosphere.** Each application renders exactly one fixed atmosphere layer (`ae-atmosphere`): soft radial light in its accent and a secondary hue (`--accent-2`, part of the accent family) over a deep vertical gradient. Applications may add their own motif on top of it (Chronos: a sparse starfield; Lyra: a projector-light gradient), but never animated loops.
2. **Elevated glass surfaces are allowed for content.** Navigation rails, toolbars, content panels and cards may use `ae-surface` (`--surface-glass`, 66 % dark tint, 22 px blur, edge highlight, float shadow). Text is never placed directly on lighter or more transparent glass than `--surface-glass`; body text keeps ≥ 10:1 contrast over the brightest atmosphere colour. Transient layers (menus, dialogs, drawers, toasts) use the stronger `--glass-2` with an accent glow.
3. **Fallbacks remain mandatory.** Without `backdrop-filter`, or with `prefers-reduced-transparency: reduce`, surfaces become solid (`--surface-solid`).
4. **Depth budget.** At most three translucent layers overlap (surface → floating layer → its menu). Gradients are for light and identity (atmosphere, primary actions, progress, selected states, artwork fallbacks), not for decorating every card.
5. **Foundation 1.1** ships the primitives (`ae-atmosphere`, `ae-surface`, `ae-gradient-text`) and modernised controls (glass secondary buttons, luminous primary buttons, translucent fields, glass segmented controls). Token set **2.1** adds the matching tokens. Both remain strict supersets, so applications that have not adopted them are unaffected.
6. Shells are designed per application: shared materials, different compositions (Chronos: floating rail and an observatory-style calendar panel; Lyra: a cinematic, artwork-led layout).

## Consequences

- Glass is now visible because something is behind it; it must be checked in screenshots, not only in CSS.
- Performance: one fixed blurred background per surface is acceptable on desktop; heavy nesting is not, hence the depth budget.
- Nexus, Mnemosyne, Janus, Gymless and the standalone book-library prototype are unchanged; adoption follows the migration path in [design-system](../concepts/design-system.md#migration-of-existing-applications).
