# References and visual-consistency review

Reference screens, source files and a repeatable way to judge whether a module belongs to the default Aureate Empyrean interface. Paths are relative to the ecosystem workspace root (`G:\AureateEmpyrean` in the current checkout) and to each public repository. This architecture repository does not contain application code.

No screenshots are generated or modified by this guide. The screenshot folders listed below already exist in the implementation repositories and were produced by their own browser checks.

## 1. Reference screens

| Screen | Application | Why it is a reference | Source |
|---|---|---|---|
| Overview | Nexus | Command-bar shell, atmosphere, one structural panel, status-first hierarchy | `empyrean-nexus/frontend/app.js` (`renderOverview`), `frontend/style.css`; `docs/screenshots/after-overview.png` |
| Modules list + details drawer | Nexus | List-not-grid, row status, drawer inspection, focus return | `empyrean-nexus/frontend/app.js` (`renderModules`, `openDrawer`, `renderDrawer`); `docs/screenshots/after-drawer-lyra.png` |
| Application mode | Nexus | Full-window module frame, receding command bar, atmosphere handoff | `empyrean-nexus/frontend/style.css` (`.application-mode`), `frontend/app.js` (`renderModuleView`); `docs/screenshots/after-app-mode.png` |
| Month / week / agenda | Chronos | Observatory composition: floating rail and toolbar, elevated calendar view, event pills | `chronos/frontend/app.js` (`shell`), `frontend/style.css`; `docs/screenshots/after-month.png`, `after-week.png`, `after-agenda.png` |
| Event details drawer | Chronos | Drawer semantics, focused close, return focus, projection provenance | `chronos/frontend/app.js` (details renderer); `docs/screenshots/after-details-projection.png` |
| Library / title | Lyra | Artwork-led grid, projector atmosphere, glass panels, display typography | `lyra/frontend/app.js` (`shell`), `frontend/style.css`; `docs/screenshots/` |
| Dashboard / People / Profile | Meridian | Banded screens, lists, tiles, one context surface, type hierarchy, restrained copper | `meridian/frontend/app.js`, `frontend/style.css`; `meridian/docs/DESIGN.md`; `docs/screenshots/stage-a2/`, `stage-b/` |
| Graph / Timeline | Meridian | Dense visual system with non-colour distinctions, keyboard and live regions | `meridian/frontend/graph.js`, `timeline.js`, `docs/DESIGN.md` § 22–23; `docs/screenshots/celestial/`, `timeline/` |
| Photo library / photo view | Argus | Content-led tiles, facts list, simple rail, no card wrapper around every record | `argus/frontend/app.js`, `frontend/style.css`; `argus/docs/ARGUS.md` § 5 |
| Notes / daily view | Mnemosyne | Historical but current violet "lamplight" composition; floating sidebar/toolbar and work sheet | `mnemosyne/frontend/app.js`, `frontend/style.css`; `docs/screenshots/` |
| Web Vault | Janus | Standalone teal application on the same tokens/foundation; not hosted by Nexus | `janus/apps/web/ui/style.css`, `app.js`; `docs/screenshots/` if present |

## 2. Implementation paths

### Nexus

| File | What to read |
|---|---|
| `empyrean-nexus/frontend/index.html` | Document shell, atmosphere, toast and dialog hosts |
| `empyrean-nexus/frontend/style.css` | Legacy rules first, then the 2.1 composition layer (`:root` at ~2103), materials, command bar, application mode, responsive |
| `empyrean-nexus/frontend/app.js` | Shell markup (~433), `renderView`, `renderModuleView` (~931), drawer, dialog, menu, toast, keyboard handling |
| `empyrean-nexus/frontend/router.js` | Top-level routes and module path envelope |
| `empyrean-nexus/frontend/navigation-protocol.js` | Optional navigation-history host/client contract |
| `empyrean-nexus/docs/design-language.md` | Product design principles and 2.1 composition |
| `empyrean-nexus/docs/ui.md` | Control-center workflows, surfaces, honest state, motion |
| `empyrean-nexus/docs/application-infrastructure.md` | `ui.application`, bridge, storage, blobs |
| `empyrean-nexus/docs/module-protocol.md` | Browser CSP and application-icon validation |

