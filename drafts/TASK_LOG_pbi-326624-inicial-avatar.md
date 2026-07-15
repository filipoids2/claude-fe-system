# Regra de 1 inicial no avatar sem imagem — MFE Salas

> **Tipo:** ajuste cirúrgico (paridade com portal)
> **PBI:** 326624
> **Branch:** feature/pbi-326624
> **Status:** implementado + commitado (`af62388`) — aguardando validação manual
> **Início:** 2026-07-01
> **Conclusão:** 2026-07-01

---

## 1. Resumo curto

Alinha o `mfe-ftd-ionica_sala` com a mesma regra já aplicada no portal:
quando o usuário não tem imagem cadastrada, o avatar exibe apenas **1
inicial** (primeira letra do nome), não 2 (primeiro + último nome). Como
todos os consumidores passavam por um util centralizado (`gerarSiglaNome`),
foi possível resolver mudando a função — de quebra, consolidei 3
implementações redundantes em 1.

## 2. Problema / Objetivo

O portal já foi ajustado para mostrar 1 inicial no avatar sem imagem. O
MFE Salas ainda gerava 2 iniciais (`FH` em vez de `F`), gerando
inconsistência visual entre telas do portal e telas do MFE (cabeçalho de
sala, cartão de mural, cartão de estudante, comentários, modal lateral de
estudantes etc.).

## 3. Solução aplicada

### Fatia única — consolidar util + trocar regra

Antes da mudança havia **3 implementações da mesma lógica** no repo:

1. `src/utils/gerarSiglaNome.ts` (retornava 2 iniciais)
2. `src/utils/gerarSiglaNome.tsx` (duplicado, retornava 2 iniciais)
3. Cópia inline dentro de `modalLateralEstudantes.tsx:92-97`

Optamos por **Opção B — cirúrgico + limpeza** (contra a Opção A de só
ajustar as 3 mantendo duplicação):

- `.ts` reescrito para retornar 1 inicial (`nome.trim().charAt(0).toUpperCase()`).
- `.tsx` deletado.
- Cópia inline do modal removida; agora consome o util.
- 4 imports migrados de `.tsx` → `.ts` (Comment, ListaDeProfessores,
  Comentarios, modalLateralEstudantes). Os outros 3 já apontavam pro `.ts`.

Todos os 8 pontos de consumo passam pela função única — mudança da regra
foi feita em 1 lugar só.

## 4. Arquivos alterados

| Arquivo:linha                                                  | Mudança                                                                 |
| -------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `src/utils/gerarSiglaNome.ts`                                  | Regra reescrita: retorna só a 1ª inicial do nome                        |
| `src/utils/gerarSiglaNome.tsx`                                 | **Deletado** (duplicado)                                                |
| `src/shared/molecules/Modal/modalLateralEstudantes.tsx:6`      | Novo import do util                                                     |
| `src/shared/molecules/Modal/modalLateralEstudantes.tsx:92-97`  | Removida cópia inline de `gerarSiglaNome` (agora usa o util importado)  |
| `src/components/Comment/Comment.tsx:7`                         | Import migrado `.tsx` → `.ts`                                           |
| `src/components/organisms/Cabecalho/ListaDeProfessores.tsx:6`  | Import migrado `.tsx` → `.ts`                                           |
| `src/components/organisms/Comentarios/Comentarios.tsx:1`       | Import migrado `.tsx` → `.ts`                                           |

Consumidores que já estavam em `.ts` (nenhuma mudança de import
necessária): `CardMuralHeader.tsx`, `HeaderPageMuralAluno.tsx`,
`CardContentEstudante.tsx`.

Componentes de Avatar que renderizam a sigla (não tocados — só recebem
a prop): `AvatarComTooltip`, `AvatarSemTooltip`, `AvatarComponent`.

## 5. Como testar

Locais no MFE Salas onde o avatar pode aparecer sem imagem:

1. **Cabeçalho da sala** (professor logado sem foto) → 1 inicial no header.
2. **Cartão de mural** (`CardMuralHeader`) → autor sem foto → 1 inicial.
3. **Cartão de estudante** (`CardContentEstudante`, visão do aluno) →
   autor sem foto → 1 inicial.
4. **Modal lateral de estudantes** (clicar em "ver todos" no cabeçalho) →
   cada estudante sem foto → 1 inicial.
5. **Comentários** (organismo de comentários dentro de post/atividade) →
   comentarista sem foto → 1 inicial.
6. **Header da tela de mural do estudante** (`HeaderPageMuralAluno`) →
   lista de professores → 1 inicial cada.
7. **Lista de professores no cabeçalho** (`ListaDeProfessores`) → mesma
   coisa.

Regressão a monitorar: quando **há** imagem, o avatar deve continuar
renderizando a imagem normalmente (a lógica de fallback dos componentes
`AvatarComTooltip` / `AvatarSemTooltip` não foi tocada).

## 6. Notas técnicas / Achados

- **Por que 2 arquivos `.ts` e `.tsx` para o mesmo util.** Legado da
  agência externa V3. A função não usa JSX — a extensão `.tsx` era
  desnecessária. Consolidação removeu ambiguidade + evita import errado
  no futuro.
- **Por que a cópia inline no modal.** Provavelmente reimplementada
  localmente por alguém que não achou o util na hora. Bom lembrete de
  buscar util antes de reimplementar — mas a solução aqui é o util
  centralizado, não uma regra.
- **Componente `AvatarComponent` novo (Tailwind) não usa esse util.** Ele
  recebe `fallback` como prop pronta, calculada pelo caller. Todos os
  callers que já usam esse componente hoje passam pelo `gerarSiglaNome`
  também, então a mudança propaga.
- **Type-check.** `npx tsc --noEmit` executado após a mudança. Nenhum
  erro novo introduzido — os erros existentes no repo são
  pré-existentes (`verbatimModuleSyntax`, tipos opcionais em outros
  arquivos).
- **Paridade com portal.** Regra idêntica à aplicada no portal: 1
  inicial, primeira letra do primeiro nome, uppercase.

## 7. Follow-ups / débitos

- Verificar se outros MFEs (`atividade`, `aula`, `biblioteca`, `home`)
  também têm a regra antiga. Se sim, aplicar o mesmo padrão neles.
- Validar no navegador em sala real com professor e estudante sem foto
  antes de subir PR.
