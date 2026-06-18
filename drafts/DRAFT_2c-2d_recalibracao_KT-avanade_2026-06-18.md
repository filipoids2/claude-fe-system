# DRAFT — Recalibração KT Avanade: 2c + 2d — 2026-06-18

> Drafts **2c** e **2d** (finais) da passada de recalibração do KT Avanade.
> Fechados juntos por serem a mesma passada e a aplicação ser em lote.
>
> **Fonte:** KT Avanade, 2026-06-17 — **relato verbal do Filipe**
> (transcrição/gravação pendente; cross-ref quando chegar).
>
> **Status: aguardando aprovação. Nada gravado nos docs finais.**
>
> Alvos:
> - **2c** → `docs/convencoes-fe-v3.md` (facade-first + stack alvo) e
>   `docs/padroes-de-task.md` (referência de MFE novo).
> - **2d** → `ONBOARDING_FRONTEND.md` Seção 9 (fechar porquê/alvo de estado e estilo).
>
> Depende de **2a** (§4 RECALIBRAÇÃO) gravado — os cross-refs apontam pra lá.

---

# 2c — Convenções de código

## [C-1] `convencoes-fe-v3.md` §4 (Estilização) — ligar ao alvo

**Âncora:** seção `## 4. Estilização — Tailwind 3.4`, logo após a frase
"Tailwind utility-first. Evitar CSS custom solto."

**Ação — inserir callout:**

> > **Alvo de time (KT Avanade, 2026-06-17):** o padrão de estilo é **Tailwind +
> > `lib-ftd-ionica-react_ions`** (DS). **Nem Sass nem styled-components** são o
> > caminho — ambos são transitórios, herança das 1ª-geração (`sala`, `mural`,
> > `bancoquestao`). **Em MFE novo: não criar `.scss` nem `styled` — usar Tailwind +
> > componentes do DS.** Conviver com o que já existe nos MFEs antigos; migrar
> > gradualmente. Ver `ONBOARDING_FRONTEND.md` §4 (RECALIBRAÇÃO).

## [C-2] `convencoes-fe-v3.md` — nova seção "Componentes (facade-first)"

**Âncora:** inserir como **nova seção** após a `## 5. Auth — Azure AD B2C`
(vira `## 6`; renumerar as seguintes se houver).

**Conteúdo a inserir:**

```markdown
## 6. Componentes — facade-first (KT Avanade)

**Diretriz do time:** ao precisar de um componente, **primeiro procurar na
`lib-ftd-ionica-react_ions-facade`**; criar componente novo no MFE **só se não
existir lá**.

**Por quê:** não é só padronização visual — é o mecanismo pelo qual a migração ao
Design System acontece, via uso. Cada componente consumido da facade é um a menos
pra migrar depois.

✅ **Correto:**
\`\`\`tsx
import { BarraDePesquisa } from '@ftd/lib-react-ions-facade';
\`\`\`

❌ **Errado (recriar o que já existe na facade):**
\`\`\`tsx
// componente próprio de busca dentro do MFE, duplicando o da facade
const MinhaBusca = () => { /* ... */ };
\`\`\`

**Atenção — facade é provisória:** a `lib-ftd-ionica-react_ions-facade` é versão
interina enquanto a `lib-ftd-ionica-react_ions` (final) não funciona. O destino é a
`ions` sem sufixo. Consumir via facade hoje **não muda** a diretriz amanhã — a troca
de import será mecânica quando a final entrar. Ver INVENTARIO → Libs React.

> O que a facade exporta (FATO, 2026-05-17): re-export de ~19 componentes do
> `@ionica/ions-ds` + wrappers (`IonsProvider`) + próprios sem equivalente no DS
> (`BarraDePesquisa`, `Dropdown`, `Loading`, `Toast`). Hooks existem mas não são
> re-exportados. Detalhe em `ONBOARDING_FRONTEND.md` §4 → DS & libs.
```

