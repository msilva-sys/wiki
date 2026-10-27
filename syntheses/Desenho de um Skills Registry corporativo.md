---
type: synthesis
status: active
updated: 2026-10-27
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

## PRD da v1 publicado no Linear (2026-10-27)

O LiveStry virou a issue `PRO-903` (projeto *Skills do time: inventário,
qualidade e distribuição*, P-PRO-45, lead Gabrielle, iniciativa Governança
de IA; criada por Carolina em 2026-10-07, assignee msilva). O PRD foi montado
seção a seção em chat e publicado como Document anexado à issue:
[PRD — LiveStry](https://linear.app/projetos-livemode/document/prd-livestry-servidor-que-entrega-as-skills-da-empresa-a05dcf2d0210).
Prazo da issue acertado de 2026-10-23 para 2026-10-30 (combinado da 1:1,
ver [[2026-10-27 Fechamento de combinados - outubro (prep)]]).

**O que mudou de contexto desde 2026-10-07.** O projeto do Linear já
entregou o que esta página chamava de "registry": repositório oficial
`livemode-org/livemode-claude-skills` + marketplace `livemode-skills` no
Claude Code (`PRO-1200`, done 2026-10-26) e a rotina que abre PR nas
cópias dentro dos projetos, modelo Dependabot via GitHub App
`livemode-skills-sync` (`PRO-1202`, done 2026-10-26). Os dois "pitches"
do segundo esboço também já são issues fechadas: a skill de mapeamento é a
`PRO-899` (done, inventário dos 65 repos: 30 skills em 39 versões) e o
teste antes de publicar é a `PRO-902` (done 2026-10-20). Ou seja: o
LiveStry da v1 **não é o registry** — é só mais um canal de entrega do
repositório oficial, pra quem não usa o Claude Code.

**Decisões fechadas no PRD** (todas de msilva, em chat, 2026-10-27):

- Duas dores na v1: distribuir e manter atualizadas. Dado de uso fica como
  medida, não como objetivo.
- Cinco ferramentas, inclusive Claude Code (convive com o marketplace por
  enquanto; orientação no README, sem bloqueio técnico). Só pessoas; agentes
  ficam fora.
- Correção vale "na próxima conversa"; atualização em conversa aberta é
  débito registrado.
- Hospedagem **Cloudflare Workers** (mesma plataforma do [[LiveAuth]]);
  comparativo com Cloud Run no anexo A do PRD. Registro de uso em D1, custo
  a confirmar.
- Login pelo LiveAuth, mesmo padrão do MCP do [[Agent Flow]]. Endereço
  `livestry.livemode.space`.
- Catálogo vai pelas `instructions` do servidor **e** pela descrição da
  tool "carregar skill" — porque a verificação por ferramenta (abaixo)
  mostrou que três das cinco não garantem as instructions. Isso substitui
  o hook por harness desenhado em 2026-10-02, que fica fora da v1.
- Atalho `/`: nome simples da skill; cada ferramenta monta o prefixo do seu
  jeito. Busca semântica fora da v1.
- Owner do app do Claude já identificado (não nomeado no Linear);
  comportamento do app é o primeiro teste, sem servidor de exemplo.

**Verificação por ferramenta** (workflow de 10 agentes, doc oficial +
leitura cética independente, 2026-10-27): instructions entram no contexto
só no Claude Code (corte em 4.096 chars) e no Gemini CLI; no Codex há bug
aberto (#29097); app do Claude e Cursor não documentam. Prompts como `/`:
Claude Code `/livestry:nome (MCP)` ou `/mcp__livestry__nome`; Gemini CLI
`/nome` (renomeia pra `/livestry.nome` em colisão); Codex **não implementa**
prompts (#5059); app e Cursor sem doc. OAuth remoto: sim nas cinco. App do
Claude em Team/Enterprise: só Owner cadastra o conector, uma vez. Tabela
completa no anexo B do PRD.

**Padrões.** Achado novo: o MCP tem extensão oficial
`io.modelcontextprotocol/skills` (SEP-2640, final 2026-09-13; `skills/list`,
`skills/get`, `resources/read` com `skill://`, manifesto com SHA-256),
formato de conteúdo = agentskills.io. Nenhuma das cinco ferramentas suporta
ainda (matriz: ChatGPT web, fast-agent, MCP Inspector, parciais). Decisão:
v1 expõe as skills também como resources `skill://`; os métodos da extensão
ficam pra quando o SDK TypeScript tiver suporte (questão em aberto no PRD).
`skillsprotocol.com` (perguntado por msilva) descartado: protocolo próprio
JSON-RPC, v0.1, anônimo, sem adotantes, manifesto `skill.toml` que conflita
com a regra de nome do agentskills.io.

## O que continua em aberto

- Custo do D1 pro registro de uso; se e quando implementar os métodos da
  extensão Skills do MCP — as duas únicas questões abertas no PRD.
- Gate de qualidade real pro caso `pm-linear` — a `PRO-902` definiu quem
  testa/com que dados/critério, mas esta página não leu o resultado dela
  ainda. *(unverified: ingerir a issue fechada.)*
- Governança/escopo por time — fora do escopo da v1 por decisão; volta se
  aparecer skill que só um time deva ver.
- Mecanismo de sync local (coldstart vs. serviço persistente) — **superado
  pela v1**: o catálogo entra via `instructions`/descrição de tool, sem
  hook nem sync. Mantido aqui como histórico.
- O PRD ainda não foi lido por Gabrielle (lead do projeto) nem por Carolina
  (criadora da issue) — publicado hoje.

## Relacionado

- [[Packaging as skills]]
- [[A6 Curador deve padronizar e sinalizar defasagem de skills]]
- [[Agent Flow]]
- [[Vitrine de IA]] — plataforma em produção com "Skill" já no modelo de
  dado, exibição desligada; ver achado acima.
