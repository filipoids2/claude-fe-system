# DRAFT — Seção 5 do ONBOARDING_FRONTEND.md (proposta, NÃO aplicada)

> Passada 4 (Seção 5 + investigação Datadog), 2026-05-15. Leitura de código:
> `package-lock.json`, `Environment.ts`, `.env.cloud`/`.env.example`, `src/services/*`,
> `src/configs/*`, `bootstrap.tsx`, `App.tsx`. Substitui `ONBOARDING_FRONTEND.md`
> linhas ~402-430 (Seção 5 inteira).
>
> **Status: aguardando aprovação. Nada gravado no arquivo final.**
> Caminhos relativos a `D:\local\ftd\ftd-frontend\`.

---

## Changelog vs. Seção 5 atual

- **Reescrita.** Versão atual é wiki-only (passada 1) — "BFF dedicado existe" sem
  detalhe, "Onde/como o login acontece — não documentado".
- **BFF/API agora é FATO:** descoberta por env var, axios por domínio, APIM.
- **Auth reescrito:** o stack novo autentica via `lib-ftd-ionica-react_toolkit`
  (que bundla MSAL). Três implementações de auth, não uma.
- **Datadog confirmado** — investigação da passada 4. Era hipótese na Seção 4.
- **SignalR** entra como ponto de contato (não existia na Seção 5).
- **Contradição registrada:** wiki diz `localStorage['session']`; o código V3 usa
  `sessionStorage['@ionica-v3/msal-token']`.
- Contrato REST (snake_case, envelope) mantido, marcado como wiki-sourced.

---

## Descobertas do inventário de 41 repos (incorporadas nesta passada)

O inventário completo do projeto (41 repos: `mfe` + `bff` + `api` + libs) trouxe 4
descobertas. Onde cada uma mexeu nas hipóteses:

- **[D1] Existe `bff-ftd-ionica_login`.** Resolve o "mistério" da auth do
  `mfe-ftd-ionica_login`. → Mudou a HIPÓTESE de auth da Seção 5 (bloco HIPÓTESES, bullet
  `login` ↔ BFF) e suavizou o framing "war room, três jeitos" da edição [S4-3].
- **[D2] Existe `bff-ftd-ionica_classificacao` sem MFE/API correspondente.** → Nova
  pergunta na Seção 9 ([S9-2]). Não muda o corpo da Seção 5.
- **[D3] Existe `bff` + `api` de `aulapronta`, mas nenhum `mfe`.** → Nova pergunta na
  Seção 9 ([S9-3]). Reforça que Aula Pronta provavelmente ainda vive no monolítico.
- **[D4] `boilerplate` é populado no backend (`api`/`bff`) e vazio no frontend (`mfe`).**
  → Nova pergunta na Seção 9 ([S9-4]). Reforça a hipótese de war room já registrada na Seção 2.

Backend **não** será clonado nesta passada — as descobertas entram só como contexto.

---

## Conteúdo proposto (substitui as linhas ~402-430)

## 5. Pontos de contato com backend

> ⚠️ Reescrita em 2026-05-15 (passada 4) com leitura do código. A versão anterior era
> passada 1 (wiki-only). FATO = verificado no código; o contrato REST documentado
> segue marcado como wiki-sourced. Citações `repo/arquivo:linha`.

### FATO — como o FE chama o backend (BFF + API)

- **Um BFF por domínio, descoberto por env var.** O portal declara em
  `mfe-ftd-ionica_portal/src/Environment.ts:18-26`: `AGENDA_BFF_URL`,
  `ATIVIDADES_BFF_URL`, `BANCO_QUESTOES_BFF_URL`, `MURAL_BFF_URL`, `SALAS_BFF_URL` — e
  também APIs diretas `AGENDA_API_URL`, `ATIVIDADES_API_URL`, `SALAS_API_URL`.
  Injetadas no build pelo Rsbuild (prefixo `PUBLIC_`) — `mfe-ftd-ionica_portal/.env.cloud:19-28`.
- **Os BFFs ficam atrás de Azure API Management (APIM).** Toda chamada leva o header
  `Ocp-Apim-Subscription-Key`, valor de `Environment.APIM_KEY` (ou `*_BFF_APIM_KEY`
  por domínio) — `mfe-ftd-ionica_portal/src/services/bffSala.ts:9`,
  `mfe-ftd-ionica_agenda/.env.example:8,12`.
- **Cliente HTTP: instâncias `axios` por domínio**, nomeadas `bff*` (vai pro BFF) vs
  `api*` (vai pra API direta) — ex.: `portal/src/services/{bffSala,apiSala,bffSignalR}.ts`,
  `bancoquestao/src/services/{bffQuestoes,apiAtividades}.ts`, `atividade/src/services/apiClient.ts`.
  (Distribuição axios vs TanStack vs fetch por repo: ver Seção 4 → tabela
  "cliente HTTP / data fetching".)
- **Padrão de instância** (`bffSala.ts:5-20`): `baseURL` do env, headers fixos
  `Content-Type: application/json` + `Ocp-Apim-Subscription-Key`, e um **request
  interceptor** que injeta `Authorization: Bearer ${getIonicaAuthToken()}` a cada request.
- **Endpoints concretos** não catalogados aqui; um exemplo verificado:
  `POST /sala/v1/signalr/token` no BFF de Salas (`portal/src/services/signalRServiceToken.ts:15`).

### FATO — autenticação client-side

- **O stack novo autentica via `lib-ftd-ionica-react_toolkit`**, que **bundla
  `@azure/msal-browser` 4.22.1 + `@azure/msal-react` 3.0.19**
  (`mfe-ftd-ionica_portal/package-lock.json:7615-7632`). É MSAL / Azure AD B2C, com a
  lib MSAL escondida dentro da toolkit — por isso não aparece nos 8 `package.json`
  (ver Seção 4).
- A toolkit expõe `useIonicaAuth()` (hook: `ionicaAuthToken`, `ionicaAuthProfile`) e
  `getIonicaAuthToken()` (token cru pros interceptors axios). Uso em
  `portal/src/contexts/WhoAmIContext.tsx:24-25`, `portal/src/components/ProtectedRoute.tsx`,
  e nos serviços HTTP de portal/atividade/bancoquestao.
- **Config de auth no portal:** `src/configs/IonicaAuthConfig.ts` — `clientId`,
  `authority`, `authorityDomain`, `signUpOrSignInFlow`, `scopes` (de `Environment.AUTH_*`,
  ADB2C), `defaultRedirectPath: "/salas"`, `loginRoutePath: "/login"`.
- **Token e profile espelhados em `sessionStorage`** pra consumidores não-React /
  cross-MFE — `portal/src/App.tsx:26-30`: `@ionica-v3/msal-token` (token),
  `@ionica-v3/escola_guid`, `@ionica-v3/escola_selected_school`,
  `@ionica-v3/usuario_crmId`, `@ionica-v3/usuario_tipo_acesso`.
- **O profile vem em `snake_case` e é consumido cru**: `school_crm_id`, `school_guid`,
  `user_crm_id`, `user_role` (`App.tsx:27-30`) — sem camada de conversão, coerente com
  o contrato REST snake_case.
- **Três implementações de auth coexistem, todas Azure AD B2C** (amplia o que a Seção 4
  → HIPÓTESES descreve):
  1. **Stack novo** (portal + 5 MFEs) — via toolkit → `IonicaAuth` (MSAL bundled).
  2. **`monolithic`** — MSAL próprio: `@azure/msal-browser`/`@azure/msal-react` direto +
     módulo local `apps/shell/src/utils/IonicaAuth/` (`ClientApplication.ts`,
     `IonicaAuthProvider.tsx`, `useIonicaAuth.ts`).
  3. **`login`** — OAuth2 redirect cru, sem lib MSAL; `apps/shell/src/pages/Token.tsx`
     usa cookie `id_token` e referencia `localStorage['@ionica/msal-token']` (prefixo
     `@ionica/`, não `@ionica-v3/`). O inventário de repos revelou um
     `bff-ftd-ionica_login` dedicado — ver HIPÓTESES.

> ⚠️ **Armadilha de naming.** Existem **dois** `IonicaAuth`/`useIonicaAuth` no projeto,
> com o mesmo nome e implementações diferentes: o do `monolithic` (módulo local
> `apps/shell/src/utils/IonicaAuth/`, MSAL próprio) e o da `lib-ftd-ionica-react_toolkit`
> (consumido pelo stack novo). Ao ler código ou buscar por `useIonicaAuth`, **confirme a
> origem do import** — pacote `lib-ftd-ionica-react_toolkit` vs. caminho local — antes de
> assumir o comportamento.

### FATO — canal realtime (SignalR)

- O `portal` mantém conexão **SignalR** (`@microsoft/signalr`) — `src/services/signalRService.ts`:
  classe `SignalRService` (singleton), `HubConnectionBuilder` via `createConnection(token, url)`.
- O token de conexão é emitido pelo **BFF de Salas**: `POST /sala/v1/signalr/token`
  (`src/services/signalRServiceToken.ts:15`), resposta
  `{ accessToken, connectionId, expiresIn, hubName, timestamp, url }`.
- Uso identificado: notificação de progresso de "Importar Aulas Prontas"
  (`src/providers/SignalRProvider.tsx`). `sala` também declara `@microsoft/signalr`
  (Seção 4) — uso simétrico provável, a confirmar.

### FATO — observabilidade (Datadog RUM) — confirmado nesta passada

- **Datadog RUM está em uso na V3.** `@datadog/browser-rum` 6.24.1 +
  `@datadog/browser-rum-react` 6.24.1 vêm **bundled na `lib-ftd-ionica-react_toolkit@1.0.3`**
  (`mfe-ftd-ionica_portal/package-lock.json:7615-7632`) — confirma a hipótese (b) da Seção 4.
- Instrumentado no **portal (host)**: `src/bootstrap.tsx:8,15,40` envolve o app em
  `<IonicaMonitorProvider config={IonicaMonitorConfig}>` (provider importado da toolkit).
- Config — `src/configs/IonicaMonitorConfig.ts`: `site: "datadoghq.com"`,
  `service: "ionica-v3"`, `sessionSampleRate: 5`, `sessionReplaySampleRate: 1`,
  `trackUserInteractions/Resources/LongTasks: true`, `defaultPrivacyLevel: "mask-user-input"`,
  `allowedTracingUrls` restrito aos domínios `plataforma.*souionica*`.
- Env: `DD_APPLICATION_ID`, `DD_CLIENT_TOKEN`, `DD_ENV`
  (`src/Environment.ts:31-34,76-78`; `.env.cloud:31-33`).
- ⚠️ A wiki citava `sessionSampleRate` 20% — **o valor real no código é 5%**.
- `monolithic` **não** está instrumentado — só um TODO
  (`apps/shell/src/utils/IonicaAuth/MsalConfig.ts:19`).

### Contrato REST (documentado na wiki — não reverificado no código nesta passada)

Fonte `🏗️-Arquitetura/Diretrizes-de-Contrato-Rest-%2D-Swagger.md`:
- Recursos no plural; verbos HTTP padrão; status 200/201/204/400/401/403/404/500.
- URL hierárquica (`/schools/{school_id}/students/{student_id}`), versionada (`/v1/...`).
- **JSON em `snake_case`** — coerente com o consumo do profile de auth (campos
  `school_crm_id` etc., acima).
- UUID pra IDs, ISO 8601 pra datas. Auth via JWT, HTTPS.
- Envelope de resposta padrão:
  ```json
  {
    "metadados": { "dominios": ["sala"], "versao_api": "v1" },
    "dados": { "turmas": [] },
    "paginacao": { "pagina": 0, "totalPaginas": 0, "totalRegistros": 0 },
    "mensagem": "string"
  }
  ```

### Eventos / mensageria

- O FE **não** consome EventHubs — a interop por evento é server-side entre módulos
  backend (wiki `CDC-%2D-Interoperabilidade-interna.md`). Tópicos `evh-{dominio}-{evento}`.

### HIPÓTESES (a validar)

- **`login` delega a complexidade de auth pro `bff-ftd-ionica_login`** (repo confirmado
  no inventário de repos — [D1] —, ainda não lido). Explica por que o `mfe-ftd-ionica_login`
  faz só OAuth2 redirect cru, sem lib MSAL: o BFF de login provavelmente guarda o
  `client_secret`, troca `code` por token e gerencia refresh — padrão **BFF-auth**,
  arquitetonicamente correto. Ou seja: `login` não é o "jeito tosco da war room" — pode
  ser a implementação **mais** correta das três. A confirmar lendo o `bff-ftd-ionica_login`
  (clonagem do backend é decisão à parte).
- `sessionStorage['@ionica-v3/msal-token']` é um **espelho** do estado do MSAL/toolkit
  pra consumidores não-React (interceptors, SignalR). A fonte canônica é
  `getIonicaAuthToken()`. A confirmar se token e espelho podem divergir.
- Os interceptors nomeiam a variável `id_token` (`bffSala.ts:15`) — sugere que o BFF
  valida o **`id_token`** (não o `access_token`). A confirmar.
- SignalR provavelmente serve mais que "Importar Aulas Prontas" (Mural? notificações de
  Sala?) — só esse uso foi visto nesta passada.

### Contradições / pendências

- **`localStorage['session']` (wiki / Seção 5 antiga) não existe no código V3.** O token
  mora em `sessionStorage['@ionica-v3/msal-token']`. A chave `'session'` vem do doc de
  Observabilidade `[DRAFT]` da wiki — parece obsoleta ou descreve V1/V2. Não tratar
  `localStorage['session']` como verdade.
- Como o token chega no `portal` após o login (handoff de `home.souionicahml.com`) —
  o intermediário provável é o `bff-ftd-ionica_login` ([D1], hipótese acima); segue
  aberto até lermos esse BFF (Seção 9, pergunta de auth B2C+JWT).
- Catálogo de endpoints do BFF (Swagger) — não obtido; perguntar URL pro TL.

---

## Mudanças na Seção 4 e na Seção 9 (a gravar junto com a Seção 5)

Decidido: corrigir a Seção 4 agora, na mesma passada. As edições abaixo, com as linhas
atuais do `ONBOARDING_FRONTEND.md` pra cross-referência.

### [S4-1] Tabela do stack novo — adicionar linha "Auth"

A tabela `### FATO — stack novo` não tem linha de Auth hoje. Adicionar, logo após a
linha `Design System`:

