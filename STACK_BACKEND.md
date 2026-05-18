# Stack Backend — Aceleração Iônica V3 · camada BFF

> Gerado em 2026-05-17 a partir de leitura de código (passada metadata-only) dos 7
> BFFs da Camada 1: bff-ftd-ionica_{boilerplate,login,sala,mural,atividade,agenda,
> bancoquestao}. Status epistêmico por afirmação — FATO / HIPÓTESE / A confirmar.
> Escopo: só os bff-*. As camadas api-* e ms-* NÃO foram lidas. Citações
> repo/arquivo:linha. Documento-mapa do time entrante (Filipe, Elias, Well);
> complementa o ONBOARDING_FRONTEND.md (lado FE).

## 0. Premissa corrigida — os BFFs não são Java

A wiki e o ONBOARDING descreviam "Java + Spring Modulith". FATO: os 7 BFFs são
NestJS/Node.js/TypeScript — nenhum tem pom.xml. A camada Java, se existe, está
nos api-*/ms-*, não analisados. Não assumir Java até verificar.

## 1. Stack & runtime — uniforme nos 7

| Dimensão | Valor |
|---|---|
| Framework | NestJS 10.4.6 |
| Runtime | Node.js >=22.8.0 <23 · TypeScript 5.6.3 · Express |
| Build | nest build → dist/, node dist/main |
| Deploy | Docker → AKS, namespace plataforma-ionica, registry cntregionica.azurecr.io |
| CI | Azure Pipelines + SonarCloud (DEV=develop, QA=main) |
| Cache | Redis (@nestjs/cache-manager + @keyv/redis) |
| Persistência própria | Nenhuma — os BFFs orquestram, não persistem |
| Observabilidade | Datadog APM (dd-trace 5.67) |

Os 7 derivam do template bff-ftd-ionica_boilerplate.

## 2. Arquitetura — Hexagonal (ports & adapters)

FATO — estrutura comum herdada do boilerplate:

```
adapters/in/controller/<dominio>/  → controllers + DTOs + mappers
adapters/out/<dominio>/            → adapters HTTP (axios) p/ os MS
application/{core,domain,usecase}/ → casos de uso e entidades
application/ports/{in,out}/        → interfaces
infrastructure/                    → http, cache, interceptors, log
```

Fluxo: controller → InputPort → UseCase → OutputPort → OutboundAdapter → axios.
DI por string tokens (bff-ftd-ionica_boilerplate/src/app.module.ts:39-62).

## 3. Contratos & endpoints

- Nenhum BFF tem OpenAPI commitado — Swagger gerado em runtime via @nestjs/swagger,
  em /swagger + swagger.json/yaml (ex.: bff-ftd-ionica_sala/src/main.ts:55-76).
- Prefixo global por BFF: login/v1, sala/v1, mural/v1, atividade/v1, agenda/v1,
  banco_questao/v1.
- JSON snake_case confirmado; validação de formato UUID é inconsistente entre BFFs.
- Envelope de resposta — NÃO uniforme:

  | BFF | Envelope |
  |---|---|
  | mural, bancoquestao | {metadados, dados, paginacao, mensagem} (completo) |
  | sala, atividade, agenda | {metadados, dados, mensagem} — sem paginacao no raiz |
  | boilerplate | sem envelope — objeto nu, camelCase |

  → refuta parcialmente a hipótese do ONBOARDING (envelope de 4 campos uniforme).
- Catálogo path+método+DTO por BFF: ver relatórios da Camada 1. Volume: sala ~40+
  rotas / mural ~14 / bancoquestao ~9 / atividade ~4 / agenda 1 / login 1.

## 4. Autenticação

FATO — três modelos coexistem entre os 6 BFFs reais:

| BFF(s) | Modelo |
|---|---|
| login | id_token no body do POST /login/v1/login-oauth |
| sala, mural, bancoquestao | id_token no header Authorization; AuthInterceptor decodifica |
| atividade, agenda | sem token — identidade via headers x-ionica-* upstream |

- Onde há inspeção de token, é o id_token do Azure AD B2C, não access_token
  (Swagger declara: bff-ftd-ionica_sala/src/main.ts:64). Resolve a pergunta [S9-5]/Q11.
