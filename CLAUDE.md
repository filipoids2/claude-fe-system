# CLAUDE.md — FTD Aceleração Iônica V3

## 1. Contexto

Plataforma educacional FTD em produção, V3 em construção. Time interno
absorvendo a V3 da agência externa. Material técnico denso já mapeado
nesta pasta — consultar antes de qualquer task.

## 2. Mapa de documentos

| Arquivo | Quando consultar |
|---|---|
| `SESSION_CONTEXT.md` | **Sempre, ao iniciar sessão.** |
| `ONBOARDING_FRONTEND.md` | Tasks de FE, arquitetura MFE, auth, convenções. |
| `STACK_BACKEND.md` | Tasks que tocam BFFs ou contratos. |
| `INVENTARIO_REPOS.md` | Saber o que é cada repo (99 catalogados). |
| `docs/workflow.md` | Workflow operacional (drafts, aprovação, memory). |
| `docs/convencoes-fe-v3.md` | Escrever código FE — padrões práticos. |
| `docs/padroes-de-task.md` | Decompor task vaga, abordar bug/feature/refactor. |

## 3. Regras críticas

- **PRIVATE/** contém material sensível (security findings, etc).
  Nunca copiar pra respostas públicas, nunca incluir em resumos
  compartilháveis.

- **Drafts antes de gravar.** Mudanças em docs principais
  (ONBOARDING, STACK_BACKEND, INVENTARIO) passam por `drafts/DRAFT_*.md`
  primeiro, com aprovação humana antes de gravação final.

- **Padrão epistêmico FATO / HIPÓTESE / A confirmar** em toda
  afirmação técnica nos docs. Não inventa, não generaliza sem
  evidência.

- **Convenção de branches:** legados FTD usam `develop-ftd/testing-ftd/
  staging/main`; V3 fugiu disso (usa `prd/qa/uat/develop`). Atenção
  ao mexer em pipeline.

- **Cross-stack só com Node.js.** Tasks que tocam camadas que o dev
  não consegue rodar/testar localmente (API Java, microsserviços
  não-Node, workers, infra) devem ser sinalizadas, não executadas.
  Decisões arquiteturais transversais passam pelo time (TL/Arquiteto).

## 4. Como começar sessão

1. Ler `SESSION_CONTEXT.md` pra estado atual.
2. Declarar persona ativa (ver seção 5). Sem declaração, default é
   master global.
3. Identificar tipo da task — consultar docs da seção 2 conforme
   `docs/padroes-de-task.md`.
4. Para mudanças não-triviais: propor plano curto antes de executar.

## 5. Persona ativa

Default deste projeto: `PERSONAS/filipe-dev-fe.md`. Declarar no
início da sessão se diferente.

Outras pessoas do time podem adicionar suas personas em `PERSONAS/`.
Ver `PERSONAS/README.md`.