> \| **Auth** \| Azure AD **B2C** via `lib-ftd-ionica-react_toolkit` — a toolkit bundla `@azure/msal-browser` 4.22.1 + `@azure/msal-react` 3.0.19; token via `getIonicaAuthToken()`. Quadro completo na Seção 5 \| `mfe-ftd-ionica_portal/package-lock.json:7615-7632`; `mfe-ftd-ionica_portal/src/configs/IonicaAuthConfig.ts` \|

### [S4-2] Tabela do stack velho — linha "Auth" (linha 300)

O conteúdo já está correto (`login` OAuth2 cru × `monolithic` libs MSAL). Só acrescentar
cross-ref no fim da célula do `monolithic`: `... (Azure AD) — 3 implementações na Seção 5`.

### [S4-3] Bullet de auth no bloco HIPÓTESES (linhas 345-353)

Substituir o bullet inteiro "**Auth fala com Azure AD por dois caminhos diferentes...**"
(hoje só fala de `login` × `monolithic`, e cita `localStorage['session']` que a Seção 5
mostrou não existir no V3) por:

> - **Auth: três implementações, todas Azure AD B2C** — confirmado na passada 4; quadro
>   completo na Seção 5 → autenticação client-side. (1) Stack novo (portal + 5 MFEs)
>   autentica via `lib-ftd-ionica-react_toolkit`, que bundla `@azure/msal-*`
>   (`mfe-ftd-ionica_portal/package-lock.json:7615-7632`). (2) `monolithic` usa MSAL
>   próprio (`@azure/msal-browser`/`@azure/msal-react` direto, módulo local
>   `apps/shell/src/utils/IonicaAuth/`). (3) `login` faz OAuth2 redirect cru, sem lib
>   MSAL — porque delega a auth pro `bff-ftd-ionica_login` (padrão BFF-auth), o que pode
>   torná-la a implementação mais correta das três, não a pior. Heterogeneidade provável
>   da war room; quadro completo e fontes na Seção 5.

