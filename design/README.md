# Aureate Empyrean design system — contributor guide

This directory is the implementation-grounded guide to the default Aureate Empyrean interface. It explains how the current applications are built, which parts are shared contracts and which parts are each module's own visual choice, and how a contributor can add a compatible module without flattening the ecosystem into one look.

It does not replace the canonical stylesheets. [`tokens.css`](tokens.css) and [`foundation.css`](foundation.css) remain the source of truth for values and primitives. This guide describes how those files are actually used, where implementations diverge, and what is safe to change. It covers public modules; private development tooling is out of scope.

- Design decisions: [design system concept](../concepts/design-system.md), [ADR 0008](../decisions/0008-shared-design-tokens.md), [ADR 0012](../decisions/0012-design-system-v2.md), [ADR 0014](../decisions/0014-liquid-glass-surfaces.md).
- Module and integration boundaries: [Nexus](../modules/nexus.md), [module contract](../concepts/module-contract.md), [interoperability](../concepts/interoperability.md), [ADR 0001](../decisions/0001-module-ownership-and-domain-ignorant-nexus.md).
- Current versions and verification: [implementation status](../implementation-status.md).

## How to read the status labels

The architecture repository classifies **decisions** as Established, Planned or Open ([README](../README.md#status-vocabulary)). This guide additionally classifies **implementation claims**:

| Label | Meaning |
|---|---|
| **IMPLEMENTED** | Verified in the current source of the named application. The described markup, token or rule exists there. |
| **DOCUMENTED** | Stated in an architecture document or a module design document, but not applied consistently in every current implementation. |
| **RECOMMENDED** | Contributor guidance derived from working implementations. It is not a binding contract unless another document says so. |
| **OPTIONAL** | A visual or structural choice a module may make for its own identity. |

A statement can be both DOCUMENTED and only partly IMPLEMENTED. Where that happens, this guide names the applications that implement it and the applications that do not.

**Binding vs visual.** Integration requirements (the bridge, sandbox, manifest icon, navigation ownership, accessibility floor) are binding for a Nexus-connected application. Visual conventions (rail vs top bar, card vs list, accent hue, radius) are defaults or recommendations. [module-ui-contract.md](module-ui-contract.md) separates the two.

## Design baseline (November 2026)

Source inspection for this guide: 2026-11-06. Version and status claims use the [implementation status](../implementation-status.md) snapshot of 2026-11-05, with production Meridian 0.3.1 verified on 2026-11-06; no browser run or version bump was performed for this documentation pass.

**IMPLEMENTED across the current default interfaces:**

- A dark-only interface. Every shipped token set declares `color-scheme: dark`; there is no light palette and no `prefers-color-scheme` switch.
- Roboto for interface text. Nexus serves it same-origin; sandboxed modules inline a `data:` font in the served page.
- One accent family per application, used for identity and interaction emphasis, not for backgrounds everywhere.
- One fixed atmosphere layer behind the interface, with a module-specific accent and secondary hue.
- Liquid-glass materials for navigation, toolbars, floating layers and (in the newer modules) elevated content surfaces. Solid fallbacks exist for missing `backdrop-filter` and `prefers-reduced-transparency`.
- A desktop-first shell with a content area and module-owned navigation. Nexus uses a horizontal command bar; content modules use a rail or sidebar.
- Shared tokens for neutrals, text, semantic states, type, spacing, radii, controls, focus and motion. `foundation.css` supplies `ae-` primitives for controls, floating layers and states.
- Inline SVG icons drawn by each application. There is no shared icon package.

**Not part of the baseline:** a runtime theme API, a light theme, user-selectable themes, a shared component JavaScript library, shared layout components, a shared icon set, breakpoint tokens, or a shared table/card component.

**Reference use.** Nexus is the reference for the application shell, the module switcher, application mode and the boundary between Nexus chrome and module content. For module **content** design, the newer interfaces (Chronos, Lyra, Meridian, Argus) show the current bar for typography, density, elevated surfaces, responsive behaviour and accessibility. Mnemosyne and older parts of Nexus retain local component styles from before `foundation.css`; they are evidence of the shared language, not a template for new markup.

## Implementation status

| Application | Version | Shared tokens | `foundation.css` | Shell / composition | Design notes |
|---|---|---|---|---|---|
| Nexus | 0.1.9 | 2.1, vendored unchanged | Not vendored; local primitives | Horizontal lens command bar, content panel, drawer, dialogs; application mode | Design-system 2.1 composition layer after legacy rules; [design language](https://github.com/Aureate-Empyrean/empyrean-nexus/blob/main/docs/design-language.md) |
| Mnemosyne | 1.2.3 | 2.1, vendored unchanged | Not vendored; local primitives | Floating glass sidebar and toolbar; elevated work sheet | Violet "lamplight" composition; local controls restyled |
| Chronos | 0.1.1 | 2.1, vendored unchanged | 1.1, vendored unchanged | Floating glass calendar rail and toolbar; elevated calendar view | Celestial-blue accent, starfield motif, `ae-` controls |
| Lyra | 0.1.1 | 2.1, vendored unchanged | 1.1, vendored unchanged | Floating glass rail; sticky glass top bar; artwork-led content | Orchid accent, projector-light motif, `ae-` controls |
| Meridian | 0.3.1 | 2.1, vendored unchanged | 1.1, vendored unchanged | Icon-capable rail plus toolbar and workspace sheet | Copper accent plus a module-local `--m-*` token layer; most extensively documented content system |
| Argus | 0.1.0 | 2.1, vendored unchanged | 1.1, vendored unchanged | Rail plus scrolling sheet | Copper accent and a copied `--m-*` layer from Meridian; simple, content-led |
| Janus Web Vault | Development build | 2.1, vendored unchanged | 1.1, vendored unchanged | Standalone teal application, not hosted by Nexus | Same tokens and foundation; not a Nexus module UI baseline |
| Janus desktop | Development build | Older trimmed token copy | Not vendored | Standalone desktop client | Not evidence for the current web baseline |

Verification basis: canonical and vendored `tokens.css` files are byte-identical in Nexus, Mnemosyne, Chronos, Lyra, Meridian, Argus and Janus Web. `foundation.css` is byte-identical in Chronos, Lyra, Meridian, Argus and Janus Web. Nexus and Mnemosyne do not vendor `foundation.css`; they compose the 2.1 materials over their own component styles.

## Contributor paths

1. **Reproduce the default identity.** Read [design-tokens.md](design-tokens.md) and [layout.md](layout.md), vendor both canonical stylesheets, keep the dark palette, choose one accent family, load tokens → foundation → your styles, and implement the shell pattern from [layout.md](layout.md). The shortest working example is one of the newer module stylesheets, for example `chronos/frontend/style.css` or `meridian/frontend/style.css`.
2. **Build a compatible module.** Read [module-ui-contract.md](module-ui-contract.md) first. It lists the binding bridge, sandbox, manifest-icon, navigation and accessibility requirements. Then use [components.md](components.md) for the shared primitives and [responsive.md](responsive.md) for the layout floor.
3. **Customize tokens and components.** Read [theming.md](theming.md). The supported customization point is the accent family (and optionally `--font-display`) in your own stylesheet. Editing a vendored copy is a bug.
4. **Provide an alternative theme.** There is no runtime theme API. A theme today is a source-level stylesheet or a downstream build that overrides token values. See [theming.md](theming.md#5-alternative-themes).
5. **Maintain accessibility and responsiveness.** [accessibility.md](accessibility.md) and [responsive.md](responsive.md) state what is verified, what is only documented and where the current gaps are.
6. **Preserve Nexus integration.** Module navigation stays inside the module. Nexus owns the top-level URL, the frame and the switcher. Do not append module pages to Nexus navigation. See [layout.md](layout.md#5-navigation-boundaries).

## Documentation map

| Document | Covers |
|---|---|
| [design-tokens.md](design-tokens.md) | Canonical colors, type, spacing, radii, shadows, borders, translucency and icons; module-local token layers |
| [layout.md](layout.md) | Nexus shell, application chrome, content layout, module shells and navigation boundaries |
| [components.md](components.md) | Buttons, fields, cards/lists, tables, dialogs/drawers/menus, empty/loading states and feedback |
| [theming.md](theming.md) | Default theme, accent overrides, alternative themes, light/dark status, safe overrides |
| [responsive.md](responsive.md) | Implemented breakpoints, mobile navigation, overflow rules and test matrix |
| [accessibility.md](accessibility.md) | Keyboard, focus, contrast, semantics, reduced motion and transparency |
| [module-ui-contract.md](module-ui-contract.md) | Binding interoperability requirements vs optional visual conventions |
| [references/README.md](references/README.md) | Reference screens, source paths and a visual-consistency review checklist |
| [tokens.css](tokens.css) / [foundation.css](foundation.css) | Canonical, vendored stylesheets |

## Known gaps and future work

These are **not implemented** and must not be described as available:

- A runtime theme API or a Nexus-delivered theme bundle.
- A light theme or a user-facing appearance setting.
- A shared JavaScript component library, layout library or icon package.
- Breakpoint, container-width or elevation tokens beyond the existing scales.
- A shared table, card, avatar, tab, toolbar, side-panel or chart primitive in `foundation.css`.
- A Nexus `theme` or `appearance` field in the application context. The context currently returns enabled applications, locale and navigation support only.
- Automatic deep-link or navigation-history support for every module. The pilot is default-off and Mnemosyne is the only adapter.
- Skip links and a fully audited focus-trap implementation across every dialog and drawer.
