---
type: source
status: active
updated: 2026-09-25
date: 2026-09-17
aliases: [mkt1 parte 3, vacation test, mkt1 own setup]
source: "raw/Fichamento da newsletter de mkt1 - terceira parte.md"
url: "https://newsletter.mkt1.co/p/multiplayer-ai-team-mkt1"
tags: [agents, skills, capabilities, agent-flow, claude]
---

# MKT1 — "Inside team MKT1's multiplayer AI setup"

Newsletter da MKT1, por **Emily Kramer e Halley Johnson**, publicada
**2026-09-17**. Parte 3 de 3 — desta vez o próprio time da MKT1 aplica o
framework a si mesmo (2 repos GitHub, 100+ skills, 30+ rotinas). Lida e
anotada por msilva em 2026-09-25 — a fonte crua é o fichamento dele
(`raw/Fichamento da newsletter de mkt1 - terceira parte.md`), curta desta
vez, sem imagens. Detalhes confirmados via WebFetch no artigo original.

## O gatilho de férias / computador novo

*"The 3 of us ship a lot of stuff, fast, but speed often leads to silos
and shortcuts. This is where the vacation and new computer incidents come
in."* — durante as férias do time e a troca de notebook de Emily Kramer,
o sistema multiplayer deles se mostrou preso demais a máquinas e contas
individuais.

msilva: *"Pois é, é o que acontece no nosso time de projetos aqui na
livemode."* — mesmo teste que Gabrielle e Mafê já tinham levantado, sem
ver este artigo ainda, no thread do Slack de 2026-09-24/25 (`#projetos`,
resposta ao pedido da Carol): intranet do RH nunca publicada porque quem
construiu saiu da empresa, portal de trocas da CazéTV que precisou trocar
de dono. Convergência tripla — MKT1, Gabrielle, Mafê — chegando no mesmo
teste por caminhos independentes.

## Contexto curado, não puxado inteiro a cada rodada

*"Pulling full context from many tools on every run hits plan usage
limits fast, even when most of that context wasn't actually needed for
that run. Having a more curated context layer works much better."* A
solução deles: guardar contexto em bases especializadas (Airtable, Attio)
em vez de embutir nas skills, e Claude acessa só os datasets necessários
via conexão MCP.

msilva: *"Já tinha pensado nisso. O gerenciamento do contexto, seu
retrieve, deve ser feito de maneira performática e que evite contexto
desnecessário."* — confirma, não introduz: é a mesma ideia de "narrow
fetching" já registrada em [[Packaging as skills]] e o "shared tool
layer" de [[Agents read primary sources]] (tools finas, uma por fonte,
cada agente importa só o que precisa).

## Skills que mantêm o sistema da MKT1 — resposta à pergunta em aberto

Três categorias, com os comandos reais:

- **Build & Publish** (manual, disparado por pessoa): `/skill-build`,
  `/skill-dupe-check`, `/skill-review`, `/skill-publish`,
  `/publish-mcp-skill`.
- **Audits**: `/skill-update` (manual — a pessoa roda depois de uma
  sessão), `/session-audit` (automático, semanal), `/skill-audit`
  (automático, semanal), `/claude-md-audit` (automático, mensal),
  `/team-repo-stats` (automático, semanal).
- **Scheduled Routines** (automático): `/daily-connection-status`,
  `/kramer-to-dos`, `/calendar-sync`.

msilva perguntou, lendo: *"Essas skills são disparadas manualmente, por
um gatilho, com um agente?"* — **resposta: as duas coisas, dependendo da
skill**, não uma única resposta uniforme (ver lista acima).

**Isso responde, com uma ressalva, a pergunta que ficou em aberto em
[[Packaging as skills]] e em [[Marketing teams are stuck in single-player
Claude mode. Here's how to go multiplayer]]** — como `/skill-update`
acessa o contexto da skill que mantém: **não acessa a skill em
abstrato.** O mecanismo é *"reads my past Claude sessions and proposes
edits based on corrections made, new rules stated, or errors encountered
and solved."* Ou seja: a pessoa usa a skill numa sessão, depois roda
`/skill-update` manualmente, e ele lê **aquela sessão específica**, não
a skill nem seu histórico completo. É observação humana guiada, não
detecção autônoma de defasagem — mais modesto do que "a skill sabe que
está desatualizada sozinha". A detecção de *staleness* sem gatilho
humano existe, mas é mais rasa: `/skill-audit` (duplicatas, semanal) e
`/team-repo-stats` (lê histórico do git, sinaliza atividade vs.
inatividade — por edição, não por conteúdo).

## Por que importa pro Fluxo Agêntico

- **Fecha o mecanismo, não o problema**: a pergunta "como uma skill de
  manutenção vê o contexto da skill mantida" tinha resposta desconhecida
  nas Partes 1 e 2. Agora sabe-se que a resposta real da MKT1 é modesta —
  session-scoped, humano no gatilho — não uma solução geral de
  auto-atualização. Vale atualizar [[Packaging as skills]] e a synthesis
  [[A6 Curador deve padronizar e sinalizar defasagem de skills]] com essa
  descoberta: mesmo quem inventou o framework não automatizou totalmente
  a manutenção.
- **Corrobora o "shared tool layer"** já decidido em [[Agents read
  primary sources]] — narrow fetching via MCP contra bases especializadas
  é a mesma resposta, chegada de forma independente.
- **Convergência tripla no teste de férias/computador** — reforça, sem
  decidir nada novo, a preocupação já registrada por Gabrielle e Mafê no
  Slack (2026-09-24/25) e a restrição de "agência do time" que msilva
  levantou lendo a Parte 2.

## Open questions

- `/skill-update` sendo manual e session-scoped, quem garante que a
  pessoa efetivamente roda ele depois de cada sessão relevante? O artigo
  não fala de enforcement — parece depender de hábito, não de mecanismo.
- Isso muda a resposta de autoridade em aberto na synthesis de A6? Se até
  a MKT1 mantém humano no gatilho pra manutenção, é argumento a favor de
  A6 propor/sinalizar em vez de atualizar sozinho — ainda não decidido,
  só mais um dado a favor de um lado.