### [S4-4] Bullet de Datadog no bloco HIPÓTESES (linhas 339-344)

Substituir o bullet "**Datadog RUM** é decisão arquitetural histórica..." por:

> - **Datadog RUM — CONFIRMADO na V3** (passada 4, 2026-05-15). A hipótese (b) se
>   confirmou: `@datadog/browser-rum` 6.24.1 vem bundled na
>   `lib-ftd-ionica-react_toolkit@1.0.3` (`mfe-ftd-ionica_portal/package-lock.json:7615-7632`)
>   — por isso não estava nos 8 `package.json`. Instrumentado no portal (host) via
>   `IonicaMonitorProvider`. Config, sample rate real (5%, não 20%) e env vars na
>   Seção 5 → observabilidade.

### [S4-5] Bloco "A confirmar" (linha 366)

Remover a linha "Se o Datadog RUM está ativo em runtime e por qual caminho." (respondida).

### [S9-1] Seção 9 → pergunta 1 (Datadog) — marcar respondida

Aplicar `~~strikethrough~~` + nota, no padrão que a Seção 9 já usa:
"~~**Datadog RUM** existe no front V1/V2... descontinuado deliberadamente?~~
**Respondido (passada 4):** bundled na `lib-ftd-ionica-react_toolkit`, instrumentado no
portal via `IonicaMonitorProvider` — ver Seção 5."

