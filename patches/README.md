# Patches — status and usage

## Current integration (theme-only)

- **`0009-one-dark-restore-final.patch`** — the current One Dark Pro
  integration, identical to the diff of
  [NousResearch/hermes-agent#160350](https://github.com/NousResearch/hermes-agent/pull/160350)
  (branch `theme/one-dark-restore`). Verified to apply cleanly
  (`git apply --check`) on a pristine upstream checkout of `8fc037444731`
  and to reproduce the PR diff exactly. This is the patch to use.

## Historical series `0001`–`0008` (reference only)

These reproduce the pre-split contribution from the #159253 era
(`theme/one-dark-sync-rebased` @ `af176f52ca`):

| Patch | Content | Status |
|---|---|---|
| `0001` | One Dark Pro preset + integration + tests | absorbed into `0009` |
| `0002` | terminal output anchoring / full-pane fit | **functional**, excluded from theme scope (ADR 0003) |
| `0003` | scrollback preservation on transport retry | **functional**, excluded |
| `0004` | boot-gap stripping on fresh shells | **functional**, excluded |
| `0005` | terminal rail icon spacing | visual (className-only), absorbed into `0009` |
| `0006` | terminal rail full-height border | visual (className-only), absorbed into `0009` |
| `0007` | CSS zoom unscale in `PersistentTerminal` | **functional**, excluded |
| `0008` | CSS zoom unscale in selection anchor + overlay floor | **functional**, excluded |

Do **not** apply `0001`–`0008` together with `0009`. The functional fixes
(`0002`–`0004`, `0007`, `0008`) are intentionally outside the visual-only theme
scope (see `.ai/adr/0003-visual-only-boundary.md` and the status note in
`.ai/adr/0002-terminal-css-zoom.md`) and are reserved for a separate,
independently validated contribution. They are kept here unmodified as
historical record.