- A validação no BFF é decode do payload + checagem de exp. A verificação de
  assinatura do token (JWKS) não ocorre nos BFFs — é assumida na borda do APIM.
  Modelo de confiança vale conversa específica do trio entrante: ver pergunta 5
  da Seção 8.
- login tem @azure/msal-node configurado mas não exercido — o code↔token exchange
  é feito pelo frontend. A hipótese do ONBOARDING de que bff_login seria um
  "BFF-auth" é refutada: é um resolver de perfil, não auth broker
  (bff-ftd-ionica_login/src/.../CriarLoginUseCase.ts:30-59).

## 5. Comunicação com o downstream

- Cliente HTTP: axios em todos (singleton AxiosFactory / AXIOS_INSTANCE_TOKEN).
  RestTemplate/WebClient/Feign são Java — N/A.
- Os BFFs chamam a camada ms-*, não a api-*: as env BASE_URL_API_* resolvem para
  ms-ionica-{dominio}-dev.aks-ionica.com.br (bff-ftd-ionica_agenda/.env.example:29-31).
  Confirma ms-* ([D6]) como destino real dos BFFs.
- APIM heterogêneo: sala/mural/bancoquestao injetam ocp-apim-subscription-key
  downstream; atividade usa x-ocp-apim-subscription-key (não-padrão); agenda declara
  a key mas não usa downstream. A confirmar o padrão pretendido.
- O token não é repassado downstream — o BFF extrai claims e as reenvia aos MS como
  headers HTTP individuais (naming varia por MS).
- Sem EventHub nos BFFs — síncronos request/response. Interop por evento é mais
  fundo (ms-*/api-*).

## 6. Observabilidade

Datadog APM (dd-trace 5.67) em todos; env DD_* extenso. Correlation-id propagado
downstream. Log estruturado winston/nest-winston (com console.log de debug
convivendo — ver Seção 7).

## 7. Sinais de débito técnico (herança da war room)

- Dependências mortas: @azure/msal-node e jsonwebtoken declarados sem uso em src/.
- Refactor não consolidado: bff-ftd-ionica_atividade versiona
  BuscarAtividadesProntas.usecase.OTIMIZADO.ts ao lado do original.
- Docs desatualizadas: bff-ftd-ionica_mural/MURAL_IMPLEMENTATION.md lista rotas
  inexistentes.
- console.log/error em produção apesar do logger estruturado.
- Comentários estilo-IA ("CORREÇÃO CRÍTICA") em SignalRClientAdapter de sala.
- Cache morto: Map estático de token SignalR em sala é populado e nunca lido.

## Camada 2 — análises cirúrgicas (2026-05-19)

> 5 repos escolhidos pra fechar perguntas pontuais — não é varredura do backend.
> Mesma regra metadata-only da Camada 1. Clonados em D:\local\ftd\ftd-backend
> (e ftd-migration para v1-v3).

### ms-ftd-ionica_signalr
- FATO — Java 21 + Spring Boot 3.2.5 (Gradle 8.10.1; build.gradle:3,14). Sem pom.xml,
  sem código Node. A camada ms-* é poliglota — convivem Java e Node.
- FATO — NÃO é o servidor SignalR/WebSocket. É microserviço REST de negotiation:
  expõe POST /signalr/v1/token, lê SIGNALR_CONNECTION_STRING, emite JWT e devolve
  {url, accessToken, hubName} apontando pro Azure SignalR Service (PaaS gerenciado,
  externo). Hub default aulapronta-notifications (application.yaml).
- FATO — fluxo realtime do FE: o frontend chama este endpoint pra obter token e só
  então conecta no Azure SignalR via @microsoft/signalr (README.md:157-167). Resolve
  parte da pergunta 6 / [D6].
- FATO — o contrato do endpoint POST /sala/v1/signalr/token diverge entre as branches
  prd e feature/189556-add-lesson-plans do ms-ftd-ionica_signalr — userId obrigatório
  em prd, não em feature. Implicação pro FE a verificar.
