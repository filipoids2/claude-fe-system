# Inventário de Repositórios — FTD Aceleração Iônica V3

> **Fonte:** `az repos list --organization https://dev.azure.com/ftd-educacao --project "FTD - Aceleração Iônica" --output table`
> **Data da extração:** 2026-05-17
> **Output bruto preservado em:** `REPOS_AZURE_DEVOPS_2026-05-17.md`
>
> **TOTAL: 99 repositórios** verificados via Azure DevOps API.
> Categorização abaixo é por prefixo do nome do repo + análise de função.

---

## Resumo por categoria

| Categoria | Prefixo | Qtd | Notas |
|-----------|---------|-----|-------|
| Configs do Azure APIM | `api-*` | 9 | Config de gateway (policy + openapi + ARM) — NÃO são apps. Só api-sala verificado (Camada 2) |
| BFFs | `bff-*` | 10 | Backend for Frontend por domínio |
| Microfrontends + Monolito FE | `mfe-*`, `fe-*` | 12 | 8 populados + 4 placeholders vazios |
| Microsserviços | `ms-*` | 15 | Camada descoberta no inventário |
| Workers assíncronos | `worker-*` | 13 | Por domínio + workers de Onboarding V2 |
| Libs Java compartilhadas | `lib-*-java-*` | 12 | Eventhub, persistência, onboarding entities |
| Libs React compartilhadas | `lib-*-react_*` | 3 | toolkit, ions-facade, shared |
| Functions (Azure Functions) | `func-*` | 2 | Datafactory + sala-pubaula |
| Data Factory | `datafactory-*` | 1 | Pipeline de dados |
| Infra as Code | `iac-*` | 2 | ftd-ionica + redash |
| Test Automation | `ta-*` | 6 | Robot Framework, testes de API por domínio (Camada 2). Só ta-sala verificado |
| `ts-*` (a definir) | `ts-*` | 7 | **Função desconhecida.** ts-sala verificado (Camada 2) — vazio, não esclarece |
| Microsserviço auxiliar | `backstage-*` | 1 | Provavelmente Spotify Backstage (catálogo) |
| Arquitetura/Diagramas | `arq-*` | 1 | Diagramas C4 (5 SVGs nunca lidos) |
| Outros standalone | — | 5 | onboarding-v3, v1-v3, repo-teste-aks, setup-frontend, FTD raiz |
| **TOTAL** | | **99** | |

**Soma de verificação:** 9 + 10 + 12 + 15 + 13 + 12 + 3 + 2 + 1 + 2 + 6 + 7 + 1 + 1 + 5 = **99**.

✅ **Reconciliado em 2026-05-17.** Contagem batida linha a linha contra `REPOS_AZURE_DEVOPS_2026-05-17.md` (99 linhas de repo). Os headers `ms-*` (era 18) e `worker-*` (era 14) estavam inflados vs. as próprias listas detalhadas; corrigidos para 15 e 13.

---

## Detalhamento por categoria

### Configs do Azure APIM (api-*) — 9 repos

> Cada repo armazena `api-policy.xml` (CORS, roteamento, possível JWT validation) + `openapi.yaml` (contrato) + config ARM pro APIM. **NÃO são aplicações backend** — implementação real está em `bff-*` e `ms-*`.
>
> ⚠️ Apenas `api-ftd-ionica_sala` foi verificado em 2026-05-19 (Camada 2). Outros 8 `api-*` ainda não verificados — provável que sigam o padrão, mas a confirmar.

- `api-ftd-ionica_agenda`
- `api-ftd-ionica_atividade`
- `api-ftd-ionica_atividadepronta`
- `api-ftd-ionica_aulapronta`
- `api-ftd-ionica_bancoquestao`
- `api-ftd-ionica_boilerplate` ← referência de estrutura padrão
- `api-ftd-ionica_home`
- `api-ftd-ionica_mural`
- `api-ftd-ionica_sala`

