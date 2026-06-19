# Excluir material em lote — barra de organização (HomeAula)

> **Tipo:** feature
> **PBI:** 181113 (a preencher)
> **Branch:** feature/pbi-181113-dev
> **Status:** em andamento — CA12-14 bloqueada por achado em PASSO 0
> **Início:** 2026-06-18
> **Conclusão:** —

---

## 1. Resumo curto

Implementação do botão "Excluir" na barra de organização do detalhe da
aula. CA06/07 (botão visível com contador) e CA08-11 (modal de confirmação
singular/plural com preservação da seleção ao cancelar) finalizadas. CA12-14
(exclusão real em lote + loading + toast) **parada** após PASSO 0 revelar
divergência de shape: endpoint quer `guid_conteudo` e a relação parent→conteúdo
é 1:N.

## 2. Problema / Objetivo

Adicionar exclusão em lote de materiais a partir da seleção múltipla da barra
de organização. Botão já tinha esqueleto comentado; modal e API DELETE em
lote já existiam no projeto e precisavam ser conectados.

## 3. Solução aplicada (até agora)

### Fatia 1 — CA06/07: botão visível + contador

- Removidos os três wrappers `{false && (…)}` que ocultavam o botão Excluir
  (desktop, mobile, tablet) — 3 edits pontuais, sem replace global (branches
  responsivos com indentação divergente).
- Contador `qtdExcluir` já calculado no Redux: incrementa 1× por item
  selecionado em `Conteudo.tsx:644-645`, **sem filtro de elegibilidade**
  (publicado/agendado/somenteprofessor não filtram aqui — diferente de
  `qtdDesativar` e `qtdPublicar`). Não alterado nesta task.
- Estados visuais reaproveitam padrão `tertiary`/`tertiaryDisabled`.

### Fatia 2 — CA08-11: modal de confirmação

- Criado `ModalPropsExcluirConteudo(qtdExcluir, right, left?)` em
  `modalProps.tsx`, espelhando `ModalPropsDesativarConteudo`. Singular/plural
  inline via `isSingular = qtdExcluir === 1`. Ícone `trash`, `modalText2`
  vermelho ("Não é possível desfazer essa ação").
- Padrão usa o `<Modal>` único renderizado por `modalProps2 + isModalOpen`
  no `HomeAula.tsx` — não há um `<Modal>` dedicado.
- Novo callback `toggleModalExcluirMaterial` no `HomeAula.tsx`, novo state
  `selectedGuidsToDelete`. Prop passada para `<Conteudo>`.
- 3 handlers do botão (desktop/mobile/tablet) coletam guids selecionados a
  partir de `selectedXxxIndices` e disparam o toggle.
- CA11 (manter seleção ao cancelar): confirmado por inspeção — nenhum
  caminho de fechamento (Cancelar/X/overlay/placeholder de confirmar) toca
  em `selectedXxxIndices`. `resetAllCheckboxes` só roda no caminho de
  sucesso da API (caminho ainda não implementado).

### Fatia 3 — CA12-14: exclusão real (BLOQUEADA)

PASSO 0 (inspeção de shape antes de codar coleta) revelou:

- `IListaEstruturaConteudo` (parent — o que a barra seleciona) **não tem
  `guid_conteudo`** direto. Expõe `guid_estrutura_aula` e
  `guid_estrutura_aula_item` + `conteudos: IContentStructuredList[]`.
- `guid_conteudo` mora **dentro de cada filho** em `conteudos[N].guid_conteudo`.
- Relação parent : conteúdo é **1 : N**. Selecionar "1 material" pode
  significar enviar N `guid_conteudo`.

Endpoint confirmado por Swagger: `DELETE …/salas/{guidSala}/conteudos`,
body `[{ guid_conteudo }]`. Service pronto: `HomeAulas.deleteContent`
(`services/HomeAula/HomeAula.tsx:321-324`).

Endpoint do single-item antigo (`HomeAulas.excluirConteudo`) **não** é o
mesmo: bate em `/salas/{guidSala}/aulas` com `guid_estrutura_aula` — apaga
a estrutura de aula inteira, não o conteúdo. Provavelmente cascateia.

Parada pelo enunciado da task: "SE algum tipo NÃO expõe guid_conteudo
diretamente: PARE, não implemente a coleta, e me reporte o shape real.
Decidimos juntos."

Decisões em aberto antes de retomar:

- **Estratégia de coleta:** (a) enviar todos os `conteudos[].guid_conteudo`
  de cada parent selecionado — qtdExcluir conta parents, body conta filhos;
  (b) enviar só o primeiro filho de cada parent — risco de órfão; (c) trocar
  para o endpoint `/aulas` com `guid_estrutura_aula` (igual ao single-item),
  que aparentemente cascateia tudo.
- **Confirmar com BFF/TL:** o backend trata `/conteudos` em lote como "apaga
  exatamente esses N conteúdos" ou tem semântica de "apaga os parents que
  contém esses conteúdos"?

## 4. Arquivos alterados (até agora)

