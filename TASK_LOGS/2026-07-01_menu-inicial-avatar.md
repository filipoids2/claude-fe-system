# [Menu] Alterar regra de inicial para quando não houver avatar

> **Tipo:** chore (ajuste de regra de UX)
> **PBI:** #326624 — [Menu] Alterar regra de inicial para quando não houver avatar
> **Branch:** feature/pbi-326624
> **Status:** concluída
> **Início:** 2026-07-01
> **Conclusão:** 2026-07-01

---

## 1. Resumo curto (pra copiar em PBI/PR/daily)

Quando o usuário (professor, aluno ou gestor) não tem foto de perfil cadastrada,
o avatar passa a exibir **apenas a primeira letra do nome** em vez de
nome+sobrenome. Ajuste evita combinações de duas letras que formavam palavras
inapropriadas. Aplicado em 3 MFEs: menu (portal), comentários/lista de
professores/modal de estudantes (salas) e avatares da correção de atividades
(atividade).

## 2. Problema / Objetivo

O padrão anterior gerava iniciais de 2 letras (`nome[0] + sobrenome[0]`, ex.: "AP")
e alguns pares resultavam em palavras inapropriadas visíveis no avatar do header.
Regra nova: exibir apenas 1 letra (primeira do nome).

## 3. Solução aplicada

### Portal (menu)

- **Serviço `whoAmIService.getUserInitials`**: assinatura reduzida de
  `(nome, sobrenome)` para `(nome)`. Retorna `nome.charAt(0).toUpperCase()`.
- **Call site em `WhoAmIContext`**: atualizado para passar somente `userData.nome`.
- **JSDoc atualizado**: descrição e exemplo (`"J"` no lugar de `"JS"`).
- Nenhum componente de UI tocado — `userInitials` já é consumido como string pelo
  `Header.tsx` e `MobileUserMenu.tsx` do MFE portal (avatar SVG desktop + avatar
  do dropdown + avatar mobile). Um só ponto de mudança propaga em todas as
  renderizações.

### Salas (comentários, cabeçalho, modal lateral de estudantes)

- **Util `src/utils/gerarSiglaNome.ts`**: lógica reduzida — de
  `firstNameInitial + lastNameInitial` para `nome.trim().charAt(0).toUpperCase()`.
  Guarda de `nome` vazio já existia.
- **Duplicata removida**: `src/utils/gerarSiglaNome.tsx` (mesmo nome, `.tsx`)
  apagada. Havia duas versões da mesma função em extensões diferentes; consumidores
  ficaram apontando pra `.ts`.
- **Imports normalizados** em 3 componentes que apontavam pra `.tsx`:
  `components/Comment/Comment.tsx`, `components/organisms/Cabecalho/ListaDeProfessores.tsx`,
  `components/organisms/Comentarios/Comentarios.tsx`.
- **`shared/molecules/Modal/modalLateralEstudantes.tsx`**: função inline
  `gerarSiglaNome` (que ainda usava a regra antiga de 2 letras) removida e
  substituída por import do util. Elimina divergência silenciosa entre modal e
  demais telas.

### Atividade (correção de atividades)

- **Novo util `src/utils/gerarSiglaNome.ts`**: `(nome: string) => string`, retorna
  `nome.trim().charAt(0).toUpperCase()` e guarda contra `nome` vazio. Nome/formato
  alinhados com o util homônimo já existente no MFE Salas (mesmo PBI) — mantém
  convergência entre repos.
- **`AccordionEstudantes.tsx`**: removida a função inline
  `getAvatarInitials(nome, sobrenome)` e substituída pelo `gerarSiglaNome(estudante.nome)`
  no fallback do avatar (círculo com background `brand-secondaryLight2`).
- Só existe **1 ponto** de render de sigla no repo — nada mais precisa ser tocado.
  Campo `avatar` (imagem) continua sendo consumido normalmente quando presente.
- Header desse MFE (`Header.tsx`) tem um `"U"` **hardcoded** no avatar do canto
  superior; não foi ajustado porque não vem de dado. Deixa consistente pro dia em
  que o menuglobal absorver.

## 4. Arquivos alterados

