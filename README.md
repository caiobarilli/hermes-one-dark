[Leia em português](docs/readme-ptbr.md)

# hermes-one-dark

Atom **One Dark Pro** theme preset and integrated terminal polish for **Hermes Desktop / Webapp** (`apps/desktop/`).

This is a standalone, lean repository containing only the theme files, clean Git patches for Hermes Desktop integration, architecture docs (`.ai/`), and project-specific agent skills (`.agents/skills/`).

## What Is Included

- **Theme & Tests (`apps/desktop/src/themes/`):**
  - `one-dark.ts` — Atom One Dark Pro token policy (`Symbol('one-dark-pro')` runtime provenance), exact dark surfaces (`#282c34` chrome/sidebar, `#21252b` editor/card/popover/terminal, `#1d1f23` inputs/composer, `#181a1f` borders), and WCAG AA high-contrast xterm `terminal`/`darkTerminal` palettes (`#d7dae0` foreground at **10.99:1** on `#21252b`, all readable ANSI colors >= **4.82:1**).
  - `one-dark.css` — Unlayered renderer stylesheet scoped to `:root[data-hermes-palette='one-dark']`: continuous `#181a1f` sidebar seam, opaque inputs/popovers, markdown typography (`#e06c75` headings, `#d19a66` strong, `#61afef` links, `#abb2bf` list markers, `#c678dd` checkboxes), `::selection` (`rgba(97, 175, 239, 0.4)` / `#e6e6e6`), and live "Thinking" wave (`od-sun` with `prefers-reduced-motion` fallback).
  - `one-dark.test.ts` & `one-dark-input.test.tsx` — Unit and CSSOM regression tests.
- **Hermes Integration Patches (`patches/`):**
  - `0001`–`0008` — Focused patches wiring the One Dark Pro preset into Hermes Desktop (`context.tsx`, `skin.ts`, `retint.ts`, `persist-accent.ts`, `user-themes.ts`, `styles.css`, `pane-tab.tsx`, `sidebar`, `message-parts.tsx`, `vitest.config.ts`) and fixing integrated terminal top anchoring, `TerminalRail` icon spacing/full-height border, and CSS `zoom` (`currentCSSZoom`) coordinate unscaling in `PersistentTerminal` and `terminalSelectionAnchor`.
- **Documentation & Skills:**
  - [`.ai/adr/`](.ai/adr/) — Architecture Decision Records (`0001-one-dark-pro-theme.md`, `0002-terminal-css-zoom.md`).
  - [`.ai/pitfalls.md`](.ai/pitfalls.md) — Real pitfalls and known behaviors (including CSS `zoom`, `125%` UI scale residual space, and upstream xterm mouse selection).
  - [`.ai/regras.md`](.ai/regras.md) & [`.ai/teto.md`](.ai/teto.md) — Technical workflow rules and scope boundaries.
  - [`.agents/skills/README.md`](.agents/skills/README.md) — 7 Hermes-adapted project skills across Code/Infra, Design, and Security/Performance.

## How to Apply to Hermes Agent

From your local `hermes-agent` checkout (on a dedicated branch, never `main`):

```bash
# 1. Copy standalone One Dark Pro theme files and tests
cp -a /path/to/hermes-one-dark/apps/desktop/src/themes/one-dark* apps/desktop/src/themes/

# 2. Verify and apply integration patches
git apply --check /path/to/hermes-one-dark/patches/*.patch
git apply /path/to/hermes-one-dark/patches/*.patch
```

## How to Build & Test (Inside `hermes-agent`)

Use the pinned Node/pnpm runtime matching `pm/lock.json` (`--no-install` reuses `.build/webapp-workspace`):

```bash
export PATH="$HOME/.hermes/tools/pnpm-12.15.0-linux-x64:$HOME/.hermes/tools/node-26.12.1-linux-x64/bin:$PATH"

# Production webapp build (no dependency install)
node apps/desktop/scripts/build-webapp.mjs --no-install

# Theme + integrated terminal Vitest suites
cd .build/webapp-workspace/apps/desktop
./node_modules/.bin/vitest run \
  src/themes \
  src/app/right-sidebar/terminal/persistent.test.tsx \
  src/app/right-sidebar/terminal/selection.test.ts \
  src/app/right-sidebar/terminal/use-terminal-session.test.tsx \
  src/app/right-sidebar/terminal/instance.test.tsx \
  src/app/right-sidebar/terminal/rail.test.tsx
```
