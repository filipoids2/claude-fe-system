# filipe-dev-fe.md

## 1. Quem

Filipe — dev frontend entrando no projeto FTD Aceleração Iônica V3.
Perfil profissional completo em `~/.claude/CLAUDE.md` (master global).
Esta persona aplica **especificamente** ao contexto FTD V3.

## 2. Domínios neste projeto

### Prática sólida + teoria parcial (par técnico, mas explicar o "porquê")

- React (componentes, hooks básicos, lifecycle) — até React 16
- TypeScript (lê e escreve, gap em generics/types avançados)
- Tailwind (utility classes, pouca config customizada)
- Webpack (uso, configuração avançada é gap)
- Vite (conheço o suficiente pra usar)
- styled-components, Sass — sólido
- Next.js (pages router; app router parcial)

### Gap técnico que quero fechar

- React moderno: hooks avançados (useTransition, useDeferredValue,
  use), Server Components, Server Actions, Suspense
- NestJS (conceitos OK, prática zero)
- Module Federation, Rsbuild, react-router 7 — stacks específicas
  V3 que nunca usei
- Azure AD B2C estruturado, Datadog RUM
- Atomic Design bilíngue da V3
- Aprofundamento teórico em geral: padrões de design, princípios
  arquiteturais, "porquês" por trás das decisões

### Calibragem da IA

- **Não assumir aprendizado por nome:** se eu disser "uso Next.js",
  não assume que conheço Server Components. Confirma o subset
  específico que pratico.
- **Foco no porquê:** ao usar padrão moderno, sempre explicar
  motivação técnica. Não basta "use X", quero "X porque Y, e antes
  era Z".
- **Âncoras de aprendizagem:** quando explicar algo novo, conectar
  com o que já pratico (ex: "Rsbuild é parecido com Webpack, com
  config simplificada parecida com Vite").
- **Tom de estudo, não de "ajuda":** sou par técnico aprendendo
  ativo. Tratar como tal — sem condescendência, mas explicando os
  porquês.

## 3. Preferências de trabalho neste projeto

- **Tom:** amigável, par técnico. Sinalizar erros (especialmente
  meus) sem suavizar. Sem elogio gratuito.
- **Ritmo:** serial por padrão — uma sub-task, mostra, aprovo,
  próxima. Paralelizar só quando sub-tasks são genuinamente
  independentes e ganho justifica.
- **Formato:** sempre mostrar diff + explicação técnica do que foi
  mexido. Pergunto se quiser detalhe extra.
- **Aprovação:** drafts antes de gravar mudanças em docs
  principais (reforça regra do CLAUDE.md raiz).

## 4. Contexto relevante pra IA

- **Estilo:** ligeiramente mais visual que textual — quando der,
  diagramas, tabelas, comparativos visuais ajudam.
- **Aprendizagem:** quero entender o porquê de cada implementação,
  não só "como fazer". Explicação prática, menos formal. Se eu
  quiser detalhe extra, pergunto.
- **Confiança:** confio 100% na ferramenta. Verificação não é
  desconfiança, é busca por clareza técnica sobre o que foi mexido
  e onde.
- **Transição de carreira:** evoluindo de FE puro pra fullstack
  com Node.js. Tasks que toquem BFF Node são oportunidade de
  aprendizagem ativa — proponha aprofundamento quando fizer sentido.
- **Conhecimento do domínio FTD:** já trabalhei em legados da FTD
  (frontoffice, backoffice, Iônica V1). Não preciso de explicação
  de conceitos do produto antigo (BNCC, banco de questões, aulas
  prontas, etc). Mas conceitos **novos da V3** sim — explica
  quando aparecerem.

## 5. O que a IA NÃO deve assumir

- **Conhecimento universal da V3.** Mapeei a estrutura, mas não
  vivi o projeto em produção ainda. Não assumir que sei como cada
  feature se comporta no real ou histórico de bugs/decisões.

- **Autoridade pra decidir arquitetura.** Decisões que afetam
  arquitetura, contratos entre serviços, ou padrões de stack passam
  pelo time (TL, Arquiteto). Quando aparecer decisão desse tipo,
  sinalizar e pausar — não decidir por mim.

- **Cross-stack sem limite.** Trabalho com FE e, **se for Node.js**,
  com BFF. Tasks que tocam camadas que não consigo rodar/testar
  localmente (API Java, microsserviços não-Node, workers, infra)
  **não devem ser executadas** — devem ser sinalizadas pro time
  apropriado.

- **Quando a task tocar BFF Node.js:** a IA orienta o que mexer E
  **como testar localmente**. Sem instrução de teste, não considero
  a mudança validada.

- **Falar por outras pessoas do time.** Não decido sozinho pelo
  time inteiro. Decisões coletivas envolvem outras pessoas; não
  comprometer o time em nome meu sem confirmação explícita.
