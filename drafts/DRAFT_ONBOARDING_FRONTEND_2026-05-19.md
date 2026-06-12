# DRAFT — ONBOARDING_FRONTEND.md (2026-05-19, passada 9)

> Draft de alterações ao `ONBOARDING_FRONTEND.md` motivadas pela descoberta do
> orquestrador de setup local mantido pelo Rafa (`setup-ftd-ionica_frontend`).
>
> Status: **aprovado em chat 2026-05-19**, pendente gravação no doc final.
>
> Convenção: 3 mudanças independentes — 1 inserção nova + 2 recalibrações
> (callouts preservando texto original, conforme opção 2 aprovada).

---

## Mudança 1 — Nova subseção dentro da Seção 2: "Setup local oficial"

**Localização:** logo após a última subseção atual da Seção 2 (antes da Seção 3 começar).

**Decisão de organização:** subseção dentro da Seção 2 (não nova seção raiz nem 2.5).
Setup local é parte da arquitetura prática — pertence conceitualmente à Seção 2.
Evita renumerar Seções 3-9 e suas referências cruzadas.

**Conteúdo a inserir:**

```markdown
### Setup local oficial — `setup-ftd-ionica_frontend` (passada 9 — 2026-05-19)

> Descoberto na passada 9 via Rafa (especialista do time). Substitui o caminho
> "na unha" de clonar/instalar/configurar cada MFE individualmente.

**FATO:** existe repo orquestrador `setup-ftd-ionica_frontend` (branch
`feature/2026`) que automatiza o setup local da V3 inteira. Categorizado em
`INVENTARIO_REPOS.md` v2.1 como "Orquestração / Tooling FE" (categoria nova,
1 repo).

#### Estrutura local esperada

​```
D:\local\ftd\ftd-frontend\
├── (docs versionados na raiz: CLAUDE.md, ONBOARDING_*.md, etc.)
├── mfe\
│   ├── setup-ftd-ionica_frontend\       ← orquestrador, branch feature/2026
│   ├── lib-ftd-ionica-react_ions-facade\ ← branch develop
│   ├── lib-ftd-ionica-react_shared\      ← branch develop
│   ├── lib-ftd-ionica-react_toolkit\     ← branch develop
│   ├── mfe-ftd-ionica_agenda\            ← branch prd
│   ├── mfe-ftd-ionica_atividade\         ← branch prd
│   ├── mfe-ftd-ionica_bancoquestao\      ← branch prd
│   ├── mfe-ftd-ionica_mural\             ← branch prd
│   ├── mfe-ftd-ionica_portal\            ← branch prd (HOST)
│   └── mfe-ftd-ionica_sala\              ← branch prd
└── _old-clones\ (gitignored)
​```

#### Comandos

| Etapa | Comando | O que faz |
|-------|---------|-----------|
| Clone inicial | `git clone -b feature/2026 ...` no `setup-ftd-ionica_frontend` (ver README do repo) | Repo orquestrador + lista de outros repos pra clonar |
| Instalação | `npm run install:mfes` | Copia envs prontos + roda `npm install` em todos os MFEs |
| Subir tudo | `npm run start:mfes` | Sobe portal + 5 remotes em paralelo |

#### Pré-requisitos

- **Node 22** (obrigatório — declarado em `engines` dos repos).
- **NPM_TOKEN** exportado: PAT Azure DevOps, escopo `Packaging (Read)`,
  **codificado em base64** (tenant FTD exige formato base64 no `_password`
  do `.npmrc` — não funciona com PAT em texto puro).
- **SSH configurado** pra Azure DevOps (clones via `git@ssh.dev.azure.com`).

#### Branches oficiais (importante)

- **MFEs (`mfe-*`)**: `prd` (não `main`, não `develop`).
- **Libs React** (`lib-ftd-ionica-react_*`): `develop`.
- **Orquestrador** (`setup-*`): `feature/2026`.

Diverge da convenção V3 documentada na Seção 6 (`prd/qa/uat/develop`).
Provável herança da war room ou escolha específica do tooling. **A confirmar
com Rafa**.

#### Implicação arquitetural — libs como source

As 3 libs React (`toolkit`, `ions-facade`, `shared`) são **clonadas como
source**, não consumidas via feed privado (Azure Artifacts `ions-ds`/`ionica-v3`).

Isso muda a leitura de achados anteriores — ver **callout de recalibração na
Seção 4** (mismatch toolkit) e **callout de recalibração na Seção 0**
(estranhezas → war room).

#### Quem mantém

Rafa (especialista do time, pessoa de referência pra setup local).
Co-owners — a confirmar.
```

> ⚠️ Nota técnica sobre o bloco de árvore de diretórios acima: dentro do
> draft, a árvore está dentro de um code block que usa caracteres zero-width
> nos delimitadores pra não conflitar com o code block markdown que envolve
> tudo. Ao gravar no doc final, substituir os ` ​``` ` por ` ``` ` (três crases
> normais).

---

## Mudança 2 — Recalibração na Seção 0 (callout, preservando texto)

