# DRAFT — SETUP_LOCAL_FE.md (2026-06-17)

> Draft de **doc novo** dedicado ao setup local do frontend V3.
> Consolida o passo-a-passo oficial (orquestrador `setup-ftd-ionica_frontend`)
> com os detalhes operacionais confirmados na máquina do Filipe.
>
> **Status: aguardando aprovação. Nada gravado no doc final.**
>
> Substitui/absorve a subseção "Setup local oficial" que estava no
> `DRAFT_ONBOARDING_FRONTEND_2026-05-19` (Seção 2). Após aplicar este doc,
> aquela subseção do ONBOARDING deve virar **só um ponteiro** pra cá
> (ver "Reconciliação pendente" no fim).
>
> Marcadores: **FATO** = verificado na máquina / no repo. **PROVISÓRIO** =
> estado atual que o time espera mudar. **A confirmar** = não verificado.

---

# Setup local — Frontend V3

> Objetivo: subir a V3 inteira na máquina pra desenvolver. Caminho oficial via
> orquestrador do Rafa (`setup-ftd-ionica_frontend`). **Não** clonar/configurar
> MFE por MFE na unha — o orquestrador resolve envs, `.npmrc` e ordem de subida.

## 1. Pré-requisitos

| Item | Detalhe | Por quê |
|---|---|---|
| **Node 22** | Obrigatório. Node 24 dá `EBADENGINE`. | `engines` dos repos trava em `>=22.8.0 <23` |
| **SSH no Azure DevOps** | Clones via `git@ssh.dev.azure.com` | Repos privados do tenant FTD |
| **NPM_TOKEN** | PAT Azure DevOps, escopo `Packaging (Read)`, **codificado em base64** no `_password` do `.npmrc` | Tenant FTD rejeita PAT em texto puro (E401). O orquestrador já injeta o `.npmrc` — só precisa do token disponível |

> ⚠️ **Credenciais:** nunca commitar o PAT. Valor real fica fora do repo
> (variável de ambiente / `.npmrc` gitignored). Aqui usamos placeholder:
> `NPM_TOKEN=<seu-pat-base64>`.

## 2. Estrutura de pastas esperada

```
<raiz-local>/                  ex: D:\local\ftd\ftd-frontend\
└── mfe/
    ├── setup-ftd-ionica_frontend/        ← orquestrador  (branch feature/2026)
    ├── lib-ftd-ionica-react_ions-facade/ ← lib source    (branch develop)
    ├── lib-ftd-ionica-react_shared/      ← lib source    (branch develop)
    ├── lib-ftd-ionica-react_toolkit/     ← lib source    (branch develop)
    ├── mfe-ftd-ionica_agenda/            ← remote         (branch prd)
    ├── mfe-ftd-ionica_atividade/         ← remote         (branch prd)
    ├── mfe-ftd-ionica_bancoquestao/      ← remote         (branch prd)
    ├── mfe-ftd-ionica_mural/             ← remote         (branch prd)
    ├── mfe-ftd-ionica_portal/            ← HOST           (branch prd)
    └── mfe-ftd-ionica_sala/              ← remote         (branch prd)
```

**FATO — branches por tipo de repo:**

| Tipo | Branch de clone |
|---|---|
| Orquestrador (`setup-*`) | `feature/2026` |
| Libs React (`lib-*`) | `develop` |
| MFEs (`mfe-*`) | **`prd`** — não `main`, não `develop` |

> As 3 libs React são clonadas como **source**, não consumidas via feed privado.
> É por isso que eventual mismatch de versão de toolkit entre repos é cosmético
> (clones de momentos diferentes) e não bug — em runtime há instância única.

## 3. Passo 1 — Clonar os repos

Dentro da pasta `mfe/`:

```bash
git clone -b feature/2026 git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/setup-ftd-ionica_frontend ./mfe/setup-ftd-ionica_frontend
git clone -b develop git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/lib-ftd-ionica-react_ions-facade ./mfe/lib-ftd-ionica-react_ions-facade
git clone -b develop git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/lib-ftd-ionica-react_shared ./mfe/lib-ftd-ionica-react_shared
git clone -b develop git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/lib-ftd-ionica-react_toolkit ./mfe/lib-ftd-ionica-react_toolkit
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_agenda ./mfe/mfe-ftd-ionica_agenda
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_atividade ./mfe/mfe-ftd-ionica_atividade
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_bancoquestao ./mfe/mfe-ftd-ionica_bancoquestao
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_mural ./mfe/mfe-ftd-ionica_mural
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_portal ./mfe/mfe-ftd-ionica_portal
git clone -b prd git@ssh.dev.azure.com:v3/ftd-educacao/FTD%20-%20Acelera%C3%A7%C3%A3o%20I%C3%B4nica/mfe-ftd-ionica_sala ./mfe/mfe-ftd-ionica_sala
```

