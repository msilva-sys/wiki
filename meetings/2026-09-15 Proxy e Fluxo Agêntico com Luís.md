---
type: meeting
status: stable
updated: 2026-09-16
date: 2026-09-15
attendees: [Matheus Silva, Luís Fernandez]
aliases: [proxy e fluxo agêntico com luís, reunião luís 15/09]
tags: [luís, airtable-proxy, agent-flow, a1, a2, skills, livescript]
---

# Proxy e Fluxo Agêntico com Luís — 2026-09-15

Continuação de [[Meeting prep - Proxy e Fluxo Agêntico com Luís - 2026-09-15]].
~54 min, diarização por turno confiável (ao contrário das reuniões recentes
com Carolina/Gabrielle). Duas pautas: estado do proxy (skills de conexão,
consumidor real) e desenho do A1/A2, cujo dev começa hoje.

## Decisions

- **Cada agente do Fluxo Agêntico que fala com usuário externo tem
  identidade e persona próprias** (nome, foto) — Luís, convicção forte: não
  dá pra ser um "agente genérico" respondendo por todos, porque a partir do
  momento em que lidam com gente de fora do time, precisam ter uma cara.
  Ver [[2026-09-15 Agente voltado a usuário externo precisa de identidade
  própria e resposta imediata]].
- **A1 Receptor tem que responder ao usuário quase na hora** (poucos
  minutos), mesmo que a decisão real (classificar/rotear) venha depois no
  fluxo — não dá pra adiar a resposta pro fim do ciclo assíncrono. Mesma
  página acima.
- Yasmin não sobe nada em produção no LiveScript antes de Luís e Matheus
  revisarem juntos.
- Luís conecta o front via skill e vai pra produção logo — risco menor,
  pouco uso real ainda.
- Confirma-se (Luís também) que não vale reaproveitar os critérios antigos
  do GPT priorizador da Gabrielle — ver
  [[2026-09-15 Discovery A1 e A2 com Gabrielle]].

## Commitments

- ~~Matheus atualiza as skills `airtable-proxy-connect`/`airtable-proxy-doctor`
  pra cobrirem a autenticação por API key (`PRO-553`/`PRO-587`), que ainda
  não estava contemplada quando Luís as criou.~~ **Feito, 2026-09-16.**
- Matheus confirma qual branch carrega exatamente o diff mínimo de conexão
  ao proxy antes de repassar pra Yasmin — **verificado no repo real
  2026-09-16 (`gh api`), não resolvido como esperado**: não existe
  `airtable-observability` (lembrança errada de Matheus). São
  `feature/airtable-proxy` (sem OTel, mas também sem a autenticação por
  API key — desatualizada) e `feature/airtable-proxy-observability` (com
  OTel real **e** com os commits recentes necessários, `PRO-96`/`PRO-587`).
  Nenhuma pronta como está; decisão de como resolver (cherry-pick vs.
  aceitar o OTel) adiada — msilva priorizou os outros pontos da reunião.
  Ver [[LiveScript]].
- ~~Matheus fala direto com Yasmin (não via Luís) pra ela testar a skill de
  conexão no LiveScript, localmente, como "cobaia".~~ **Feito, 2026-09-16.**
- Matheus e Luís desenham um plano de rollback rápido pro LiveScript antes
  de qualquer coisa ir pra produção — hipótese de Luís: só variável de
  ambiente, a confirmar.
- Luís vai falar com a Gabrielle sobre a reunião de critérios novos de
  priorização de portfólio (compromisso já registrado em
  [[2026-09-15 Discovery A1 e A2 com Gabrielle]]).
- Matheus processa a conversa toda e volta com decisões pra Luís.
- Próxima conversa — Matheus apresenta o código/fluxo técnico dos agentes
  pra Luís — marcada pra sexta-feira (talvez amanhã).
- Matheus vai demonstrar o Zellij (multiplexador de terminal que usa) pro
  time numa tech session de sexta, ideia de Luís depois de ver o terminal
  de Matheus na call.

## Open questions

- Os critérios de classificação do A2 (complexidade de uma demanda) têm
  ligação real com os critérios de priorização de portfólio do A10?
  Matheus acha que sim (mesma análise de complexidade, profundidades
  diferentes); Luís acha que são coisas distintas (classificador rápido vs.
  decisão de priorização que exige mais contexto) — nenhum dos dois tem
  convicção forte. Não resolvido.
- A1 e A2 continuam sendo dois agentes ou viram um só? Luís não decide —
  sugere simplificar pra um único agente primeiro, avançar, e quebrar em
  dois só se ficar evidente que faz muita coisa.
- Mecanismo de rollback do LiveScript em produção — hipótese de variável de
  ambiente, não confirmado.
- Qual branch usar de fato pra Yasmin testar — nenhuma das duas branches
  reais (`feature/airtable-proxy`, `feature/airtable-proxy-observability`)
  está pronta como está; decisão de cherry-pick vs. aceitar o OTel ainda
  em aberto, ver Commitments acima e [[LiveScript]].

## Facts stated

- Luís: tem um fluxo pessoal parecido (LangGraph + Claude Code) já
  funcionando, pra um projeto que ele chama de "Dinda" — mas descobriu que
  não vai dar pra usar a API/assinatura do próprio Claude Code
  comercialmente; precisa ser API da Anthropic direta, com custo maior.
  Resolve a pergunta em aberto de [[Claude Agent SDK]] sobre o que ele
  estava testando.
- Luís: as skills `doctor`/`connect` já existem dentro do repo do proxy —
  usadas com sucesso ao conectar o front, sem intercorrência.
- Luís: propôs originalmente três skills — uma fixa (roda sempre, detecta
  se o projeto usa Airtable) mais duas chamáveis (`doctor`, `connect`).
- Luís: quem está puxando a central de skills do time hoje é a Carolina
  (repo `livemode`) — ele começou algo parecido antes, mas foi
  despriorizado; não vai se meter enquanto ela cuida disso, a menos que ela
  peça.
- Luís: destino ideal de longo prazo pras skills genéricas seria o admin do
  Claude Cloud (ele não tem acesso), disponível pra empresa toda
  automaticamente — mas segurou a ideia porque não está sendo envolvido
  nessas decisões de plataforma.
- Luís: corrige o entendimento inicial de Matheus sobre o escopo do
  A1/A2 — não é geral pra qualquer demanda de qualquer área da empresa, só
  pros sistemas internos do próprio time (LiveScript, Orca, proxy, etc.)
  com usuário interno.
- Matheus: usa Zellij (multiplexador de terminal), funciona bem no
  Windows — Luís pede apresentação.

## Notable quotes

- Luís: *"Se demorar 4 minutos para responder alguma coisa, já não valeu a
  pena. Essa resposta ela tem que ser muito rápida, principalmente com
  usuário de fora."*
- Luís, sobre a central de skills: *"eu não tô sendo envolvido nessas
  coisas e eu não vou ficar questionando muita coisa, não tô sendo
  envolvido, eu tô tentando fazer o que tem para eu fazer."*

## Relacionado

- [[Meeting prep - Proxy e Fluxo Agêntico com Luís - 2026-09-15]]
- [[2026-09-15 Discovery A1 e A2 com Gabrielle]]
- [[Airtable Proxy]]
- [[LiveScript]]
- [[Agent Flow]]
- [[Packaging as skills]]
- [[Luís Fernandez]]
- [[Yasmin Macedo]]
