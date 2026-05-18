# DRAFT — Seção 8 do ONBOARDING_FRONTEND.md (proposta, NÃO aplicada)

> Passada 6 (Seções 1 + 8), 2026-05-15. Inclui também a correção do título do Apêndice.
>
> **Status: aguardando aprovação. Nada gravado no arquivo final.**

---

## Changelog vs. Seção 8 atual

- **"Hoje"**: cai "pedir acesso ao repositório" (os 8 repos já foram lidos). Entra
  "ler este ONBOARDING inteiro". Mantém os 5 diagramas C4 SVG (nunca lidos).
- **"Esta semana"**: só os docs da wiki que **não** entraram no ONBOARDING (UX, QA,
  Produto, Governança). `Gestão-de-Código/` sai — é stub vazio (ver Seção 9 → Stubs).
- **Tabela "Quando pegar task em…"**: recolunada — aponta primeiro pra **seção deste
  ONBOARDING**, depois pra wiki só onde ela ainda não foi absorvida. + 2 linhas novas
  (Auth, Criar MFE).
- **"Próxima sprint"**: mantida.
- **Apêndice**: título "arquivos lidos nesta passada" → "fontes lidas ao longo das 5
  passadas", com o conteúdo de código das passadas 2-5 explicitado (ver [A1] abaixo).

---

## Conteúdo proposto — Seção 8 (substitui ~linhas 657-685)

## 8. Plano de leitura sugerido

> ⚠️ Atualizado em 2026-05-15 (passada 6). A maior parte do que era "ler da wiki" já
> está sintetizada neste ONBOARDING — o plano aponta primeiro pras seções deste doc, e
> pra wiki só onde ela ainda não foi absorvida.

### Hoje (antes de qualquer coisa)

1. **Ler este `ONBOARDING_FRONTEND.md` inteiro.** É a síntese de 5 passadas de leitura
   de wiki + código (Seções 0-7) mais a lista de perguntas abertas (Seção 9).
2. **Ler os 5 diagramas C4 SVG** referenciados em `🏗️-Arquitetura/Arquitetura-Aceleração-Iônica.md`
   (`.attachments/IonicaAzure - *.svg`) — única fonte visual da arquitetura inteira,
   ainda não lida (binários).

### Esta semana — docs da wiki que NÃO entraram neste ONBOARDING

3. `Aceleração-iônica/👩‍🔬-UX.md` (~8.6 KB) — personas e contexto de design.
4. `Aceleração-iônica/🪲-QA.md` — o que QA testa; deve revelar a ferramenta de E2E, que
   o código não nomeou.
5. `Aceleração-iônica/💻-Produto/` — especificações de feature.
6. `Aceleração-iônica/🗃️-Governança.md` (~4.7 KB) — fluxo de desenvolvimento e PR.

(`Gestão-de-Código/` saiu do plano — é stub vazio, 0-11 B; ver Seção 9 → Stubs documentais.)

### Quando pegar uma task em…

| Área da task | Ler primeiro |
|--------------|--------------|
| Sala / Turma / inscrições | Seção 5 + glossário (Seção 7) + wiki `CDC-Interoperabilidade-Onboarding.md` |
| Eventos / integração entre módulos | Seção 5 → "Eventos / mensageria" + wiki `CDC-Interoperabilidade-interna.md` |
| Auth / token / login | Seção 5 → "autenticação client-side" |
| Performance / observabilidade | Seção 5 → "observabilidade (Datadog RUM)" |
| Componente novo / convenções | Seção 6 + `.github/copilot-instructions.md` do MFE + Design System no Zeroheight |
| Endpoint / contrato REST | Seção 5 → "contrato REST" + wiki `Diretrizes-de-Contrato-Rest-Swagger.md` |
| Banco de Questões / Aulas Prontas | Seção 4 + Seção 9 (pergunta sobre Aulas Prontas) + wiki `CDC-Interoperabilidade-v1.md.md` (stub) |
| Criar um MFE novo | Seção 2 + `copilot-instructions.md` do `portal` (tem o passo-a-passo) |

### Próxima sprint

7. Estudar **Spring Modulith** (`Arquitetura-Modular.md`) e **Hexagonal**
   (`Arquitetura-Hexagonal.md`) o suficiente para conversar com o backend — não para
   escrever Java. Saber que o backend é modular e que a comunicação interna é assíncrona
   via EventHub explica latência eventual e o BFF agregando.
8. Ler as 2-3 últimas Sprint Reviews em `[Iônica-3.0]--Review.md` antes de cada planning.

---

## [A1] Correção do Apêndice (gravar na mesma tacada)

O Apêndice no fim do doc se chama "arquivos lidos **nesta passada**" — eram 5 passadas.
O conteúdo lista só os arquivos de wiki da Passada 1. Proposta: retitular e **explicitar
as fontes de código das passadas 2-5** (sem listar arquivo por arquivo), pra o título
ficar honesto. Fica assim:

> ## Apêndice: fontes lidas ao longo das 5 passadas
>
> **Passada 1 — wiki** (`D:\local\ftd\ftd_v3`):
> - **Âncoras / Arquitetura / Frontend / Apoio:** *(as listas atuais, mantidas)*
> - **Skip:** `.attachments/` (binários) · `🪲-QA` · `👩‍💻-Backend` · `DADOS` ·
>   `🗃️-Governança` · `TechLeads` · `👩‍🔬-UX` · `💻-Produto` · `Gestão-de-Código`.
>
> **Passadas 2-5 — código** (`D:\local\ftd\ftd-frontend`, 14-15/05): `package.json`,
> `package-lock.json` e configs (`rsbuild.config.ts`, `eslint.config.mjs`,
> `azure-pipelines.yaml`, `.env.cloud`/`.env.example`) dos 8 repos populados; arquivos
> `src/` selecionados do `portal` (`Environment.ts`, `services/*`, `configs/*`,
> `bootstrap.tsx`, `App.tsx`); `.github/copilot-instructions.md` do `portal`.

**Nota:** você sugeriu "ao longo das 5 passadas". Segui isso, mas o conteúdo precisou
da linha de código (passadas 2-5) pra o título não mentir — o apêndice só tinha os
arquivos de wiki da passada 1.

---

**O que será gravado numa tacada, após aprovação:**
1. Seção 1 — bloco reescrito (`DRAFT_secao1.md`).
2. Seção 8 — bloco reescrito (~linhas 657-685).
3. Apêndice — título + conteúdo [A1].

Nada gravado até a aprovação. `DRAFT_secao1.md` e `DRAFT_secao8.md` ficam em disco como histórico.
