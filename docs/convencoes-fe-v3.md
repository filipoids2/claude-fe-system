# docs/convencoes-fe-v3.md — convenções práticas FE V3

> Consultar quando: criar componente, novo arquivo, integrar com
> BFF, mexer em auth, observabilidade, ou qualquer escrita de
> código novo no FE.
>
> Referência completa: `ONBOARDING_FRONTEND.md`. Este arquivo
> extrai **só o essencial** pra escrita rápida de código.

## 1. Estrutura de pastas e componentes

**No portal (host MFE):**
- Páginas em `src/pages/`
- Componentes compartilhados em `src/components/`
- Ícones em `src/assets/icons/`

**Atomic Design bilíngue da wiki NÃO está implementado no portal.**
Wiki diz `shared/{subAtomic,atoms,molecules}` (EN) + `app/`
(organisms, templates, pages — PT). Código real não segue. Em caso
de dúvida sobre **MFE de feature** (sala, mural, etc), perguntar
pro time qual padrão usar.

> Detalhe completo: ONBOARDING_FRONTEND.md Seção 4 → "FATO —
> estrutura no portal" e Seção 9 → "Atomic Design vs estrutura
> real".

## 2. Naming

- **Arquivos de componente:** PascalCase. Ex: `BotaoExportar.tsx`
- **Arquivos de hooks:** camelCase com prefixo `use`. Ex: `useAuth.ts`
- **Funções/componentes:** PascalCase pra componentes, camelCase
  pra funções
- **IDs HTML pra teste:** kebab-case, começa com ação. Padrão:
  `<acao>-<contexto>-<tela>`

✅ **Correto:**
```tsx
<button id="btn-exportar-atividade-detalhe">Exportar</button>
```

❌ **Errado:**
```tsx
<button id="exportBtn">Exportar</button>
<button id="btn_exportar_atividade">Exportar</button>
```

> Planilha mestra de IDs no Azure DevOps (`Ids-para-Testes.xlsx`).
> Atomic Design + bilíngue: ONBOARDING Seção 4.

## 3. Ícones — SVGR obrigatório

**Padrão estabelecido em outubro/2025** (`copilot-instructions.md`
do portal). Sem exceção.

✅ **Correto:**
```tsx
import { IconExport } from '@/assets/icons';

<IconExport className="w-6 h-6 text-blue-500" />
```

❌ **Errado:**
```tsx
<img src="/icons/export.svg" alt="Exportar" />
<svg viewBox="0 0 24 24">...</svg>
<div style={{ background: "url('data:image/svg+xml...')" }} />
```

Pra adicionar ícone novo: gerar via SVGR no pipeline e re-exportar
do barrel `src/assets/icons/index.ts`.

## 4. Estilização — Tailwind 3.4

Tailwind utility-first. Evitar CSS custom solto.

✅ **Correto:**
```tsx
<div className="flex items-center gap-2 px-4 py-2 bg-white rounded-lg shadow">
```

❌ **Errado:**
```tsx
<div style={{ display: 'flex', padding: '8px 16px' }}>
<div className="meu-card-customizado">
```

**Exceções aceitas:**
- Customização declarada no `tailwind.config.ts`
- `@apply` em CSS Modules pra padrões muito repetidos

**Atenção:** workaround `md:!block` documentado no ONBOARDING —
indica problema de CSS injection conhecido. Se precisar, sinalizar.

## 5. Auth — Azure AD B2C

Token guardado em `sessionStorage['@ionica-v3/msal-token']`
(NÃO localStorage como dizia wiki antiga).

**Pra ler o token:**
```ts
// Via toolkit (preferível)
import { getIonicaAuthToken } from '@ftd/lib-react-toolkit';
const token = getIonicaAuthToken();

// Direto (evitar)
const token = sessionStorage.getItem('@ionica-v3/msal-token');
```

**Pra chamar BFF:** axios já injeta `Authorization: Bearer <token>`
via interceptor configurado em `AxiosFactory`. Não setar manualmente.

> Detalhe da arquitetura: ONBOARDING Seção 5 → "Autenticação".

## 6. Comunicação com BFF

Todos os BFFs são NestJS/Node. Cliente HTTP: **axios via singleton**.

**Convenção do contrato:**
- Request body: **snake_case** (atípico, mas é o padrão)
- Response: envelope `{ metadados, dados, paginacao, mensagem }`
  (parcial em sala/atividade/agenda — sem `paginacao`)

✅ **Correto:**
```ts
const { data } = await api.post('/atividade/v1/exportar', {
  atividade_id: id,
  formato_arquivo: 'pdf',
});

const lista = data.dados; // não data.data ou data.items
```

❌ **Errado:**
```ts
const { data } = await api.post('/atividade/v1/exportar', {
  atividadeId: id,        // ❌ camelCase
  formatoArquivo: 'pdf',
});
```

> Endpoints completos: `STACK_BACKEND.md` Seção 3.

## 7. Datadog RUM — observabilidade

Inicialização já está no toolkit (`@ftd/lib-react-toolkit`). Não
re-inicializar em MFE de feature.

**Pra registrar evento customizado:**
```ts
import { datadogRum } from '@datadog/browser-rum';

datadogRum.addAction('export_atividade_pdf', {
  atividade_id: id,
});
```

**Sample rate:** 5% em prod (não 20% como wiki antiga). Não mudar
sem alinhar com time.

> Detalhe: ONBOARDING Seção 5 → "Datadog RUM".

## 8. Quando wiki e código divergirem

**Regra:** código vence. Wiki está desatualizada em vários pontos.

**Fluxo:**
1. Sinalizar a divergência na resposta (não silenciar)
2. Seguir o código pra implementação atual
3. Levar pro time como pergunta — pode ser bug ou wiki stale

**Casos conhecidos** (documentados no ONBOARDING):
- Atomic Design (wiki diz, código não segue)
- Sample rate Datadog (wiki: 20%, real: 5%)
- localStorage vs sessionStorage do token
- Estrutura `shared/` + `app/` vs `src/pages/` + `src/components/`

## 9. O que NÃO escrever sem perguntar

- Mudança em interceptors HTTP (auth, headers de tracing)
- Mudança em config do MF (host/remote)
- Re-inicialização de SDKs (Datadog, MSAL)
- Customização do `tailwind.config.ts` global
- Novos providers globais no portal
- Mudança em estrutura compartilhada (`@ftd/lib-react-toolkit`,
  `@ftd/lib-react-ions-facade`)

Esses tocam **infraestrutura compartilhada** — quebram outros MFEs
silenciosamente. Sempre consultar time antes.
