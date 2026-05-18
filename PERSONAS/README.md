# PERSONAS — sistema de personas

Personas declaram **quem está usando o Claude CLI neste projeto** e
**como ele deve ser tratado**. Cada pessoa do time tem sua persona
própria.

## Como funciona

1. **Master global** (`~/.claude/CLAUDE.md`) — perfil pessoal,
   universal a qualquer projeto. É **privado**, mora na máquina
   da pessoa.

2. **Persona do projeto** (`PERSONAS/<nome>-<papel>.md`) — perfil
   específico **neste projeto FTD V3**. Versionado, compartilhável
   com o time.

3. **CLAUDE.md do projeto** (raiz) — aponta pra persona ativa no
   default. Pode ser sobrescrito no início de sessão.

**Em sessão:** o usuário declara qual persona está usando. Sem
declaração, default é o que está no `CLAUDE.md` raiz seção 5.

## Pra que serve

Persona faz a IA:

- **Ajustar tom** (par sênior vs aprendiz, formal vs amigável)
- **Calibrar profundidade** (explicar fundamento vs ir direto)
- **Respeitar limites** (não decidir arquitetura por quem não tem
  autoridade)
- **Conectar com estilo de aprendizagem** (visual vs textual,
  serial vs paralelo)

Sem persona → IA usa default. Com persona → IA personaliza.

## Como adicionar nova persona

1. Copie `_TEMPLATE.md`
2. Renomeie pra `<nome>-<papel>.md` (kebab-case, papel curto)
   - Exemplos: `elias-tech-lead.md`, `well-arquiteto.md`,
     `rafa-dev-fe.md`
3. Preencha as 5 seções
4. Salve e commit
5. No início de sessão, declare: "use persona `<nome>-<papel>`"

## Persona pode mudar com o tempo

Persona é **viva**. Recomendado revisar a cada 2-3 meses, ou
quando perceber mudança real:

- Tu fechou gap técnico que estava em "aprendiz" → move pra
  "prática sólida"
- Mudou de papel no time (dev → tech lead) → ajusta seções
- Preferência mudou (descobriu que prefere paralelo a serial) →
  atualiza

**Não é compromisso vitalício.** É calibração atual.

## Personas existentes

- `filipe-dev-fe.md` — dev frontend
- (adicionar conforme entrar no time)

## Limites

- Não use persona pra registrar **informação sensível** (saúde,
  estratégia de carreira, opinião sobre colegas). Isso vai pro
  master global ou pra `PRIVATE/`.
- Persona é **profissional**, compartilhável. Tudo aqui pode
  virar público se algum dia o repo virar open-source ou for
  compartilhado com gestores/RH.

## Cross-references

- Template: `_TEMPLATE.md`
- Exemplo preenchido: `filipe-dev-fe.md`
- Como CLAUDE.md raiz consome: `../CLAUDE.md` Seção 5
