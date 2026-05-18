# docs/padroes-de-task.md — como abordar uma task

> Consultar **sempre** que pegar task nova, antes de codar.
> Foco em decompor input vago e padronizar abordagem por tipo.
>
> Convenções de código → `convencoes-fe-v3.md`.
> Workflow de docs → `workflow.md`.
> Template de log final → `TASK_LOGS/TEMPLATE.md`.

## 1. Princípio central

Tasks chegam vagas. PBI tipo "botão não funciona" não diz qual
MFE, qual comportamento, se é regressão. **Antes de codar,
esclarecer.** Custa 5 minutos, economiza horas de retrabalho.

Fluxo geral:

1. **Esclarecer** a task vaga (perguntas a fazer)
2. **Classificar** o tipo (bug, feature, refactor, MFE vazio)
3. **Aplicar padrão** do tipo correspondente (Seção 3)
4. **Executar** com convenções (`convencoes-fe-v3.md`)
5. **Registrar** em TASK_LOGS ao concluir (`TASK_LOGS/TEMPLATE.md`)

## 2. Decompondo task vaga — prompt utilitário

Ao receber task vaga (PBI, Slack, conversa), antes de codar usar
este prompt no Claude CLI:

Recebi a task abaixo. Antes de começar, me ajude a esclarecer:

1- Qual MFE/tela exato é afetado?
2- Qual o tipo da task (bug / feature / refactor / MFE vazio)?
3- Qual o comportamento esperado vs atual?
4- Há acceptance criteria explícitos?
5- Toca auth, BFF Node, ou alguma infraestrutura compartilhada?
6- Há dependência com outras tasks/pessoas do time?
7- Posso testar localmente do início ao fim?

[COLA A TASK ORIGINAL AQUI]

A IA responde com perguntas extras específicas do contexto. Tu
decide quais levar pro time vs quais responder sozinho.

## 3. Padrões por tipo de task

### 3.1. Bug em código existente

**Antes de codar — esclarecer:**
- Em qual MFE/tela exato?
- É regressão (funcionava antes) ou bug novo?
- Reproduzível localmente? Em qual ambiente apareceu (dev/qa/prd)?
- Há screenshot, log, stack trace?
- Afeta auth/BFF/contrato externo?

**Fluxo:**
1. Reproduzir localmente
2. Hipotetizar causa (ranqueada: mais provável → menos provável)
3. Confirmar causa antes de aplicar fix
4. Aplicar fix mínimo (não refactor disfarçado)
5. Validar — caso original + 2-3 casos similares
6. Gerar TASK_LOG

### 3.2. Feature nova

**Antes de codar — esclarecer:**
- Em qual MFE deve viver?
- Há design no Figma? Link?
- Toca BFF (Node)? Tem endpoint já documentado?
- Afeta auth/permissões?
- Tem acceptance criteria explícitos?
- É feature isolada ou parte de épico maior?

**Fluxo:**
1. Mapear arquivos a criar/alterar (proposta antes de codar)
2. Validar mapeamento com humano
3. Implementar serial (1 sub-task por vez)
4. Testar cada sub-task antes da próxima
5. Validar fluxo end-to-end
6. Gerar TASK_LOG

### 3.3. Refactor / melhoria

**Antes de codar — esclarecer:**
- Qual o objetivo (performance, legibilidade, manutenibilidade)?
- Há comportamento que pode mudar, ou é puramente interno?
- Há testes existentes pra garantir não-regressão?
- Tem timebox (até quando)?

**Fluxo:**
1. Snapshot do estado atual (`SNAPSHOTS/` se mudança grande)
2. Validar comportamento atual (testes ou manual)
3. Refatorar em passos pequenos
4. Validar não-regressão após cada passo
5. Comparar antes/depois (objetivo atingido?)
6. Gerar TASK_LOG

### 3.4. MFE vazio (preencher placeholder)

**Mais delicado — envolve decisões arquiteturais. NÃO decidir sozinho.**

**Antes de codar — esclarecer com TL/Arquiteto:**
- Qual o propósito do MFE? (Ex: `mfe-home` vai ser dashboard?
  landing?)
- Vai consumir quais BFFs? Vai expor algo pro portal?
- Stack: usar mesmo padrão do portal (Rsbuild + MF) ou outro?
- Quais bibliotecas internas usar
  (`@ftd/lib-react-toolkit`, `@ftd/lib-react-ions-facade`)?
- Quem é dono/owner do MFE depois?

**Fluxo:**
1. Discussão arquitetural com time (não pular)
2. Usar `mfe-ftd-ionica_portal` como referência viva
   (boilerplate oficial está vazio)
3. Copiar estrutura, adaptar config (`rsbuild.config.ts`,
   `package.json`, MF exposes)
4. Implementar mínimo viável (1 página, 1 rota)
5. Validar integração com portal (host consegue carregar?)
6. Iterar features
7. Gerar TASK_LOG **detalhado** (esse é histórico arquitetural)

## 4. Quando pedir ajuda do time

Sinalizar bloqueio **cedo**, não tarde. Pedir ajuda em:

- Decisões arquiteturais (qualquer mudança transversal)
- Cross-stack além de BFF Node (Java, microsserviços, infra)
- Ambiguidade entre wiki e código (sempre — wiki precisa
  ser atualizada)
- Bug que não reproduz localmente
- Feature que toca infraestrutura compartilhada
  (`@ftd/lib-react-*`, MF config, interceptors)

**Padrão de mensagem pro time:**
- Qual a task
- O que tu tentou
- Hipóteses descartadas (com razão)
- Pergunta específica

## 5. Anti-patterns transversais

Vale pra qualquer tipo de task:

- ❌ Codar sem entender a task — esclarecer primeiro
- ❌ Aplicar fix sem confirmar causa raiz (especialmente bug)
- ❌ Decidir mudança arquitetural sozinho
- ❌ Refatorar disfarçado de bugfix
- ❌ Pular validação local "porque é mudança pequena"
- ❌ Mexer em interceptor/MF config sem alinhar
- ❌ Esquecer de gerar TASK_LOG ao concluir

## 6. Cross-references

- Convenções de código → `convencoes-fe-v3.md`
- Workflow de docs → `workflow.md`
- Template de log final → `TASK_LOGS/TEMPLATE.md`
- Persona ativa → `PERSONAS/filipe-dev-fe.md`
- Mapa do projeto → `ONBOARDING_FRONTEND.md`
