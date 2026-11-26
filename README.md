[Leia em português](docs/readme-ptbr.md)

# hermes-one-dark

Atom **One Dark Pro** theme preset for **Hermes Desktop / Webapp** (`apps/desktop/`) — **visual layer only**.

This is a standalone, lean repository containing only the theme files, Git patches for Hermes Desktop integration, architecture docs (`.ai/`), and project-specific agent skills (`.agents/skills/`).

**Scope (ADR 0003):** the theme is exclusively a visual customization layer — palette, backgrounds, borders, contrast, text/selection colors, accent and visual states. Structure, layout, navigation, menus, terminal behavior and zoom handling belong to upstream Hermes and are out of scope here. Functional fixes (CSS `zoom` unscale, terminal output anchoring, scrollback/boot-gap handling) are intentionally **excluded** and tracked for a separate contribution.

Current upstream integration: [NousResearch/hermes-agent#160350](https://github.com/NousResearch/hermes-agent/pull/160350) (branch `theme/one-dark-restore`, visual validation completed by the maintainer; upstream review pending — the theme is **not** officially merged into Hermes Agent yet).

## What Is Included

- **Theme & Tests (`apps/desktop/src/themes/`):**
  - `one-dark.ts` — Atom One Dark Pro token policy (`Symbol('one-dark-pro')` runtime provenance), exact dark surfaces (`#282c34` chrome/sidebar, `#21252b` editor/card/popover/terminal, `#1d1f23` inputs/composer, `#181a1f` borders), and WCAG AA high-contrast xterm `terminal`/`darkTerminal` palettes (`#d7dae0` foreground at **10.99:1** on `#21252b`, all readable ANSI colors >= **4.82:1**).
  - `one-dark.css` — Unlayered renderer stylesheet scoped to `:root[data-hermes-palette='one-dark']`: continuous `#181a1f` sidebar seam, opaque inputs/popovers, markdown typography (`#e06c75` headings, `#d19a66` strong, `#61afef` links, `#abb2bf` list markers, `#c678dd` checkboxes), `::selection` (`rgba(97, 175, 239, 0.4)` / `#e6e6e6`), titlebar tool-button contrast lift (`--dt-muted-foreground: #9da5b4`, ~4.5:1 blended), and live "Thinking" wave (`od-sun` with `prefers-reduced-motion` fallback).
  - `one-dark.test.ts` & `one-dark-input.test.tsx` — Unit and CSSOM regression tests.
- **Hermes Integration Patches (`patches/`):**
  - `0009-one-dark-restore-final.patch` — **the current, theme-only integration** (matches
    [#160350](https://github.com/NousResearch/hermes-agent/pull/160350) exactly; verified to apply
    cleanly on a pristine upstream `8fc037444731` checkout). Wires the preset into
    `context.tsx`, `skin.ts`, `retint.ts`, `persist-accent.ts`, `user-themes.ts`, `styles.css`,
    `pane-tab.tsx`, `search-field.tsx`, sidebar, `message-parts.tsx` and `vitest.config.ts`.
  - `0001`–`0008` — **historical series** from the pre-split contribution (#159253 era). Kept for
    the record: `0002`–`0004` are terminal *functional* fixes and `0007`–`0008` are CSS `zoom`
    unscale fixes — all outside the visual-only theme scope (ADR 0003), not part of #160350, and
    not needed to apply the theme. Do not apply them together with `0009`.
- **Documentation & Skills:**
  - [`.ai/adr/`](.ai/adr/) — Architecture Decision Records (`0001-one-dark-pro-theme.md`,
    `0002-terminal-css-zoom.md`, `0003-visual-only-boundary.md`).
  - [`.ai/pitfalls.md`](.ai/pitfalls.md) — Real pitfalls and known behaviors (including CSS `zoom`, `125%` UI scale residual space, and upstream xterm mouse selection).
  - [`.ai/regras.md`](.ai/regras.md) & [`.ai/teto.md`](.ai/teto.md) — Technical workflow rules and scope boundaries.
  - [`.agents/skills/README.md`](.agents/skills/README.md) — 7 Hermes-adapted project skills across Code/Infra, Design, and Security/Performance.

## How to Apply to Hermes Agent

From your local `hermes-agent` checkout (on a dedicated branch, never `main`):

```bash
# 1. Copy standalone One Dark Pro theme files and tests
cp -a /path/to/hermes-one-dark/apps/desktop/src/themes/one-dark* apps/desktop/src/themes/

# 2. Verify and apply the final theme-only integration patch
git apply --check /path/to/hermes-one-dark/patches/0009-one-dark-restore-final.patch
git apply /path/to/hermes-one-dark/patches/0009-one-dark-restore-final.patch
```

The `0001`–`0008` historical series is reference-only (see the scope note above).

## How to Build & Test (Inside `hermes-agent`)

Use the pinned Node/pnpm runtime matching `pm/lock.json` (`--no-install` reuses `.build/webapp-workspace`):

```bash
export PATH="$HOME/.hermes/tools/pnpm-12.15.0-linux-x64:$HOME/.hermes/tools/node-26.12.1-linux-x64/bin:$PATH"

# Production webapp build (no dependency install)
node apps/desktop/scripts/build-webapp.mjs --no-install

# Theme Vitest suites (terminal suites are upstream code under the visual-only scope)
cd .build/webapp-workspace/apps/desktop
./node_modules/.bin/vitest run src/themes
```
