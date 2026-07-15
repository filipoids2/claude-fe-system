# claude-system — Living Software Design Document (SDD) + Knowledge Base

Repositório pessoal de **Filipe** para o projeto **FTD Aceleração Iônica V3**.

Este é um **Living SDD** (Documentação de Arquitetura Viva) + base de conhecimento técnica do frontend, com evolução gradual para visão fim-a-fim (BFFs, integrações e backend).

## Objetivos

- Servir como **referência técnica principal** do projeto
- Acelerar o dia a dia (reconhecimento rápido, decisões e produtividade)
- Facilitar onboarding de novos desenvolvedores frontend
- Registrar decisões técnicas importantes (ADRs)
- Funcionar como ponte entre Frontend e o ecossistema backend (BFFs, EventHub, Onboarding V2)

## Como usar

**Grok (xAI):**
- `use GROK.md` → contexto global
- `use GROK.md + ARCHITECTURE.md` → contexto completo de arquitetura

**Claude:**
- Use o `CLAUDE.md` global

**Documentos principais:**
- `ARCHITECTURE.md` → Visão geral da arquitetura (C4, componentes, fluxos e decisões)
- `ONBOARDING_FRONTEND.md` → Documento detalhado do frontend (fonte rica de verdade)
- `INVENTARIO_REPOS.md` → Inventário completo dos 99 repositórios
- `GROK.md` / `CLAUDE.md` → Diretrizes de interação com cada AI
- `docs/adr/` → Architecture Decision Records
- `TASK_LOGS/` → Registro de PBIs e lições aprendidas
- `SESSION_CONTEXT.md` → Contexto da sessão atual

## Estrutura

- `/` → Arquivos raiz e documentos centrais
- `docs/` → Convenções, padrões, workflows e ADRs
- `backend/` → Integração com BFFs, contratos e microsserviços (em construção)
- `diagrams/` → Diagramas C4 e outros visuais (em construção)
- `TASK_LOGS/` + `drafts/` → Execução e rascunhos