| Arquivo:linha | Mudança |
|---|---|
| `mfe/mfe-ftd-ionica_portal/src/services/whoAmIService.ts:28-33` | `getUserInitials` agora recebe só `nome` e retorna 1 letra maiúscula |
| `mfe/mfe-ftd-ionica_portal/src/contexts/WhoAmIContext.tsx:58` | Call site atualizado — passa somente `userData.nome` |
| `mfe/mfe-ftd-ionica_sala/src/utils/gerarSiglaNome.ts` | Lógica reduzida pra 1 letra (`.trim().charAt(0).toUpperCase()`) |
| `mfe/mfe-ftd-ionica_sala/src/utils/gerarSiglaNome.tsx` | **Deletado** — duplicata do util em `.tsx` |
| `mfe/mfe-ftd-ionica_sala/src/components/Comment/Comment.tsx` | Import: `.tsx` → `.ts` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Cabecalho/ListaDeProfessores.tsx` | Import: `.tsx` → `.ts` |
| `mfe/mfe-ftd-ionica_sala/src/components/organisms/Comentarios/Comentarios.tsx` | Import: `.tsx` → `.ts` |
| `mfe/mfe-ftd-ionica_sala/src/shared/molecules/Modal/modalLateralEstudantes.tsx` | Remove `gerarSiglaNome` inline (regra antiga de 2 letras); passa a importar o util |
| `mfe/mfe-ftd-ionica_atividade/src/utils/gerarSiglaNome.ts` | **Novo util** — `(nome: string) => string` retornando 1 letra maiúscula, com guarda de `nome` vazio |
| `mfe/mfe-ftd-ionica_atividade/src/components/CorrecaoDaAtividade/AccordionEstudantes/AccordionEstudantes.tsx` | Import do novo util; remove `getAvatarInitials` inline; fallback do avatar passa a chamar `gerarSiglaNome(estudante.nome)` |

## 5. Como testar

1. Login com usuário **sem foto de perfil** cadastrada (professor, aluno ou gestor).
2. Header (canto superior direito): avatar deve exibir **apenas 1 letra** — a
   primeira do nome, maiúscula. Ex.: usuário "Admin Prd" → **"A"** (antes: "AP").
3. Clicar no avatar → dropdown desktop: bloco de identificação no topo também
   exibe **1 letra** no círculo roxo.
4. Mobile (< 768px) → gaveta lateral: avatar do topo idem, **1 letra**.
5. Usuário **com foto**: comportamento inalterado — `<img>` continua exibida em
   todos os 3 pontos (fallback só dispara sem `userData.avatar`).
6. **MFE salas** — abrir uma sala e verificar:
   - **Comentários** (post/mural): avatar do autor sem foto exibe **1 letra**.
   - **Cabeçalho / lista de professores**: avatares dos professores idem.
   - **Modal lateral de estudantes** (abrir clicando em ver estudantes): cada linha
     com estudante sem foto exibe **1 letra**. Antes desta mudança essa tela ainda
     seguia a regra antiga de 2 letras — confirmar visualmente que **caiu pra 1**.
   - Usuário com foto: comportamento inalterado.
7. **MFE atividade** — abrir uma atividade em correção (tela do professor,
   componente `AccordionEstudantes`). Cada linha de estudante tem um avatar
   circular à esquerda do nome:
   - Estudante **sem** `avatar`: círculo deve exibir **apenas a inicial do primeiro
     nome** (ex.: "Maria Silva" → **"M"**).
   - Estudante **com** `avatar`: `<img>` renderizada normalmente, sem mudança.
   - Testar nas 3 abas: **Para corrigir**, **Aguardando respostas**, **Corrigidas**.

## 6. Notas técnicas / Achados

- O PBI fala em "MFE de menu" mas o menuglobal (`mfe-ftd-ionica_menuglobal`) está
  como esqueleto — só `Button.tsx` do scaffold. O menu com avatar hoje mora no
  MFE portal (`mfe-ftd-ionica_portal/src/components/Header/Header.tsx`), que é
  onde a mudança foi aplicada. Quando o menuglobal absorver o header, replicar a
  regra lá.
- Fonte única da verdade: `whoAmIService.getUserInitials`. Header desktop, header
  dropdown e MobileUserMenu consomem o mesmo `userInitials` do `WhoAmIContext` —
  1 edição, 3 lugares corrigidos.
- Assinatura da função foi reduzida (removido o parâmetro `sobrenome`) em vez de
  ignorá-lo. Consistente com o CLAUDE.md global: sem backwards-compat shim quando
  a base de código todinha é conhecida e o único call site é interno.
- Sem testes existentes pro `whoAmIService` — não criei suite só pra esse ajuste
  cirúrgico. Se o time decidir cobrir o serviço, o caso feliz é trivial
  (`getUserInitials("joão")` → `"J"`).
- Comportamento com nome vazio: `"".charAt(0).toUpperCase()` → `""`. Idêntico ao
  fallback anterior quando `userData` era `null`. Aceitável.
- **MFE salas — duplicata `.ts`/`.tsx`**: existiam dois arquivos `gerarSiglaNome`
  (um `.ts` e um `.tsx`) com o mesmo conteúdo. Padronizamos no `.ts` (não há JSX
  na função) e removemos o `.tsx`. Isso obrigou o ajuste de import em 3
  componentes que ainda apontavam pra `.tsx`. Aproveitei o PBI pra fechar essa
  divergência silenciosa.
- **MFE salas — regra antiga sobrevivendo em inline function**: o
  `modalLateralEstudantes.tsx` tinha uma cópia local de `gerarSiglaNome` que
  retornava 2 letras — nunca sincronizada com o util. Se a gente só tivesse mudado
  o util, esse modal continuaria mostrando 2 letras. Substituição por import
  elimina a divergência.
- **MFE atividade — decisão de extrair util** em vez de ajuste inline: o único
  render de sigla vive em `AccordionEstudantes.tsx`, mas o util (`gerarSiglaNome`)
  já existe no MFE Salas com o mesmo nome/assinatura. Extrair aqui zera o custo
  futuro de convergir os repos quando alguém puxar código pra shared/toolkit.
- MFE atividade — o `type-check` acusa 1 erro em `AccordionEstudantes.tsx:419`
  (`Button` recebendo `children` do tipo `Element` onde espera `string`) que é
  **pré-existente** — confirmado com `git stash` + `tsc --noEmit`. Não é regressão
  desta mudança.

## 7. Cross-references

- Commit: (a criar)
- PR: (a abrir)
- Docs afetados: nenhum.
- TASK_LOGS relacionados: —
