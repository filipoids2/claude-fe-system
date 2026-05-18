# FTD v3 — Resume da sessão de onboarding FE

> Cole isso como contexto inicial de uma nova sessão em outra pasta do projeto.
> Sessão original: leitura da wiki em `D:\local\ftd\ftd_v3` (repo de docs PT-BR).

---

## 1. Contexto do usuário

- **Perfil**: 20+ anos de experiência. Frontend architect, full-stack confortável.
- **Stack que domino**: React, Next.js, React Native, Design Systems, TypeScript,
  Tailwind. Atualizado em RSC, Server Actions, React Compiler, New Architecture RN,
  Edge runtime.
- **Papel no FTD**: estou começando agora no projeto **Aceleração Iônica v3**.
  Sou dev frontend (não tech lead, não arquiteto).
- **Gaps de domínio neste projeto**: ver Seção 6 (Domínios de aprendiz).
- **Herança da agência**: o projeto foi tocado por agência terceirizada antes —
  ler com olho crítico, padrões podem refletir decisões de fora, não do time
  interno atual.
- **Preferências de comunicação** (já no CLAUDE.md global):
  - Direto ao ponto, sem preâmbulo, brevidade > volume.
  - Português BR. Tom de par técnico — pode discordar e me puxar a orelha.
  - Sem emojis. Sem inventar API/lib (confirmar quando incerto).
  - Não criar arquivos novos sem necessidade.

---

## 2. Estado atual da análise

### O que já foi feito (Passada 1 — wiki only)

1. **Leitura controlada** da wiki em `D:\local\ftd\ftd_v3`, restrita a:
   - `Aceleração-iônica.md` (índice raiz)
   - `🏗️-Arquitetura/` (todos os .md com conteúdo)
   - `👨‍💻-Frontend/` (todos os .md com conteúdo)
   - `📦-Release-Notes/` (template only)
   - `Aceleração-iônica/🎒-Onboarding.md` (institucional)
   - `Aceleração-iônica/[Iônica-3.0]--Review.md` (sprints jun–out 2025)
2. **Ignorado nesta passada**: `.attachments/` (binários SVG/PNG dos diagramas C4),
   `👩‍💻-Backend/`, `🪲-QA/`, `📊-DADOS/`, `📋-Governança/`, `🧠-TechLeads/`,
   `🎨-UX/`, `🎯-Produto/`.
3. **Gerado**: `D:\local\ftd\ftd_v3\ONBOARDING_FRONTEND.md` — 9 seções,
   com Mermaid diagram (linhas tracejadas para conexões inferidas), citações
   com `(fonte: arquivo.md → seção "X")` em toda afirmação técnica, e Seção 9
   "Perguntas abertas" com stubs, contradições e decisões não documentadas.

### Achados consolidados

**Stack FE confirmada em texto**:
- React + TypeScript
- Atomic Design bilíngue (EN em `shared/` para SubAtomic/Atoms/Molecules;
  PT em `app/` para Organisms/Templates/Pages)
- Datadog RUM (`@datadog/browser-rum`) — sample 20% prod / 100% dev,
  `defaultPrivacyLevel: 'mask-user-input'`