### BFFs — 10 repos

- `bff-ftd-ionica_agenda` ⭐ Camada 1
- `bff-ftd-ionica_atividade` ⭐ Camada 1
- `bff-ftd-ionica_aulapronta`
- `bff-ftd-ionica_bancoquestao` ⭐ Camada 1
- `bff-ftd-ionica_boilerplate` ⭐ Camada 1 (template)
- `bff-ftd-ionica_classificacao` ← sem MFE correspondente
- `bff-ftd-ionica_home` ← BE construído, FE vazio (`mfe-*_home` placeholder)
- `bff-ftd-ionica_login` ⭐ Camada 1 (resolve [D1] auth)
- `bff-ftd-ionica_mural` ⭐ Camada 1
- `bff-ftd-ionica_sala` ⭐ Camada 1

### Frontend — 12 repos (já analisados na fase FE)

- `fe-ftd-ionica-monolithic` (Nx + Webpack, sem MF)
- `mfe-ftd-ionica_agenda`
- `mfe-ftd-ionica_atividade`
- `mfe-ftd-ionica_bancoquestao`
- `mfe-ftd-ionica_login`
- `mfe-ftd-ionica_mural`
- `mfe-ftd-ionica_portal` (host)
- `mfe-ftd-ionica_sala`
- `mfe-ftd-ionica_assets` — placeholder vazio
- `mfe-ftd-ionica_boilerplate` — placeholder vazio
- `mfe-ftd-ionica_componentes` — placeholder vazio
- `mfe-ftd-ionica_home` — placeholder vazio

### Microsserviços (`ms-*`) — 15 repos ⚠️ CATEGORIA NOVA

**Padrão observado:** microsserviços standalone, alguns por domínio, outros por **ação** (publicar/expirar/encerrar). Provável que consomem eventos do EventHub.

- `ms-ftd-ionica_agenda`
- `ms-ftd-ionica_atividade`
- `ms-ftd-ionica_atividade_encerrar` ← microsserviço de ação
- `ms-ftd-ionica_atividade_expirar` ← branch `feature/224488-nodejs-expira-banco` (sugere Node.js!)
- `ms-ftd-ionica_atividade_publicar` ← branch `feature/284488-node-update-tabela-atividade`
- `ms-ftd-ionica_atividadepronta`
- `ms-ftd-ionica_aulapronta`
- `ms-ftd-ionica_bancoquestao`
- `ms-ftd-ionica_boilerplate` ← template de microsserviço
- `ms-ftd-ionica_classificacao`
- `ms-ftd-ionica_login`
- `ms-ftd-ionica_mural`
- `ms-ftd-ionica_sala`
- `ms-ftd-ionica_sala_publicar` ← branch `feature/263950-migracao-impl`
- `ms-ftd-ionica_signalr` ← **realtime/WebSocket server**, branch `feature/189556-add-lesson-plans`

**Confirmado (Camada 2, 2026-05-19):** `ms-ftd-ionica_signalr` NÃO é o servidor SignalR — é serviço de *negotiation* (Java 21/Spring Boot) que emite token JWT. O servidor real é o **Azure SignalR Service** (PaaS gerenciado). O FE pega o token aqui e conecta no Azure SignalR via `@microsoft/signalr`.

**Linguagem (Camada 2):** `ms-signalr` é Java — a camada `ms-*` é **poliglota** (Java + Node convivem). Branches de outros `ms-*` citam Node; cada `ms-*` precisa ser verificado individualmente.

### Workers assíncronos (`worker-*`) — 13 repos ⚠️ CATEGORIA NOVA

**Padrão observado:** workers por domínio + workers que consomem eventos do Onboarding V2.

