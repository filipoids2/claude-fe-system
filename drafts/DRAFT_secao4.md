# DRAFT — Seção 4 do ONBOARDING_FRONTEND.md (proposta, NÃO aplicada)

> Gerado em 2026-05-15 lendo os 8 repos populados (`package.json`, `package-lock.json`,
> presença de `rsbuild.config.ts` / `nx.json`). Substitui `ONBOARDING_FRONTEND.md`
> linhas **225-256** (cabeçalho `## 4.` até o `---` antes da Seção 5).
>
> **Status: aguardando aprovação. Não foi escrito no arquivo final.**
> Este é o primeiro draft da Seção 4 a partir de código — não existe versão anterior
> baseada em código pra comparar (a Seção 4 atual é wiki-only / passada 1).
>
> Caminhos das citações são relativos a `D:\local\ftd\ftd-frontend\`.

---

## Changelog vs. Seção 4 atual

- **Reescrita inteira da tabela.** A versão atual lista 13 itens como `gap`/`não
  documentado` (build tool, framework UI, roteamento, estado, estilização, testes,
  lint, monorepo). Todos respondidos pelo código.
- **Datadog rebaixado de FATO para hipótese.** A Seção 4 atual afirma "Datadog RUM"
  citando a wiki. `datadog` **não aparece em nenhum dos 8 `package.json`** — ver hipótese abaixo.
- **Separação em stack novo vs stack velho**, consistente com a Seção 2.
- **Itens wiki-only saem da tabela de stack.** Atomic Design + IDs `kebab-case` já
  estão na Seção 6 (verificado). Analytics/Zeroheight/Figma — não cobertos em outra
  seção — ficam num bloco wiki-only explícito dentro da própria Seção 4.
- **"Mapa pessoal"** atualizado: itens que eram "novo / não documentado" agora são
  FATO; o que sobra de aprendiz é Module Federation em runtime + Rsbuild.

---

## Conteúdo proposto (substitui as linhas 225-256)

## 4. Stack frontend detalhada

> ⚠️ Reescrita em 2026-05-15 (passada 3) a partir da leitura dos `package.json`,
> `package-lock.json` e da presença de configs (`rsbuild.config.ts`, `nx.json`) nos
> **8 repos populados**. A versão anterior desta seção era passada 1 (wiki-only) e
> marcava quase tudo como `gap` — a wiki segue sem documentar a stack, mas o código
> responde. Citações apontam `repo/arquivo:linha`.

Dois mundos, como na Seção 2: **stack novo** (6 MFEs em Rsbuild) e **stack velho**
(`login` + `monolithic`, Nx). A tabela abaixo é o stack novo; o velho vem depois.

### FATO — stack novo (`portal`, `agenda`, `atividade`, `bancoquestao`, `mural`, `sala`)

| Item | Valor | Fonte (exemplo) |
|------|-------|-----------------|
| **Framework** | React **18.3.1** — idêntico nos 6 | `mfe-ftd-ionica_portal/package.json:26-27` |
| **Linguagem** | TypeScript **5.9.2** | `mfe-ftd-ionica_portal/package.json:47` |
| **Build tool** | **Rsbuild 1.5.6** (`@rsbuild/core`) + `@rsbuild/plugin-react` 1.4.0. Script `"build": "rsbuild build"` | `mfe-ftd-ionica_portal/package.json:7,34-35` |
| **Framework de UI** | SPA React puro — **sem Next/Remix**. Cada MFE tem `rsbuild.config.ts` na raiz | `mfe-ftd-ionica_portal/rsbuild.config.ts` (existe) |
| **Composição MFE** | Module Federation via `@module-federation/rsbuild-plugin` **0.19.1** | `mfe-ftd-ionica_portal/package.json:18` (detalhe na Seção 2) |
| **Roteamento** | `react-router-dom` **7.9.1** | `mfe-ftd-ionica_portal/package.json:29` |
| **Gerenciador de estado** | **Heterogêneo** — Redux Toolkit, Zustand e TanStack Query convivem; ver tabela dedicada abaixo | — |
| **Estilização** | **Tailwind 3.4.17** (nos 6) + **styled-components 6.1.x** (em 5 dos 6 — `portal` é só Tailwind) + PostCSS/autoprefixer. **Sass** (`@rsbuild/plugin-sass`) só em `bancoquestao` e `sala` | `mfe-ftd-ionica_portal/package.json:39,44,46`; `mfe-ftd-ionica_bancoquestao/package.json:73` |
| **Editor rico** | **CKEditor 5** (`ckeditor5` 47.0.0 + `@ckeditor/ckeditor5-react` 9.5.0) em `portal`, `atividade`, `mural`, `sala`. **Lemonade** (`@oneclick/react-lemonade-editor` 3.38.1) em `atividade`, `bancoquestao`, `sala` | `mfe-ftd-ionica_portal/package.json:14,22`; `mfe-ftd-ionica_atividade/package.json:24` |
| **Testes** | **Zero.** Script `test` nos 6 é literalmente `echo "this project doesn't have any tests"`; nenhuma lib de teste (Jest/Vitest/Testing Library) instalada | `mfe-ftd-ionica_portal/package.json:11` |
| **Lint** | **ESLint 9.35.0** com flat config (`@eslint/js` + `typescript-eslint` 8.43.0) | `mfe-ftd-ionica_portal/package.json:33,40,48` |
| **Format** | **Prettier ausente** — nenhum `prettier` nos `devDependencies` dos 6 | (ausência em `package.json`) |
| **Git hooks** | **Nenhum** — sem `husky`/`lint-staged`/`commitlint` nos `package.json` | (ausência em `package.json`) |
| **Monorepo** | **Não.** Cada MFE é repo isolado, com seu próprio `package-lock.json` | `mfe-ftd-ionica_portal/package-lock.json` (existe, 1 por repo) |
| **Package manager** | **npm** — `package-lock.json` nos 8; `engines.npm >=10.8.2` em `bancoquestao` | `mfe-ftd-ionica_bancoquestao/package.json:9-12` |
| **Design System** | `@ionica/ions-ds` 2.6.1, consumido via facade `lib-ftd-ionica-react_ions-facade` 0.0.8 + `lib-ftd-ionica-react_toolkit` (publicados no Azure Artifacts) | `mfe-ftd-ionica_portal/package.json:23-24` |

### FATO — gerenciamento de estado (o caos heterogêneo, quantificado)

Versões lidas direto de cada `package.json`. "—" = não declarado como dependência.

| Repo | Redux Toolkit | Zustand | TanStack Query | redux-persist |
|------|---------------|---------|----------------|---------------|
| `portal` | 2.9.2 | — | 5.87.4 | — |
| `agenda` | — | 5.0.4 | 5.87.4 | — |
| `atividade` | — | 5.0.4 | 5.87.4 | — |
| `bancoquestao` | 2.8.2 | 5.0.6 | **— (sem TanStack)** | 6.0.0 |
| `mural` | 2.3.0 | 5.0.4 | 5.87.4 | — |
| `sala` | 2.3.0 | 5.0.4 | 5.87.4 | 6.0.0 |
| `login` | 2.3.0 | 5.0.4 | 5.83.0 | 6.0.0 |
| `monolithic` | 2.3.0 | 5.0.4 | 5.83.0 | 6.0.0 |

Citações: `portal/package.json:19,20`; `agenda/package.json:28,34`; `atividade/package.json:26,34`;
`bancoquestao/package.json:39,60,65`; `mural/package.json:23,24,33`; `sala/package.json:28,29,50,55`;
`login/package.json:48,49,73,78`; `monolithic/package.json:52,56,79,84`.

**Leitura:** 5 dos 8 repos (`bancoquestao`, `mural`, `sala`, `login`, `monolithic`)
carregam **Redux e Zustand ao mesmo tempo**. `portal` é só Redux; `agenda` e
`atividade` são só Zustand. Não há um "jeito do time" — cada MFE escolheu o seu.

### FATO — cliente HTTP / data fetching

| Repo | axios | TanStack Query |
|------|-------|----------------|
| `portal`, `agenda`, `atividade`, `mural` | — | sim |
| `bancoquestao` | 1.10.0 | — |
| `sala` | 1.11.0 | sim |
| `login`, `monolithic` | 1.11.0 | sim |

Citações: `bancoquestao/package.json:40`; `sala/package.json:30`; `login/package.json:50`;
`monolithic/package.json:57`.

### FATO — stack velho (`login`, `monolithic`)

| Item | `mfe-ftd-ionica_login` | `fe-ftd-ionica-monolithic` |
|------|------------------------|----------------------------|
| **Build** | Nx **21.3.10** + Webpack | Nx **21.6.2** + Webpack |
| **Module Federation** | `@module-federation/enhanced` **0.7.6** + `@nx/module-federation` | **Nenhum** — sem `@nx/module-federation`, sem `@module-federation/*` |
| **Linguagem** | TypeScript **5.8.3** | TypeScript **5.8.3** |
| **Roteamento** | `react-router-dom` **6.28.2** | `react-router-dom` **6.28.2** |
| **Auth** | Azure AD **B2C** via OAuth2 redirect cru — **sem lib MSAL** (config em `.env.example`) | **`@azure/msal-browser` 4.18.0 + `@azure/msal-react` 3.0.16** (Azure AD) |
| **Testes** | Jest 30 + Testing Library **instalados**, mas script `test` ainda é `echo "...no tests"` | idem |
| **Format** | **Prettier 3.4.2** + `eslint-config-prettier` + `prettier-plugin-tailwindcss` | idem |
| **Git hooks** | **husky 9.1.7 + lint-staged + commitlint** (`config-conventional`) + **git-branch-validator** | idem |
| **Node** | `engines: node >=22.8.0 <23` | `engines: node >=22.8.0 <23` |

Citações: `mfe-ftd-ionica_login/package.json:86,91,129,138,141,149,176-205`;
`fe-ftd-ionica-monolithic/package.json:8-11,37-38,125,134,137,145,175`;
auth do `login`: `mfe-ftd-ionica_login/.env.example:4-7` +
`mfe-ftd-ionica_login/apps/shell/src/pages/Token.tsx:28`.
Ausência de MF no monolithic: não há `@nx/module-federation` nem `@module-federation/*`
em `fe-ftd-ionica-monolithic/package.json` (devDeps lidos integralmente).

### FATO — artefatos de copy-paste em `package.json`

(A Seção 2 aponta isto e remete a esta seção para os fatos verificáveis.)

- `mfe-ftd-ionica_agenda` declara `"name": "mfe-ftd-ionica_atividade"` — `mfe-ftd-ionica_agenda/package.json:2`.
- `fe-ftd-ionica-monolithic` declara `"name": "fe-ftd-ionica_login"` e
  `"description": "...módulo de LOGIN..."` — `fe-ftd-ionica-monolithic/package.json:2,4`.
- `mfe-ftd-ionica_login` declara `"name": "fe-ftd-ionica_login"` — prefixo `fe-` no
  campo `name` vs. `mfe-` no nome do diretório/repo — `mfe-ftd-ionica_login/package.json:2`.
- Mismatch de versão de singleton: `sala` consome `lib-ftd-ionica-react_toolkit@0.0.13`
  enquanto os demais usam `1.0.3` — `mfe-ftd-ionica_sala/package.json:79`.

### HIPÓTESES (a validar)

- **Estado heterogêneo confirma a war room (Seção 0).** Redux + Zustand juntos no mesmo
  repo (5 dos 8) sugere migração incompleta Redux→Zustand, ou uso paralelo sem critério.
  A confirmar lendo onde cada store é instanciado.
- **`portal`/`agenda`/`atividade`/`mural` não têm cliente HTTP explícito.** Sem `axios`
  nem outro client → provavelmente `fetch` nativo por baixo do TanStack Query. A confirmar.
- **`bancoquestao` usa só `axios`, sem TanStack Query nem outro data-fetching layer.**
  Implica gerenciamento manual de cache/loading/error em cada chamada. A confirmar se
  é decisão consciente ou herança da war room.
- **Sass (`@rsbuild/plugin-sass`) está em `bancoquestao` e `sala` apenas**, não nos
  outros 4 MFEs do stack novo. A confirmar: herança de código com `.scss` do monolítico
  durante a extração? Preferência dos devs? Exigência de componentes específicos do DS?
  Vale saber o critério pra não criar `.scss` em outros MFEs sem entender o porquê.
- **Datadog RUM** é decisão arquitetural histórica do projeto FTD — confirmado em uso
  nas V1 e V2 (front) e backend (input do dev). Para a V3 (stack novo): não aparece em
  nenhum dos 8 `package.json`. Hipóteses: (a) ainda não portado pro stack novo —
  feature pendente; (b) embutido na `lib-ftd-ionica-react_toolkit` (caixa-preta
  interna); (c) injetado via HTML/`index.html` externamente; (d) deliberadamente não
  trazido pra V3.
- **Auth fala com Azure AD por dois caminhos diferentes.** `monolithic` usa as libs
  `@azure/msal-browser`/`@azure/msal-react`. `login` **não declara MSAL** (confirmado:
  ausente no `package.json`), mas faz **Azure AD B2C na mão** — `mfe-ftd-ionica_login/.env.example:4-7`
  tem o endpoint OAuth2 `.../oauth2/v2.0/authorize`, `client_id` e `scope`
  (`openid profile offline_access`); o token vai para `localStorage['@ionica/msal-token']`
  (`mfe-ftd-ionica_login/apps/shell/src/pages/Token.tsx:28`). Mesmo IdP (Azure AD B2C),
  duas implementações — lib vs. redirect cru. Provável herança da war room. Como isso se
  concilia com o JWT em `localStorage['session']` descrito na Seção 5 está em aberto
  (ver Seção 9).
- **Ferramental de qualidade (Prettier, husky, commitlint, branch-validator) só existe
  no stack velho (Nx).** Não foi portado para os MFEs Rsbuild. Reforça a hipótese B da
  Seção 0: o time *tinha* o ferramental e o perdeu na migração apressada — débito por
  pressa, não decisão consciente de descartar.
- **"Lemonade"** (mistério do glossário, Seção 7): `@oneclick/react-lemonade-editor` é
  um editor de questões npm de terceiro (escopo `@oneclick`). O
  `Lemonade-Json-vs-Relacional.md` da wiki provavelmente trata do formato JSON desse editor.

### A confirmar (próximas passadas)

- Onde Redux vs. Zustand são de fato usados em cada repo (store global vs. estado local).
- Cliente HTTP real de `portal`/`agenda`/`atividade`/`mural` (fetch puro? wrapper na toolkit?).
- Se o Datadog RUM está ativo em runtime e por qual caminho.
- Drift de versão entre stacks: React igual (18.3.1), mas TS 5.9.2 (novo) vs 5.8.3
  (velho), `react-router-dom` 7.9.1 vs 6.28.2 — intencional ou só falta de bump?
- CKEditor em três versões: `ckeditor5` 47.0.0 (stack novo), `ckeditor5-build-classic`
  41.4.2 (`login`), `ckeditor5` 46.0.0 (`monolithic`). Plano de unificação?

### Documentado na wiki, não verificável no código nesta passada

Itens que vêm da wiki (`Aceleração-iônica.md`) e não dá para confirmar pelos
`package.json`/configs. Ficam registrados aqui — sem promessa de cobertura em
alguma outra seção que ainda não existe:

- **Analytics de produto**: Google Analytics, Hotjar, PowerBI.
- **Design System** publicado externamente no [Zeroheight](https://zeroheight.com/813ec1e30/p/914d22-design-tokens) (design tokens).
- **Figma**: projeto FTD compartilhado (link no `Aceleração-iônica.md`).

(**Atomic Design bilíngue** e **convenção de IDs `kebab-case`** — também wiki-only
na Seção 4 antiga — não entram neste bloco porque já estão cobertos na Seção 6,
verificado em `ONBOARDING_FRONTEND.md:287-300`.)

### Mapa pessoal: o que você já domina vs. o que é novo

- **Confortável (você já manja):** React 18, TypeScript, Tailwind, styled-components,
  Redux Toolkit, TanStack Query, react-router, Atomic Design, design systems.
- **Provavelmente novo / vale estudar:** **Module Federation em runtime** (plugin
  Rsbuild) — domínio de aprendiz declarado; **Rsbuild** como build tool (Rspack por
  baixo) se você vinha de Vite/Webpack; **Zustand**, se não usou; a coexistência de
  dois build systems (Rsbuild novo + Nx velho).
- **Atenção no projeto (não é skill, é estado do código):** não existe um padrão único
  de estado entre os MFEs; **zero testes** rodando em qualquer repo; **sem Prettier no
  stack novo** → formatação tende a divergir entre arquivos e entre devs.
- **Domínio (negócio):** terminologia Iônica (Sala, Turma, Mural, Aula Pronta, Atividade
  Pronta, Conteúdo Próprio, Objeto Digital, Componente Curricular, BNCC) — estude antes
  da primeira reunião.
