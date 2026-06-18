# DRAFT — Recalibração Seção 4 (KT Avanade) — 2026-06-18

> Draft **2a** da passada de recalibração do KT Avanade.
> Alvo: `ONBOARDING_FRONTEND.md` → Seção 4 (Stack frontend detalhada).
>
> **Fonte:** KT Avanade, 2026-06-17 — **relato verbal do Filipe**
> (transcrição/gravação pendente de download; cross-ref quando chegar).
>
> **Status: aguardando aprovação. Nada gravado no doc final.**
>
> Convenção: texto original **preservado**. As mudanças entram como **callouts /
> subseções novas** e as HIPÓTESES ganham marca de "resolvido" sem serem apagadas —
> mantém o histórico epistêmico (o que se acreditou e quando mudou).
>
> **Padrão epistêmico desta passada:**
> - As **diretrizes e decisões de time** (stack alvo, gerações, facade-first) =
>   **FATO (decisão de time)**, fonte = KT Avanade. É fala de quem construiu — fonte
>   forte — mas é relato verbal; quando a transcrição chegar, confirmo verbatim.
> - Os **detalhes de stack por MFE** já eram FATO (lidos dos `package.json`) — não mudam;
>   o KT só **explica o porquê** deles.
>
> Os demais alvos da recalibração (INVENTARIO, convencoes/workflow, §9) são drafts
> separados (2b, 2c, 2d) — não estão aqui.

---

## [R4-1] Novo callout no topo da Seção 4

**Onde entra:** logo após o parágrafo "Dois mundos, como na Seção 2: **stack novo**...
o velho vem depois.", **antes** da tabela "### FATO — stack novo".

**Texto a inserir:**

> ### 🔄 RECALIBRAÇÃO — gerações de MFE e stack alvo (KT Avanade, 2026-06-17)
>
> O KT de transição com a Avanade (time que construiu a V3) confirmou a leitura que
> esta seção já vinha fazendo por código, e **adicionou um eixo que faltava: maturidade
> por MFE**. Os MFEs do stack novo **não são uma leva homogênea** — há gerações, e a
> heterogeneidade de stack que esta seção documenta é, por palavras do próprio time,
> **falta de padrão por timing de entrega** (muita gente, pressa), não decisão técnica.
> Confirma a **hipótese B da Seção 0** (débito por pressa, não escolha).
>
> **FATO (decisão de time) — gerações de MFE:**
>
> | Geração | MFEs | Stack | Diretriz |
> |---|---|---|---|
> | **1ª geração** (primeiros criados) | `sala`, `mural`, `bancoquestao` | mais depreciada | conviver agora, migrar depois — **não usar como base** |
> | **Referência** | `agenda`, `atividade` | mais próxima do alvo | **base oficial pra criar MFE novo / tirar dúvida de "como se faz aqui"** |
>
> > ⚠️ **Consequência prática imediata:** ao criar um MFE ou copiar um padrão, a
> > referência é **`agenda` / `atividade`** — **não** qualquer MFE. Copiar de `sala`,
> > `mural` ou `bancoquestao` propaga stack que o time já decidiu aposentar.
>
> **FATO (decisão de time) — stack alvo vs. atual:**
>
> | Camada | Hoje (conviver) | **Alvo** (decisão de time) |
> |---|---|---|
> | Estilo | Sass / styled-components / Tailwind — misto, **sem padrão** | **Tailwind + `lib-ftd-ionica-react_ions`** |
> | Estado | Redux / Zustand / misto | **Zustand + TanStack Query** |
> | Componentes | criados dentro do MFE | **consumir da `lib-facade` primeiro** (ver facade-first abaixo) |
>
> O time foi explícito: **nem Sass nem styled-components** é o caminho — ambos são
> transitórios. O destino é Tailwind + a lib de componentes. Mas reconhecem que as
> primeiras entregas vão sair **com o que cada MFE já tem** — a migração é gradual.
>
> **FATO (decisão de time) — `lib-facade` é ponte provisória.** A
> `lib-ftd-ionica-react_ions-facade` é uma versão **interina**, lançada enquanto a
> `lib-ftd-ionica-react_ions` (final, sem sufixo) **ainda não funciona**. O destino é a
> `ions` sem `-facade`. Isso explica o sufixo que o INVENTARIO registrava sem motivo.
>
> **Diretriz — "facade-first" (exercício deixado pelo time):** ao precisar de um
> componente, **primeiro procurar na `lib-facade`**; criar novo no MFE **só se não
> existir lá**. Não é só estética — é o mecanismo pelo qual a migração ao DS acontece,
> via uso. Promovido a princípio de execução de task (ver `convencoes-fe-v3.md`,
> draft 2c).