| Arquivo:linha                                                    | Mudança                                                                                                                            |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `src/components/organisms/Cabecalho/hook/modalProps.tsx:245-272` | Novo helper `ModalPropsExcluirConteudo` (id `modal-excluir-conteudo`, singular/plural, ícone trash, modalText2 vermelho)           |
| `src/components/organisms/Aulas/Conteudo.tsx:73`                 | Assinatura `toggleModalExcluirMaterial?: (qtdExcluir, selectedGuids) => void` (era `() => void` sem params)                        |
| `src/components/organisms/Aulas/Conteudo.tsx:176`                | Desestruturação renomeada                                                                                                          |
| `src/components/organisms/Aulas/Conteudo.tsx:1227-1277`          | Botão Excluir desktop: wrapper `{false &&}` removido, handler real coletando guids, id renomeado `btn-excluir-materiais-home-aula` |
| `src/components/organisms/Aulas/Conteudo.tsx:1418-1467`          | Botão Excluir mobile (icon-only): mesma mudança, id `…-mobile`                                                                     |
| `src/components/organisms/Aulas/Conteudo.tsx:1572-1622`          | Botão Excluir tablet: mesma mudança, id `…-tablet`                                                                                 |
| `src/components/pages/Aulas/HomeAula.tsx:48-53`                  | Import `ModalPropsExcluirConteudo`                                                                                                 |
| `src/components/pages/Aulas/HomeAula.tsx:235`                    | State `selectedGuidsToDelete`                                                                                                      |
| `src/components/pages/Aulas/HomeAula.tsx:861-877`                | Callback `toggleModalExcluirMaterial` (rightButtonAction ainda placeholder — só fecha)                                             |
| `src/components/pages/Aulas/HomeAula.tsx:1459`                   | Prop passada ao `<Conteudo>`                                                                                                       |

## 5. Como testar (fatias fechadas)

URL: `/salas/<guidSala>/aulas/<guidAula>` (tela de detalhe da aula).

1. Selecionar 1 material → barra mostra "Excluir (1)" ativo.
2. Selecionar 2+ materiais → "Excluir (N)".
3. Clicar "Excluir" → modal abre com `id="modal-excluir-conteudo"`,
   título "Excluir material?" / "Excluir materiais?", `modalText2` vermelho.
4. Cancelar / X / clicar fora → modal fecha, checkboxes permanecem marcados.
5. Confirmar → **hoje só fecha** (placeholder). Exclusão real depende de
   desbloqueio da fatia 3.

Inspecionar DOM: se `id="modal-excluir-aulas"` aparecer, é o fluxo de SALA
(tela errada) — confirmar que está em HomeAula, não SalaProfessor.

## 6. Notas técnicas / Achados

- **`qtdExcluir` sem filtro de elegibilidade.** Conta todo item selecionado
  (`Conteudo.tsx:644-645`). CA13 fala em "só não-publicado" — divergência a
  alinhar com PBI/backend. Hoje, body do DELETE pode incluir conteúdos
  publicados e o backend é quem decide. Registrar, não corrigir.
- **Colisão de nomes resolvida.** Esqueleto antigo do botão tinha
  `toggleModalExcluirAula` + `id="btn-excluir-home-sala"` — idênticos aos
  do fluxo de SalaProfessor (excluir AULA inteira). Bateu no debug do dia.
  Renomeado para `toggleModalExcluirMaterial` + `btn-excluir-materiais-home-aula`
  (3 ids, com sufixos `-mobile` / `-tablet`). Confirma sem import cruzado.
- **`guid_conteudo` vs `guid_estrutura_aula`.** Achado central da fatia 3.
  Single-item antigo (`HomeAulas.excluirConteudo`) bate em endpoint
  diferente (`/aulas`) — aparentemente apaga estrutura inteira via
  `guid_estrutura_aula`. Em lote, endpoint correto é `/conteudos` com
  `guid_conteudo`. **Não é drop-in.**
- **Exclusão é LÓGICA (soft delete).** Reversível no banco, confirmado pelo
  PBI. Mesmo assim, validar em sala descartável.
- **Escopo "só desta aula" (CA13 nota 1).** Provavelmente via
  `guid_estrutura_aula_item` na hierarquia (não checado em código FE). A
  confirmar com TL — o BFF que escopa? O FE precisa enviar o
  `guid_estrutura_aula` da aula corrente no body?
- **Erro parcial não se aplica.** É uma request única — tudo-ou-nada. Não
  precisamos tratar "5 sucessos, 2 falhas".
- **Copy do Figma furada.** Botão direito do modal no Figma diz
  "Excluir aula" (texto do fluxo de SALA). Implementado como "Excluir
  material(is)" conforme texto da PBI. Confirmar com design se a divergência
  é intencional ou se o Figma precisa atualizar.
- **Regra dos 2s.** Padrão estabelecido no projeto (18 sites). Sempre
  `await new Promise((r) => setTimeout(r, 2000))` antes do `await api.xxx`.
  Não é "minimum guaranteed" (Promise.race), é piso fixo somado. Funciona
  pra CA12 sem precisar de util novo.
- **Loading.** `LoadingFullPage` já existe (overlay com barra animada). O
  template `AulaTemplate` renderiza ele via prop `loading` + `textLoading`.
  Padrão: `setLoadingText('Salvando alterações...'); setLoadingSendBody(true)`
  no try, `setLoadingSendBody(false)` no finally.
- **Toast.** `ToastSuccess`/`ToastError` de `shared/molecules/Toast`. Padrão
  width: 297×56 sucesso, condicional por device no erro
  (`screenType === 'mobile' ? undefined : 400`).
- **Reset SÓ no sucesso.** `resetAllCheckboxes()` chamado APÓS o `await
HomeAulas.xxx()` ter sucesso. Em catch/finally só fecha modal e zera
  array local de guids. Manter esse padrão na fatia 3.

## 7. Cross-references

- PR: —
- Commit(s): — (todos working tree, sem commit ainda)
- Docs afetados: —
- TASK_LOGS relacionados: —
- Notas anexas:
  - `NOTA_pbi-exclusao_endpoint-daily-2026-06-18.md` — pergunta pra daily
    sobre endpoint A vs B (desbloqueia fatia 3)
