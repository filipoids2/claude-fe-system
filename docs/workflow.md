# docs/workflow.md — workflow operacional FTD V3

> Consultar este arquivo quando: mudança em doc principal,
> varredura de repos, análise de código, ou qualquer task que
> envolva alteração de material em `D:\local\ftd\ftd-frontend\`.

## 1. Padrão epistêmico — FATO / HIPÓTESE / A confirmar

Toda afirmação técnica em docs precisa marcador epistêmico:

- **FATO** — verificado em código ou fonte primária. Inclui
  citação `arquivo:linha`.
- **HIPÓTESE** — inferência razoável baseada em evidência
  parcial. Sinalizar grau de confiança.
- **A confirmar** — pergunta aberta, precisa validação externa.

Exemplo:

FATO — BFFs são NestJS/Node (bff-sala/package.json:14-22).
HIPÓTESE — todos os api-* são configs APIM (apenas
api-sala verificado).
A confirmar — relação entre worker-* e onboarding-v3.

**Regra:** se a afirmação não cabe em nenhuma das 3 categorias,
não vai pro doc. Não inventa, não generaliza sem evidência.

## 2. Drafts antes de gravar

Mudanças em **docs principais** (ONBOARDING_FRONTEND.md,
STACK_BACKEND.md, INVENTARIO_REPOS.md, CLAUDE.md, persona)
passam por draft antes de gravação final.

**Fluxo:**

1. Criar `drafts/DRAFT_<nome>.md` com a proposta
2. Mostrar diff prévio ao usuário
3. Aguardar aprovação explícita (item por item se necessário)
4. Aplicar mudança ao doc final
5. **Manter o draft em disco** — histórico de raciocínio,
   não deletar após gravar

**Pasta `drafts/`:**
- Não vai pro `.gitignore` (drafts são histórico útil pra
  revisão futura)
- Acumula ao longo do tempo — não limpar sem necessidade

**Exceção:** ajustes triviais (1-2 linhas, sem implicação
arquitetural) podem ser gravados diretamente, mas sempre com
diff prévio.

## 3. Snapshots — modo híbrido

Snapshots = cópia do estado completo de doc antes de mudança
arriscada. Vão pra `SNAPSHOTS/<arquivo>_<data>.md`.

**Quando criar (regra híbrida):**

- IA propõe snapshot quando achar arriscado:
  - Reescrita de seção completa
  - Mudanças em 3+ docs simultâneas
  - Refactor estrutural
  - Antes de revisão final
- Usuário decide criar (sem proposta da IA) quando achar
  necessário

**Não criar automaticamente em toda mudança** — overhead alto,
ruído baixo.

**`SNAPSHOTS/`:** está no `.gitignore` (volume + sensibilidade).

## 4. Memory persistente — modo híbrido

Claude CLI mantém memory persistente entre sessões. Comportamento
esperado:

- **Salvar automaticamente:** marcos importantes do projeto
  (passadas, achados arquiteturais, decisões fixadas).
- **Propor antes de salvar:** quando a IA não tem certeza se
  algo merece persistência. Usuário decide.
- **Consultar no início de sessão:** sempre, junto com
  `SESSION_CONTEXT.md`.

**Padrão de nomeação:** memory deve apontar pra arquivo de
fonte primária quando aplicável (ex: "inventário completo em
INVENTARIO_REPOS.md") — não duplicar conteúdo grande na memory.

## 5. Cross-reference entre docs

Documentos crescem e linhas mudam. Cross-ref deve ser
**resistente a mudança**.

**Regras:**

- ✅ **Por símbolo:** "Seção 6 → bloco FATO"
- ✅ **Por achado:** "[D6] camada ms-*"
- ✅ **Por pergunta:** "pergunta 5 da Seção 8"
- ❌ **Por linha absoluta:** "linha 287-300" (vira stale)

**Padrão `[DX]`:** descobertas/achados numeradas
sequencialmente nos docs. Quando uma descoberta de uma passada
é referenciada em outra, usa `[D6]`, `[D7]`, etc — não a
descrição completa.

## 6. Camada vs Passada — vocabulário

Dois termos do vocabulário do projeto, fáceis de confundir:

- **Camada** = lote de repos analisados juntos. Ex: "Camada 1"
  (8 repos: 7 BFFs + ions-facade). "Camada 2" (5 repos:
  ms-signalr, api-sala, v1-v3, ts-sala, ta-sala). Numeração
  cronológica.

- **Passada** = ciclo completo de trabalho documental que
  resulta em mudança nos docs. Ex: "passada 7" (consolidação
  Camada 1). "Passada 8" (Camada 2). Numeração cronológica.

**Relação:** uma passada **geralmente** corresponde a uma
camada (clonagem + análise + gravação), mas nem sempre.
Passada 1 (wiki only) não envolveu clonagem nenhuma.

## 7. Quando este workflow NÃO se aplica

- Task de código direto (escrever componente, fix de bug em
  arquivo) — segue convenções de `docs/convencoes-fe-v3.md`,
  não este workflow.
- Conversa exploratória / brainstorm — pode pular drafts,
  snapshots etc. Workflow é pra **mudança formal em docs**.
- Tarefas urgentes (raríssimas) onde overhead de aprovação
  bloqueia ação — usuário sinaliza explicitamente.

## 8. TASK_LOGS — registro de tarefas concluídas

Cada task que tu pega (bug, feature, refactor) gera um log
em `TASK_LOGS/<data>_<tipo>-<resumo>.md`. Formato em
`TASK_LOGS/TEMPLATE.md`.

**Quando criar:**
- Task tem PBI/issue associada
- Mudança não é trivial (>1 arquivo OU >30 minutos de trabalho)
- Vale ter histórico do que foi feito

**Quando NÃO criar:**
- Ajustes de copy/typo
- Configuração local que não vai pro repo
- Conversa exploratória

**Fluxo:**

1. Ao concluir task, falar pro Claude: "task concluída, gera o log"
2. Claude lê `git diff` + commits + contexto da conversa
3. Preenche o template, mostra pra validação
4. Tu ajusta o que precisar
5. Salva em `TASK_LOGS/`

**Estrutura do nome do arquivo:**
- `2026-05-20_bug-export-pdf.md`
- `2026-05-22_feature-tagueamento-salas.md`
- `2026-06-01_refactor-auth-interceptor.md`

Data primeiro (ordenação cronológica), tipo no meio (bug/feature/
refactor/chore), resumo no fim (kebab-case, 2-4 palavras).

**Conteúdo do log (ver TEMPLATE.md):**
- Resumo curto pra copiar em PBI/PR
- Arquivos alterados com cross-ref (`arquivo:linha`)
- Como testar
- Achados/notas técnicas pra memória pessoal

**Versionado** (não em `.gitignore`) — valor cumulativo, vale
ter histórico mesmo trocando de máquina ou compartilhando com
time.

> Logs antigos viram **portfólio interno** ao longo do tempo:
> ~~"Tu fez X?"~~ → "Tá em TASK_LOGS/aaaa-mm-dd_X.md, posso
> mandar".