Por domínio:
- `worker-ftd-ionica_agenda`
- `worker-ftd-ionica_atividade`
- `worker-ftd-ionica_atividadeexportacao` ← confirma "AtividadeExportacao" como entidade real separada
- `worker-ftd-ionica_atividadepronta`
- `worker-ftd-ionica_bancoquestao`
- `worker-ftd-ionica_boilerplate` ← template
- `worker-ftd-ionica_mural`
- `worker-ftd-ionica_sala`

De Onboarding V2 (consomem eventos `onboarding-*`):
- `worker-ftd-ionica_onboarding_agenda`
- `worker-ftd-ionica_onboarding_atividade`
- `worker-ftd-ionica_onboarding_bancoquestao`
- `worker-ftd-ionica_onboarding_mural`
- `worker-ftd-ionica_onboarding_sala`

Wiki da V3 mencionava workers consumindo `onboarding-*` — **confirmado em código aqui**.

### Libs Java compartilhadas — 12 repos

- `lib-ftd-ionica_java_eventhub` ← provavelmente cliente comum de EventHubs
- `lib-ftd-ionica_java_onboarding_class`
- `lib-ftd-ionica_java_onboarding_course`
- `lib-ftd-ionica_java_onboarding_course_discipline`
- `lib-ftd-ionica_java_onboarding_dlq` ← Dead Letter Queue
- `lib-ftd-ionica_java_onboarding_enrollment`
- `lib-ftd-ionica_java_onboarding_scholl` ← **typo confirmado** ("scholl" em vez de "school")
- `lib-ftd-ionica_java_onboarding_schollcontract` ← **typo confirmado**
- `lib-ftd-ionica_java_onboarding_schoolenrollment` ← este sem typo
- `lib-ftd-ionica_java_onboarding_user`
- `lib-ftd-ionica_java_onboarding_userclass`
- `lib-ftd-ionica_java_persistense` ← **typo confirmado** ("persistense" vs "persistence")

⚠️ **Typos confirmados no nome dos repos.** Consistente com "war room" (Seção 0 do ONBOARDING_FRONTEND.md) — pressão de prazo causou erros não corrigidos.

### Libs React compartilhadas — 3 repos

- `lib-ftd-ionica-react_ions-facade` ⭐ Camada 1 (Design System real)
- `lib-ftd-ionica-react_toolkit` ← **gap fechado** (estava ausente do inventário antigo); bundla MSAL + Datadog + SignalR
- `lib-ftd-ionica-react_shared` ← **descoberta nova** — função a confirmar

### Functions (Azure Functions) — 2 repos

- `func-ftd-ionica_datafactory`
- `func-ftd-ionica_sala-pubaula` ← provável publisher de eventos relacionados a Sala/Aula

### Data Factory — 1 repo

- `datafactory-ftd-ionica` ← Azure Data Factory (ETL/pipelines)

### Infra as Code — 2 repos

- `iac-ftd-ionica`
- `iac-redash` ← Redash = ferramenta de BI/dashboards

### Test Automation (`ta-*`) — 6 repos ✅

- `ta-ftd-ionica_agenda`
- `ta-ftd-ionica_atividade`
- `ta-ftd-ionica_atividadepronta`
- `ta-ftd-ionica_bancoquestao`
- `ta-ftd-ionica_mural`
- `ta-ftd-ionica_sala`

**Padrão:** prefixo `ta-` + domínio. Existe pra 6 dos domínios principais (falta Banco de Questões? Não — tem. Falta agendar? Falta aula pronta? Falta login? Falta home).

**Confirmado (Camada 2, 2026-05-19):** `ta-*` = **Test Automation** (Robot Framework). `ta-ftd-ionica_sala` verificado: testes de **API/backend** (alvo `bff-ionica-sala-uat`), não UI. Casos de teste vivem numa tabela MySQL, suite gerada em runtime. Dispara por push/manual, não em PR. Dono provável: QA/SDET. Só `ta-sala` verificado — outros 5 a confirmar.

### `ts-*` — 7 repos ❓ FUNÇÃO DESCONHECIDA