**Localização:** Seção 0 → "Implicação na leitura do código" → lista de
"estranhezas" provavelmente consequência da war room.

**Antes (manter intacto):**

```markdown
Várias estranhezas do código provavelmente são **consequência da war room**, não decisão arquitetural consciente:

- Stack heterogênea entre MFEs (Redux vs Zustand vs ambos).
- HTTP client inconsistente (axios em alguns, fetch em outros).
- Mismatch de versão em singleton (`lib-ftd-ionica-react_toolkit@0.0.13` em Sala vs `1.0.3` no portal).
- Names errados em `package.json` (copy-paste apressado).
- Zero testes em todos os MFEs novos.
- CSS injection conhecido com workaround `!important`.
- Mesh entre MFEs (Atividades→Banco, Salas→Mural+Atividades) sem rastreamento formal de consumers.
```

**Adicionar logo depois da lista (callout novo):**

```markdown
> 🔁 **Recalibração — passada 9 (2026-05-19):** o item "Mismatch de versão em
> singleton" foi **revisto**. Não era bug — é artefato esperado do setup
> oficial do Rafa, que clona as libs React como **source** (não via feed privado).
> Cada lib tem 1 clone único servindo todos os MFEs em runtime; mismatch nos
> `package.json` dos clones antigos refletia momentos diferentes de extração,
> não estado real do sistema. Ver Seção 2 → "Setup local oficial" e Seção 4
> → FATO do mismatch (recalibrado).
```

---

## Mudança 3 — Recalibração na Seção 4 (callout, preservando texto)

**Localização:** Seção 4 → "FATO — artefatos de copy-paste em `package.json`"
→ último bullet da lista.

**Antes (manter intacto):**

```markdown
- Mismatch de versão de singleton: `sala` consome `lib-ftd-ionica-react_toolkit@0.0.13`
  enquanto os demais usam `1.0.3` — `mfe-ftd-ionica_sala/package.json:79`.
```

**Adicionar logo depois do bullet (callout novo):**

```markdown
> 🔁 **Recalibração — passada 9 (2026-05-19):** este FATO **muda de
> classificação**. O mismatch em `package.json` é real e citação válida, mas a
> interpretação anterior ("bug de war room que pode quebrar singleton em runtime")
> era equivocada.
>
> **Setup oficial do Rafa** (Seção 2 → "Setup local oficial") clona as libs
> React como source — `lib-ftd-ionica-react_toolkit`, `lib-ftd-ionica-react_ions-facade`,
> `lib-ftd-ionica-react_shared` ficam em `mfe/lib-*` e são referenciadas via
> path local pelos MFEs. Em runtime, **há 1 instância única** da lib servindo
> todos os MFEs — não há mismatch real.
>
> O `package.json` de `sala` permanece declarando `0.0.13` por inércia (não
> foi atualizado), mas isso não é consumido. Vale PR pra alinhar, mas é higiene,
> não bug.
>
> **Classificação anterior:** indício de risco arquitetural.
> **Classificação atual:** artefato cosmético de clone — alinhar versões nos
> `package.json` quando tocar nesses repos.
```

---

## Decisões registradas (chat 2026-05-19)

1. **Opção de inserção:** A (subseção dentro da Seção 2), como recomendado.
2. **Descrição do Rafa:** "especialista do time, pessoa de referência pra
   setup local". Aprovado.
3. **Comandos no doc:** referência ao README do repo orquestrador em vez de
   transcrever os `git clone` (trade-off: menos autocontido, mais resistente a
   desatualização).
4. **Emoji 🔁 nos callouts:** aceito como elemento estrutural (não emoji
   conversacional). Persona "sem emojis" não conflita com marker de mudança
   visual no doc.

---

## Próximas etapas (ordem combinada)

1. ✅ DRAFT INVENTARIO (gravado em `drafts/DRAFT_INVENTARIO_REPOS_2026-05-19.md`)
2. ✅ DRAFT ONBOARDING_FRONTEND (este arquivo)
3. ⏭️ DRAFT SESSION_CONTEXT — passada 9 marcada com achados do dia
4. ⏭️ DRAFT CLAUDE.md — apontar ONBOARDING como local de consulta de setup local (decisão C)

Mudança não-draft (direto):

5. `.gitignore` — adicionar `mfe/` e `_old-clones/` se não estiverem

---

## Notas de aplicação no doc final

Ao gravar as 3 mudanças no `ONBOARDING_FRONTEND.md`:

- **Mudança 1:** atenção à árvore de diretórios dentro do bloco de markdown
  (substituir `​```` por ` ``` ` — ver nota técnica acima).
- **Mudança 2 e 3:** **NÃO apagar nem reescrever** o texto original. Inserir
  os callouts **logo depois** dos trechos existentes. Isso preserva o histórico
  de raciocínio (alinha com `workflow.md` → drafts são histórico).
- **Cross-references:** os callouts apontam para "Seção 2 → Setup local oficial"
  e "Seção 4 → FATO do mismatch". Confirmar que os textos finais usam exatamente
  esses títulos de âncora antes de gravar.
