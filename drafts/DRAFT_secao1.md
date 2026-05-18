# DRAFT — Seção 1 do ONBOARDING_FRONTEND.md (proposta, NÃO aplicada)

> Passada 6 (Seções 1 + 8), 2026-05-15. Seção 1 não tem leitura de código — é overview
> de produto; baseada na wiki + input do dev (V2 e personas confirmados; V1, posicionamento
> e escala ficaram pendentes por opção do dev — não inventados).
>
> **Status: aguardando aprovação. Nada gravado no arquivo final.**

---

## Changelog vs. Seção 1 atual

- Abertura e "Valor" mantidos (estavam bons).
- **Personas** passam de "wiki" para confirmadas pelo dev.
- **"Estado"** reescrito: a versão atual fala em "MVP planejado pro 2º sem/2025" e
  Sprint 28 (out/25) — hoje é mai/2026. Agora reflete o presente e faz cross-ref pra Seção 0.
- **"Versões coexistindo"** (1 linha rasa) vira um **quadro das 3 versões** com status —
  V2 com o detalhe do dev (UP-INSERT, eventos `onboarding-*`, "Ensino Superior" ignorado).
- **Cross-refs** novos: Seção 0 (contexto histórico), Seção 2 (arquitetura), Seção 7 (glossário).
- **Bloco "Em aberto"**: posicionamento estratégico e escala — pendentes, registrados
  como tal em vez de inventados.

---

## Conteúdo proposto (substitui as linhas ~45-55)

## 1. O que é o projeto

A **Iônica** é a plataforma educacional da FTD (`ola.souionica.com.br`). O time
**Aceleração Iônica** está construindo a **versão 3.0** — um LMS que substitui a v1 e
integra com o Onboarding v2. (fonte: `Aceleração-iônica.md` → "Visão do produto")

- **Valor**: LMS que integra ensino físico e digital — conteúdo curricular FTD,
  criação/curadoria de aulas, mural de turma, atividades e banco de questões.
- **Usuários**: professores, gestores e alunos de escolas FTD (confirmado pelo dev;
  bate com a wiki). Credenciais de homolog em `🎒-Onboarding.md`.

### As três versões coexistindo

| Versão | O que é | Status |
|--------|---------|--------|
| **V1** (`souionica.com.br`) | LMS legado — banco de questões, aulas prontas, atividades prontas, classificações (BNCC, ano/série, componente curricular). *Definição da wiki; o papel exato em produção hoje não foi reconfirmado nesta passada.* | Em produção |
| **V2 — Onboarding** | Sistema de provisionamento: sincroniza Escola / Ano / Turma / Usuário / Sala via UP-INSERT e dispara eventos `onboarding-*` que a V3 consome para montar suas entidades. O padrão "Ensino Superior" é ignorado no fluxo. *(relato do dev — não verificado em código nesta passada)* | Ativo, alimentando a V3 |
| **V3 — Aceleração Iônica** | A nova plataforma, foco deste time (ver módulos na Seção 4 e glossário na Seção 7). | Em construção; em produção iterativa e instável — ver Seção 0 |

A interoperabilidade **V1 ↔ V3** fica **pós-MVP** (ver Seção 7 → glossário; wiki
`CDC-%2D-Interoperabilidade-v1.md.md`).

### Estado e contexto

- **Cronograma**: o MVP foi planejado para o 2º semestre de 2025 (Sprint 28 referenciada
  em out/2025). Em mai/2026 a V3 já está em produção iterativa — mas **instável**. O
  histórico de como se chegou aqui (agência externa → war room → time interno assumindo)
  está na **Seção 0**, leitura recomendada antes de propor mudanças.
- **Time**: dois squads ("Time 1" e "Time 2") em paralelo no mesmo produto — composição
  detalhada na Seção 0.
- **Arquitetura**: stack de microfrontends (host + remotes + monolítico legado), Module
  Federation — ver **Seção 2**.

### Em aberto (pendente de confirmação com o time)

- **Posicionamento estratégico**: a Iônica é vendida atrelada ao material didático FTD
  ou é produto standalone? A V3 vai **substituir** a V1 por completo ou as duas coexistem
  em definitivo? Qual o driver de negócio da V3 (modernização técnica, função nova,
  exigência de cliente)? — não confirmado.
- **Escala** (nº de escolas / alunos / professores) — não levantado.