- `ts-ftd-ionica_agenda`
- `ts-ftd-ionica_atividade`
- `ts-ftd-ionica_atividadepronta`
- `ts-ftd-ionica_aulapronta`
- `ts-ftd-ionica_bancoquestao`
- `ts-ftd-ionica_mural`
- `ts-ftd-ionica_sala`

**Padrão:** prefixo `ts-` + domínio. Cobre **7 domínios** (tem aulapronta que `ta-*` não tem).

**Hipóteses:** TypeScript libs? Test Suite? Trigger Service? Tasks Scheduled? — perguntar pro time.

**Camada 2 (2026-05-19):** `ts-ftd-ionica_sala` foi clonado e está **vazio** (branch `master`, zero commits) — placeholder. Não esclarece o prefixo. Os outros 6 `ts-*` podem ou não estar populados — a verificar.

### Microsserviço auxiliar — 1 repo

- `backstage-ms-ftd-ionica_sala` ← provavelmente Spotify Backstage (catálogo de serviços)

### Arquitetura/Diagramas — 1 repo

- `arq-ftd-ionica_diagramas` ← **provavelmente onde estão os 5 SVGs C4** que ainda não foram lidos

### Outros standalone — 5 repos

- `FTD - Aceleração Iônica` ← repo "guarda-chuva" com a wiki (branch padrão: `wikiMaster`)
- `onboarding-v3` ← repo standalone da V3 do Onboarding
- `v1-v3` ← **repo de migração V1→V3** — interesse alto pra fase pós-MVP
- `repo-teste-aks-ionica-v3` ← **testes em Azure Kubernetes Service (AKS)** — confirma uso de K8s
- `setup-ftd-ionica_frontend` ← setup geral do FE, função a confirmar

---

## Prioridades de análise (Camada 1)

### Alta — clonar primeiro (8 repos)

1. `bff-ftd-ionica_boilerplate` ← **primeiro a analisar** (template, baseline)
2. `bff-ftd-ionica_login` ← resolve hipótese [D1] de auth
3. `bff-ftd-ionica_sala` ← domínio principal do FE
4. `bff-ftd-ionica_mural` ← cluster com Sala
5. `bff-ftd-ionica_atividade` ← cluster com Sala (consumido)
6. `bff-ftd-ionica_agenda` ← domínio principal do FE
7. `bff-ftd-ionica_bancoquestao` ← domínio principal do FE
8. `lib-ftd-ionica-react_ions-facade` ← Design System real

### Média — Camada 2 (5 repos)

9. `bff-ftd-ionica_classificacao` — funcionalidade transversal
10. `bff-ftd-ionica_aulapronta` — entender por que não tem MFE
11. `bff-ftd-ionica_home` — entender por que MFE está vazio
12. `api-ftd-ionica_sala` — referência de API Spring real
13. `lib-ftd-ionica-react_toolkit` — finalmente abrir a "caixa-preta" (MSAL+Datadog+SignalR)

### Baixa/Depois — investigação aberta (~80 repos)

- 8 APIs Java restantes (analisar quando bater task específica)
- 18 `ms-*` microsserviços (categoria nova — pergunta arquitetural pro time antes de mergulhar)
- 14 workers (idem)
- 11 libs Java de onboarding (referência, leitura leve quando precisar)
- 13 `ta-*` + `ts-*` (resolver mistério com time primeiro)
- 5 outros standalone (`v1-v3` e `arq-ftd-ionica_diagramas` têm interesse alto — diagramas C4)
- Functions, IaC, datafactory (não tua área prioritária)

---

## Achados arquiteturais consolidados

### [D1] BFF Login isolado
`bff-ftd-ionica_login` existe e é o pivô do handoff de auth entre Azure AD B2C e o resto do sistema. **Camada 1 alta.**

