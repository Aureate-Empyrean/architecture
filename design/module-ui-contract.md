# Module UI contract

What a module must do to work inside Aureate Empyrean, and what it may decide for itself. This document separates **binding interoperability requirements** from **default visual conventions**. It does not replace the [module contract](../concepts/module-contract.md) (data, lifecycle, capabilities, references) or the Nexus implementation documentation for wire details.

Authoritative implementation references:

- `empyrean-nexus/docs/application-infrastructure.md` — `ui.application`, bridge, storage, blobs.
- `empyrean-nexus/docs/module-protocol.md` — manifest, browser CSP, icon validation.
- `empyrean-nexus/docs/navigation-history.md` — default-off navigation pilot.
- `empyrean-nexus/frontend/app.js`, `frontend/navigation-protocol.js` — current host behavior.
- [architecture.md](../architecture.md#application-boundary) and [ADR 0001](../decisions/0001-module-ownership-and-domain-ignorant-nexus.md) — ownership boundary.

## 1. Binding vs optional at a glance

### Binding for a Nexus-connected full application

| # | Requirement | Evidence / status |
|---|---|---|
| 1 | Register through Module Protocol v1 with a stable ID, version, protocol version and declared capabilities | IMPLEMENTED; [module contract](../concepts/module-contract.md) |
| 2 | Request `ui.application` for a full-window application; exclude Nexus versions that lack it | IMPLEMENTED (Nexus 0.1.2+) |
| 3 | Serve HTML with bundled inline styles and scripts; no external network, forms or CDN assets | IMPLEMENTED CSP |
| 4 | Use the `empyrean-v1` message bridge for module API calls; never read Nexus cookies, storage or DOM | IMPLEMENTED |
| 5 | Own internal navigation and rendering; never write the parent URL or top-level History API | IMPLEMENTED; [architecture](../architecture.md#application-boundary) |
| 6 | Switch applications only through bridge `navigate` with a module ID (or `nexus`) | IMPLEMENTED |
| 7 | Open external links through bridge `external`, not an unmediated popup | IMPLEMENTED |
| 8 | If a manifest icon is declared, serve a square, full-bleed, static tile within the validation limits | IMPLEMENTED (Nexus 0.1.8+) |
| 9 | Work without same-origin privileges; treat `localStorage`/`sessionStorage` as unavailable or ephemeral | IMPLEMENTED sandbox consequence |
| 10 | Provide keyboard access, visible focus, labelled controls, a live region for status and reduced-motion/transparency fallbacks | DOCUMENTED ecosystem requirement; not automatically enforced |
| 11 | Keep the module usable at 390 px and 1440 px with no horizontal page scroll | DOCUMENTED/RECOMMENDED; verify per module |
| 12 | Do not require Nexus to inject a theme, accent, font or CSS variable at runtime | IMPLEMENTED limitation: no such API |

### Default visual conventions (not interoperability contracts)

| Choice | Default | Status |
|---|---|---|
| Dark-only palette | `--canvas` `#090A0C`, light text | IMPLEMENTED default; a light module would not reproduce the default identity |
| One accent family | Module-owned `:root` override | IMPLEMENTED |
| Roboto | Bundled UI face | IMPLEMENTED for official applications |
| Atmosphere | One fixed `ae-atmosphere` (or module equivalent) | IMPLEMENTED in newer modules; not all historical modules |
| Glass materials | `ae-surface` / `ae-glass` with solid fallbacks | IMPLEMENTED in newer modules |
| Shell composition | Rail/sidebar + toolbar + workspace, or Nexus command bar | OPTIONAL; module owns layout |
| Radius, density, display type | Module-local scale | OPTIONAL |
| Card vs list vs grid | Content-driven | OPTIONAL |
| Accent hue and motif | Module identity | OPTIONAL |
| Settings layout, empty-state art, icon style | Module choice | OPTIONAL |

**Rule.** A module may look different. It may not break the bridge, the sandbox, navigation ownership, the icon contract, keyboard access or the reduced-motion/transparency floor.

## 2. Official vs community modules

**DOCUMENTED.** [ADR 0008](../decisions/0008-shared-design-tokens.md) requires every **official** application to vendor `tokens.css` unchanged and load it before its own styles. Community modules **may** vendor the same file; whether they must is still Open ([design-system](../concepts/design-system.md#open-questions)).

**IMPLEMENTED.** All current official applications vendor token set 2.1. Chronos, Lyra, Meridian, Argus and Janus Web vendor `foundation.css` 1.1 unchanged. Nexus and Mnemosyne do not vendor the foundation; they keep local primitives and restyle them with the 2.1 materials. This is a documented migration gap, not a reason to fork the foundation in a new module.

**RECOMMENDED for a new official module:** vendor both files unchanged, load tokens → foundation → module styles, and override only the accent family, `--accent-2` and `--font-display`. **RECOMMENDED for a community module:** do the same if you want the closest visual match; at minimum, use the token names and the dark palette so shared references and screenshots remain coherent.

## 3. Application lifecycle and sandbox

**IMPLEMENTED** ([application-infrastructure.md](https://github.com/Aureate-Empyrean/empyrean-nexus/blob/main/docs/application-infrastructure.md)):

1. The manifest declares `ui.application` and a compatible Nexus range.
2. Nexus serves the module at `/app/modules/<id>` and loads the owner-authenticated gateway document in an iframe.
3. The iframe uses `sandbox="allow-scripts allow-downloads"`. There is **no** `allow-same-origin`, no owner cookie, no bearer token and no CSRF value.
4. The gateway imposes `default-src 'none'`, no network/forms, data images/fonts, inline style/script, and a 2 MiB response cap. The module's browser code cannot make arbitrary network requests.
5. The module calls its own API through the bridge; Nexus validates the module identity, action, method, own API prefix, path and body size.
6. Module APIs must verify `X-Nexus-Gateway-Key`. The gateway injects it; it is never forwarded from the browser.
7. Optional `storage.data` gives a broker-owned `/data` volume retained across disable, uninstall and reinstall. Optional blob grants provide up to 1 MiB shared blobs.
8. Disabling, stopping or uninstalling does not delete user data; destructive deletion is separate.

**Consequences for UI code:**

- Do not assume `window.localStorage` works. The opaque origin can make storage throw. Wrap access in try/catch and keep unsaved state in memory.
- Do not assume a service worker, IndexedDB, cookies, `window.opener`, downloads via fetch, or cross-origin fonts.
- Do not load a CDN, Google Fonts, an external icon set or a remote stylesheet.
- Bundle Roboto as a `data:font/woff2` face in the served page, or rely on the system fallback. Chronos, Lyra, Meridian and Argus inline it server-side.
- Keep the served page under the response limit; inline or build-time-bundle assets.

## 4. The Nexus bridge

**IMPLEMENTED.** Message shape:

```js
parent.postMessage({ channel: "empyrean-v1", id, action, ...data }, "*");
```

Response:

```js
{ channel: "empyrean-v1", id, result }   // or { ..., error }
```

| Action | Request data | Result | Notes |
|---|---|---|---|
| `context` | — | `{ applications: [{id, name}], locale, navigation: { versions: [] } }` | `navigation.versions` includes `1` only when the default-off pilot is enabled |
| `request` | `{ path, method, body? }` | Module API response body | `path` must be under the module's own API prefix; body is JSON; subject to size and rate limits |
| `navigate` | `{ module }` | No reply on success | `module` is an enabled module ID or `nexus`; it does **not** carry an internal route today |
| `external` | `{ url }` | `{ opened, trusted }` or an error | HTTP(S) only for an origin the owner trusted for this module; `mailto:` limited to the reviewed recipient |

Example client wrapper (Chronos, Lyra, Meridian and Argus use the same pattern; their timeouts differ, roughly 15–30 s):

```js
function bridge(action, data = {}) {
  return new Promise((resolve, reject) => {
    const id = String(++serial);
    const timer = setTimeout(() => reject(Error("Nexus did not respond.")), 15000);
    pending.set(id, { resolve, reject, timer });
    parent.postMessage({ channel: "empyrean-v1", id, action, ...data }, "*");
  });
}
window.addEventListener("message", event => {
  if (event.source !== parent || event.data?.channel !== "empyrean-v1") return;
  const request = pending.get(event.data.id);
  if (!request) return;
  clearTimeout(request.timer);
  pending.delete(event.data.id);
  event.data.error ? request.reject(Error(event.data.error)) : request.resolve(event.data.result);
});
```

**Rules:**

- Validate `event.source === parent` and the channel before resolving a request.
- Use one pending map and clear timeouts on response.
- Do not send credentials. The bridge is the credential boundary.
- Do not use the bridge for cross-module domain reads. Use documented APIs, references or projections.
- Handle a missing host: a standalone preview may run without the bridge, but the production path must fail clearly, not silently pretend success.

## 5. Navigation ownership

**IMPLEMENTED:**

- Nexus owns the top-level URL, `pushState`/`popstate`, the application frame and the switcher.
- The module owns internal routes, breadcrumbs, tabs, drawers and rendering.
- `navigate` changes the application, not the module's internal page.
- A module must not call `history.pushState` on the parent document (it cannot, cross-origin) and must not rely on the parent URL to represent its internal state.

**Optional pilot.** `navigation-protocol.js` implements `empyrean-navigation-v1`: a probe/hello handshake, challenge/session/revision fencing, `restore`, `commit`, `checkpoint`, `prepare-leave` and `applied` messages. The host advertises it only when `NEXUS_NAVIGATION_HISTORY=1`; the checked-in Compose configuration does not forward it. Only Mnemosyne has an adapter. It is **not** a manifest field, capability or required module dependency. Do not build a new module against it unless you are deliberately joining the pilot and have read `empyrean-nexus/docs/navigation-history.md`.

**RECOMMENDED.** Keep an in-module location indicator. Make the module's entry route deterministic. Do not deep-link to a domain object until the pilot contract or a future stable navigation contract exists.

## 6. Visual integration

**Binding floor (identity and legibility):**

- Dark canvas and light text. A module that renders a light UI is outside the default identity and needs an explicit exception; it is not a drop-in default module.
- One accent family, used for place, selection, focus and primary action. Do not use the accent as a background everywhere or as a status colour.
- Roboto (or the declared UI stack) for functional text. Do not introduce a second UI face for body copy.
- Readable contrast on the actual surface, including glass over atmosphere and artwork.
- Visible focus and non-colour-only state.
- Reduced-motion and reduced-transparency fallbacks.

**Recommended visual match:**

- Vendor both canonical stylesheets unchanged; load tokens → foundation → module styles.
- Start from `ae-surface` for the workspace and `ae-glass` for floating layers; customize the material only if the composition needs it.
- Use the `ae-btn`, `ae-field`, `ae-input`, `ae-select`, `ae-check`, `ae-switch`, `ae-seg`, `ae-chip`, `ae-menu`, `ae-dialog`, `ae-drawer`, `ae-toast`, `ae-empty`, `ae-skeleton` primitives.
- Use one radius scale (controls, groups, panels, floating).
- Use one spacing scale (4 px base for foundation; a small local grid if needed).
- Keep one primary action per surface. Rare and destructive actions go in an overflow menu with confirmation.
- Keep the workspace calm: no card per row, no nested cards, no filler widgets.

**Not promised:** a runtime theme API, a Nexus-supplied accent, a shared CSS reset beyond `foundation.css`, a shared icon set, a shared layout component, or a light theme. A module that needs these must implement them locally and document the gap.

## 7. Component and state expectations

A compatible module provides:

| Area | Requirement | Status |
|---|---|---|
| Loading | A visible pending state; skeletons only for known shapes; no fake progress | DOCUMENTED/RECOMMENDED |
| Empty | Says what is missing, why and the next action; distinguishes no-data from no-match and error | DOCUMENTED/RECOMMENDED |
| Error | Keeps the real message; offers recovery; does not show success early | DOCUMENTED/RECOMMENDED |
| Destructive action | Confirmed in a dialog that names what is lost; danger styling; focus starts safely | DOCUMENTED/RECOMMENDED |
| Forms | Visible labels, associated errors, keyboard submit, preserved values on failure | DOCUMENTED/RECOMMENDED |
| Busy action | Button keeps size, shows a spinner, is disabled and `aria-busy` | IMPLEMENTED in foundation and Nexus |
| Status | A word plus a visual cue (dot/icon), not colour alone | IMPLEMENTED pattern; apply consistently |
| Toasts | One live region, transient confirmation, no sole copy of critical information | IMPLEMENTED in newer modules |

## 8. Application icon

**IMPLEMENTED** (Nexus 0.1.8+, validation in `empyrean-nexus/docs/module-protocol.md`):

- Manifest field: `"icon": "/icon.svg"` (or `.png`/`.webp`). Path must be served by the module; no host, query or `..`.
- A manifest that declares an icon must exclude Nexus 0.1.7 and earlier (for example `>=0.1.8,<0.2.0`).
- Square, full-bleed tile in the application's own colours. Do not round the corners or add a shadow; Nexus applies its radius and edge light.
- At most 64 KiB. PNG/WebP must carry a valid signature. SVG must be UTF-8, well-formed, no DTD/entities/style sheets, static drawing elements only, no script/`foreignObject`/`image`/links/animation, and no external references.
- Nexus 0.1.9 fetches and stores the last valid copy; the icon survives module outages and is refreshed only when the manifest icon path, version or image changes. A missing/invalid icon falls back to a neutral letter tile.
- Icon changes are not permissions and do not appear in update review.

**Current examples:** `chronos/frontend/icon.svg`, `lyra/frontend/icon.svg`, `meridian/frontend/icon.svg`, `mnemosyne/frontend/icon.svg`, `argus/frontend/icon.svg`, `janus/apps/web/ui/icon.svg`.

## 9. Accessibility floor

At minimum, a module must satisfy the checklist in [accessibility.md](accessibility.md#7-accessibility-checklist-for-a-module). The highest-risk gaps in current modules are:

- A `role="dialog"` drawer without a focus trap or inert background (Chronos, Lyra).
- Off-canvas navigation without a scrim/focus trap (Chronos, Lyra).
- No skip link in any current shell.
- Static `lang="en"` in some modules despite runtime localization.

Do not copy those gaps into a new module.

## 10. Conformance checklist

**Integration**

- [ ] Manifest declares protocol 1, a stable ID, version, `ui.application` and a compatible Nexus range.
- [ ] Served page is self-contained: inline style/script, bundled fonts, no network/forms/CDN.
- [ ] All module API calls go through `empyrean-v1` `request`.
- [ ] No parent URL writes, no parent DOM access, no owner cookies/tokens.
- [ ] Cross-application navigation uses `navigate` with a module ID.
- [ ] External links use `external` and handle `{opened:false, trusted:true}`.
- [ ] Storage is optional and failure-tolerant.
- [ ] If an icon is declared, it passes the manifest icon rules.

**Navigation**

- [ ] The module has a deterministic entry route.
- [ ] Internal navigation is visible and owned by the module.
- [ ] The module does not assume browser Back/Forward restores its internal state.
- [ ] No module page is appended to Nexus navigation.

**Visual**

- [ ] Tokens are vendored unchanged; load order is tokens → foundation → module.
- [ ] Accent family sets all translucent derivatives; contrast is verified.
- [ ] One primary action per surface; destructive actions are confirmed.
- [ ] Glass has solid fallbacks; reduced motion is honoured.
- [ ] Radius/spacing scale is coherent; no card-inside-card.

**Accessibility and responsive**

- [ ] Keyboard-only operation; visible focus; modal focus containment.
- [ ] Labels, `aria-current`, live regions and error association are present.
- [ ] Works at 390 px and 1440 px; no horizontal page scroll.
- [ ] Text remains readable at 200% zoom.

## 11. Anti-patterns

- Loading a CSS framework or icon font from a CDN inside the sandbox.
- Reading `localStorage` without a try/catch in an opaque-origin frame.
- Calling Nexus APIs directly with a guessed token or cookie.
- Writing the top-level URL or trying to control the Nexus shell.
- Adding the module to Nexus's primary navigation.
- Copying a historical Nexus/Mnemosyne local button style instead of using `ae-btn`.
- Forking a vendored `tokens.css`/`foundation.css` in the module repository.
- Shipping a light theme and calling it the default AE identity.
- Claiming a theme API, a shared icon set or automatic deep links that do not exist.
- Hiding the only action behind hover with no focus/touch equivalent.
