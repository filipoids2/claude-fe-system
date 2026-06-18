# DRAFT — Recalibração INVENTARIO_REPOS.md (KT Avanade) — 2026-06-18

> Draft **2b** da passada de recalibração do KT Avanade.
> Alvo: `INVENTARIO_REPOS.md` → seções "Frontend — 12 repos" e
> "Libs React compartilhadas — 3 repos".
>
> **Fonte:** KT Avanade, 2026-06-17 — **relato verbal do Filipe**
> (transcrição/gravação pendente; cross-ref quando chegar).
>
> **Status: aguardando aprovação. Nada gravado no doc final.**
>
> ⚠️ **Ordem de aplicação no INVENTARIO:** existe o `DRAFT_INVENTARIO_REPOS_2026-05-19`
> (aprovado, pendente gravação — categoria "Orquestração / Tooling FE" + remanejo do
> `setup-*`). Este 2b **não conflita** com aquele (toca seções diferentes: Frontend e
> Libs React, não "Outros standalone"). Aplicar os dois na mesma sessão de gravação,
> em qualquer ordem.
>
> Convenção: anotação inline no estilo que o doc já usa (`← nota`) + um callout no
> topo da seção. Texto original preservado.

---

## [I2b-1] Seção "Frontend — 12 repos" — anotar gerações de MFE

**Âncora:** seção `### Frontend — 12 repos (já analisados na fase FE)`, a lista de
12 itens.

**Ação A — inserir callout** logo abaixo do título da seção, antes da lista:

> > **Gerações de MFE (KT Avanade, 2026-06-17).** Os MFEs do stack novo não são uma
> > leva homogênea — há um eixo de maturidade confirmado pelo time que construiu a V3.
> > A heterogeneidade de stack (Sass/styled, Redux/Zustand, axios-só) é **falta de
> > padrão por timing**, não decisão técnica. Detalhe e stack-alvo no
> > `ONBOARDING_FRONTEND.md` → Seção 4 (RECALIBRAÇÃO). Resumo:
> > - **Referência** (base oficial pra MFE novo): `agenda`, `atividade`.
> > - **1ª geração** (primeiros criados, stack a migrar — **não usar como base**):
> >   `sala`, `mural`, `bancoquestao`.

**Ação B — anotar os itens da lista** (manter os já existentes, acrescentar o sufixo):

```diff
 - `fe-ftd-ionica-monolithic` (Nx + Webpack, sem MF)
-- `mfe-ftd-ionica_agenda`
-- `mfe-ftd-ionica_atividade`
-- `mfe-ftd-ionica_bancoquestao`
+- `mfe-ftd-ionica_agenda` ⭐ MFE de referência (KT Avanade) — base pra novos MFEs
+- `mfe-ftd-ionica_atividade` ⭐ MFE de referência (KT Avanade) — base pra novos MFEs
+- `mfe-ftd-ionica_bancoquestao` ← 1ª geração — stack a migrar (axios sem TanStack)
 - `mfe-ftd-ionica_login`
-- `mfe-ftd-ionica_mural`
+- `mfe-ftd-ionica_mural` ← 1ª geração — stack a migrar
 - `mfe-ftd-ionica_portal` (host)
-- `mfe-ftd-ionica_sala`
+- `mfe-ftd-ionica_sala` ← 1ª geração — stack a migrar
 - `mfe-ftd-ionica_assets` — placeholder vazio
 - `mfe-ftd-ionica_boilerplate` — placeholder vazio
 - `mfe-ftd-ionica_componentes` — placeholder vazio
 - `mfe-ftd-ionica_home` — placeholder vazio
```

> Nota: `portal` (host) e `login`/`monolithic` (stack velho) ficam **fora** do eixo de
> gerações — o eixo só classifica os 5 remotes de domínio do stack novo. `portal` é
> host, não remote de domínio; `login`/`monolithic` são mundo velho.

---

## [I2b-2] Seção "Libs React compartilhadas — 3 repos" — facade é ponte provisória

**Âncora:** seção `### Libs React compartilhadas — 3 repos`, item
`lib-ftd-ionica-react_ions-facade`.

**Ação — acrescentar nota ao item:**

```diff
-- `lib-ftd-ionica-react_ions-facade` ⭐ Camada 1 (Design System real)
+- `lib-ftd-ionica-react_ions-facade` ⭐ Camada 1 (Design System real) ← **ponte provisória** (KT Avanade): versão interina, lançada enquanto a `lib-ftd-ionica-react_ions` (final, sem sufixo) não funciona. Destino é a `ions` sem `-facade`. Diretriz de uso: **facade-first** (consumir componente da facade antes de criar novo no MFE) — ver `ONBOARDING_FRONTEND.md` §4
```

> Isso fecha o "porquê do sufixo `-facade`" que o inventário registrava sem explicação.
> Quando a `ions` final entrar, **provável surgimento de um repo/pacote
> `lib-ftd-ionica-react_ions`** (sem sufixo) — a confirmar; se aparecer, a contagem de
> "Libs React compartilhadas" sobe de 3 pra 4.

---

## [I2b-3] Resumo por categoria — nota leve (opcional)

**Âncora:** tabela "Resumo por categoria", linha
`| Libs React compartilhadas | lib-*-react_* | 3 | toolkit, ions-facade, shared |`.

**Ação (opcional):** acrescentar à célula "Notas":
`; ions-facade é ponte provisória pra ions final (KT Avanade)`.

> Marcado opcional porque o resumo é índice — o detalhe vive em [I2b-2]. Aplica só se
> quiser o sinal já na tabela-resumo.

---

## Pendente / cross-refs (não faz parte do doc final)

- Depende de **2a** (ONBOARDING §4) estar gravado, pois [I2b-1] e [I2b-2]
  referenciam a RECALIBRAÇÃO da §4. Aplicar 2a antes, ou os dois juntos.
- Próximos da recalibração: **2c** (convencoes/workflow — facade-first + base
  agenda/atividade) e **2d** (§9 — marcar respondidas).
- Quando a transcrição do KT chegar: validar verbatim, trocar fonte "relato verbal"
  por referência ao `DIARIO_REUNIOES`.