### [D2] BFF Classificacao sem MFE
`bff-ftd-ionica_classificacao` não tem MFE nem API direta correspondente — funcionalidade transversal? **Pergunta pro time.**

### [D3] Aula Pronta: BE construído, FE vazio
`api-` e `bff-ftd-ionica_aulapronta` populados, `mfe-` placeholder ausente. **Roadmap?**

### [D4] BFF/API Boilerplate vs MFE Boilerplate
Backend tem template (`api-*` e `bff-*_boilerplate` populados); frontend não (`mfe-*_boilerplate` placeholder vazio). **Por quê? Padronizar?**

### [D5] Home: BE construído, FE vazio (novo)
`api-` e `bff-ftd-ionica_home` populados, `mfe-*_home` placeholder. Mesmo padrão de Aula Pronta. **Roadmap?**

### [D6] Camada de microsserviços (`ms-*`) descoberta na Camada 1
15 microsserviços standalone, alguns por domínio, outros por ação. `ms-ftd-ionica_signalr` é serviço de *negotiation* SignalR (emite token; servidor real é o Azure SignalR Service PaaS), confirmado Java/Spring Boot na Camada 2. **Arquitetura mais complexa que parecia — e poliglota (Java + Node).**

### [D7] Workers de Onboarding V2 → V3 confirmados
5 workers `worker-*_onboarding_*` consomem eventos do Onboarding V2. **Confirma a arquitetura event-driven mencionada na wiki.**

### [D8] Migração V1→V3 tem repo dedicado
`v1-v3` é repo próprio. Interesse pós-MVP.

### [D9] Typos confirmados no Azure DevOps
`scholl`, `schollcontract`, `persistense` — confirmados na origem. **Consistente com "war room" (Seção 0).**

---

## Perguntas pendentes pro time

Adicionar à Seção 9 do `ONBOARDING_FRONTEND.md`:

- ~~**Q: O que é o prefixo `ta-*`?**~~ ✅ Respondido (Camada 2): Test Automation (Robot Framework).
- **Q: O que é o prefixo `ts-*`?** 7 repos com padrão `ts-ftd-ionica_<dominio>`. `ts-sala` verificado e vazio — segue em aberto.
- ~~**Q: `ms-ftd-ionica_signalr` é o server WebSocket consumido pelo FE?**~~ ✅ Respondido (Camada 2): não — é serviço de *negotiation*; o server é o Azure SignalR Service. Ver [D6].
- **Q: Linguagem dos `ms-*`?** Parcial (Camada 2): `ms-signalr` é Java; camada é poliglota. Demais `ms-*` a verificar.
- **Q: `lib-ftd-ionica-react_shared` — qual a função?** Não estava no nosso mapa anterior.
- ~~**Q: `v1-v3` — qual o estado da migração V1→V3?**~~ ✅ Respondido (Camada 2): ativa em maio/2026, escola-a-escola (rede Marista), por ondas.
- **Q: `repo-teste-aks-ionica-v3` confirma uso de AKS (Azure Kubernetes Service)?** Toda V3 roda em K8s?
- **Q: Relação entre `onboarding-v3` (repo standalone) e os `worker-*_onboarding_*`?**

---

## Pendências pra reconciliação

1. **Soma 103 vs 97** — reconciliar contagem (provável dupla contagem em alguma categoria)
2. **Typos `scholl`/`schollcontract`/`persistense`** — confirmar com time se deve abrir issue pra rename ou se nome ficou assim por dependência externa
3. **Categorias `ta-*` e `ts-*`** — definir função antes de clonar
4. **`FTD - Aceleração Iônica` (repo raiz, branch wikiMaster)** — esta é a wiki que já lemos, certo?

---

## Histórico do inventário

- **v1 (anterior):** 41 repos categorizados — número stale, baseado em conversa antiga (não-verificado)
- **v2 (este, 2026-05-17):** 99 repos verificados via `az repos list` — fonte de verdade primária