- CKEditor Free (com problema de marca d'água mencionado em sprint review)
- JWT em `localStorage.getItem('session')` (decode pra extrair `guid`,
  `name`, `lastname`, `email` pro Datadog)

**Stack FE NÃO documentada (gaps críticos)**:
- Build tool (Vite? CRA? Next? Webpack custom?)
- Framework (SPA puro? Next? algo customizado?)
- Gerenciamento de estado (Redux? Zustand? Context only?)
- Sistema de estilização (Tailwind? styled-components? CSS Modules?)
- Roteamento (React Router? File-based?)
- Testes (Jest? Vitest? Playwright? RTL?)
- Monorepo (Nx? Turbo? pnpm workspaces? nada?)
- Lint/format (ESLint config? Prettier? Biome?)

**Backend confirmado**:
- Java + Spring Modulith + Hexagonal Architecture + DDD
- Azure EventHubs (mensageria entre módulos com naming `evh-*` e `sigr-*`)
- MongoDB + MySQL
- BFF dedicado entre FE e backend modular

**API conventions** (Diretrizes-de-Contrato-Rest-Swagger.md):
- REST/OpenAPI/Swagger
- JSON **snake_case** (atípico em FE moderno — atenção no FE)
- IDs em UUID
- Datas em ISO 8601
- JWT na auth
- Envelope de resposta: `{metadados, dados, paginacao, mensagem}`

**Versões coexistindo**:
- **V1**: legado (questões, aulas prontas, BNCC)
- **V2**: Onboarding — provisiona Escola/Turma/Usuário/Sala via UP-INSERT.
  `class.guid = tb_sala.pk_guid`. Padrão "Ensino Superior" é ignorado.
- **V3**: nova, em construção. Módulos v3 (Agenda, Atividade, AtividadePronta,
  AtividadeExportacao, Banco de Questões, Mural, Sala) trocam eventos via
  EventHubs.

**Hipótese principal não confirmada**:
- A palavra **"microfrontend" aparece APENAS no nome do arquivo de observabilidade**
  (`Observabilidade-Projetos-React-Frontend-[DRAFT--Adaptar-para-microfrontend].md`).
  O conteúdo descreve um único app "Iônica Platform Frontend". **Não dá pra
  afirmar pela wiki que existe MFE em runtime hoje.** Pode ser monolito React
  sendo planejado pra evoluir pra MFE. `Módulos.md` (0 B) e
  `Arquitetura-Micro-Services.md` (0 B) seriam os docs canônicos.

**Convenções de IDs pra teste** (Frontend.md):
- Kebab-case, começa pela ação: `btn-{acao}-{contexto}-{tela}`
- Exemplo: `btn-adicionar-livros-modal-adicionar-materiais-home-aula`
- Planilha de controle: `Ids-para-Testes.xlsx` no `.attachments/`

### Stubs documentais críticos (Seção 9 do ONBOARDING)

| Arquivo | Tamanho | Criticidade |
|---|---|---|
| `Módulos.md` | 0 B | **Alta** — único doc que poderia confirmar MFE vs monolito |
| `Arquitetura-Micro-Services.md` | 0 B | **Alta** — relacionado a MFE/microservices |
| `🚧-DDD.md` | 331 B | Média — DDD é base do backend |
| `Lemonade-Json-vs-Relacional.md` | 526 B | Média — "Lemonade" não definido |
| `🏗️-Arquitetura.md` | 12 B (`[[_TOSP_]]`) | Baixa — placeholder de TOC |
| `📦-Release-Notes.md` | 0 B | Baixa — apenas template |
| `♾️-DevOps.md` | 94 B | Média — pipelines/deploy não documentado |
| `👩‍💻-Backend.md` | 18 B | Baixa — não é foco |
| `CDC-v1.md.md` (typo) | 235 B | Baixa — V1 legado |

---

## 3. Próximo passo planejado

### Camada 1: analisar `fe-ftd-ionica-monolithic/`

Objetivo: fechar os gaps de stack FE listados acima E mapear em que estágio
está a migração para microfrontends (se existe). O repo é o monolítico atual,
então é a fonte de verdade pra estado real.

### Prompt completo a usar na nova sessão

```
ultrathink

Estou continuando o onboarding no FTD Aceleração Iônica v3.
Acabei a Passada 1 (wiki) e gerei o ONBOARDING_FRONTEND.md em
D:\local\ftd\ftd_v3\ONBOARDING_FRONTEND.md.

Agora estou no repo do **monolito FE atual** (fe-ftd-ionica-monolithic).
Quero entender a stack REAL e em que estágio está a migração pra MFE.

OBJETIVOS:
1. Mapear stack completa do FE:
   - Build tool, framework, versões
   - Gerenciamento de estado
   - Sistema de estilização
   - Roteamento
   - Testes
   - Lint/format
   - Monorepo (se houver)
2. Confirmar se Atomic Design bilíngue (shared/ EN + app/ PT) está
   implementado conforme Boilerplate.md da wiki.
3. Identificar sinais de migração pra MFE:
   - Module Federation config?
   - single-spa registry?
   - Pastas/branches/comentários indicando split?
   - Webpack/Vite config com remotes/exposes?
4. Confirmar pontos da wiki:
   - Datadog RUM init (procurar @datadog/browser-rum)
   - JWT em localStorage.getItem('session')
   - CKEditor Free
   - IDs kebab-case em componentes
5. Mapear comunicação com BFF:
   - Cliente HTTP usado (axios? fetch? tanstack-query?)
   - Onde mora o tratamento do envelope {metadados, dados, paginacao, mensagem}
   - Snake_case ↔ camelCase: tem converter? deserializer? trata cru?
6. Mapear infraestrutura compartilhada (DS, assets):
   - Onde mora o design system hoje no monolítico (pasta interna?
     package publicado? linkado de fora?)
   - Algum dos 7 MFEs populados já consome 'componentes' ou 'assets'
     por outro caminho? Buscar em package.json por: pacote npm
     interno (escopo @ftd/?), git submodule (.gitmodules), path
     mapping no tsconfig/vite, symlink, monorepo workspace.
   - Se SIM em algum MFE: infra existe em outro lugar — os 4 repos
     placeholder são wishlist, não estado real. Se NÃO em todos:
     hipótese de duplicação se confirma (DS copy-pasted entre repos).

REGRAS:
- Não invente. Se não tá no código, sinaliza.
- Cite arquivo:linha em toda afirmação técnica.
- Sem encher linguiça. Bullets curtos.
- Linguagem direta, par técnico.
- Português BR.

EXECUÇÃO:
1. Primeiro: ls do repo raiz pra ver estrutura macro.
2. Depois: ler package.json + arquivo de config principal (vite/webpack/next).
3. Depois: pasta src/ ou app/+shared/ pra confirmar Atomic Design.
4. Depois: buscar por "datadog", "ModuleFederation", "single-spa",
   "localStorage.getItem('session')", "@datadog/browser-rum".
5. Por fim: gerar um doc curto STACK_REAL.md com o que encontrou,
   ao lado do ONBOARDING_FRONTEND.md (mesma pasta D:\local\ftd\ftd_v3\
   ou no próprio repo do monolito — me pergunte antes).
```

---

## 4. Contexto técnico relevante

- **Modelo**: Opus 4.7 (`claude-opus-4-7`) com janela de **1M tokens**.
  Posso ler repo grande inteiro de uma vez se precisar — usar a vantagem.
- **OS**: Windows 11 Pro. Shell: PowerShell (sintaxe `$null`, `$env:VAR`,
  backtick pra line continuation). Bash disponível via tool Bash.
- **Working dir atual** (sessão anterior): `D:\local\ftd\ftd_v3`.
  Nova sessão provavelmente em `D:\local\ftd\fe-ftd-ionica-monolithic` ou similar.

### Convenções do time já confirmadas

- **Atomic Design bilíngue**:
  - `shared/` (EN): SubAtomic (variáveis de estilo), Atoms (styled),
    Molecules (componentes). Ex.: `Button`, `ButtonStyled`, `Color`.
  - `app/` (PT): Organisms, Templates, Pages.
  - Nomes de arquivo: **PascalCase**.
  - Palavras de negócio: PT. Reservadas/config: EN.
  - Ações em PT no **infinitivo**.
- **Assets**: hierarquia por escopo (componente → shared → organism → app).
- **IDs HTML**: kebab-case, começa por ação. Tem planilha mestre
  `Ids-para-Testes.xlsx`.
- **JSON da API**: snake_case (BFF). Atípico — pode quebrar tipos TS
  ingenuamente gerados.
- **Auth**: JWT em `localStorage.getItem('session')`, decodificado pra
  pegar `guid`/`name`/`lastname`/`email`.
- **Observabilidade**: Datadog RUM, `mask-user-input` por padrão,
  sample 20% prod / 100% dev.
- **Diagramas**: C4 Model (5 SVGs no `.attachments/`), Mermaid em docs.

---

## 5. Perguntas abertas pro time (consolidado da Seção 9)

### Críticas (bloqueiam entender arquitetura)

1. **Existe microfrontend em runtime hoje, ou é roadmap?**
   `Módulos.md` e `Arquitetura-Micro-Services.md` estão zerados. Único
   indício é o nome do arquivo de observabilidade.
2. **Qual a abordagem MFE planejada/atual?** Module Federation? single-spa?
   iframe? Web Components? Build-time composition?
3. **Existe shell/host app?** Qual repo? Como descobre remotes?

### Stack (gaps documentais)

4. Build tool e versão?
5. Framework (Next? SPA com React Router? algo custom)?
6. Gerenciamento de estado?
7. Sistema de estilização (Tailwind? CSS-in-JS? CSS Modules)?
8. Estratégia de testes?
9. Monorepo? Como repos se relacionam (`fe-ftd-ionica-monolithic` vs
   futuros MFEs)?

### Decisões não documentadas (perguntar o "porquê")

10. **Por que JWT em localStorage e não httpOnly cookie?** Trade-off XSS
    conhecido foi aceito?
11. **Por que snake_case no contrato JSON?** Decisão de backend que ficou,
    ou intencional? Tem converter no FE?
12. **Por que CKEditor Free** (com problema de watermark)? Licença premium
    foi avaliada?
13. **Diferença `evh-*` vs `sigr-*`** no naming de tópicos EventHubs?

### Glossário (termos não definidos em texto)

14. **CDC** — Change Data Capture? Comunicação de Dados Centralizada?
    Aparece em vários docs sem definição.
15. **Lemonade** — JSON vs Relacional. Lib? Padrão? Produto interno?
16. **"Ensino Superior"** — por que é o padrão ignorado no Onboarding V2?

### Contradições / ambiguidades

17. Arquitetura é **monolito modular (Spring Modulith)** OU **microsserviços**?
    Wiki menciona ambos em contextos diferentes.
18. Time 1 vs Time 2 — divisão por feature, por módulo, por camada?

---

## 6. Domínios de aprendiz neste projeto

Aplicar o modo "explicar como mentor" do CLAUDE.md global quando o tópico
cair em um destes:

- **Arquitetura de microfrontends em runtime** (Module Federation,
  single-spa, etc) — nunca trabalhei.
- **Domínio LMS / Iônica** — Sala, Turma, Mural, Aula Pronta, BNCC.
- **Ecossistema V1/V2/V3 deste projeto específico** — diferenças,
  coexistência, migração.
- **Spring Modulith e Azure EventHubs** — só pra conversar com backend,
  não pra implementar.

Demais tópicos: tratamento padrão (par técnico sênior).

---

## Arquivos de referência (na máquina)

- **Wiki**: `D:\local\ftd\ftd_v3\`
- **Onboarding gerado**: `D:\local\ftd\ftd_v3\ONBOARDING_FRONTEND.md`
- **CLAUDE.md global do user**: `C:\Users\filip\.claude\CLAUDE.md`
- **Diagramas C4 (binários, não lidos ainda)**: `D:\local\ftd\ftd_v3\.attachments\*.svg`
