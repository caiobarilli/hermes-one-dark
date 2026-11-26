# ADR-0003: One Dark Pro is a visual-only layer — the skin/behavior boundary

- **Status:** Accepted
- **Date:** 2026-11-26
- **Scope:** Hermes desktop theme contribution (`apps/desktop/src/themes`), contribution policy for theme PRs

## Context

The One Dark Pro desktop theme (`apps/desktop/src/themes/one-dark.ts`,
`one-dark.css`, and the integration glue in `skin.ts`, `persist-accent.ts`,
`user-themes.ts`, `context.tsx`) was visually approved by the maintainer:
background `#282c34`, accent `#61afef`, borders `#181a1f`. The current upstream
integration is [NousResearch/hermes-agent#160350](https://github.com/NousResearch/hermes-agent/pull/160350)
(branch `theme/one-dark-restore`), which supersedes #159253.

An earlier iteration of the contribution (#159253 era; historical patches
`0001`–`0008` in `patches/`) also carried fixes that changed renderer
*behavior*: terminal zoom unscale corrections (`0007`, `0008`), output
anchoring (`0002`), scrollback preservation and boot-gap stripping (`0003`,
`0004`). Those are quality fixes, but they are not theme work: bundling them
into a theme PR makes the PR impossible to review as a skin, couples cosmetic
adoption to functional risk, and forces upstream maintainers to re-validate
terminal behavior when they only asked for a palette.

## Decision

One Dark Pro is exclusively a **visual layer**. The boundary:

- **Upstream Hermes owns:** component structure and composition, navigation,
  menus, sidebar and its toggle (behavior), layout and responsiveness, terminal
  behavior, zoom management, functional accessibility, and the original UX.
- **The theme owns:** palette, backgrounds, borders and separators, visual
  contrast, text and selection colors, accent, visual states, component
  appearance, and other strictly visual properties.

Behavioral fixes are **never reimplemented in the theme** to achieve an
aesthetic result.

## Acceptance criteria

A change is acceptable in a One Dark Pro PR when it is:

1. Purely visual (color, background, border, contrast, visual state), scoped
   to the theme's `one-dark.css` / preset tokens — geometry, hit areas,
   hover/focus/active behavior and event flow remain upstream.
2. Required glue for the preset to register with the current upstream theme
   architecture (`skin.ts`, `persist-accent.ts`, `user-themes.ts`,
   `context.tsx`), preserving upstream behavior for every other theme.
3. Accompanied by theme tests (unit/CSSOM regressions).

A change is **rejected** from a theme PR when it:

1. Alters component behavior, layout flow, event handling, keyboard or
   pointer interaction, or accessibility semantics.
2. Touches terminal behavior (scrollback, retry, boot gap, zoom coordinate
   math) — those belong to a separate, independently validated contribution
   (see ADR 0002 status note).
3. Depends on patching files outside the theme/integration glue surface
   without an upstream-maintainer decision.

## Consequences

- Future visual improvements are welcome as long as they respect this
  separation; future functional fixes (zoom, terminal behavior) go to their
  own PRs with reproduction, tests and validation independent of the theme.
- The historical `0001`–`0008` patch series stays in `patches/` as a record of
  the pre-split state; the current theme-only integration is
  `0009-one-dark-restore-final.patch`, verified identical to the #160350 diff
  against a pristine upstream `8fc037444731` checkout.
- Historical documentation is preserved, not rewritten.
