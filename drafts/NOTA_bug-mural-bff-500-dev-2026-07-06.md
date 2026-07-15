# Nota — bloqueador local/dev: mural-bff retorna HTTP 500 ao listar postagens

> **Data:** 2026-07-06
> **Para:** time de backend Mural (BFF/MS) — abrir issue no Azure DevOps
> **Status:** aguardando triagem
> **Ambientes afetados:** local (dev-server 3030) e ambiente `dev` (APIM dev)
> **Ambientes OK:** qa, uat, prd

---

## Sintoma

Ao acessar a aba Mural (professor) do MFE Sala rodando localmente
(portal `localhost:4200`), a tela cai no `EmptyState type="error"`
com a mensagem:

> "Algo deu errado. Não conseguimos carregar o mural no momento.
> Verifique sua conexão e tente novamente."

O mesmo comportamento reproduz apontando o local pro APIM de `dev`.
Em `qa`, `uat` e `prd` a tela abre normalmente com o mesmo usuário.

## Request que falha

```
GET https://api-management-plataformaionica-dev.azure-api.net/mural-bff/mural/v1/escolas/badd955f-29a9-ea11-a813-000d3ac058c3/salas/97402e9a-efb6-402e-86d6-476bd44dd253/postagem?pagina=0&tamanho=10
```

- **Status HTTP:** `500 Internal Server Error`
- **Corpo (mensagem):** `"Request failed with status code 400"`
- **Headers do request no FE:** `Authorization: Bearer <msal-token>`,
  `Ocp-Apim-Subscription-Key: <apim-key>`, `Accept: application/json`

Rota FE: `mfe-ftd-ionica_mural/src/services/Postagem/postagemRoutes.ts:11-12`
Chamador FE: `useGetPostagemPaginado.ts:48` → `PostagemService.getPostagemPorEscolaSala`

## Análise (FE)

A mensagem `"Request failed with status code 400"` **é o `.message` de
um erro do axios**. Ela nunca deveria virar corpo de resposta HTTP — é
string interna do cliente axios. A hipótese mais provável:

1. O **mural-bff** faz uma chamada axios pra um serviço interno (candidato:
   `mural-ms` — no `mfe-ftd-ionica_portal/src/Environment.ts:26` tem
   `MURAL_MS_URL` marcado com `// CHANGE BFF→MS — Mural MS`, indicando
   migração em curso do BFF pro MS).
2. Esse chamado interno retorna **400 Bad Request**.
3. O BFF **não trata** essa exceção e o erro estoura como 500, com o
   `error.message` do axios sendo serializado como corpo.

Ou seja: **o erro real é 400 num serviço downstream do mural-bff**, não
500 no BFF.

## Por que o FE está descartado

- Payload (`pagina=0&tamanho=10`) e rota batem com o contrato definido
  em `postagemRoutes.ts`.
- Mesmo código FE funciona contra `qa`, `uat`, `prd` com o mesmo usuário
  e sala.
- `guid_escola` e `guid_sala` vêm de fontes válidas (`sessionStorage`
  populado pelo portal + route param) e o usuário está autenticado
  (token MSAL presente, APIM aceita).
- Um bug 4xx no FE geraria 4xx no APIM, não 500 com mensagem-de-axios
  no corpo.

## Perguntas pro backend

1. **Qual serviço downstream o `mural-bff` está chamando** nesse endpoint
   no ambiente `dev`? BFF→MS já ativo em dev, ou ainda BFF→BFF-legado?
2. **O que esse downstream retornou 400?** Log/traceId disponível no
   Application Insights / Datadog de dev pra
   `guid_escola=badd955f-29a9-ea11-a813-000d3ac058c3` +
   `guid_sala=97402e9a-efb6-402e-86d6-476bd44dd253` em 2026-07-06.
3. **Contrato divergente entre envs?** O `mural-ms` de dev pode estar
   com um contrato mais novo (ex.: `pagina` 1-based) que o de qa/uat/prd
   ainda não recebeu — ou vice-versa.
4. **Dados de seed em dev estão consistentes?** Se essa sala/escola só
   existe em qa/uat/prd, o MS de dev pode estar rejeitando o par
   escola/sala como inválido — o que justificaria o 400 downstream.
5. **Independente da causa raiz do 400:** o `mural-bff` deveria capturar
   erros axios e propagar o status original (400) com corpo estruturado
   `{ code, message, traceId }`, não deixar vazar `error.message` como 500.
   Vale abrir tarefa separada de hardening no BFF.

## Ações do lado do FE (após correção)

Nenhuma mudança de código prevista. Assim que o backend confirmar,
retesto local apontando pro APIM de dev.

## Como reproduzir

1. Rodar `mfe-ftd-ionica_portal` (4200), `mfe-ftd-ionica_sala` (3040)
   e `mfe-ftd-ionica_mural` (3030) locais, todos apontando pro APIM dev.
2. Logar como usuário School Admin / Professor com acesso à escola
   `badd955f-...`.
3. Entrar em uma sala e clicar em **Mural**.
4. DevTools → Network → request pro `mural-bff/.../postagem` retorna 500.

## Prereq de setup que já foi corrigido no FE

Antes desse bug de backend, havia um erro de bundling ao entrar na aba
Mural local (`ChunkLoadError` de `cssExtractHmr` apontando pra `:4200`).
Causa: `.env` do `mfe-ftd-ionica_mural` não definia `PUBLIC_URL_MFE_MURAL`,
então o `assetPrefix` em dev virava `/`, fazendo chunks async do Mural
serem pedidos ao origin do host (portal). Corrigido adicionando o bloco
de URLs de MFEs no `.env` do Mural — não relacionado ao 500.