> O `%20` / `%C3%A7` / `%C3%B4` no path é URL-encode de `FTD - Aceleração Iônica`
> (nome do projeto no Azure DevOps com espaços e acentos). Manter como está.

## 4. Passo 2 — Instalar

Entrar na pasta do **setup** e rodar:

```bash
cd mfe/setup-ftd-ionica_frontend
npm run install:mfes
```

O `install:mfes` instala deps de todos os MFEs **e** configura as `.env` de cada
um (já apontando pros backends de **DEV**) + o `.npmrc`. Não precisa criar env na
mão.

## 5. Passo 3 — Subir

```bash
npm run start:mfes
```

Abre um menu pra escolher **qual MFE** levantar — ou levantar **todos**.
Na prática, subir todos é o mais fácil (ver dependências abaixo).

## 6. Portas

| O que | Porta | Observação |
|---|---|---|
| **Portal (HOST)** | **4200 — obrigatória** | Ver "Por quê" abaixo |
| Remotes (agenda, atividade, ...) | 3001, 3002, 3003... | O portal "gruda" todos em runtime |

**FATO — por que o portal TEM que ser 4200 (Azure AD B2C):**
No fluxo OAuth do Azure AD B2C, a app manda um `redirect_uri` e o B2C só aceita
se ele **bater exatamente** com uma URI cadastrada no *app registration*. Pra
localhost, a única URI liberada é `http://localhost:4200`. Subir o portal em
outra porta → o B2C recusa o redirect no login (erro tipo `redirect_uri
mismatch`) e tu não loga. Por isso o host é fixo em 4200; os remotes podem variar
porque não fazem o redirect de auth — quem faz é o host.

## 7. Dependência entre MFEs

Pra rodar o front tu precisa do **Portal** (host) + os módulos que a tela usa.
Alguns módulos dependem de outros (ex: **Atividade** e **Banco de Questões**),
então subir só um costuma quebrar. **Recomendado: subir todos.**

**Por que (Module Federation):** o Portal é o *host* e carrega os remotes em
runtime via `remoteEntry`. Se o host referencia um remote que não está no ar, o
load quebra — o grafo de dependências entre MFEs **não tem fallback graceful**.
É o mesmo mecanismo do import síncrono de `MF_Salas/hooks` em
`SignalRProvider.tsx` e do mesh `Atividades→Banco` / `Sala→Mural+Atividades`
(ver ONBOARDING §4). "Rodar todos" não é preguiça — é consequência dessa
arquitetura.

## 8. Ambientes e processo de subida

**FATO — ambientes:** `develop` / `qa` / `uat` / `prd`.

**PROVISÓRIO — processo de branch atual:** pra subir uma PBI num ambiente, cria-se
uma branch derivada por ambiente:

| Ambiente | Branch |
|---|---|
| prd | `feature/pbi-xxxx` |
| dev | `feature/pbi-xxxx-dev` |
| qa | `feature/pbi-xxxx-qa` |
| uat | `feature/pbi-xxxx-uat` |

> ⚠️ É chato e duplica branch por ambiente. **Direção esperada (hipótese do time,
> não decidida):** criar uma branch única a partir de `prd` e promovê-la entre os
> ambientes, em vez de uma branch por ambiente. **A confirmar** com o time antes
> de adotar como padrão.

## 9. Massas de teste (DEV)

> ⚠️ **Escopo: somente DEV.** Senha trivial e compartilhada do time pra ambiente
> de desenvolvimento. **Não** é padrão de senha de outras massas e **não** vale
> pra qa/uat/prd. Não reusar fora de DEV.

| Usuário | Papel |
|---|---|
| `modernizacao.aluno1` | Aluno |
| `modernizacao.aluno0` | Aluno |
| `modernizacao.professor.m` | Professor |
| `modernizacao.professor2` | Professor |
| `modernizacao.professor3` | Professor |
| `modernizacao.admin1` | Admin |

Senha (todos): `123456a`

## 10. Cross-references

- `ONBOARDING_FRONTEND.md` §2 → ponteiro pra este doc
- `ONBOARDING_FRONTEND.md` §4 → arquitetura MF / mesh entre MFEs (contexto da §7 daqui)
- `ONBOARDING_FRONTEND.md` §5 → auth Azure AD B2C (contexto da §6 daqui)
- `INVENTARIO_REPOS.md` → categoria "Orquestração / Tooling FE" (`setup-*`)
- `TASK_LOGS/` → fluxo de task usa este ambiente local

---

## Reconciliação pendente (não faz parte do doc final)

Ao aplicar este doc, ajustar o `DRAFT_ONBOARDING_FRONTEND_2026-05-19`:
a subseção "Setup local oficial" da Seção 2 deve **encolher** pra um ponteiro
("Setup local detalhado em `SETUP_LOCAL_FE.md`") em vez de duplicar comandos e
estrutura. Senão os dois docs divergem com o tempo.
