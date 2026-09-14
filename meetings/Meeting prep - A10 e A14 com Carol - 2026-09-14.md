---
type: meeting-prep
status: draft
updated: 2026-09-14
date: 2026-09-14
aliases: [carol a10 a14 prep, prep carol setembro, pauta carol a10 a14]
tags: [agents, carol, a10, a14, meeting-prep, portfolio, pm-agent]
---
# A10 e A14 com Carol — 2026-09-14

Objetivo: mostrar a ela o A10 e o A14, e entender que comportamentos
queremos que eles tenham — perspectiva de produto, domínio dela. Reunião
de **15 min**. Ela não tem contexto técnico prévio sobre os dois agentes.

Grounding técnico auditado direto no código (`livemode-fluxo-agentico`,
`a10/`, `a14/`) nesta mesma sessão de prep — ver também
[[Fronteira A10×A14 (informação e métricas)]] pro desenho de papel mais
amplo (o que cada um deveria fazer versus o que já está implementado).

## Pauta (tela/condução) — 15 min

**1. Intro** *(1 min)*
A10 e A14 só sinalizam, não decidem. A10 = radar de risco de portfólio
(não aprova/recusa demanda, não realoca capacidade, não recomenda encerrar
iniciativa — isso é decisão humana hoje). A14 = acompanha execução de
entregas já aprovadas. Critérios calibrados de forma técnica — hoje
validando com visão de negócio dela.

**2. Critérios atuais** *(5 min, mostrar rápido)*

Abrir com exemplo real antes da lista abstrata — **Airtable GC**, dado
auditado em 2026-09-04 (conferir se ainda bate antes da reunião, pode ter
mudado): consome **15,4% da capacidade ativa** do time; **nenhuma issue
ativa tem prioridade definida** (não é "prioridade baixa" — é campo vazio,
ambiguidade real do dado); **28 das 35 issues ativas têm descrição curta
demais**. Três critérios diferentes (concentração, prioridade desalinhada,
escopo mal definido) batendo na mesma iniciativa de uma vez — bom gancho
pra mostrar como os sinais se cruzam na prática, não só em teoria.

*A10 — por iniciativa:*
- **Estagnação** — sem entrega há muito tempo (~14 dias)
- **Concentração de esforço** — puxando fatia desproporcional da capacidade do time (~30%)
- **Prioridade desalinhada** — esforço não bate com a prioridade declarada
- **Escopo mal definido** — tarefas com descrição vaga demais
- **Fila represada** — trabalho acumulando (ou quase esvaziando) de forma anormal
→ status: saudável / atenção / crítico

*A14 — por projeto:*
- **Atraso de prazo** — passou da data alvo sem concluir
- **Código parado** — trabalho pronto esperando tempo demais pra ser integrado (~2 dias)
- **Entregue sem integrar** — marcado como concluído, mas código ainda não integrado
→ status: no prazo / em risco / fora dos trilhos

**3. Três perguntas-chave** *(8 min)*
- Esses critérios são os que importam pra decisão de vocês, ou falta/sobra algo?
- Hoje só medimos se saiu no prazo, não se resolveu o problema de negócio — vale priorizar fechar isso?
- Se isso evoluir de "radar" pra "decisor" (recomendar realocar capacidade, encerrar iniciativa), quem toma essa decisão — o agente sugere e uma pessoa aprova, ou algum nível pode ser automático?

**4. Fechamento** *(1 min)*
Definir dono do próximo ajuste.

## Material de apoio — mecânica de cada critério

Não ler ao vivo — usar só se ela perguntar "como isso é calculado".

**Concentração de esforço:** conta quantas tarefas estão em aberto naquela
iniciativa e divide pelo total de tarefas em aberto de todo o time. Uma
iniciativa com 30 das 100 tarefas abertas do time "consome 30%".
Importante: é contagem de tarefas, não esforço/tempo — uma tarefa gigante
e uma de 10 minutos contam igual. Não existe estimativa de tamanho
confiável no Linear hoje, então o sistema nem tenta medir isso.

**Estagnação:** dias desde a última tarefa marcada como concluída em
qualquer projeto da iniciativa. Sem essa marcação, o sistema não tem como
saber que "andou".

**Prioridade desalinhada:** cada tarefa no Linear tem um campo de
prioridade (Urgente/Alta/Média/Baixa, ou vazio). O sistema tira a média
das tarefas abertas da iniciativa e compara com o esforço que ela recebe
(a concentração acima). Recebe muito esforço com prioridade média baixa
(ou o contrário) → sinaliza. Se ninguém preencheu o campo, o sistema não
confunde "vazio" com "baixa prioridade" — trata como "sem dado" (ambiguidade
real, já observada em produção no caso Airtable GC).

**Escopo mal definido:** olha o texto da descrição de cada tarefa e avalia
se está curta/vaga demais perto do título — sinal de que ninguém detalhou
o que precisa ser feito antes de começar.

**Fila represada:** conta quantas tarefas estão em cada fase (esperando
pra começar vs. em execução) e julga se a fila de espera está crescendo
muito mais rápido que a capacidade de executar (ou o oposto: fila quase
vazia, risco de faltar trabalho definido em breve).

**Atraso de prazo:** cada etapa (milestone) tem uma data alvo. Passou da
data sem concluir → sempre vira alerta, sem exceção (regra quase binária,
diferente do julgamento de contexto que o A10 faz).

**Código parado:** quando alguém termina o código de uma tarefa, ele fica
esperando revisão antes de entrar no sistema ("PR aberto"). Esperando
tempo demais (~2 dias) → sinal de gargalo na entrega, mesmo que a tarefa
pareça andando.

**Entregue sem integrar:** a tarefa foi marcada como concluída na
ferramenta de gestão, mas o código dela ainda não entrou de fato no
sistema — inconsistência: marcaram como pronto cedo demais, ou esqueceram
uma etapa. Único sinal 100% determinístico do A14, calculado em Python,
nunca julgado pelo LLM.

## Notas pra levar em mente, não necessariamente falar

- Thresholds (14 dias, 30%, 2 dias) são "chute inicial" — nunca calibrados
  contra dado real. Boa munição se a discussão de calibração render.
- O sistema passou de "2 iniciativas piloto" pra cobrir o portfólio inteiro
  do time recentemente — contexto de fundo, não entrou como pergunta
  formal na pauta de 15 min por decisão do msilva (pergunta B sobre
  radar→decisor cobre a mesma preocupação de forma mais produtiva).
