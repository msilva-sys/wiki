---
type: meeting
status: stable
updated: 2026-10-07
date: 2026-10-07
attendees: [Matheus Silva, Daniel Robillotta]
transcription_confidence: low
aliases: [daniel robillotta inventario, reuniao daniel cfo]
tags: [liveauth, governance, api-keys, inventory, auth]
---

# Daniel / Matheus — Inventário de Processos e Governança de Auth — 2026-10-07

Primeira reunião com [[Daniel Robillotta]] — novo na Livemode, liderando a
pedido do CFO [[Zoca]] um mapeamento de processos/ferramentas/API keys em
uso pelas áreas. msilva demonstra a POC do [[LiveAuth]]; os dois encontram
overlap entre as duas frentes e combinam seguir em paralelo por ora,
juntando os esforços depois.

**Confiança de transcrição baixa** — canal único de microfone, sem
diarização entre Daniel e msilva. Atribuição abaixo reconstruída por
conteúdo e confirmada por msilva em chat (2026-10-07).

## Decisions

Nenhuma decisão formal fechada — primeiro alinhamento entre duas
iniciativas paralelas.

## Commitments

- Daniel: reunião no dia seguinte (2026-10-08) com seu time, pra definir
  atividades e cronograma do mapeamento.
- Daniel: marcar reunião com o time da Gabrielle (e possivelmente com
  msilva) pra alinhar como as duas frentes vão se conectar.
- msilva: validar a POC do [[LiveAuth]] com Carolina e Gabrielle antes de
  ampliar o uso — open issue já existente, reafirmado aqui.
- Cada time: levantar suas próprias chaves de API ativas e sinalizar quais
  são críticas vs. descartáveis, em conjunto com o TI de cada área — pedido
  de Daniel.

## Open questions

- Como o [[LiveAuth]] (autenticação/autorização) e o inventário de
  processos/API keys do Daniel vão se conectar — sem modelo definido,
  "vai tocando constantemente" até decidir.
- Se Daniel incorpora o escopo de processos/API keys dentro do próprio
  LiveAuth ou mantém como esforço separado.
- Grau de overlap entre o inventário do Daniel e a [[Vitrine de IA]]
  (projeto da Carolina Bezerra) — levantado em chat, sem resposta fechada.

## Facts stated

- Daniel: não existe hoje padrão de auditoria/proteção de credenciais entre
  as áreas; poucos projetos têm segundo fator, a maioria só login
  @livemode.
- Daniel: um bot do N8N ficou ligado e foi acionado sem querer dentro de um
  grupo do Slack — exemplo citado do problema de automações esquecidas.
- Daniel: uma chave da API da OpenAI consumiu muito crédito sem
  necessidade; descoberta só quando o TI questionou o gasto, dono real da
  chave identificado depois.
- Daniel: a iniciativa de mapeamento de processos/ferramentas foi puxada
  pelo CFO [[Zoca]].
- Daniel: já construiu uma visualização em grafo do mapeamento (quadrados =
  projetos, bolinhas = processos), com servidor MCP pra um agente consultar
  os dados. Acha times usando as mesmas bases em paralelo, isolados, sem se
  falar — gera conflito de nomenclatura/cálculo.
- Daniel: o time de Transfer de Patrimônio já usa e atualiza os processos
  que ele mapeou.
- Daniel: viu que Gabrielle já tem algumas chaves de API organizadas,
  sabendo onde está quase tudo.
- msilva: está desenhando algo parecido (grafo/memória de agente) em outro
  projeto — referência provável ao [[Cognee como memória dos agentes e do time|Cognee]];
  convergência só apontada em chat, não aprofundada na reunião.
- msilva: tem um projeto (Fluxo Agêntico, provável) que usa vários agentes
  sob uma chave de API compartilhada, dada pelo time de projetos — corre
  risco de ficar sem crédito por ser de uso exclusivo de LLM.
- msilva, sobre o LiveAuth: POC em estágio inicial — apps se cadastram no
  hub, usuário é redirecionado pro login central, hub devolve as permissões
  (leitura/edição) pro projeto de origem. Objetivo: evitar reimplementação
  de SSO e permitir acesso granular por pessoa pra times externos
  (comercial, jurídico).

## Notable quotes

- Daniel: *"Cara, isso é gravíssimo. A gente tá trazendo pra esse
  inventário esse controle também de chave de API."*
- Daniel: *"A gente tem essa cultura da autonomia, que eu acho super
  certo... mas a gente tem que ter certa centralização em algumas coisas."*

## Relacionado

- [[LiveAuth]]
- [[Daniel Robillotta]]
- [[Vitrine de IA]]
- [[Zoca]]