### Mnemosyne

| File | What to read |
|---|---|
| `mnemosyne/frontend/style.css` | `:root` accent/layout, legacy components, then the composition layer at ~3817 |
| `mnemosyne/frontend/app.js` | Shell, icons, navigation, editor/drawer patterns |
| `mnemosyne/frontend/navigation.js`, `navigation-routes.js` | Pilot navigation adapter |

### Chronos

| File | What to read |
|---|---|
| `chronos/frontend/style.css` | Accent/layout `:root` (6), shell (71), top bar (297), responsive (1282) |
| `chronos/frontend/app.js` | Accent-independent shell (~382), icons (58), bridge (96), menus/dialogs (~817), keyboard (1498) |
| `chronos/frontend/index.html` | Atmosphere, starfield, toast/dialog hosts |
| `chronos/frontend/icon.svg` | Application icon tile |

### Lyra

| File | What to read |
|---|---|
| `lyra/frontend/style.css` | Accent/layout `:root` (6), shell (54), top bar (159), artwork/cards (243–522), responsive (1188) |
| `lyra/frontend/app.js` | Shell (~224), icons (58), bridge, menus/dialogs (~541) |
| `lyra/frontend/icon.svg` | Application icon tile |

### Meridian

| File | What to read |
|---|---|
| `meridian/frontend/style.css` | `--m-*` tokens (9–89), shell (213), lists (748), dashboard (790), tiles (1027), vrows (1216), feedback (1618), Stage B screens (1676), responsive (3785) |
| `meridian/frontend/app.js` | Shell (~369), icons (55), bridge (~127), panel/dialog (~2060), focus trap (~2617), graph/timeline integration |
| `meridian/frontend/graph.js`, `timeline.js` | Canvas rendering and timeline layout |
| `meridian/docs/DESIGN.md` | The most complete module design standard: colour, type, spacing, components, motion, responsive, accessibility |
| `meridian/docs/screenshots/` | Stage A.2, Stage B, M2, Celestial, Timeline references |

### Argus

| File | What to read |
|---|---|
| `argus/frontend/style.css` | Accent and `--m-*` tokens (4–104), shell (120), grid/tiles (264), photo view (409), responsive (487) |
| `argus/frontend/app.js` | Bridge (24), shell/nav (~93), grid, photo view, picker navigation |
| `argus/docs/ARGUS.md` | Boundaries, screens and storage decisions |

### Janus (optional, standalone)

| File | What to read |
|---|---|
| `janus/apps/web/ui/style.css`, `app.js` | Teal standalone application on canonical tokens/foundation |
| `janus/apps/desktop/ui/aureate-tokens.css` | Older trimmed token copy; not evidence for the current web baseline |

### Architecture