- A confirmar — qual branch é a fonte de verdade. Sem OpenAPI commitado; o contrato
  vive no código.

### api-ftd-ionica_sala
- FATO — não é aplicação backend. Sem src/, sem linguagem de app. Contém só a config
  do Azure APIM: api-policy.xml (CORS + roteamento set-backend-service),
  config-apim-arm.yml (definição ARM) e openapi.yaml (contrato). O pipeline publica a
  definição no APIM (dev→qa→uat→prod).
- FATO — a policy local faz CORS + set-backend-service; sem validate-jwt nem
  rate-limit próprios. Se há validação de JWT, é em escopo superior do APIM — a confirmar.
- HIPÓTESE — o padrão vale pros 9 api-*: configs de gateway, um por API publicada;
  implementação real em bff-*/ms-*. Só api-ftd-ionica_sala verificado (2026-05-19).

### v1-v3
- FATO — migrador de dados V1→V3, Node 22 CLI (mysql2/dotenv/uuid, sem ORM). Lê o
  MySQL da V1 e escreve em 4 bancos V3 por domínio (agenda, sala, atividade, mural).
- FATO — migração ATIVA em maio/2026 (último commit 2026-05-11), feita escola-a-escola
  no rollout da rede Marista. "Onda" = lote de escolas por data. Não congelada nem
  concluída — run_onda2.sh tem escolas pendentes.
- HIPÓTESE — ferramenta efêmera, descartável quando o rollout terminar. Não idempotente
  (scripts têm SKIP: com erros reais).

### ts-ftd-ionica_sala
- FATO — repositório VAZIO. Branch master, zero commits. Placeholder. O prefixo ts-*
  segue sem função confirmada — este repo não esclarece.

### ta-ftd-ionica_sala
- FATO — Test Automation em Robot Framework. Confirma ta-* = Test Automation.
- FATO — foco em teste de API/backend (alvo bff-ionica-sala-uat), não UI React. Os
  casos de teste não são versionados como .robot — vivem numa tabela MySQL
  (lms_aceleracao_automated_tests_robot), suite gerada em runtime. Dispara por
  push/manual, não em PR.
- HIPÓTESE — relevância indireta pro time de FE: valida o contrato HTTP do BFF, não
  a interface. Dono provável: QA/SDET.

## 8. Perguntas abertas

1. Linguagem de api-* e ms-*? Branches de ms-* citam "node"; rótulo Java da wiki
   precisa de verificação.
   → Parcial (Camada 2): ms-signalr é Java 21/Spring Boot; api-* não são apps
     (configs APIM). Camada ms-* é poliglota. Demais api-*/ms-* não verificados.
2. bff_atividade não consolida Atividade+AtividadePronta+AtividadeExportacao —
   cobre só AtividadePronta+Salas. Onde estão os endpoints de Atividade (CRUD) e
   AtividadeExportacao?
3. Por que o envelope de resposta é inconsistente (com/sem paginacao)?
4. APIM BFF→MS é padrão? Hoje 3 comportamentos diferentes.
5. O APIM valida assinatura do JWT (JWKS)? O modelo de confiança depende disso.
   → Parcial (Camada 2): a policy de api-ftd-ionica_sala faz só CORS + roteamento,
     sem validate-jwt local. Se há validação, é em escopo superior do APIM. Segue aberto.
6. SignalR: bff_sala proxia POST /sala/v1/signalr/token p/ ms-ftd-ionica_signalr.
   Confirma [D6]. Outros BFFs consomem SignalR?
   → Parcial (Camada 2): ms-signalr é serviço de negotiation; o servidor é o Azure
     SignalR Service (PaaS). Se outros BFFs consomem segue aberto.
7. Verificar estado dos 9 repos api-*: nomes batem com conteúdo? api-ftd-ionica_sala
   (verificado em 2026-05-19) tem conteúdo relativo a bancoquestao — provável
   copy-paste de template não renomeado. Outros api-* podem ter o mesmo problema.

## Apêndice — escopo não lido

api-* (9), ms-* (15), worker-* (13) — não clonados. Nos 7 BFFs:
services/repositories/lógica de negócio, Swagger runtime, pastas mock/.
