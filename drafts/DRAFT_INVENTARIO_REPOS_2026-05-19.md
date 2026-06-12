# DRAFT — INVENTARIO_REPOS.md (2026-05-19, passada 9)

> Draft de alterações ao `INVENTARIO_REPOS.md` motivadas pela descoberta do
> orquestrador de setup local mantido pelo Rafa (`setup-ftd-ionica_frontend`).
>
> Status: **aprovado em chat 2026-05-19**, pendente gravação no doc final.
>
> Convenção: cada mudança numerada abaixo é um patch independente. A soma 99
> repositórios não muda — apenas remanejamento de 1 repo + categoria nova.

---

## Mudança 1 — Resumo por categoria (tabela principal)

**Localização:** seção "Resumo por categoria", tabela de categorias.

**Diff:**

```diff
 | Categoria | Prefixo | Qtd | Notas |
 |-----------|---------|-----|-------|
 ...
 | Test Automation | `ta-*` | 6 | Robot Framework... |
 | `ts-*` (a definir) | `ts-*` | 7 | **Função desconhecida.**... |
 | Microsserviço auxiliar | `backstage-*` | 1 | Provavelmente... |
 | Arquitetura/Diagramas | `arq-*` | 1 | Diagramas C4... |
+| Orquestração / Tooling FE | `setup-*` | 1 | Orquestrador oficial de setup local FE (Rafa). Único nesta categoria por enquanto — espaço pra futuros `setup-*` ou tooling cross-stack |
-| Outros standalone | — | 5 | onboarding-v3, v1-v3, repo-teste-aks, setup-frontend, FTD raiz |
+| Outros standalone | — | 4 | onboarding-v3, v1-v3, repo-teste-aks, FTD raiz |
 | **TOTAL** | | **99** | |
```

**Linha de soma de verificação (logo abaixo da tabela):**

```diff
-**Soma de verificação:** 9 + 10 + 12 + 15 + 13 + 12 + 3 + 2 + 1 + 2 + 6 + 7 + 1 + 1 + 5 = **99**.
+**Soma de verificação:** 9 + 10 + 12 + 15 + 13 + 12 + 3 + 2 + 1 + 2 + 6 + 7 + 1 + 1 + 1 + 4 = **99**.
```

---

## Mudança 2 — Nova seção detalhada "Orquestração / Tooling FE"

**Localização:** inserir **antes** da seção "Outros standalone" no
"Detalhamento por categoria".

**Conteúdo a inserir:**

```markdown
### Orquestração / Tooling FE — 1 repo ⚠️ CATEGORIA NOVA (passada 9 — 2026-05-19)

> Categoria criada com espaço pra crescer. Por hora, único membro é o orquestrador
> oficial de setup local mantido pelo Rafa.

- `setup-ftd-ionica_frontend` ⭐ Camada 3 — **orquestrador oficial de setup local FE**

**FATO** (passada 9 — 2026-05-19): repo coordena clone, instalação de deps,
geração de `.env.development` e startup paralelo dos 7 repos FE V3 (1 host +
5 remotes + 3 libs source). Estrutura: `envs/`, `scripts/`, `repositorios.json`,
scripts npm `install:mfes` e `start:mfes`.

**Branch oficial pra rodar local:** `feature/2026` (não `main`, não `develop`).
Diferente da convenção V3 prd/qa/uat/develop — provável herança da war room ou
escolha deliberada do Rafa pra setup tooling.

**Implicação arquitetural:** libs React (`toolkit`, `ions-facade`, `shared`) são
clonadas como **source**, não consumidas via feed privado. Recalibra achado
anterior do mismatch de versão do toolkit (Seção 4 do ONBOARDING) — mismatch
era artefato de clones de momentos diferentes, não bug.

**A confirmar:**
- Função exata de cada script em `scripts/`.
- Estrutura de `envs/` (templates por MFE? por ambiente?).
- Quem mantém o repo (Rafa primário, mas tem co-owners?).
- Pipeline próprio do setup-* ou só uso local?
```

---

## Mudança 3 — Ajustar seção "Outros standalone"

**Localização:** seção "Outros standalone" no "Detalhamento por categoria".

**Diff:**

```diff
-### Outros standalone — 5 repos
+### Outros standalone — 4 repos

 - `FTD - Aceleração Iônica` ← repo "guarda-chuva" com a wiki (branch padrão: `wikiMaster`)
 - `onboarding-v3` ← repo standalone da V3 do Onboarding
 - `v1-v3` ← **repo de migração V1→V3** — interesse alto pra fase pós-MVP
 - `repo-teste-aks-ionica-v3` ← **testes em Azure Kubernetes Service (AKS)** — confirma uso de K8s
-- `setup-ftd-ionica_frontend` ← setup geral do FE, função a confirmar
```

---

## Mudança 4 — Histórico do inventário

**Localização:** seção "Histórico do inventário" no final do doc.

**Diff:**

```diff
 ## Histórico do inventário

 - **v1 (anterior):** 41 repos categorizados — número stale, baseado em conversa antiga (não-verificado)
 - **v2 (este, 2026-05-17):** 99 repos verificados via `az repos list` — fonte de verdade primária
+- **v2.1 (2026-05-19, passada 9):** sem mudança no total de 99, mas remaneja `setup-ftd-ionica_frontend` de "Outros standalone" pra nova categoria "Orquestração / Tooling FE". Função do repo confirmada como FATO via setup oficial do Rafa.
```

---

## Mudança 5 — Novo achado [D10] sobre o setup

**Localização:** seção de achados-chave, logo após `[D9]`.

**Conteúdo a inserir:**

```markdown
### [D10] Existe orquestrador FE oficial — `setup-ftd-ionica_frontend`

**FATO** (passada 9 — 2026-05-19): Rafa mantém repo orquestrador que automatiza
o setup local da V3 inteira. Resolve várias dores que devs novos enfrentariam
no caminho "na unha" (npm token, base64 do PAT, envs por MFE, ordem de subida).

**Impacto:**
- Reduz `hipóteses anteriores de bug` a `artefatos de clone` (toolkit mismatch — Seção 4 do ONBOARDING).
- Sinaliza que **propor ferramental novo de setup duplicaria esforço** —
  conversar com Rafa antes.
- Indica que o time tem mais ferramental institucional do que aparenta em wiki.

**Pendente em camada 3:** ler `repositorios.json`, scripts e templates `envs/`
do orquestrador pra mapear o que ele faz exatamente.
```

---

## Notas de aprovação (chat 2026-05-19)

1. **Marcador "Camada 3"** confirmado como nomenclatura válida (analogia com Camada 1 ⭐ BFFs / Camada 2 ⭐ verificações).
2. **Achado [D10]** confirmado como localização correta (após D9).
3. **"Passada 9"** confirmada como numeração da sessão atual.
4. **Ordem de inserção:** categoria "Orquestração / Tooling FE" antes de "Outros standalone" — mantida ordem do resumo.

---

## Próximas etapas (ordem combinada)

1. ✅ DRAFT INVENTARIO (este arquivo)
2. ⏭️ DRAFT ONBOARDING_FRONTEND — nova seção "Setup local oficial" + recalibração do mismatch toolkit
3. ⏭️ DRAFT SESSION_CONTEXT — passada 9 marcada com achados do dia
4. ⏭️ DRAFT CLAUDE.md — apontar ONBOARDING como local de consulta de setup local (decisão C)

Mudança não-draft (direto):

5. `.gitignore` — adicionar `mfe/` e `_old-clones/` se não estiverem
