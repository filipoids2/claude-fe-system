# Excluir material(is) de uma aula em lote (Home Aula — Professor/Gestor)

> **Tipo:** feature
> **PBI:** #181113 — Excluir Material de uma Aula
> **Branch:** feature/pbi-181113
> **Status:** concluída
> **Início:** 2026-06-18
> **Conclusão:** 2026-06-18
>
> Sub-tasks no Azure (CA06-07, CA08-11, CA12-14) são métrica de quebra do PBI; entrega
> consolidada em **1 commit único** da PBI.

---

## 1. Resumo curto (pra copiar em PBI/PR/daily)

Professor/Gestor pode excluir material(is) **não-publicados** de uma aula, em lote,
via barra de organização da Home Aula. Botão "Excluir" com contador → modal de
confirmação (singular/plural) → loading 2s → toast. Cobre desktop, tablet e mobile.
Material **publicado ou agendado** não é excluível (requer "Desativar" antes).

## 2. Problema / Objetivo

A barra de organização da Home Aula já tinha botão "Excluir" desenhado/codado mas
inacessível (gated por `{false && ...}`) e sem fluxo de modal/serviço. Faltava
destravar o botão, plugar modal de confirmação, integrar o endpoint de exclusão em
lote e impedir exclusão de material já publicado/agendado.

## 3. Solução aplicada

- **CA06/07 — destravar o botão:** removido o gate `{false &&}` nos 3 branches
  responsivos (desktop, mobile, tablet) já existentes em `Aulas/Conteudo.tsx`. O
  contador `qtdExcluir` já existia no `useEffect` do `listaTooltipBotoes`.
- **CA08-11 — modal de confirmação:** clonado do fluxo Desativar. Novo helper
  `ModalPropsExcluirConteudo` (singular/plural via ternário sobre `qtdExcluir === 1`)
  + `toggleModalExcluirMaterial` em `HomeAula.tsx`. Reusa o `<Modal>` compartilhado —
  backdrop, "X", clicar-fora e loading-no-botão saem de graça.
- **CA12-14 — exclusão em lote:** `sendExcluirSelecaoMultipla` chama
  `HomeAulas.deleteContent(guidSala, body)` (DELETE `/conteudos`, body
  `[{guid_conteudo}]`). A coleta achata os `guid_conteudo` filhos de cada card
  selecionado. Loading via `LoadingFullPage` (com `await new Promise(r => setTimeout(r,2000))`
  fixo) + toast sucesso/erro + refetch da aula.
- **CA13 — elegibilidade:** predicado único `isItemExcluivel`
  (`!fg_publicado && !dt_agendamento`), aplicado **em dois lugares**: contador
  `qtdExcluir` no `useEffect` E nas 3 coletas (desktop/mobile/tablet) antes do
  flatten para `guid_conteudo`. Mantém coerência entre o "Excluir (N)" e o payload
  enviado ao backend.

**Zero componente novo:** todos os componentes (Modal, ButtonWithImage,
LoadingFullPage, Toast, helpers de tracking) já existiam — feature foi composição
de blocos prontos + um helper de modal + um handler de send.

## 4. Arquivos alterados

| Arquivo:linha | Mudança |
|---|---|
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Cabecalho/hook/modalProps.tsx:245-270` | Novo `ModalPropsExcluirConteudo` (singular/plural via ternário sobre `qtdExcluir === 1`) |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/Aulas/HomeAula.tsx:51` | Import `ModalPropsExcluirConteudo` |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/Aulas/HomeAula.tsx:235-236` | States `selectedGuidsToDelete`, `qtdParaExcluir` |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/Aulas/HomeAula.tsx:862-909` | `sendExcluirSelecaoMultipla` — body `[{guid_conteudo}]` → `HomeAulas.deleteContent(...)`, loading 2s, toast singular/plural, analytics |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/Aulas/HomeAula.tsx:911-927` | `toggleModalExcluirMaterial` — abre modal via `ModalPropsExcluirConteudo` |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/Aulas/HomeAula.tsx:1509` | Prop `toggleModalExcluirMaterial` passada para `<Conteudo />` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:73,176` | Tipo + destructure da prop `toggleModalExcluirMaterial` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:262-266` | Predicado `isItemExcluivel` (`!fg_publicado && !dt_agendamento`), tipado via `Pick<StructuredContentWithEditing,...>` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:651` | Contador `qtdExcluir` filtrado por `isItemExcluivel(item)` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:1234-1281` | Botão "Excluir" desktop destravado (id `btn-excluir-materiais-home-aula`); coleta com filtro `isItemExcluivel` antes do `pushConteudoGuids` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:1425-1465` | Botão "Excluir" mobile (id `btn-excluir-materiais-home-aula-mobile`); coleta filtrada |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Aulas/Conteudo.tsx:1574-1614` | Botão "Excluir" tablet (id `btn-excluir-materiais-home-aula-tablet`); coleta filtrada |

