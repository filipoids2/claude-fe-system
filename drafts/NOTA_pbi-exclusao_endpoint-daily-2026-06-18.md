# Nota daily — bloqueador CA12-14 (endpoint de exclusão em lote)

> **Data:** 2026-06-18
> **Anexa a:** `TASK_LOG_pbi-exclusao-WIP.md` (fatia 3, CA12-14 bloqueada no PASSO 0)
> **Para:** daily / TL / BFF
> **Status:** aguardando resposta

---

## Texto a levar pra daily

**CA12-14 — confirmação de endpoint pra excluir MATERIAL em lote**

Requisito de produto (confirmado com o time/Filipe): excluir remove o
"material inteiro" — o card todo, com tudo que tiver dentro (N arquivos/
links/itens). A unidade que o professor seleciona é o card.

Suspeita do FE: o endpoint correto é B, não o A que veio no Swagger da task.

  A) `DELETE /escolas/{e}/salas/{s}/conteudos`   body `[{ guid_conteudo }]`
     ← Swagger anexado à task (parece copy-paste de PBI de excluir sala/aula)
  B) `DELETE /escolas/{e}/salas/{s}/aulas`        body `[{ guid_estrutura_aula }]`
     ← já existe e funciona em prod (exclusão single)

Evidências no código que sustentam B:
- `HomeAulas.excluirConteudo` (`services/HomeAula/HomeAula.tsx:344`) usa B
  pro single — confirmado funcionando em prod (botão lixeira de item único).
- O card que a barra seleciona (`IListaEstruturaConteudo`) tem
  `guid_estrutura_aula` direto; `guid_conteudo` só existe nos filhos
  (`conteudos[]`). Relação card:conteúdo é 1:N. Pra usar A, o FE teria que
  achatar os filhos.

Perguntas:

1. Confirma B? (single já usa essa rota; lote segue a mesma?)
2. **[decide a coleta]** Mandando o `guid_estrutura_aula` do card, o
   backend cascateia e apaga TODOS os conteúdos de dentro (o "material
   inteiro")? Ou cada conteúdo precisa ir explícito no body?
3. Escopo "só desta aula" (CA13 nota 1): garantido por
   `guid_estrutura_aula` ser único por aula no banco?
4. **[paranoia útil]** A não é um endpoint v3 novo que o BFF subiu e que a
   gente precisa adotar? Quero descartar A ativamente, não só confirmar B.

---

## Observação interna (não levar pra daily)

- Nit na pergunta 3: "guid_estrutura_aula ser único por aula" pode soar
  como "cada aula tem um guid_estrutura_aula". O que importa é "cada
  guid_estrutura_aula pertence a uma única aula" — escopo derivado da
  unicidade no domínio. Texto mantido como original; TL entende no
  contexto. Ajustar só se virar registro escrito.

## Próximo passo dependente desta resposta

- **Se B + cascata (resposta provável):** retomar fatia 3, coletar
  `guid_estrutura_aula` dos parents selecionados, chamar service novo
  `HomeAulas.deleteContentBatch` (ou estender `excluirConteudo` pra aceitar
  array — decidir naming junto). UX coerente: `qtdExcluir` = parents,
  body = parents.
- **Se A:** criar service novo, achatar filhos no FE (`parents.flatMap(p
  => p.conteudos.map(c => ({ guid_conteudo: c.guid_conteudo })))`), e o
  body pode ter tamanho > `qtdExcluir` — gera incongruência UX/payload.
- **Se híbrido (B + body com todos os filhos):** evidência de que o
  endpoint tem semântica de "apagar os exatos N IDs" e não cascata.
  Pior dos mundos pra UX. Quase certamente não.
