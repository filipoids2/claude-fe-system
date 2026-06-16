> ⚠️ **NÃO COMPARTILHAR.** Arquivo pessoal de rascunho. Conteúdo sensível
> (auth, secrets) — circulação apenas no trio entrante (Filipe, Elias, Well)
> durante sessão privada acordada.

# Achados de segurança — BFFs V3 · RASCUNHO TÉCNICO

> Status: rascunho, NÃO é documento fechado. Input pra sessão privada do trio
> entrante (Filipe, Elias, Well) sobre como tratar achados de segurança herdados
> da agência externa. Não circular antes dessa conversa.
> Base: leitura metadata-only dos 7 BFFs da Camada 1, 2026-05-17.
> Atualizado 2026-05-19 com achados da Camada 2 (ms-signalr, api-sala, v1-v3, ts-sala, ta-sala).

## Por que este arquivo existe

Os achados são herdados — débito da agência, não decisão do time atual. Os três
entramos juntos; ninguém é "dono" desse código. A questão não é só técnica, é de
processo: como reportar sem parecer acusação a quem ainda está na casa, e
priorizando certo. As sugestões no fim são pra decidirmos juntos.

## 1. JWT sem verificação de assinatura

Todos os BFFs que inspecionam token usam jwt.decode() — nunca jwt.verify(). Sem
JWKS do Azure AD B2C, sem checagem de iss/aud/tid. "Validação" = decodificar
payload + conferir exp.

| BFF | Arquivo:linha |
|---|---|
| login | bff-ftd-ionica_login/src/infrastructure/session/session.service.ts:41-58 |
| sala | bff-ftd-ionica_sala/src/.../session.service.ts:12,29 |
| mural | bff-ftd-ionica_mural/src/.../session.service.ts:19-46 |
| bancoquestao | bff-ftd-ionica_bancoquestao/src/.../session.service.ts:19,117 |

Implicação: um JWT bem-formado, não-expirado e com as claims certas passa — mesmo
forjado. A integridade depende do APIM ser a única porta de entrada.
A confirmar ANTES de concluir severidade: o APIM faz validação JWKS na borda?
Sem essa resposta, qualquer número de severidade é chute.

## 2. Secrets em arquivo versionado

bff-ftd-ionica_login tem credenciais em texto puro:
- .env.example — ADB2C_CLIENT_SECRET, OCP_APIM_SUBSCRIPTION_KEY
- docker-compose.yml — idem + senha do Redis Azure

Ambiente dev, mas: (a) client_secret de dev ainda obtém token no tenant B2C de
dev; (b) ficam no histórico git. A confirmar: valores ainda válidos? Rotação feita?

## 3. Três padrões de auth (inconsistência estrutural)

| BFF(s) | Modelo | Risco |
|---|---|---|
| login | id_token no body, decode | sem verify (item 1) |
| sala, mural, bancoquestao | id_token no header, decode | sem verify (item 1) |
| atividade, agenda | sem token — headers x-ionica-* em claro | spoofing se acessível fora do APIM |

Soma: CORS aberto (* / origin:true sem allowlist) em mural, atividade, agenda,
bancoquestao — amplia a superfície se o BFF não estiver estritamente atrás do gateway.

## Achados que merecem atenção imediata (2026-05-19, Camada 2)

- **Divergência de contrato ms-signalr** — risco de quebra em FE conforme qual branch
  for promovida. O endpoint `POST /sala/v1/signalr/token` (proxiado pelo bff_sala,
  consumido pelo FE via `@microsoft/signalr`) exige `userId` no body em `prd`, não na
  branch `feature/189556-add-lesson-plans`. Levantar com o trio ANTES da reunião de
  segunda.

## Achados tangenciais de débito (não-segurança) (2026-05-19, Camada 2)

- **api-ftd-ionica_sala tem conteúdo de bancoquestao** — `openapi.yaml` e policy do
  APIM descrevem/apontam o domínio Banco de Questão, não Sala. Provável template não
  renomeado durante a war room. Higiene de repositório, não segurança. Pode afetar
  outros `api-*`.

## Sinais de processo Git frágil (a investigar se é geral) (2026-05-19, Camada 2)

- **ms-ftd-ionica_signalr** tem 25+ branches, incluindo 5-6 variações do bugfix
  #254241 (com typo `bufix`), merges como branches dedicadas (`feature/merge_qa_prd`),
  e a default branch é uma `feature`, não `main`/`develop`. Padrão a investigar nos
  outros repos.

## Sugestões — pro trio decidir junto

Nada aqui é decisão tomada. Opções:

1. Confirmar a premissa primeiro: o APIM valida JWKS? Sem isso, não dá pra
   classificar severidade. Dono da resposta: TL/Arquiteto ou plataforma.
2. Formalização — opções: (a) issue/PBI por achado, rastreável; (b) um PBI único
   "hardening de auth dos BFFs" com checklist; (c) backlog interno do trio sem
   formalizar ainda. Trade-off: formal dá visibilidade mas expõe; interno some.
3. Comunicação com a liderança (Macha): o item 2 (secrets) provavelmente merece
   heads-up direto e rápido — rotação é ação de curto prazo. Itens 1 e 3 podem ir
   pelo fluxo normal de backlog.
4. Prioridade sugerida (a debater): secrets > confirmar JWKS > unificar os 3
   padrões de auth > CORS. Mas depende da resposta do item 1.
5. Tom: enquadrar como "débito herdado a endereçar", não como falha. Quem escreveu
   não está mais no time — apontar dedo não ajuda.

## Próximo passo

Levar este rascunho pra sessão do trio. Depois, decidir o que vira artefato formal
e o que fica interno — e só então atualizar/criar docs versionados.
