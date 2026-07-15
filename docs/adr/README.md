# Architecture Decision Records (ADRs)

Esta pasta guarda as **decisões arquiteturais importantes** do projeto.

## O que é um ADR?

Um **Architecture Decision Record** é um documento curto que registra:

- O problema que precisávamos resolver
- A decisão que tomamos
- As alternativas que consideramos
- As consequências (boas e ruins)

O objetivo é **não perder o "porquê"** das decisões técnicas ao longo do tempo.

## Quando criar um ADR?

Crie um ADR quando a decisão:

- Afeta a arquitetura de forma relevante
- Tem trade-offs significativos
- Pode gerar dúvida no futuro ("por que fizeram assim?")
- Envolve escolha de tecnologia, padrão ou abordagem importante

**Não** crie ADR para mudanças pequenas ou óbvias.

## Como usar

1. Copie o arquivo `000-template.md`
2. Renomeie para o próximo número (ex: `001-estado-padrao-mfe.md`)
3. Preencha as seções
4. Atualize o status conforme a decisão evolui (`Proposed` → `Accepted`)

## Convenção de nomenclatura

```
XXX-titulo-curto-em-kebab-case.md
```

Exemplos:
- `001-module-federation-strategy.md`
- `002-estado-padrao-novos-mfes.md`
- `003-facade-first-design-system.md`