## [PT-1] `padroes-de-task.md` §3.4 (MFE vazio) — corrigir referência

**Âncora:** seção `### 3.4. MFE vazio (preencher placeholder)`, no **Fluxo**, item 2:
"Usar `mfe-ftd-ionica_portal` como referência viva (boilerplate oficial está vazio)".

**Problema:** `portal` é o **host**, não um remote de domínio. Pra criar um **MFE de
feature** (remote), a base certa é um remote de referência, não o host.

**Ação — substituir o item 2 do fluxo:**

```diff
-2. Usar `mfe-ftd-ionica_portal` como referência viva
-   (boilerplate oficial está vazio)
+2. Usar **`mfe-ftd-ionica_agenda` ou `mfe-ftd-ionica_atividade`** como referência
+   viva — são os **MFEs de referência** definidos pelo time (KT Avanade), stack mais
+   próxima do alvo (boilerplate oficial está vazio). **Não** copiar de `sala`/`mural`/
+   `bancoquestao` (1ª geração, stack a migrar). Usar `portal` como referência **só**
+   se o que se cria é host-level, não um remote de domínio.
```

**Ação complementar — no bloco "esclarecer com TL/Arquiteto" da mesma §3.4**, o item
"Quais bibliotecas internas usar (`@ftd/lib-react-toolkit`, `@ftd/lib-react-ions-facade`)"
já existe e fica — só acrescentar ao fim dele:
`— lembrar do facade-first (convencoes-fe-v3.md §6)`.

---

# 2d — ONBOARDING_FRONTEND.md Seção 9

> A §9 já marca q4 (estado) e q5 (estilização) como "Respondido (passada 3)" — mas só
> no nível **"o que é"**. O KT fecha o **"por quê + alvo"**. Acrescento às respostas
> existentes, sem apagar.

## [S9-1] Pergunta 4 (stack de estado)

**Âncora:** bloco `### 🚨 Críticas pro frontend`, item **4** (começa com
"~~**Qual a stack de estado**~~ **Respondido (passada 3):** heterogênea...").

**Ação — acrescentar ao fim do item:**

> *Porquê + alvo fechados (KT Avanade, 2026-06-17):* a heterogeneidade é **herança de
> timing**, não critério. **Alvo de time: Zustand + TanStack Query** pra todos; Redux
> sai na migração gradual. Ver §4 → RECALIBRAÇÃO.

## [S9-2] Pergunta 5 (estratégia de estilização)

**Âncora:** mesmo bloco, item **5** ("~~**Qual a estratégia de estilização**~~
**Respondido (passada 3):** Tailwind... Sass em 2 repos...").

**Ação — acrescentar ao fim do item:**

> *Porquê + alvo fechados (KT Avanade, 2026-06-17):* não há critério pro Sass — é
> herança de 1ª geração. **Nem Sass nem styled-components** é padrão; **alvo: Tailwind
> + `lib-ftd-ionica-react_ions`**. Em MFE novo, não criar `.scss`/`styled`. Ver §4 →
> RECALIBRAÇÃO e `convencoes-fe-v3.md` §4 e §6.

> Nota: a pergunta **6 (testes)** **não** é tocada por esta recalibração. O KT2 falou
> de cobertura de **backend** (~90%) — isso entrou no §4 como nota de contraste no 2a,
> não muda o status dos testes de **front** (que seguem zero). q6 fica como está.

---

## Fim da passada de recalibração KT Avanade

Drafts da passada: **2a** (§4) · **2b** (INVENTARIO) · **2c** (convencoes +
padroes-de-task) · **2d** (§9). Todos com âncora, prontos pra aplicação em lote.

**Cross-ref futuro:** quando a transcrição/gravação do KT chegar, validar verbatim e
trocar "relato verbal do Filipe" por referência ao `DIARIO_REUNIOES/2026-06-17_kt-avanade.md`.
