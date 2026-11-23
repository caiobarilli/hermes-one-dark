[Read in English](../README.md)

# hermes-one-dark

Preset do tema Atom **One Dark Pro** e ajustes do terminal integrado para o **Hermes Desktop / Webapp** (`apps/desktop/`).

Este é um repositório independente e enxuto contendo apenas os arquivos do tema, patches Git limpos para integração no Hermes Desktop, documentação de arquitetura (`.ai/`) e skills específicas do projeto (`.agents/skills/`).

## O que está incluído

- **Tema e Testes (`apps/desktop/src/themes/`):**
  - `one-dark.ts` — Política de tokens Atom One Dark Pro (proveniência em runtime `Symbol('one-dark-pro')`), superfícies escuras exatas (`#282c34` chrome/sidebar, `#21252b` editor/cards/popovers/terminal, `#1d1f23` inputs/composer, `#181a1f` bordas) e paletas xterm `terminal`/`darkTerminal` de alto contraste WCAG AA (`#d7dae0` com contraste **10.99:1** sobre `#21252b`; todas as cores ANSI legíveis >= **4.82:1**).
  - `one-dark.css` — Folha de estilos unlayered escopada em `:root[data-hermes-palette='one-dark']`: fio contínuo `#181a1f` na sidebar, inputs/popovers opacos, tipografia Markdown (`#e06c75` títulos, `#d19a66` negrito, `#61afef` links, `#abb2bf` marcadores de lista, `#c678dd` checkboxes), `::selection` (`rgba(97, 175, 239, 0.4)` / `#e6e6e6`) e onda "Thinking" (`od-sun` com fallback para `prefers-reduced-motion`).
  - `one-dark.test.ts` e `one-dark-input.test.tsx` — Testes unitários e de regressão CSSOM.
- **Patches de Integração no Hermes (`patches/`):**
  - `0001`–`0008` — Patches focados que conectam o preset One Dark Pro ao Hermes Desktop (`context.tsx`, `skin.ts`, `retint.ts`, `persist-accent.ts`, `user-themes.ts`, `styles.css`, `pane-tab.tsx`, `sidebar`, `message-parts.tsx`, `vitest.config.ts`) e corrigem ancoragem no topo do terminal integrado, espaçamento/borda completa do `TerminalRail` e desescalamento por CSS `zoom` (`currentCSSZoom`) em `PersistentTerminal` e `terminalSelectionAnchor`.
- **Documentação e Skills:**
  - [`.ai/adr/`](../.ai/adr/) — Architecture Decision Records (`0001-one-dark-pro-theme.md`, `0002-terminal-css-zoom.md`).
  - [`.ai/pitfalls.md`](../.ai/pitfalls.md) — Armadilhas reais e comportamentos conhecidos (incluindo CSS `zoom`, espaço residual em `125%` e seleção de mouse upstream do xterm).
  - [`.ai/regras.md`](../.ai/regras.md) e [`.ai/teto.md`](../.ai/teto.md) — Regras técnicas e limites de escopo.
  - [`.agents/skills/README.md`](../.agents/skills/README.md) — 7 skills adaptadas ao Hermes nas frentes de Código/Infra, Design e Segurança/Performance.

## Como Aplicar no Hermes Agent

A partir do seu checkout local do `hermes-agent` (em uma branch dedicada, nunca na `main`):

```bash
# 1. Copie os arquivos standalone do tema One Dark Pro e seus testes
cp -a /caminho/para/hermes-one-dark/apps/desktop/src/themes/one-dark* apps/desktop/src/themes/

# 2. Valide e aplique os patches de integração
git apply --check /caminho/para/hermes-one-dark/patches/*.patch
git apply /caminho/para/hermes-one-dark/patches/*.patch
```

## Como Buildar e Testar (Dentro do `hermes-agent`)

Use o runtime Node/pnpm compatível com `pm/lock.json` (`--no-install` reutiliza `.build/webapp-workspace`):

```bash
export PATH="$HOME/.hermes/tools/pnpm-12.15.0-linux-x64:$HOME/.hermes/tools/node-26.12.1-linux-x64/bin:$PATH"

# Build de produção do webapp (sem instalar dependências)
node apps/desktop/scripts/build-webapp.mjs --no-install

# Suítes Vitest do tema e do terminal integrado
cd .build/webapp-workspace/apps/desktop
./node_modules/.bin/vitest run \
  src/themes \
  src/app/right-sidebar/terminal/persistent.test.tsx \
  src/app/right-sidebar/terminal/selection.test.ts \
  src/app/right-sidebar/terminal/use-terminal-session.test.tsx \
  src/app/right-sidebar/terminal/instance.test.tsx \
  src/app/right-sidebar/terminal/rail.test.tsx
```
