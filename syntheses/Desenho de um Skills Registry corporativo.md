---
type: synthesis
status: active
updated: 2026-10-07
date: 2026-10-02
aliases: [skills registry, registro de skills, LiveStry]
tags: [skills, agent-skills, mcp, agent-flow, governance]
---

# Desenho de um Skills Registry corporativo

## A pergunta

Como resolver, na Livemode, os três problemas reais de skill compartilhada
entre o time: compartilhamento manual e defasado, manutenção que não
propaga (fix local não chega aos outros), e controle de qualidade
inexistente (caso concreto: a skill `pm-linear` não funciona direito)? O
ponto de partida foi um esboço de msilva (2026-10-02, desenhado à mão,
não salvo em `raw/`) com um "Skills registry" central, servidor MCP/API, e
usuários — cobrindo autoria, compartilhamento, auditoria, review, evals,
governança, analytics, busca vetorial/semântica.

## Como chegou aqui

Validação dos três problemas com msilva: compartilhamento hoje é zip
manual (defasagem, falta de praticidade, descoberta fraca — "não sabia que
existia skill pra esse fluxo"); manutenção não propaga pros usuários locais;
qualidade já falhou de verdade (`pm-linear`); escala é "todos" — não é
problema de poucas pessoas.

**Alternativa nativa descartada em parte**: o plugin marketplace do Claude
Code (`marketplace.json`, `/plugin install`, auto-update) resolve
distribuição e versionamento de graça, mas é mecanismo específico do Claude
Code — não existe equivalente em Cursor, Copilot, Gemini CLI etc. Como a
Livemode precisa ser **harness-agnostic**, isso derruba o marketplace como
solução única (docs: code.claude.com/docs/en/plugins/*).

**Formato do pacote de skill**: resolvido adotando a spec aberta
[agentskills.io/specification](https://agentskills.io/specification) —
`SKILL.md` com frontmatter (`name`, `description`, `license`,
`compatibility`, `metadata`, `allowed-tools`), carregamento progressivo
(metadata sempre carregada, corpo só na ativação). Confirmado como
cross-harness de verdade, não aposta: `agentskills.io/clients.md` lista
suporte nativo em ~45 produtos, incluindo Cursor, GitHub Copilot/VS Code,
Gemini CLI, Codex/ChatGPT, Goose, OpenHands, Roo Code. A spec **não define**
registry/distribuição — deixa esse espaço aberto de propósito, o que
confirma que a ideia do diagrama (registry + MCP) não compete com nada
existente.

**O problema de trigger**: dado um registry central exposto via MCP
(`search_skills`/`load_skill`), nada faz o modelo decidir chamar
`search_skills` espontaneamente — ele só age sobre o que já está no
contexto. Mecanismo real usado hoje (e observável nesta própria sessão):
metadata (nome+descrição) de toda skill é injetada passivamente no contexto
no início da sessão; só a ativação completa é uma tool call. Alternativas
mapeadas, por quanto dependem do modelo "lembrar sozinho": instrução fixa
em arquivo estático (`AGENTS.md`/`CLAUDE.md`), uma MCP tool por skill (sem
catálogo, mas escala mal), MCP "prompts" como slash command manual, híbrido
sync-local + registry pra cauda longa, recomendação fora do loop do agente
(bot no Linear/Slack), hook automático, e convenção cultural.

**Hook escolhido como solução**: roda antes do prompt chegar ao modelo,
fora da decisão dele — elimina o problema de trigger de vez. Risco
levantado por msilva: vendor lock-in. Investigado: 11 de 14 harnesses
pesquisados têm mecanismo equivalente (`SessionStart`/`UserPromptSubmit` ou
nome próximo), convergência de nomenclatura forte o bastante pra sugerir
imitação direta do Claude Code. **Não existe padrão cross-vendor para
hooks** — o Agent Plugins Spec (agent-plugins.org, TSC com Amazon/Cursor/
Microsoft/OpenAI/Vercel, sucessor do AGENTS.md/SKILL.md) exclui hooks
explicitamente do contrato portável, por serem "client-específicos demais".
Lock-in é real, mas administrável: lógica central de busca fica igual,
muda só um adapter fino por harness (schema JSON via stdin/stdout).

**Escopo final, decidido por msilva**: limitar a 4 harnesses — Claude Code,
Cursor, Codex/GPT, Gemini CLI. Todos os quatro confirmados na camada mais
forte da pesquisa (hook injeta contexto automaticamente, sem decisão do
modelo), sem gaps de documentação a cobrir — ao contrário de Windsurf/Goose/
OpenCode, que ficaram como "parcial, não confirmado".

## Segundo esboço, mesmo dia — nome e esteira de qualidade

msilva trouxe uma segunda versão do diagrama, já com nome: **LiveStry**
(mesma convenção Live* de [[LiveAuth]]/[[LiveScript]]). Novidades:

- **"Manutenção" explicitada** na lista de funções do registry (antes só
  implícita nos três problemas validados).
- **"Upload de skills → Esteira de qualidade"**: ataca de frente o gate de
  qualidade que ficou em aberto na primeira passada (caso `pm-linear`).
  Ainda não definido o que a esteira roda de fato — validação estrutural
  (`skills-ref validate`), eval automatizado, ou review humano obrigatório
  antes do upload ser aceito. Segue como open issue abaixo.
- **Três pitches ("Soluciona?")**: "definir o teste de uma skill antes de
  publicar" (= a esteira acima), "servidor (MCP) de skills da LiveMode" (=
  a arquitetura já desenhada), e "skill de mapeamento das skills pessoais e
  de projeto" — escopo ainda não esclarecido com msilva; hipótese de
  trabalho é uma skill de inventário que varre `.claude/skills`
  local/projeto pra descobrir o que já existe antes de migrar pro LiveStry,
  não confirmada.
- **Inconsistência encontrada no mecanismo de trigger**: o diagrama
  descreve a solução como "busca semântica e hook ao inicializar a
  sessão" — mas um hook de `SessionStart` roda antes do usuário escrever
  qualquer coisa, sem texto pra servir de query à busca semântica. O par
  correto é `UserPromptSubmit` (roda a cada mensagem, tem o texto do
  usuário como query) para a busca; `SessionStart` serve só pra injetar um
  catálogo estático leve, não pra rodar `search_skills`. Os dois podem
  coexistir, mas não são o mesmo evento — corrige a arquitetura abaixo.

## LiveStry vs. Claude Marketplace

Comparação ao longo dos três eixos levantados por msilva no segundo
diagrama:

| Eixo | Claude Marketplace (nativo) | LiveStry (build próprio) |
|---|---|---|
| **Granularidade de controle** | Binário: skill existe no repo ou não. Sem etapas de ciclo de vida nativas (rascunho → review → aprovado → publicado). `claude plugin eval` é opcional, dev-time, não é gate no fluxo de publish. Update é pull — ninguém força a versão nova rodar. | Controle total porque você constrói o pipeline: pode exigir evaluation antes de aceitar upload, bloquear skill reprovada, forçar re-teste quando o conteúdo muda. Custo: você constrói e mantém esse pipeline. |
| **Analytics** | Telemetria via OpenTelemetry (`plugin_installed`, `plugin_loaded`, `skill_activated`) só se a org configurar exporter — não é automático. API de analytics agregada é Enterprise-only, por dia/por plugin, nome de plugin third-party redigido por padrão. `/skill-doctor` só dá visão local por usuário. | Cada chamada a `search_skills`/`load_skill` já é uma request no seu servidor — log granular, por usuário, por skill, por timestamp, sem depender de tier de plano nem de setup de exporter. |
| **Provider lock-in** | Mecanismo 100% proprietário do Claude Code (`marketplace.json`, `/plugin install`). Não existe em Cursor, Copilot, Gemini CLI — falha o requisito harness-agnostic direto. | Formato (`SKILL.md`, spec agentskills.io) e distribuição (MCP) já são cross-vendor por design. Único lock-in residual é o hook de trigger por harness — real mas administrável (wrapper fino, mesma lógica central). |

**Síntese**: o marketplace nativo ganha em "já existe, zero esforço" só
pra quem fica 100% no Claude Code. Perde nos três eixos assim que entra o
requisito harness-agnostic e a necessidade real de controle de qualidade e
visibilidade — os dois problemas que motivaram o LiveStry desde o início.
Não é alternativa ao LiveStry, é o fallback grátis de quem não sai do
Claude Code.

## Arquitetura resultante

- Formato de pacote: `SKILL.md` per agentskills.io spec.
- Fonte de verdade: registry central (git ou DB), servido via MCP —
  `search_skills` (busca semântica/vetorial) + `load_skill` (corpo
  completo), com log de uso por chamada (resolve auditoria/analytics de
  graça).
- Trigger: `UserPromptSubmit` (ou equivalente) roda `search_skills` com o
  texto do usuário como query a cada mensagem; `SessionStart` (ou
  equivalente), opcional, só injeta um catálogo estático leve. Um adapter
  fino por um dos 4 harnesses escopados, chamando a mesma lógica central de
  busca.
- Gate de qualidade: ainda em aberto — `claude plugin eval` é early-access
  e dev-time, não serve como gate automático; `skills-ref validate` (lib
  de referência da spec) cobre só validação estrutural do frontmatter, não
  "a skill funciona certo". Não é só o Claude Code que não resolve isso:
  [[Inside team MKT1's multiplayer AI setup]] (Parte 3) mostra que nem a
  própria MKT1 automatizou manutenção por conteúdo — `/skill-update` é
  manual, disparado depois de uma sessão, e lê só aquela sessão específica,
  não a skill nem seu histórico.

## Achado novo — a Vitrine de IA já modela "Skill" (2026-10-06)

Lendo o código de [[Vitrine de IA]] (`livemode-org/vitrine-ia-lmarques`),
achado que muda o enquadramento: não é só um registry parecido, é uma
plataforma já em produção que **já tem o conceito de "Skill" no modelo de
dado** — campo `tipo` com valores `Projeto`/`Skill`/`Artefato`, campo
próprio pra comando de ativação (`/nome-da-skill`), exibição do conteúdo
como `SKILL.md`, contador de downloads, badge própria na tela de curadoria
interna.

**Mas a exibição pública está desligada de propósito** (comentário no
código: "escondida por ora, só projetos aparecem no catálogo") e **o form de
cadastro não deixa ninguém escolher `tipo`** — só entra como Skill por
edição direta do dado. Ou seja: a infraestrutura de dado existe, a
publicação/descoberta de skill via Vitrine não foi ligada.

**Isso muda a pergunta do LiveStry**: antes de desenhar um registry do zero,
cabe perguntar pra quem mantém a Vitrine (dono do repo, programa de
Carolina) se o plano é ligar essa parte — o que resolveria sozinho o
problema de "compartilhamento manual e defasado" nomeado na abertura desta
página, e já viria com o mesmo gate de qualidade (Sentinela) usado pra
projetos. Ainda não levado a ninguém do time; só achado de código.

## Escopo: LiveStry termina no repositório (2026-10-07)

msilva (raw/sobre o livestry.md, 2026-10-07): questionou se o sync local
das skills no início da sessão é tarefa do LiveStry. Conclusão discutida em
chat: o "sync local" é o catálogo estático leve injetado no `SessionStart`
(já citado em "Arquitetura resultante" acima). O LiveStry deve se
restringir ao repositório/registry central das skills — fonte de verdade
servida via MCP (`search_skills`/`load_skill`). A sincronização/injeção
local via hook do harness é outra abstração, separada do registry.

**Duas opções mapeadas pro mecanismo de sync**:

- **Coldstart (fetch síncrono no `SessionStart`)**: a sessão busca o
  catálogo fresco toda vez que abre. Sem processo residente, sem instalação
  de serviço — só o hook chama o registry. Custo: latência de rede a cada
  sessão.
- **Serviço persistente a nível de OS**: processo contínuo (systemd/
  launchd/Windows Service) mantém o cache local sempre quente; o hook só
  lê do disco, sem roundtrip. Elimina o coldstart por sessão — o único
  coldstart vira a instalação/primeiro boot do serviço. Custo: processo
  residente, instalação e manutenção cross-platform, recuperação de crash.

Cache local + refresh assíncrono sem processo residente foi descartado:
não resolve a primeira sessão (sem cache ainda) nem atualiza uma sessão já
em andamento — mesma limitação do coldstart síncrono, mas com complexidade
extra de staleness e sem o benefício real de eliminar a latência, que só o
serviço persistente entrega.

Ainda não decidido entre as duas opções.

## O que continua em aberto

- Schema exato do hook em cada um dos 4 harnesses (nome do evento, formato
  de `additionalContext`) — pesquisa inicial via resumo de doc, não leitura
  verbatim; precisa confirmação campo-a-campo antes de implementar.
- Gate de qualidade real pro caso `pm-linear` — "esteira de qualidade" foi
  nomeada no segundo esboço, mas o que ela roda de fato (validação
  estrutural, eval automatizado, review humano) ainda não foi definido.
- Escopo da "skill de mapeamento das skills pessoais e de projeto" — não
  esclarecido com msilva ainda.
- Governança/escopo por time — ainda não desenhado como o registry decide
  o que cada time vê.
- Mecanismo de sync local: coldstart síncrono vs. serviço persistente a
  nível de OS — duas opções mapeadas, nenhuma escolhida. Dono da
  abstração (quem constrói/mantém) também em aberto.
- Não foi levado a Luís, Gabrielle, ou qualquer outra pessoa do time —
  raciocínio só entre msilva e Claude até aqui.

## Relacionado

- [[Packaging as skills]]
- [[A6 Curador deve padronizar e sinalizar defasagem de skills]]
- [[Agent Flow]]
- [[Vitrine de IA]] — plataforma em produção com "Skill" já no modelo de
  dado, exibição desligada; ver achado acima.
