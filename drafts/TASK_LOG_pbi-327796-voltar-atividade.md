# Botão Voltar respeitando origem — Correção/Criação/Hub de Atividades

> **Tipo:** bug fix
> **PBI:** 327796
> **Branch:** feature/pbi-327796
> **Status:** implementado — aguardando validação manual (professor e sala descartável)
> **Início:** 2026-07-01
> **Conclusão:** —

---

## 1. Resumo curto

Corrige o botão **Voltar** da tela de correção de atividade dentro de
sala/aula (bug oficial do PBI) e, de quebra, dois efeitos irmãos descobertos
na análise: (a) o Voltar da tela de criar/editar rascunho, que caía sempre
em `/atividades` (Início), e (b) as tabs do hub de atividades (Publicadas,
Rascunhos, etc.) que não sincronizavam URL — então mesmo voltando, o hub
reiniciava em Início. Padrão único aplicado nas 3 telas: `navigate(-1)`
quando há histórico SPA + fallback `ionicaNavigateTo('/atividades')`. Só o
MFE de atividades foi tocado.

## 2. Problema / Objetivo

**Bug oficial (PBI):** o professor abre uma atividade a partir de
`/salas/{guidSala}/aulas/{guidAula}` (cartão "Abrir"), cai em
`/atividades/{guidAtividade}/correcao/{guidAplicacao}` e, ao clicar Voltar,
é redirecionado para o Hub de Atividades (`/atividades`, tab Início) — não
para a sala de origem. Retrabalho para reencontrar a sala e a atividade.

**Efeitos irmãos descobertos na análise (mesma causa raiz — navegação
hardcoded ignorando origem):**

- Rascunhos → Editar → Voltar (ou modal "Sair sem salvar") → cai em
  `/atividades` (Início), não Rascunhos.
- Publicadas/Agendadas/Rascunhos → clica em atividade → Voltar → cai em
  `/atividades` (Início), mesmo tendo navegado a partir de outra tab.

## 3. Solução aplicada

Padrão único em 3 pontos do MFE:

```
if (location.key !== 'default') navigate(-1)   // volta 1 passo SPA
else                            ionicaNavigateTo('/atividades')  // deep link / refresh
```

Por que `location.key !== 'default'`: quando o React Router é montado
sem histórico anterior (usuário chegou por deep link, refresh ou bookmark
direto na tela de correção), `location.key === 'default'`. Nesse caso,
`navigate(-1)` sairia do site — o fallback preserva o comportamento antigo.

### Fatia 1 — Voltar da tela de correção (bug oficial)

- `CorrecaoAtividadesPage.tsx`: o `onClick` do botão Voltar era
  `ionicaNavigateTo('/atividades')` cru. Substituído pela condicional acima.
- `useNavigate` de `react-router-dom` importado junto com o `useLocation`
  que já existia — mesma dep, zero deps novas.
- Tracking `trackBotaoVoltar()` preservado exatamente igual (chamado antes
  da decisão de navegação).

### Fatia 2 — Voltar / "Sair sem salvar" da tela de criar/editar rascunho

Ligação FE ↔ store: o botão Voltar dessa página chama
`useCriarAtividadeStore().handleBack(...)`. A action, dentro de
`resetarTodosOsEstados`, fazia `ionicaNavigateTo("/atividades")`. Esse
mesmo caminho é acionado tanto no Voltar direto (sem mudanças) quanto no
`onConfirm` do modal "Sair sem salvar" (com mudanças). Uma única alteração
cobriu os dois caminhos.

- `CriarAtividadeStore.ts` (`ExternalDependencies`): nova prop opcional
  `voltarParaOrigem?: () => void`. Adicionada ao `Pick<...>` da assinatura
  do `handleBack`.
- Dentro de `resetarTodosOsEstados`: se `voltarParaOrigem` foi passado,
  usa; senão, mantém `ionicaNavigateTo('/atividades')`. A opcionalidade é
  intencional — mantém o store agnóstico a react-router, útil se algum dia
  outro caller quiser usar só o `ionicaNavigateTo`.
- `AtividadesCriarPage.tsx`: `useLocation`/`useNavigate` importados de
  `react-router-dom`. Função local `voltarParaOrigem` implementa a mesma
  condicional (`location.key !== 'default'`). Passada como dep no
  `handleBack(...)` do `handleBackClick`.

### Fatia 3 — Sincronizar tabs do Hub Professor com a URL

Achado central: o `AtividadesHubProfessorPage` já aceita `tabIndex` como
prop (a shell mapeia `/atividades/{n}` → prop; usado hoje pelo salas MFE
que abre atividades em `/atividades/1|2|3`). **Mas ao clicar em uma tab
visualmente, `setIndexTab(index)` só mudava state local — a URL continuava
`/atividades`.** Consequência: `navigate(-1)` da correção/edição volta pra
`/atividades` corretamente, mas o hub inicia em Início (default do state).