## 5. Como testar

1. **Visibilidade do botão (3 layouts):** Home Aula → selecionar ≥1 card → botão
   "Excluir (N)" aparece (desktop/tablet com label + contador; mobile só ícone).
   Sem seleção → botão some.
2. **Contador correto:** selecionar 2 não-publicados + 1 publicado → tooltip mostra
   **"Excluir (2)"** (não 3). Selecionar só publicados → "Excluir (0)" e botão
   desabilitado (`tertiaryDisabled` + `handleClick = undefined`).
3. **Modal (4 fechamentos):** clicar "Excluir" → modal abre. Singular vs plural
   conforme `qtdExcluir`. Fechamentos: botão "Cancelar", "X" do header, click no
   backdrop, click em "Excluir material(is)" — todos mantêm a seleção dos cards.
4. **Exclusão real em sala de teste:** selecionar 2 cards não-publicados →
   confirmar → loading full-page por ~2s → toast "Materiais excluídos!" → cards
   somem da lista (refetch).
5. **CA13 mista:** selecionar 2 não-publicados + 1 publicado → confirmar → loading
   → toast sucesso → os 2 somem, o **publicado permanece** (não entrou no payload).

## 6. Notas técnicas / Achados

- **Endpoint correto:** `DELETE /conteudos` com body `[{guid_conteudo}]` — confirmado
  com TL + Swagger. `DELETE /aulas` com `guid_estrutura_aula` é exclusão de **aula**
  inteira, fluxo diferente. PBI descrevia o endpoint errado (copy-paste de outra
  task). Lição: confirmar endpoint com TL antes de codar qualquer `DELETE`.
- **Exclusão é lógica (soft delete):** reversível no banco; o backend marca como
  excluído mas mantém o registro.
- **Agendado = publicado** para CA13: alinhado ao padrão dos irmãos `qtdDesativar` /
  `qtdPublicar`. Predicado único = mudar essa regra é 1 linha.
- **`qtdExcluir` era o único contador sem filtro:** `qtdDesativar` e `qtdPublicar`
  já filtravam corretamente. Agora os 4 contadores são consistentes.
- **Débito herdado (não tocado):** coleta de cards no botão Excluir do **desktop**
  usa `orderedContentGroups.filter(g => g.originalType === ...)[index]`, enquanto
  **mobile/tablet** usam os arrays diretos (`resources[index]` etc). Inconsistência
  pré-existente em todos os 3 botões da barra (Excluir/Desativar/Publicar) — fora
  do escopo dessa PBI.
- **Tipagem do predicado:** `Pick<StructuredContentWithEditing, 'fg_publicado' | 'dt_agendamento'>`
  aceita tanto `OrderedContentGroup` (filho via `extends`, usado pelo desktop)
  quanto `StructuredContentWithEditing` (pai, usado por mobile/tablet) sem `any` e
  sem precisar importar 2 tipos.
- **Granularidade card → conteúdos:** 1 card pode ter N `guid_conteudo` filhos. A
  coleta achata todos os filhos de cada card selecionado num único `string[]`.
- **`HomeAula.tsx` tem 2 funções de "publicado":** `isContentPublishedOrScheduled`
  (lookup por `guid_estrutura_aula`, usado por Desativar) e o novo predicado
  `isItemExcluivel` (recebe item direto, usado por Excluir). Diferentes porque o
  Excluir trabalha com `guid_conteudo` na coleta — não daria pra reusar
  `isContentPublishedOrScheduled` no `.filter` do array final.

## 7. Cross-references

- Commit: `081a50e` — `feature/pbi-181113 - [DEV] [Plano de aula] Exclusão de material dentro da aula (Professor/Gestor)`
- PR: (a abrir)
- Docs afetados: nenhum doc principal alterado.
- TASK_LOGS relacionados:
  - `2026-06-12_botao-excluir-sala-permissao.md` — mesma barra de ações no MFE de
    Sala; origem do aprendizado "cuidado com `replace_all` em molecules responsivas
    duplicadas" (aplicado aqui nas 3 edições pontuais desktop/mobile/tablet).
- Achados (memória bruta, não-trackado em git):
  `mfe-ftd-ionica_sala/drafts/ACHADOS_pbi-181113-exclusao-materiais.md`
