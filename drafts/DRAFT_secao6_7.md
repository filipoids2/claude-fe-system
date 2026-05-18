# DRAFT — Seções 6 e 7 do ONBOARDING_FRONTEND.md (proposta, NÃO aplicada)

> Passada B (Seções 6 + 7), 2026-05-15. Leitura: `package.json` de `login`/`monolithic`
> (commitlint + branch-validator), glob de configs (`azure-pipelines.yaml`,
> `eslint.config.mjs`, `.editorconfig`, `.github/`).
>
> **Status: aguardando aprovação. Nada gravado no arquivo final.**
> Caminhos relativos a `D:\local\ftd\ftd-frontend\`.

---

## Changelog

- **Seção 6 reescrita** no padrão FATO / wiki-não-confirmado / A confirmar / gaps.
  - Branching + commit saem de "não documentado": a convenção existe e é verificável
    no código (`commitlint` + `git-branch-validator`).
  - **Atomic Design rebaixado** de "Documentado" para "documentado na wiki, não
    confirmado no código" — conforme combinado.
  - CI/CD por MFE (`azure-pipelines.yaml`) entra como FATO.
- **Seção 7: só a entrada "Lemonade" muda** (resolvida na passada 3). As demais —
  CDC, Objeto Digital, "Carga Zero", "Ensino Superior", `evh-`/`sigr-` — foram conferidas
  e **nenhuma virou clara** nas passadas 2-4; ficam como estão.

---

## Conteúdo proposto — Seção 6 (substitui o bloco atual, ~linhas 551-577)

## 6. Convenções e padrões do time

> ⚠️ Atualizada em 2026-05-15 (passada 5) com leitura de configs dos repos. FATO =
> verificado no código; itens vindos só da wiki ficam marcados como não confirmados.

### FATO — verificado no código

- **Commit message: Conventional Commits.** `login` e `monolithic` declaram `commitlint`
  estendendo `@commitlint/config-conventional`, com `type-enum` explícito: `ci`, `chore`,
  `docs`, `feat`, `bugfix`, `hotfix`, `perf`, `refactor`, `revert`, `style`, `release`,
  `merge` — `mfe-ftd-ionica_login/package.json:176-199`,
  `fe-ftd-ionica-monolithic/package.json:175-198`.
- **Branch naming validado por regex.** `git-branch-validator` exige
  `(feature|hotfix|bugfix)/(pbi|bug|glpi|fis)-{3-8 dígitos}` ou `(release|sprint|context)/...`
  — `mfe-ftd-ionica_login/package.json:201-205`. A mensagem de erro do validator aponta
  pra wiki Azure DevOps "Regras-de-Nomenclaturas-de-Branchs" (página 515): **há convenção
  de branch documentada na wiki**, fora do que foi lido nesta passada.
- **Enforcement só no stack velho.** Commit/branch são checados via **husky** em
  `login`/`monolithic` (scripts `hook:commitlint`, `hook:branch-validator`,
  `hook:lint-staged`). Os 6 MFEs Rsbuild **não têm husky** (ver Seção 4) — a mesma
  convenção não é imposta localmente neles.
- **CI/CD por MFE.** Cada um dos 6 MFEs do stack novo tem um `azure-pipelines.yaml`
  próprio na raiz — confirma pipeline/deploy independente por MFE (era hipótese na
  Seção 3). Conteúdo dos pipelines não lido nesta passada.
- **Lint: ESLint flat config** (`eslint.config.mjs`) nos 8 repos — versões na Seção 4.
- **`.editorconfig`** existe em `login` e `monolithic`; ausente nos 6 MFEs Rsbuild —
  coerente com "ferramental de formatação só no stack velho" (Seção 4).
- **`.github/copilot-instructions.md` por MFE — a fonte de convenção mais concreta e
  atual que apareceu.** Existe na maioria dos MFEs do stack novo (5 de 6). Lido só o do
  `portal` (284 linhas, inglês, estruturado): documenta o padrão de Module Federation
  (host/remote, async boundary `index.tsx`→`bootstrap.tsx`, passo-a-passo pra adicionar
  MFE), **padrão obrigatório de ícones SVG via SVGR** ("Established Oct 2025" — proíbe
  inline SVG, `img src` e data-URI), estrutura de pastas (`src/pages`, `src/components`,
  `src/assets/icons`, `Config.ts`, `bootstrap.tsx`), padrão de rota+auth (`ProtectedRoute`
  + `Master`) e TypeScript strict. Mais concreto e atual que a wiki. Os outros 5
  `copilot-instructions.md` não foram lidos — podem divergir.

### Documentado na wiki — NÃO confirmado no código (esta passada)

- **Atomic Design** com regras de idioma: SubAtomic / Atoms / Molecules → `shared/`
  (nomes em inglês); Organisms / Templates / Pages → `app/` (nomes em português).
  Justificativa documentada: palavras reservadas/config em EN, negócio em PT. **Fonte:
  `👨‍💻-Frontend/Boilerplate.md`. Não confirmado no código — e há contra-indício: o
  `copilot-instructions.md` do `portal` descreve estrutura `src/pages` + `src/components`,
  não a hierarquia `shared/{subAtomic,atoms,molecules}` + `app/` da wiki. Possível que o
  host (portal) tenha estrutura própria e o Atomic Design valha — ou não — nos MFEs de
  feature. A confirmar lendo as árvores `src/`.**
- **Naming**: PascalCase para arquivos, imagens e ícones; ações em PT no infinitivo. (wiki)
- **IDs de elementos**: `kebab-case`, `btn-{acao}-{contexto}-{tela}`, com planilha mestre
  `Ids-para-Testes.xlsx`. (wiki)
- **Assets**: localização hierárquica por escopo (componente → `shared/`, organismo →
  `app/components/organisms/`, etc.). (wiki)
- **REST**: ver Seção 5.
- **Versionamento de wiki**: `.order` em cada pasta define a sequência de leitura. (wiki)
- **Sprints**: cadência quinzenal aparente (Sprint 23 → 28). (wiki — `[Iônica-3.0]--Review.md`)
- **PBI** (Product Backlog Item): unidade de trabalho no Azure DevOps. (wiki)

### A confirmar (próximas passadas)

- Os outros 5 `.github/copilot-instructions.md` (lido só o do `portal`) e os
  `troubleshooting.instructions.md` — podem divergir entre MFEs.
- Conteúdo dos `azure-pipelines.yaml` (etapas de build/lint/test/deploy, gatilho por PR).
- CI/CD dos repos Nx (`login`/`monolithic`) — pipeline não localizado nesta varredura.
- A convenção de branch na wiki Azure DevOps (página 515) referenciada pelo branch-validator.

### Não documentado (gaps → Seção 9)

- Checklist de code review.
- Critério de pronto / Definition of Done.
- Convenção de componentes (props, ref forwarding, controlled vs uncontrolled).
- Convenção de hooks customizados.
- Política de feature flags.
- (Naming de testes: sem objeto — zero testes em todos os repos, ver Seção 4.)

---

## Conteúdo proposto — Seção 7: uma linha muda

A Seção 7 é um glossário de domínio. Só a entrada **Lemonade** muda.

**Linha atual:**
> \| **Lemonade** \| Citado em `Lemonade-Json-vs-Relacional.md` como mapeamento JSON↔relacional — **conteúdo é stub** (3 linhas). Pode ser ferramenta interna. Perguntar. \|

**Linha proposta:**
> \| **Lemonade** \| Editor de questões de terceiro — pacote npm `@oneclick/react-lemonade-editor` 3.38.1, usado em `atividade`, `bancoquestao` e `sala` (ver Seção 4). O doc `Lemonade-Json-vs-Relacional.md` da wiki (stub) provavelmente trata do formato JSON desse editor. Confirmar com o time o escopo de uso. \|

### Demais termos do glossário — conferidos, sem mudança

Verificado se as passadas 2-4 esclareceram outros termos abertos:
- **CDC** — segue sem definição; nenhuma passada leu o conteúdo dos docs `CDC-*`.
- **Objeto Digital** — segue "definição exata não documentada".
- **"Carga Zero"** — não esclarecido (continua na Seção 9 → Contradições).
- **"Ensino Superior"** (padrão ignorado no Onboarding v2) — não esclarecido.
- **`evh-` / `sigr-`** — diferença entre os prefixos segue em aberto.
- **CKEditor** — Seção 4 achou as versões (47/46/41), mas a definição de glossário
  ("editor WYSIWYG, licença Free") continua válida; sem mudança.

Mantidos como estão.

---

**O que será gravado numa tacada, após aprovação:**
1. Seção 6 — bloco inteiro reescrito (~linhas 551-577).
2. Seção 7 — 1 edição: a linha "Lemonade".
3. Seção 9 — 2 perguntas novas na subseção "🔍 Stack": copilot-instructions como fonte
   autoritativa de convenções; e a "wiki Azure DevOps pág. 515" do branch-validator.

Nada gravado até a aprovação. `DRAFT_secao6_7.md` fica em disco como histórico.
