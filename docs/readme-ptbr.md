[Read in English](../README.md)

# hermes-one-dark

Preset do tema Atom **One Dark Pro** para o **Hermes Desktop / Webapp** (`apps/desktop/`) — **camada exclusivamente visual**.

Este é um repositório independente e enxuto contendo apenas os arquivos do tema, patches Git para integração no Hermes Desktop, documentação de arquitetura (`.ai/`) e skills específicas do projeto (`.agents/skills/`).

**Escopo (ADR 0003):** o tema é exclusivamente camada de personalização visual — paleta, backgrounds, bordas, contraste, cores de texto/seleção, accent e estados visuais. Estrutura, layout, navegação, menus, comportamento do terminal e zoom pertencem ao upstream Hermes e estão FORA do escopo aqui. Correções funcionais (desescalamento de CSS `zoom`, ancoragem de saída do terminal, preservação de scrollback/boot-gap) estão intencionalmente **excluídas** e ficam para uma contribuição separada.

Integração upstream atual: [NousResearch/hermes-agent#160350](https://github.com/NousResearch/hermes-agent/pull/160350) (branch `theme/one-dark-restore`, validação visual feita pelo maintainer; revisão upstream pendente — o tema **não** está oficialmente incorporado ao Hermes Agent ainda).

## O que está incluído

- **Tema e Testes (`apps/desktop/src/themes/`):**
  - `one-dark.ts` — Política de tokens Atom One Dark Pro (proveniência em runtime `Symbol('one-dark-pro')`), superfícies escuras exatas (`#282c34` chrome/sidebar, `#21252b` editor/cards/popovers/terminal, `#1d1f23` inputs/composer, `#181a1f` bordas) e paletas xterm `terminal`/`darkTerminal` de alto contraste WCAG AA (`#d7dae0` com contraste **10.99:1** sobre `#21252b`; todas as cores ANSI legíveis >= **4.82:1**).
  - `one-dark.css` — Folha de estilos unlayered escopada em `:root[data-hermes-palette='one-dark']`: fio contínuo `#181a1f` na sidebar, inputs/popovers opacos, tipografia Markdown (`#e06c75` títulos, `#d19a66` negrito, `#61afef` links, `#abb2bf` marcadores de lista, `#c678dd` checkboxes), `::selection` (`rgba(97, 175, 239, 0.4)` / `#e6e6e6`), contraste dos botões de ferramenta do titlebar (`--dt-muted-foreground: #9da5b4`, ~4.5:1 blended) e onda "Thinking" (`od-sun` com fallback para `prefers-reduced-motion`).
  - `one-dark.test.ts` e `one-dark-input.test.tsx` — Testes unitários e de regressão CSSOM.
- **Patches de Integração no Hermes (`patches/`):**
  - `0009-one-dark-restore-final.patch` — **a integração atual, somente tema** (idêntica à
    [#160350](https://github.com/NousResearch/hermes-agent/pull/160350); verificada aplicando
    limpa num checkout upstream `8fc037444731` intocado). Conecta o preset em
    `context.tsx`, `skin.ts`, `retint.ts`, `persist-accent.ts`, `user-themes.ts`, `styles.css`,
    `pane-tab.tsx`, `search-field.tsx`, sidebar, `message-parts.tsx` e `vitest.config.ts`.
  - `0001`–`0008` — **série histórica** da contribuição pré-divisão (era #159253). Mantida como
    registro: `0002`–`0004` são correções *funcionais* do terminal e `0007`–`0008` são de CSS
    `zoom` — todas fora do escopo visual do tema (ADR 0003), fora da #160350 e desnecessárias
    para aplicar o tema. Não aplique junto com a `0009`.
- **Documentação e Skills:**
  - [`.ai/adr/`](../.ai/adr/) — Architecture Decision Records (`0001-one-dark-pro-theme.md`,
    `0002-terminal-css-zoom.md`, `0003-visual-only-boundary.md`).
  - [`.ai/pitfalls.md`](../.ai/pitfalls.md) — Armadilhas reais e comportamentos conhecidos (incluindo CSS `zoom`, espaço residual em `125%` e seleção de mouse upstream do xterm).
  - [`.ai/regras.md`](../.ai/regras.md) e [`.ai/teto.md`](../.ai/teto.md) — Regras técnicas e limites de escopo.
  - [`.agents/skills/README.md`](../.agents/skills/README.md) — 7 skills adaptadas ao Hermes nas frentes de Código/Infra, Design e Segurança/Performance.

## Como Aplicar no Hermes Agent

A partir do seu checkout local do `hermes-agent` (em uma branch dedicada, nunca na `main`):

```bash
# 1. Copie os arquivos standalone do tema One Dark Pro e seus testes
cp -a /caminho/para/hermes-one-dark/apps/desktop/src/themes/one-dark* apps/desktop/src/themes/

# 2. Valide e aplique o patch final de integração (somente tema)
git apply --check /caminho/para/hermes-one-dark/patches/0009-one-dark-restore-final.patch
git apply /caminho/para/hermes-one-dark/patches/0009-one-dark-restore-final.patch
```

A série histórica `0001`–`0008` é apenas de referência (veja a nota de escopo acima).

## Como Buildar e Testar (Dentro do `hermes-agent`)

Use o runtime Node/pnpm compatível com `pm/lock.json` (`--no-install` reutiliza `.build/webapp-workspace`):

```bash
export PATH="$HOME/.hermes/tools/pnpm-12.15.0-linux-x64:$HOME/.hermes/tools/node-26.12.1-linux-x64/bin:$PATH"

# Build de produção do webapp (sem instalar dependências)
node apps/desktop/scripts/build-webapp.mjs --no-install

# Suítes Vitest do tema (suítes de terminal são código upstream no escopo visual-only)
cd .build/webapp-workspace/apps/desktop
./node_modules/.bin/vitest run src/themes
```
