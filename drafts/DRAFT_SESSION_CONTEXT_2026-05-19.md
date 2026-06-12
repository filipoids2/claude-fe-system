# DRAFT — SESSION_CONTEXT.md (2026-05-19, passada 9)

> Draft de adição ao `SESSION_CONTEXT.md` registrando a passada 9 — descoberta do
> orquestrador de setup local mantido pelo Rafa e setup local da V3 funcionando.
>
> Status: **aprovado em chat 2026-05-19**, pendente gravação no doc final.
>
> Convenção: adição cirúrgica, **não reescrita**. Anexar como nova entrada de
> passada ao final da seção de passadas (depois da passada 8 — Camada 2 do backend).

---

## Mudança 1 — Nova entrada de passada

**Localização:** anexar como nova entrada após a passada 8 na seção de passadas
do `SESSION_CONTEXT.md`.

**Conteúdo a inserir:**

```markdown
## Passada 9 — Setup local oficial (Rafa) — 2026-05-19

> Marco do projeto: V3 inteira rodando localmente pela primeira vez.

### Estado atual

**FATO:** setup local da V3 inteira funcionando. `npm run start:mfes`
(orquestrador do Rafa) sobe portal + 5 remotes em paralelo, navegação entre
MFEs e auth contra Azure AD B2C DEV funcionando. Próximo passo de FE: pegar
tasks reais.

### Descoberta principal

**`setup-ftd-ionica_frontend`** (branch `feature/2026`) — repo orquestrador
mantido pelo Rafa que não estava no inventário. Automatiza clone + install +
envs + start de portal, 5 remotes e 3 libs source. Detalhes:

- Categorizado em `INVENTARIO_REPOS.md` v2.1 como "Orquestração / Tooling FE".
- Documentado em `ONBOARDING_FRONTEND.md` → Seção 2 → "Setup local oficial".
- Branches oficiais: MFEs em `prd`, libs em `develop`, orquestrador em
  `feature/2026`.
- **Libs React (`toolkit`, `ions-facade`, `shared`) são clonadas como source**,
  não consumidas via feed privado. Recalibra achado anterior de mismatch.

### Recalibrações nesta passada

**Mismatch de versão do toolkit (`0.0.13` em sala vs `1.0.3` em portal):**
deixa de ser FATO de bug e passa a ser artefato cosmético de clone. Em runtime
há instância única da lib. Callouts adicionados na Seção 0 e Seção 4 do
ONBOARDING preservando texto original.

### Falsos positivos (caminho na-unha, antes de conhecer o setup do Rafa)

Tentativa inicial de rodar portal+mural sem orquestrador apareceram 3 "bugs"
que **não existem** no caminho oficial. Marcar como aprendizado, não débito:

1. **React/react-dom sem `eager: true` no host (portal)** — `loadShareSync
   failed`. Não reproduz no setup oficial.
2. **CKEditor sem `eager: true` no host** — mesmo `loadShareSync` envolvido.
   Não reproduz.
3. **Hard import síncrono de `MF_Salas/hooks` em `SignalRProvider.tsx:13`** —
   só vira problema se algum remote estiver fora do ar. No setup oficial todos
   sobem juntos. Pode ser pattern revisitável (fallback graceful), mas não é
   bug bloqueante.

**Implicação:** os 3 caminhos "corretos" que pensamos eram **soluções pro
problema errado**. Não levar pro Elias/Well como bug. Se algum dia virar
discussão de robustness pattern, [3] é candidato (hard dep no boot).

### Outros achados operacionais

- **NPM_TOKEN do Azure DevOps** exige formato **base64**, não texto puro. O
  `.npmrc` espera valor já codificado em `_password`. Reconhecível porque
  PAT puro dá `E401` mesmo com `curl -u` funcionando (curl codifica
  automático com `-u`, npm não).
- **Lock file dessincronizado em alguns MFEs** (mural visto, outros prováveis):
  `npm ci` falha com `EUSAGE`, `npm install` resolve. Mexe no lock — não
  commitar reescritas de lock como parte de feature, vira PR isolado de
  manutenção.

### Drafts gravados (2026-05-19)

- `drafts/DRAFT_INVENTARIO_REPOS_2026-05-19.md` — 5 mudanças, soma 99 mantida.
- `drafts/DRAFT_ONBOARDING_FRONTEND_2026-05-19.md` — 3 mudanças (nova subseção
  2 + 2 recalibrações via callout).
- `drafts/DRAFT_SESSION_CONTEXT_2026-05-19.md` — esta passada.

Pendentes pra gravação no doc final: aguardar aprovação.

### Pendentes pra próxima sessão

- Aplicar drafts aprovados aos docs finais (INVENTARIO + ONBOARDING +
  SESSION_CONTEXT).
- Gravar DRAFT CLAUDE.md (decisão C: linha curta no Mapa de docs apontando
  ONBOARDING como referência de setup local).
- Adicionar `mfe/` e `_old-clones/` ao `.gitignore` (mudança não-draft).
- Confirmar com Rafa: co-owners do setup-* + razão da branch `feature/2026`.
- Levantar com Elias/Well, se quiseres: pattern do hard import em
  `SignalRProvider` como discussão de robustness (deploy parcial, graceful
  degradation) — não como bug.
```

---

## Decisões registradas (chat 2026-05-19)

1. **Tom "marco do projeto":** mantido como celebratório-discreto.
2. **Separação dos falsos positivos:** [1][2] como sintomas do caminho errado;
   [3] como pattern questionável que afeta resiliência — sugerido como
   discussão de robustness pattern, não bug.
3. **Mencionar Elias/Well por nome:** aprovado (mais específico que "trio
   entrante").
4. **Pendência CLAUDE.md:** mantida como item 4 da próxima sessão.

---

## Notas de aplicação no doc final

- **Não reescrever** seções anteriores do SESSION_CONTEXT. Apenas anexar a
  passada 9 ao final da seção de passadas.
- **Cross-refs:** os links apontam pra Seção 2 → "Setup local oficial" do
  ONBOARDING e pra v2.1 do INVENTARIO. Garantir que esses dois drafts já
  estejam aplicados (ou serão aplicados na mesma tacada) antes de gravar este.
- **Camada vs Passada:** a passada 9 não tem "Camada" associada (não é lote de
  repos analisados juntos). É descoberta operacional + atualização documental.
  Coerente com a definição em `docs/workflow.md` Seção 6 ("uma passada
  geralmente corresponde a uma camada, mas nem sempre").