- `AtividadesHubProfessorPage.tsx`: `useNavigate` de `react-router-dom`
  importado. Helper `sincronizarUrlComTab(index)` chama
  `navigate(rota, { replace: true })` onde `rota = index === 0 ?
'/atividades' : '/atividades/${index}'`.
- Chamado em 2 pontos: dentro de `handleMudarParaTab` (troca de tab via
  callback interno, ex.: card com filtro) e dentro do `onChange` do
  `<ResponsiveTabs>` (troca de tab manual pelo usuário).
- **`replace` intencional** (não `push`): trocar de tab não deve criar
  entrada de histórico. Se o usuário pula Publicadas → Agendadas →
  Rascunhos e clica Voltar, deve sair do hub, não passar tab por tab.
  `replace` mantém apenas a última tab visitada na URL.
- Mapeamento: `/atividades` = Início (URL canônica limpa),
  `/atividades/{1..5}` = demais. Bate com o mapeamento que o salas MFE já
  usa.

## 4. Arquivos alterados

| Arquivo:linha                                                                                             | Mudança                                                                                                                     |
| --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/CorrecaoAtividadesPage/CorrecaoAtividadesPage.tsx:22`      | Import `useNavigate` junto com `useLocation` de `react-router-dom`                                                          |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/CorrecaoAtividadesPage/CorrecaoAtividadesPage.tsx:34`      | `const navigate = useNavigate()`                                                                                            |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/CorrecaoAtividadesPage/CorrecaoAtividadesPage.tsx:142-151` | `onClick` do Voltar: condicional `navigate(-1)` (com `location.key !== 'default'`) + fallback `ionicaNavigateTo`             |
| `mfe-ftd-ionica_atividade/src/stores/CriarAtividadeStore.ts:33`                                           | Nova prop `voltarParaOrigem?: () => void` em `ExternalDependencies`                                                          |
| `mfe-ftd-ionica_atividade/src/stores/CriarAtividadeStore.ts:96-101`                                       | `Pick<...>` de `handleBack` inclui `voltarParaOrigem`                                                                        |
| `mfe-ftd-ionica_atividade/src/stores/CriarAtividadeStore.ts:255`                                          | `handleBack` desestrutura `voltarParaOrigem`                                                                                 |
| `mfe-ftd-ionica_atividade/src/stores/CriarAtividadeStore.ts:288-294`                                      | Dentro de `resetarTodosOsEstados`: usa `voltarParaOrigem()` quando presente; senão mantém `ionicaNavigateTo('/atividades')` |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesCriarPage/AtividadesCriarPage.tsx:12`            | Import `useLocation`, `useNavigate` de `react-router-dom`                                                                    |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesCriarPage/AtividadesCriarPage.tsx:84-93`         | `useLocation`, `useNavigate` + função `voltarParaOrigem` local (mesma condicional)                                           |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesCriarPage/AtividadesCriarPage.tsx:545-552`       | `handleBack({...})` passa `voltarParaOrigem`                                                                                 |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesHubProfessorPage/AtividadesHubProfessorPage.tsx:2` | Import `useNavigate` de `react-router-dom`                                                                                    |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesHubProfessorPage/AtividadesHubProfessorPage.tsx:24-32` | `useNavigate` + helper `sincronizarUrlComTab(index)` (`replace` para `/atividades` ou `/atividades/{index}`)             |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesHubProfessorPage/AtividadesHubProfessorPage.tsx:73` | `handleMudarParaTab` chama `sincronizarUrlComTab(index)`                                                                     |
| `mfe-ftd-ionica_atividade/src/pages/Atividades/AtividadesHubProfessorPage/AtividadesHubProfessorPage.tsx:217` | `onChange` do `<ResponsiveTabs>` chama `sincronizarUrlComTab(index)`                                                         |

## 5. Como testar

### Fluxo do PBI (bug oficial)

1. Logado como professor. Entrar em `/salas/{guidSala}/aulas/{guidAula}`
   com uma atividade vinculada.
2. Clicar em **Abrir** no cartão da atividade → URL vira
   `/atividades/{guidAtividade}/correcao/{guidAplicacao}`.
3. Clicar em **Voltar** → deve retornar para `/salas/{guidSala}/aulas/{guidAula}`
   com o scroll/estado da sala.

### Fluxo Hub → Correção

1. `/atividades` (Início). Clicar em **Publicadas** → URL deve virar
   `/atividades/1` (via `replace`).
2. Clicar em uma atividade publicada → `/atividades/{id}/correcao/{id}`.
3. Voltar → deve cair em `/atividades/1` (tab Publicadas ativa). Repetir
   com Agendadas (`/atividades/2`), Rascunhos (`/atividades/3`) etc.

### Fluxo Rascunhos → Editar

1. `/atividades/3` (Rascunhos). Clicar em **Editar** de um rascunho.
2. **Sem mudanças** → clicar Voltar → volta direto pra `/atividades/3`
   (sem modal).
3. **Com mudanças** (renomear atividade / mexer questões) → clicar Voltar
   → modal "Tem certeza que quer sair sem salvar?" abre.
   - **Cancelar** → modal fecha, permanece na tela de edição. Idêntico ao
     antigo.
   - **Sair sem salvar** → volta pra `/atividades/3` (Rascunhos).

### Fluxo deep link / refresh (fallback)

1. Acessar `/atividades/{id}/correcao/{id}` direto (colar URL, F5, ou
   bookmark).
2. Clicar Voltar → deve cair no Hub `/atividades` (Início). Comportamento
   antigo preservado — não vaza pra fora do site.

### Regressões a monitorar

- Hub Estudante (`AtividadesHubEstudantePage`) NÃO foi tocado. Se o time
  quiser o mesmo padrão lá, é fatia futura.
- Não há mais entradas de histórico por troca de tab: se o QA testar
  "clicar em várias tabs seguidas e depois Voltar", agora sai do hub em
  vez de navegar tab por tab. Isso é intencional e melhor UX, mas alinhar
  com o design.

## 6. Notas técnicas / Achados

- **Origem cross-MFE resolvida sem cooperação.** A shell mantém o
  histórico do react-router entre MFEs (o `ionicaNavigateTo` dispara um
  `CustomEvent` que a shell traduz para `navigate` do router — histórico
  preservado). Isso deixa `navigate(-1)` naturalmente respeitar sala,
  hub, rascunho ou qualquer origem futura sem modificar
  `mfe-ftd-ionica_sala` ou qualquer outro MFE.
- **Por que não `document.referrer`.** Como o hub e a correção rodam na
  mesma janela SPA, `document.referrer` fica congelado no valor de antes
  de a app subir. Não reflete navegação in-app. Descartado no plano.
- **Por que não sessionStorage.** Requer cooperação de escrita no MFE de
  origem (salas) + tem side-effect entre sessões. Menos limpo que usar o
  histórico nativo do router.
- **Por que `location.key !== 'default'` e não `history.length`.**
  `history.length` conta a sessão inteira do tab (inclui rotas anteriores
  do usuário no domínio, inclusive antes do login). `location.key` do
  react-router 7 é `'default'` só quando o Router acabou de montar sem
  navegações SPA — critério mais preciso para "cheguei aqui direto".
- **`ionicaNavigateTo` mantido como fallback, não removido.** Compatível
  com deep link/refresh. Deletar seria uma dor à toa — a lib emite um
  evento que a shell traduz, funciona bem para "ir para uma rota
  específica" independente do histórico. Papel do `navigate(-1)` é outro
  (voltar 1 passo).
- **Store agnóstico a react-router.** A prop `voltarParaOrigem` no
  `ExternalDependencies` é opcional intencionalmente. O `CriarAtividadeStore`
  não importa `react-router-dom`. Se algum dia o store for consumido em
  outro contexto (teste, storybook, MFE menor sem Router), o comportamento
  antigo `ionicaNavigateTo` continua ativo como fallback.
- **`replace` na sincronização de tabs.** Ao trocar de tab não pushamos
  entrada — só substituímos a atual. Isso deixa Voltar semanticamente
  correto: volta para a rota anterior ao hub (sala, home, etc.), não para
  a tab anterior do hub.
- **Bug latente registrado, não corrigido.** O type-check do MFE mostra
  vários erros pré-existentes em `stores/CriarAtividadeStore.ts` (linhas
  1023, 1025, 1029, 1033, 1052), `stores/AtividadeStore.ts`,
  `stores/VisualizacaoEstudanteStore.ts`, `types/InfoDrawer.ts`,
  `types/TemaTreeElement.ts` etc. Não introduzidos nesta task — confirmado
  rodando type-check antes e depois do diff. Registrar como débito
  técnico separado.
- **`CorrecaoAtividadesPage.tsx` tinha 2 warnings pré-existentes** de
  `TS6133` (`TabBlockedScreen` importado mas não usado, `forceActivation`
  desestruturado mas não usado). Fora do escopo do PBI — não removidos
  pra manter o diff mínimo.

## 7. Cross-references

- PR: —
- Commit(s): — (working tree, sem commit ainda; aguardando validação)
- Docs afetados: —
- TASK_LOGS relacionados: —
- Notas anexas: —

## 8. Escopo consciente NÃO tocado

- **`AtividadesHubEstudantePage`.** Provavelmente sofre do mesmo problema
  de tabs sem URL, mas o PBI é do perfil professor. Se o time quiser, é
  fatia idêntica à Fatia 3 aplicada no arquivo do estudante.
- **`mfe-ftd-ionica_sala` e outros MFEs.** Zero mudanças. A correção
  aproveita o histórico já preservado pela shell.
- **Rotas `/atividades/4` e `/atividades/5`.** O código gera essas rotas
  ao clicar em Sugestões e Excluídas, mas não confirmei em execução que
  a shell as aceita (só vi evidência das rotas 1, 2, 3 sendo usadas hoje
  pelo salas MFE). Testar durante validação. Se a shell rejeitar,
  fallback é adicionar essas rotas no config da shell (fora deste MFE) ou
  ajustar o mapeamento para não sincronizar essas duas tabs.
