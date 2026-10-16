# 0008. One canonical token file, vendored by each application

- Status: Accepted (2026-10-16)

## Context

Every Aureate Empyrean application must read as one ecosystem: the same foundation, typography, states and motion, with only the module accent changing ([design-system](../concepts/design-system.md)). Nexus and Mnemosyne had copied the shared values independently. The values matched, but the names did not (`--gold*` vs `--accent*`, `--bad` vs `--danger`, `--motion-fast` vs `--fast`), and semantic colors and shadows had already drifted. Each new module (Janus next) would copy one of them and drift further.

Options considered:

- **Served by Nexus.** Rejected: local-first clients such as Janus must work without Nexus, and Nexus-delivered client code is a threat for Janus ([janus](../modules/janus.md)).
- **Shared package** (npm/pip). Rejected for now: a release pipeline and a dependency for a ~70-line file, contrary to "prefer boring technology".
- **One canonical file, vendored.** Chosen.

## Decision

- [`design/tokens.css`](../design/tokens.css) in this repository is the canonical, versioned token set: neutrals, surfaces, text, lines, depth, the ecosystem gold, semantic states, type, motion and shape.
- Every official application vendors it unchanged (as `frontend/aureate-tokens.css` or equivalent) and loads it before its own styles. A vendored copy is never edited; changes are made here and re-vendored.
- Components use only the token names. A module changes its identity by overriding the accent family (`--accent`, `--accent-hi`, `--accent-deep`, `--accent-hover`, `--accent-soft`, `--accent-sel`, `--accent-line`, `--focus`) and optionally `--font-display` in its own `:root`. The defaults are the ecosystem gold, which is Nexus's accent.
- Roboto (SIL OFL 1.1) is the shared UI face and is bundled with each application rather than relying on system fonts. Sandboxed module applications embed it, which requires Nexus to permit `data:` fonts in the module application policy.
- Module-specific tokens (layout widths, priority colors, user resource colors) stay in the module.

## Consequences

- Nexus renames `--gold*` to `--accent*`, `--bad*` to `--danger*`, `--surface` to `--surface-1` and `--motion-normal/--motion-ease/--motion-in` to `--motion-base/--ease-out/--ease-in`; Mnemosyne renames `--fast/--base/--slow` to `--motion-*`.
- A token change is a deliberate, versioned change in this repository followed by a re-vendor in each application; a vendored copy that differs from the canonical version it names is a bug.
- Community modules may vendor the same file; whether they must is still Open ([design-system](../concepts/design-system.md#open-questions)).