### [S9-2] Seção 9 → nova pergunta (classificação)

Adicionar à subseção "🔍 Stack — achados da leitura de código":
"Existe `bff-ftd-ionica_classificacao` sem MFE/API correspondente. Confirma que é
funcionalidade transversal (BNCC, componente curricular) usada por múltiplos MFEs?
Quem mantém?"

### [S9-3] Seção 9 → nova pergunta (Aulas Prontas)

"Aulas Prontas tem BFF+API mas não MFE. Vive no monolítico ainda? Tem plano de extrair?
Em qual fase do roadmap?"

### [S9-4] Seção 9 → nova pergunta (boilerplate BE vs FE)

"`api-ftd-ionica_boilerplate` e `bff-ftd-ionica_boilerplate` existem e são populados;
`mfe-ftd-ionica_boilerplate` é vazio. Vale espelhar as convenções do backend pro
frontend (estrutura, CI/CD, README) ou há razão pra não ter sido feito?"

### [S9-5] Seção 9 → nova pergunta (id_token vs access_token nos interceptors)

Adicionar à subseção "🔍 Stack — achados da leitura de código":
"Os interceptors HTTP do stack novo injetam `Authorization: Bearer ${getIonicaAuthToken()}`
e nomeiam a variável `id_token` (ex.: `mfe-ftd-ionica_portal/src/services/bffSala.ts:15`).
Os BFFs validam o `id_token` ou o `access_token` do Azure AD B2C? Se for o `id_token`, é
intencional?"

---

**O que será gravado numa tacada, após aprovação deste draft:**
1. Seção 5 inteira reescrita (~linhas 402-430).
2. Seção 4: 5 edições — [S4-1] a [S4-5].
3. Seção 9: 5 edições — [S9-1] (marcar respondida) + [S9-2..S9-5] (4 perguntas novas).

Nada gravado até a aprovação. `DRAFT_secao5.md` permanece em disco como histórico.