---

## [R4-2] HIPÓTESE de estado → marcar resolvida

**Onde entra:** bloco `### HIPÓTESES (a validar)`, bullet que começa com
"**Estado heterogêneo confirma a war room (Seção 0).**"

**Ação:** manter o bullet original, **acrescentar** ao fim dele:

> > ✅ **RESOLVIDO (KT Avanade, 2026-06-17):** é herança, não critério. O alvo de time é
> > **Zustand + TanStack Query** pra todos; Redux sai na migração gradual. A coexistência
> > Redux+Zustand nos repos de 1ª geração é débito de timing, conforme [R4-1].

---

## [R4-3] HIPÓTESE do `bancoquestao` (axios) → marcar resolvida

**Onde entra:** bloco `### HIPÓTESES (a validar)`, bullet que começa com
"**`bancoquestao` usa só `axios`, sem TanStack Query**".

**Ação:** manter o bullet original, **acrescentar** ao fim dele:

> > ✅ **RESOLVIDO (KT Avanade, 2026-06-17):** não é decisão consciente. `bancoquestao`
> > é **1ª geração** (junto de `sala` e `mural`) — stack mais antiga, anterior à
> > padronização em TanStack. Entra na fila de migração pro alvo (Zustand + TanStack).
> > **Não replicar** o padrão "axios puro" em MFE novo — usar `agenda`/`atividade` como
> > base ([R4-1]).

---

## [R4-4] HIPÓTESE de Sass → marcar resolvida

**Onde entra:** bloco `### HIPÓTESES (a validar)`, bullet que começa com
"**Sass (`@rsbuild/plugin-sass`) está em `bancoquestao` e `sala` apenas**".

**Ação:** manter o bullet original, **acrescentar** ao fim dele:

> > ✅ **RESOLVIDO (KT Avanade, 2026-06-17):** não há critério — é herança de 1ª geração.
> > O time foi explícito que **nem Sass nem styled-components** é o padrão; o alvo é
> > **Tailwind + `lib-ftd-ionica-react_ions`**. Resposta à dúvida do bullet ("não criar
> > `.scss` em outros MFEs sem entender o porquê"): **não criar `.scss` em MFE novo** —
> > o caminho é Tailwind + DS.

---

## [R4-5] Atualizar linha "Estilização" e "Design System" da tabela do stack novo (opcional, leve)

**Onde entra:** tabela `### FATO — stack novo`, células de **Estilização** e
**Design System**.

**Ação:** **não** mexer nos FATOs (versões lidas do `package.json` seguem corretas).
Só acrescentar um ponteiro no fim de cada célula, pra ligar o "o que é" ao "pra onde vai":

- Estilização → ao fim da célula: `— alvo de time é Tailwind + lib-ions; Sass/styled são transitórios (ver RECALIBRAÇÃO no topo da seção)`.
- Design System → ao fim da célula: `(o sufixo -facade é provisório enquanto a ions final não funciona — ver RECALIBRAÇÃO)`.

---

## Pendente desta passada (não faz parte do doc final)

Itens que **dependem** desta §4 e entram nos próximos drafts da mesma recalibração:

- **2b — INVENTARIO:** `agenda`/`atividade` = MFE de referência; `sala`/`mural`/`banco`
  = 1ª geração a migrar; `-facade` = ponte provisória.
- **2c — convencoes/workflow:** diretriz **facade-first** + "base novo MFE em
  agenda/atividade".
- **2d — §9:** marcar respondidas q5 (axios), q6 (Sass) e a de estado, no padrão
  strikethrough que a seção já usa.
- **Cross-ref futuro:** quando a transcrição/gravação do KT chegar, validar verbatim e
  trocar a fonte de "relato verbal do Filipe" por referência ao `DIARIO_REUNIOES`.
