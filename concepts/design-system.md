# Design System and Visual Identity

The shared visual language of the ecosystem, and the rule for how individual modules carry their own accent identity. This document defines identity principles and tokens, not components, layouts or frameworks.

## Concept

Two separate things:

- **Shared design language**: the dark foundation, typography, restraint and quality bar that every Aureate Empyrean application shares.
- **Module accent**: the color identity of one application inside that shared language.

```
Aureate Empyrean / Nexus  → shared black/graphite foundation → gold identity
Mnemosyne                 → same shared foundation           → purple/violet identity
Other modules             → same shared foundation           → their own accent (later)
```

## Established decisions

- The visual identity is celestial, black/charcoal, premium and serious, with restrained aureate gold. Not cyberpunk, not crypto, not gaming-launcher; avoid excessive glow, gradients and glass.
- **Every major module may have its own recognizable accent identity** while remaining recognizably part of Aureate Empyrean. This is an ecosystem-wide rule, not something one module owns.
- Nexus / Aureate Empyrean uses **gold** as its primary identity. Gold stays part of the broader ecosystem identity but should not visually compete with a module's own accent inside that module's application.
- **Mnemosyne's module identity is purple/violet**, not Nexus gold.
- The shared neutral and typography tokens are common to all applications (below).
- User-selectable resource colors (e.g. a Note or Project set to red, blue, gold, green) are **separate** from the module accent. Resource customization is not module theme semantics, and color carries no hidden meaning unless the user gives it one.
- Accent use is deliberate and restrained: active navigation, selected states, primary actions, focus states, important interactive highlights, the module's icon/identity, subtle progress/state visualization. Not: accent-colored backgrounds everywhere, neon glow, excessive gradients, cyberpunk or streaming-platform styling, generic SaaS look, accent borders on every card, decoration overwhelming content.

- Interfaces favor information density with restraint: compact, useful, hierarchical, without decorative filler cards, meaningless counts or empty dashboards. UI copy states, identifies, explains a non-obvious action or warns about consequences, and does not expose implementation terminology needlessly. Modules do not imitate another product's look or interaction design.

## Shared tokens

Neutrals and text (shared by all applications):

| Name | Hex |
|---|---|
| Void Black | `#090A0C` |
| Obsidian | `#101216` |
| Graphite | `#191C22` |
| Elevated | `#222630` |
| Primary Text | `#F2F0EA` |
| Secondary Text | `#A7A9AF` |
| Muted Text | `#70747D` |

Aureate ecosystem identity (Nexus primary accent):

| Name | Hex |
|---|---|
| Aureate Gold | `#D6AD60` |
| Solar Gold | `#F0D58A` |
| Deep Bronze | `#8C6734` |

## Module accents

**Mnemosyne** (Planned direction; values approximate and may be refined during design review):

| Name | Hex |
|---|---|
| Mnemosyne Purple | `#8B7CF6` |
| Mnemosyne Lavender | `#B7AEFF` |
| Mnemosyne Deep Purple | `#5B4FC4` |
| Subtle purple surface/tint | derived from the accent at low contrast/opacity on the shared dark surfaces |

No accents are assigned to other modules yet. They are chosen when each module's design is done; none are established here.

## Planned direction

- A shared token set that modules consume, with a module accent family (accent, light, deep, subtle tint) per module.
- Modules keep the same foundation and typography while swapping only the accent family.
- Module accent tokens are centralized in each application so they can be changed later without touching components. Current Mnemosyne values are unchanged.

## Open questions

- Final Mnemosyne token values, and accent families for the other modules.
- Accessibility/contrast requirements for accents on the shared surfaces, and light-theme support, if any.
- Where the shared tokens live in practice (a shared package, per-module copies, served by Nexus) and how community modules adopt them.
- Whether community modules are expected to follow the shared language or only encouraged to.
- Icon and logo system per module.
- Atlas's module accent and map style: the map follows the shared design language, not a data provider's default appearance ([atlas](../modules/atlas.md#map-presentation)). No Atlas accent is assigned yet.
