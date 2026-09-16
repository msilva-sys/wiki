---
type: decision
status: active
updated: 2026-09-15
date: 2026-09-15
aliases: [identidade dos agentes, resposta imediata do A1, personas dos agentes]
tags: [agent-flow, a1, a2, ux, design]
---

# Agente voltado a usuário externo precisa de identidade própria e resposta imediata

De [[2026-09-15 Proxy e Fluxo Agêntico com Luís]]. Duas regras de produto pro
[[Agent Flow]], decididas por Luís com convicção forte — as *"poucas coisas
que ele não abriria mão"*.

## A decisão

1. **Cada agente que fala com usuário externo ao time tem identidade e
   persona próprias** — nome, foto, jeito de escrever. Não existe um
   "agente genérico" respondendo por todos no mesmo canal. Exemplo dado por
   Luís: o "Mateuzinho" que recebe a mensagem, responde "tô vendo", dá
   orientação, avisa que abriu a issue ou que já executou — um agente é o
   atendente, outro é quem repassa, outro é o teacher, cada um com sua cara.
2. **A1 Receptor responde ao usuário quase na hora** (poucos minutos),
   mesmo que a decisão real de classificação/roteamento venha depois no
   fluxo. Não dá pra adiar essa resposta pro fim do ciclo assíncrono
   esperando o A2 decidir o que fazer.

## Por que

Luís argumenta que essa decisão **não dá pra adiar**: no momento em que uma
mensagem chega num canal onde o usuário fez um pedido, alguém já precisa
responder algo — mesmo que seja só "calma aí, tô vendo aqui". Se quem
responde primeiro é um "agente genérico" e só depois o agente com persona
própria (ex.: o teacher) responde de fato, a decisão de identidade já foi
tomada por omissão — o genérico virou a cara do time sem ninguém escolher
isso.

Matheus propôs simplificar adiando a decisão: o A1 Receptor não responderia
nada, só decidiria o que fazer, e quem quer que fosse chamado no fim do
loop (Linear, teacher, etc.) responderia. Luís aceita a lógica de
simplificação, mas rejeita essa instância específica dela: adiar a resposta
é **adiar a decisão de quem responde**, não evitá-la — e o custo de
demorar é imediato e mensurável (usuário de fora esperando).

## Análise

Contrapõe diretamente ao princípio de simplificação que o próprio Luís usa
em outros pontos da mesma conversa (ex.: "bota tudo num agente só, quebra
depois" pro A1/A2). A diferença que ele traça: simplificar arquitetura
(quantos agentes existem) é aceitável enquanto não há evidência de que
precisa de mais; simplificar a experiência do usuário externo (tempo de
resposta, quem aparece) não é, porque o custo de errar aparece na hora,
não em refatoração futura.

## Consequências

- O A1 Receptor precisa de uma implementação de acknowledgment rápido
  (mesmo que trivial: "recebi, já te aviso") **antes** do resultado da
  classificação/decisão do A2 estar pronto — não pode ser um subproduto
  natural do pipeline, precisa ser desenhado como etapa própria.
- Toda a discussão de nome/foto/persona por agente vira trabalho de design
  de produto explícito, não um detalhe de implementação a decidir depois —
  ver [[Agent Flow]].

## Em aberto

Não ficou definido *quantos* agentes precisam de persona própria (só os que
falam com usuário externo — A1, e possivelmente A4 Teacher — ou também os
que ficam por trás, como A2/A3?), nem quem desenha as personas em si (nome,
foto). Não discutido nesta conversa.
