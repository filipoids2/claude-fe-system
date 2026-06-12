# Regra de permissão no botão "Excluir sala" (EditarSalas)

> **Tipo:** feature
> **PBI:** #323909
> **Branch:** feature/pbi-323909
> **Status:** concluída
> **Início:** 2026-06-12
> **Conclusão:** 2026-06-12

---

## 1. Resumo curto (pra copiar em PBI/PR/daily)

Botão "Excluir sala" da tela de edição (MFE Sala) agora só fica habilitado se o usuário for o criador da sala (`flg_usuario_criador=true` no payload do endpoint de detalhe da sala) **OU** se for SchoolAdmin (role R03). Para demais usuários, o botão aparece visível mas em estado disabled. Cobre desktop, tablet e mobile.

## 2. Problema / Objetivo

Qualquer usuário com acesso à tela de edição da sala (`/salas/{guid}/editar`) conseguia clicar em "Excluir sala", independente de ter criado a sala ou de ter papel administrativo. Regra de negócio: exclusão deve ficar restrita ao criador da sala ou ao Coordenador (R03 / SchoolAdmin) do colégio.

## 3. Solução aplicada

- Estendido o `BottomBar` (molecule compartilhada) com nova prop opcional `ButtonLeftDisabled?: boolean` — backward-compatible com todos os consumers existentes. Aplicada nos dois branches de renderização (mobile/tablet e desktop).
- Tipada a resposta do `EditarSala.getInfo` com a flag `flg_usuario_criador: boolean` (`ISalaGetInfoResponse`).
- `EditarSalas.tsx`: state local `flgUsuarioCriador` populado em `getSala()`; leitura síncrona de `sessionStorage.getItem('@ionica-v3/usuario_tipo_acesso') === 'SchoolAdmin'` para detectar role administrativa; `podeExcluir = flgUsuarioCriador || isSchoolAdmin` passado como `ButtonLeftDisabled={!podeExcluir}` ao BottomBar.
- Cleanup: removido cast manual no `getSala` (tipagem agora flui via genérico do `bffSala.get<...>`); removida interface local `IInitialBody` que ficou órfã.

## 4. Arquivos alterados

| Arquivo:linha | Mudança |
|---|---|
| `mfe/mfe-ftd-ionica_sala/src/shared/molecules/BottomBar/bottomBar.tsx:18,29,58,61,78,81` | Nova prop `ButtonLeftDisabled?: boolean`, aplicada nos dois branches (mobile + desktop) com `tertiaryDisabled` + `disabled` |
| `mfe/mfe-ftd-ionica_sala/src/services/EditarSalas/EditarSalas.tsx:20-28` | Nova interface `ISalaGetInfoResponse`; `bffSala.get<ISalaGetInfoResponse>(...)` |
| `mfe/mfe-ftd-ionica_sala/src/components/pages/EditarSalas/EditarSalas.tsx:66-69,98-105,431` | State `flgUsuarioCriador`, `isSchoolAdmin`, `podeExcluir`; `BottomBar` recebe `ButtonLeftDisabled={!podeExcluir}` |

## 5. Como testar

1. Logar como **Professor que NÃO criou a sala** → entrar em `/salas/{guid}/editar` → botão "Excluir sala" aparece em cinza, sem clique.
2. Logar como **Professor criador da sala** → mesma tela → botão "Excluir sala" habilitado, clique abre modal de confirmação normalmente.
3. Logar como **Coordenador / SchoolAdmin (R03)** → qualquer sala da escola → botão habilitado independente de ter criado ou não.
4. Testar em **mobile**, **tablet** e **desktop** — todos os layouts respeitam a regra (o BottomBar tem branches separados).

## 6. Notas técnicas / Achados

- **Canal portal → MFE de info do usuário logado**: o portal (`mfe-ftd-ionica_portal`) chama `GET /sala/v1/whoami` e grava o **nome traduzido** da role em `sessionStorage` com chave `@ionica-v3/usuario_tipo_acesso`. MFEs (incluindo Sala) só consomem. Em prod, R03 cai como **`'SchoolAdmin'`** (em EN — divergente do `whoAmIService.ts:60-66` que mapeia pra PT). Hipótese: existe um `getUserRoleName` no `App.tsx:34` do portal diferente do `getRoleName` do service. Vale conferir antes de assumir outros valores.
- **`BottomBar` tem branches duplicados** (mobile/tablet vs desktop) com formatação levemente diferente. Cuidado ao usar `Edit replace_all` em molecules responsivas — pode pegar só um branch. Já registrado em memória pessoal.
- **`useEffect` import órfão pré-existente** em `bottomBar.tsx:1` (vem do commit `2d89f58`). Não tocado nessa task — candidato a housekeeping isolado.
- **`ListarSalasProfessor.tsx` com reformatação Prettier stale** no working tree (631+/456−) ao iniciar essa task. Não tocado — fora do escopo. Decisão pendente: reverter ou commit isolado de style.
- **Sugestão de refactor futuro**: se a leitura de role do sessionStorage se repetir em ≥2 telas do MFE, vale extrair um `useUserRole()` em `shared/hooks/` retornando `{ isSchoolAdmin, isTeacher, isStudent, ... }` com nomes derivados das strings em prod. Evita repetir literal `'SchoolAdmin'` espalhado.
- **Dependência implícita**: feature depende do BFF retornar `flg_usuario_criador` no `GET /sala/v1/escolas/{escola}/salas/{sala}`. Confirmado em runtime, mas não há contrato Swagger consultado nessa task. Vale validar com backend se a flag é estável.

## 7. Cross-references

- Commit: `c07509d` — feature(pbi-323909): Adicionando Regra para o botão excluir sala
- PR: (a abrir)
- Docs afetados: nenhum doc principal alterado. Memórias pessoais (`~/.claude/projects/.../memory/`):
  - `project_roles_e_sessionstorage.md` — mapping de roles e canal sessionStorage portal→MFE
  - `feedback_replace_all_branches_responsivos.md` — cuidado com Edit replace_all em molecules responsivas
- TASK_LOGS relacionados: nenhum (primeira task registrada no kickoff V3)
