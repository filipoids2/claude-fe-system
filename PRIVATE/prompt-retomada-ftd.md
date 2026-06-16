Continuando trabalho FTD Aceleração Iônica V3.

CONTEXTO RÁPIDO:
- Plataforma educacional FTD em produção, V3 em construção
- Time interno absorvendo da agência externa
- Sou dev FE entrando no projeto, junto com Elias (TL) e Well
  (Arquiteto)

INFRAESTRUTURA QUE EU JÁ TENHO:

Repos clonados em D:\local\ftd\ftd-frontend\:
- mfe-ftd-ionica_portal (HOST), sala, atividade, bancoquestao,
  mural, agenda, login, monolithic
- ions-facade lib local

Sistema de docs versionado:
- github.com/filipoids2/claude-fe-system (PRIVADO)
- CLAUDE.md, docs/, PERSONAS/, TASK_LOGS/, drafts/, ONBOARDING,
  STACK_BACKEND, INVENTARIO, SESSION_CONTEXT, REPOS_AZURE_DEVOPS

PRIVATE/ (não versionado, máquina local apenas):
- security-findings (achados sensíveis Camada 1+2)
- env-vars-legacy.md (vars coletadas de projetos legados)

ACHADOS-CHAVE JÁ MAPEADOS:
- BFFs são NestJS/Node (não Java como wiki dizia)
- api-* são configs do Azure APIM (não apps)
- ms-* é poliglota (Java + Node convivem)
- ms-signalr tem divergência de contrato entre prd e feature
  default (risco pro FE)
- 99 repos catalogados, 13 analisados em profundidade

STACK FE:
- React 18.3.1, TypeScript 5.6, Rsbuild 1.5.6
- Module Federation 0.19.1, react-router 7.9.1
- Tailwind 3.4.17, Azure AD B2C + MSAL
- Datadog RUM 6.24, sessionStorage pra token
- npm feeds privados Azure DevOps (ions-ds + ionica-v3)

MEU PERFIL TÉCNICO (calibragem importante):
- React até v16 (gap em hooks novos, RSC, Server Actions)
- TS lê e escreve, gap em generics/types avançados
- Webpack mais que Vite, Rsbuild nunca usei
- NestJS conceito sim, prática zero
- Module Federation nunca usei
- Azure AD B2C estruturado nunca configurei
- Conheço domínio FTD (legados frontoffice/backoffice/V1)

PREFERÊNCIAS DE TRABALHO:
- Par técnico + aprendiz ativo, conforme já configurado em
  Personal Preferences
- Cross-stack só Node.js — não decido fora de FE/BFF Node
- Modo serial, aprovação entre passos
- Mascarar credenciais sempre

PRÓXIMO PASSO HOJE:
[AQUI TU PREENCHE O QUE QUER FAZER NA SESSÃO]

Não comece a executar sem mapear primeiro.