| File | What to read |
|---|---|
| [tokens.css](../tokens.css) | Canonical values |
| [foundation.css](../foundation.css) | Canonical `ae-` primitives |
| [concepts/design-system.md](../../concepts/design-system.md) | Established identity, accents, migration |
| [decisions/0008-shared-design-tokens.md](../../decisions/0008-shared-design-tokens.md) | Vendoring and accent override |
| [decisions/0012-design-system-v2.md](../../decisions/0012-design-system-v2.md) | Token v2, foundation, translucency |
| [decisions/0014-liquid-glass-surfaces.md](../../decisions/0014-liquid-glass-surfaces.md) | Atmosphere, elevated glass, depth budget |
| [concepts/module-contract.md](../../concepts/module-contract.md) | Required module areas |
| [architecture.md](../../architecture.md#application-boundary) | Nexus/module application boundary |

## 3. How to evaluate visual consistency

Use a running installation and the module's own test data. Evaluate in this order; stop at the first structural break.

### A. Identity

1. At 1440 px, is there one calm dark canvas with a single atmosphere, not a flat black page?
2. Is there exactly one accent family, used for place/selection/focus/primary action? Count accent uses: if accent borders appear on every card or accent fills behind whole sections, it is overused.
3. Is gold still present as an ecosystem cue without competing with the module accent?
4. Is Roboto (or the declared fallback) used for functional text? Is there a second UI face for body copy?

### B. Shell and navigation

5. Does the module own its navigation, with a visible current location (`aria-current`, rail selection, tab or breadcrumb)?
6. Does Nexus chrome disappear or recede in application mode, leaving the module full-window?
7. Is there exactly one primary destination/action per region? Are rare/destructive actions in a menu?
8. Does the module avoid writing the top-level URL or appearing in Nexus's primary navigation?

### C. Material and hierarchy

9. Are navigation/toolbars/floating layers visibly glass over the atmosphere, while reading surfaces stay quieter and more opaque?
10. Is any text sitting on glass lighter than `--surface-glass`, or on a bright artwork without a scrim?
11. Are there at most three overlapping translucent layers? Is there a solid fallback with blur disabled and reduced transparency?
12. Does hierarchy come from type and spacing before boxes? Are there cards inside cards or a card for every row?

### D. Components and states

13. Do buttons, fields, menus, dialogs and toasts look and behave like the `ae-` primitives? If restyled, do focus, disabled, busy and error states survive?
14. Are loading, empty and error states distinct and honest? Does an empty state say what is missing and offer one next action?
15. Is a destructive action confirmed in a dialog that names what is lost?
16. Is status a word plus a cue, not colour alone?

### E. Accessibility and responsive

17. Can every action be reached by keyboard with a visible focus ring? Does Escape close the top layer and return focus?
18. Does a modal/drawer trap focus and make the background inert? (If not, record it as a defect.)
19. At 390 px, is there no horizontal page scroll? Is navigation reachable and is the primary action visible?
20. With reduced motion, does movement stop while progress remains readable? With reduced transparency, do glass surfaces become solid?

### F. Interoperability

21. Does the module call Nexus only through the bridge? Are there any direct parent DOM/cookie/storage assumptions?
22. Does the manifest icon follow the square, full-bleed, static, ≤64 KiB rule?
23. Does the module avoid claiming a runtime theme API, automatic deep links or a shared icon set that do not exist?

## 4. What counts as a defect vs a difference

| Observation | Classification |
|---|---|
| A module uses a different accent or atmosphere motif | OPTIONAL difference |
| A module uses a rail instead of a top bar | OPTIONAL difference |
| A module uses a 16 px panel radius where foundation uses 20 px | OPTIONAL difference, if internally consistent |
| A module uses a light background | Outside the default identity; needs an explicit exception |
| A module does not vendor tokens/foundation | DOCUMENTED migration gap for official modules; not a visual difference to celebrate |
| A module changes only `--accent` and leaves gold translucent derivatives | Defect |
| A drawer claims `aria-modal` but Tab escapes | Accessibility defect |
| A module writes the parent URL / adds itself to Nexus navigation | Integration defect |
| A module loads a CDN font or framework in the sandbox | Defect; blocked by CSP |
| A module shows success before the server confirms | Truthfulness defect |
| A module has no empty/error state | Completeness defect |
| A module's navigation is unreachable at 390 px | Responsive defect |

## 5. Evidence and limits

- The source inspection for this guide covered representative files, not entire repositories. Line references drift; use the named function or section.
- No browser run was performed for this documentation pass. Statements about rendered appearance rely on the stylesheets, the module design documents and existing screenshots.
- Screenshot sets are module-owned evidence, not a guarantee of current behaviour. Re-run the module's own browser QA after a CSS change.
- The design-system concept and the implementation can disagree. Where they do, this guide names both; resolve the difference explicitly rather than treating one as silently authoritative.
