---
type: synthesis
status: active
updated: 2026-10-21
aliases: [opinião do Luís sobre os agentes, veredito do Luís, Luís e o Fluxo Agêntico]
tags: [agent-flow, luis, arquitetura, adr, feedback, mcp, tokens]
---

# O que Luís acha do projeto dos agentes

Pergunta de msilva em 2026-10-21: *"Qual a opinião do Luís sobre como está o
projeto dos agentes?"*. Respondida cruzando a wiki com o que ela **não**
tinha: as mensagens dele no Slack (set–out/2026), o raw ainda não ingerido
de 2026-09-22 (`raw/Agente de portfólio - 2026_09_22 15_00 GMT-03_00 -
Anotações do Gemini.md`), a issue `PRO-1005` e uma verificação adversarial
das atribuições de [[2026-10-20 Matheus - Luís (arquitetura dos agentes, ADR e tokens)]]
contra os dois raws (diarização colapsada). Luís sai da empresa no fim de
outubro/2026 — esta é, na prática, a avaliação final dele.

## Resposta curta

Luís **não condena o projeto**: em 2026-10-20 diz que o repo *"vai
funcionar"*, que *"tu vai conseguir evoluir com isso"*, que a reorganização
*"não tá ruim"* e aprova peças específicas (ports, camada `review`, intenção
do MCP). O que ele considera errado é **estrutural e de método**: o código
não passa no teste de portabilidade (*"parece que tá tudo ainda grudadão"*),
não existe template de agente, e a arquitetura vive na cabeça de msilva em
vez de ser uma ADR "sagrada" no repositório — por isso o modelo "desanda" e
msilva acaba revisando tudo. Prognóstico dele: dívida alta cobrada em
*"meses, um ano"*. A causa-raiz, para ele, não é o projeto: *"talvez o mais
importante de tudo não é o projeto, é a dificuldade que você está tendo de
dar um direcionamento claro para ele."* A trajetória é consistente desde
agosto: otimista com o potencial, crítico do processo (opções antes de
decidir, produto antes de código, o mais simples primeiro, saída que
recomende ação), e confiante em msilva como quem herda tudo (*"Você é o
desenvolvedor mais experiente do time agora, cara."*).

## Linha do tempo da opinião

### Agosto — otimista com o potencial, crítico do "como antes do o quê"

- **2026-08-18**: descrição do A5 *"muito genérico"*; pede discovery antes de
  desenho e estabelece a norma de
  [[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
- **2026-08-20**: *"você ainda não sabe o que que você quer, me parece"* —
  msilva preso no "como" (memória, LangGraph, Claude Code) antes do "o quê"
  ([[2026-08-20 1-1 Matheus - Luís]]). No walkthrough enxuga os 14 agentes
  e prescreve ator → input → output
  ([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]). Sobre
  framework: *"não caia na besteira de falar 'vou fazer em LangGraph'"*.
- **2026-08-24**: *"eu tô bem bem bem bem otimista"* com o fluxo agêntico
  ([[2026-08-24 1-1 Matheus - Luís]]).
- **2026-08-26**: processo indo bem, *a intensidade das correções é parte do
  processo*; agente como funcionário novo precisa de objetivo e entrega de
  valor claros ([[2026-08-26 1-1 Matheus - Luís (agentes A10-A14)]]).
- **2026-08-31** (Slack `#projetos-arquitetura-de-agentes`): pede, como
  necessidade de backlog, um agente que monitore uma release por X horas.

### Setembro — fronteira, formato e simplicidade; a primeira queixa de ruído

- **2026-09-01**: A10 comentando por issue *"polui a issue"* →
  status update agregado ([[2026-08-24 Build A10 and A14 together, PoC first]]).
- **2026-09-02**: dashboard do A10 desce a nível de tarefa; misturar
  portfólio com PM deixa os agentes *"capengas"* →
  [[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]].
- **2026-09-04** (Slack): sugere testar **CrewAI** como segunda alternativa de
  implementação e o Agent Designer do Gemini Enterprise, para apresentar à
  Carol *"um comparativo de soluções entre a nossa atual em evolução e com
  o CrewAI"* — a origem do pedido que volta em 2026-10-13.
- **2026-09-11** (Slack DM): pede renomear *"M1 - PoC A10 + A14"* nos reports:
  *"Esses códigos não devem ajudar a entenderem o valor do que vc tá
  fazendo."*
- **2026-09-15**: inegociáveis — persona própria por agente voltado a usuário
  externo e resposta em minutos; fora isso, *"bota tudo num agente só, quebra
  depois"* ([[2026-09-15 Proxy e Fluxo Agêntico com Luís]]).
- **2026-09-16** (Slack `#projetos-arquitetura-de-agentes`, **não estava na
  wiki**): *"Tô achando que essas mensagens que estão chegando nos canais
  estão sendo pouco efetivas e estão 'flodando' os canais [...] caso
  contrário as mensagens dos agentes e esses alertas vão acabar virando uma
  dor de cabeça ao invés de um benefício."* E: *"Diariamente não me parece
  fazer sentido o conteúdo como está chegando. Não tô achando útil ainda
  (visão pode estar errada)."* Gabrielle concorda na thread.
- **2026-09-22** (raw **não ingerido**, reunião do cálculo de saúde com Gabi e
  Carol): não ataca o que foi construído — valida os sinais *fluxo
  represado* e *escopo descontrolado*, gosta da entrada de projetos via
  agente e diz *"em nenhum momento eu quis dar a entender que o que você tá
  pensando tá errado [...] o que a gente quer é muito o que você tá
  falando [...] não agora"*. A crítica é de enquadramento: msilva misturou
  saúde (execução) com priorização (backlog) e inventou eixos novos em vez
  de partir do modelo do Airtable — *"não consegui conectar"*, *"tô um
  pouco perdido"*, *"a gente tá rodando em círculo"*, *"a tua pergunta me
  preocupou"*. Prescrições: *"o mais simples possível"*, *"o problema que a
  gente quer resolver agora é como que a gente prioriza o que está em
  backlog. Ponto."*, muitas iterações curtas em vez do agente perfeito,
  critérios fáceis de trocar, e o agente **não** atribui valores — só
  sinaliza no Slack e o humano preenche (*"por medo, por cagaço, talvez por
  simplicidade"*). No mesmo dia, por DM: *"Qual é o objetivo? Com o que
  gostaria de sair do papo para considerar que foi sucesso?"* e *"Essa
  reunião do Agente de Portfólio poderia ser de 30min? Sendo produtiva, é
  claro."*
- **2026-09-23** (Slack `#privado-pulso`, sobre um update do A14): *"Estou na
  mesma página que Gabi e Carol [...] o agente deveria ser capaz das 2
  coisas [update e ação], mas em momentos e com objetivos distintos."* E a
  prescrição de método: deixar *"muito claros os problemas e necessidades
  que o agente precisa atender, pra aí sim talvez simular formatos de
  respostas diferentes para cada caso e aprovar com o time"* — como
  wireframes antes de um app.
- **2026-09-25** (Slack `#projetos`, thread da newsletter multiplayer): a
  leitura mais estratégica dele sobre o projeto — *"se acertarmos a mão na
  criação do fluxo agêntico que estamos tocando, poderemos ter um caminho
  para nossos agentes, e depois possivelmente outros de outras áreas,
  ficarem acessíveis da mesma forma dentro de uma solução dessas."* Na
  mesma mensagem: *"Uma preocupação que tenho é o consumo excessivo de
  tokens."*

### Outubro — saída, método e, no fim, a arquitetura

- **2026-10-01** (Slack DM + [[2026-10-01 1-1 Matheus - Luís]]): o exercício
  *"pegar 2 dos reports que o agente enviou e me apresentasse o que fazer com
  cada uma das informações que ele mandou"*; quando msilva responde, ele
  pergunta *"Isso foi sua leitura ou leitura de algum agente?"*. Veredito:
  os agentes **descrevem, não recomendam**; exceção boa, o Pulso. Manda um
  formato alternativo (Resumo / Recomendação / Progresso). Na mesma 1:1,
  três violações da norma de comunicação (intake, Cloudflare Workers,
  Supabase) e o corolário "stack simples e padronizada".
- **2026-10-02** (Slack): à proposta de somar *urgência* ao ROI — *"Antes de
  dar opinião. Chegamos a considerar uma regra/fórmula mais simples?"*
- **2026-10-05**: relatado por msilva a Carolina — reports *"expositivos, não
  propositivos"* ([[2026-10-05 Carolina - Matheus (feedback reports A14)]]).
- **2026-10-07**: transição; Agent Flow fica com msilva como dono único, MCP
  *"não é prioridade"* ([[2026-10-07 Divisão de projetos na transição de Luís]]).
- **2026-10-08** — **revisão de arquitetura sem página na wiki.** Duas calls
  à tarde (15:40 e 17:46, marcadas por DM). O único registro é a issue
  `PRO-1005`: *"Surgiu internamente, a partir da revisão de arquitetura do
  líder técnico no repositório (2026-10-08), que apontou a estrutura atual
  como difícil de manter."* Reação de msilva na mesma noite, por DM:
  *"deixei MUITA dívida técnica mesmo"*. Primeiros commits do plano de
  reorganização às 23:27. `(unverified: não há transcript; o "uma bagunça"
  lembrado em sessões anteriores não aparece em nenhuma fonte escrita)`
- **2026-10-09**: msilva apresenta o plano de reorganização; práticas revistas
  ([[2026-10-09 Matheus - Luís (práticas de IA e governança)]]).
- **2026-10-13**: pede a comparação LangGraph vs CrewAI
  ([[CrewAI vs LangGraph para o Fluxo Agêntico]]) — continuação do 04/09.
- **2026-10-20**: a revisão final, abaixo.

## O veredito de 2026-10-20

Fonte: [[2026-10-20 Matheus - Luís (arquitetura dos agentes, ADR e tokens)]]
e os dois raws. Toda atribuição é inferida por contexto; os itens abaixo
foram **verificados** um a um contra o raw (18 de 20 confirmados, ver
*Correções*).

**O que ele aprova.** *"A tua reorganização não tá ruim."* *"Esse modelo que
já tá muito mais organizado."* Separação domínio/infra: *"me incomoda um
pouco, mas não tanto quanto antes."* Ports: *"Perfeito."* Camada `review`:
*"pelo que você tá explicando, é para qualquer agente."* MCP: *"a intenção
é boa"* — entregar só tools ao Claude perderia *"o coração de cada
agente"*; rotas por objetivo são *"algo que a gente quer explorar [...]
falta pensar, planejar e talvez testar"*. E o veredito pragmático: *"Aqui vai
funcionar, tá? Tu vai conseguir evoluir com isso aqui."*

**O que ele considera errado.** O teste de portabilidade (*"deletar tudo que
é específico de Linear e levar para outra empresa. Dá para fazer isso?"*)
falha: *"Parece que tá tudo ainda grudadão."* `core/outcomes` é *"coisa
específica dos agentes que a gente tá construindo"*, não genérico de agente;
`infra` está *"meio saco de gato"*; os use cases agruparam funções, não
casos de uso; a integração com Bugsnag pode ter sido *"inventada"* pelo
modelo (não verificada). O teto: *"Se você tiver 20 agentes aqui, tu não tem
um template de agente, você não tem uma forma de configurar um agente."*
Prognóstico: *"vai começar a cobrar essa dívida alta em sei lá se meses, um
ano [...] em seis meses você pode pegar e falar: 'agora eu preciso voltar
uma arquitetura correta'."*

**A causa-raiz, segundo ele, não é o código.** *"Você se empolgou para ver
algo funcionando antes de ter aquela base muito bem validada"* — padrão que
ele diz já ter visto em outros fluxos dos dois, e assume metade: *"é mole
meu também de não ter falado 'não desenvolve nada, me mostra a arquitetura
primeiro'."* *"Você não torna essa decisão de arquitetura sagrada."* A
arquitetura principal não está no repositório (msilva: está no second brain
dele) — *"Como é que ele vai seguir a arquitetura que você, como arquiteto,
quer que ele siga, se você não tá disponibilizando a arquitetura para
ele?"* *"Papel do arquiteto é garantir que a arquitetura está sendo seguida
— e você não está fazendo isso."* Por cima disso, msilva economizava token
com esforço médio numa conta inadequada sem avisar: *"Tu não pode economizar
token"*, *"em que momento você me falou 'tô estourando token'?"*, e a CLI
sem Fable/Opus 5.5: *"daí não dá nem para discutir, tem que resolver isso."*

**O tom.** Didático e socrático no walkthrough (*"não é uma crítica ainda"*,
*"aqui é uma conversa nossa de troca de ideia"*), duro em dois momentos
(*"numa entrevista tu não passa [...] e tu não pode aceitar isso"*; *"você
tá admitindo que você não tá sabendo usar uma Ferrari"*), com autocrítica
explícita (*"eu não sei ensinar as pessoas ainda"*, *"eu vou cometer os meus
erros também"*) e confiança declarada: *"Isso você tem que levar depois, vai
ser um pouco de responsabilidade sua por ser o cara mais técnico."*

**O que ele prescreve.** (1) ADR sagrada no repositório, com "puxadinhos"
registrados em ADR própria; (2) arquiteto garante a arquitetura, não revisa
tudo; (3) não economizar token — plano de 200, que ele oferece repassar na
renovação; (4) Ultra Code só com plano lido e confirmado; (5) refazer o
exercício *por fora* do repo a partir do desenho do quadro — um *"projeto
open source na caixa"*, *"70% talvez esteja bom"*; (6) um **projeto
conjunto**, do zero: *"a única forma de eu conseguir tentar te passar é a
gente fazer alguma coisa junto"*. E a prioridade acima do projeto: *"a
coisa mais importante, independente do projeto, é você conseguir ficar
confortável desenvolvendo com o Claude."*

## O que ele nunca avaliou

Qualidade funcional das saídas do A10/A14 depois dos ajustes de outubro,
prompts, testes, Langfuse e o prioritizer — a revisão de 20/10 é de
estrutura e método, e a gravação do walkthrough corta aos 13 min, antes das
pastas de cada agente. A posição dele sobre LangGraph vs CrewAI nunca foi
registrada; só os pedidos (04/09, 13/10).

## Correções à página de 2026-10-20 (verificação de 2026-10-21)

- A pergunta *"Em que momento o desenho do quadro virou a solução atual?"*
  **não existe no raw** — é paráfrase de dois trechos separados por ~20 min
  (00:04:58 e 00:24:10). Só *"muito mais filosófico"* é verbatim.
- *"Brain"* é palavra de **msilva** (*"tá na minha, no meu second brain"*),
  não diagnóstico de Luís; Luís perguntou *"Cadê a arquitetura principal da
  solução?"* e concluiu a partir da admissão.
- *"Talvez esteja muito bagunçado"* (00:17:22) é **provavelmente msilva**,
  sobre o próprio doc de reorganização; o turno de Luís começa em *"Faz um
  exercício depois"*.
- *"Provavelmente devolveria uma melhor"* (pasta `infra`) não é verbatim; o
  raw diz *"ele vai te dar uma que você vai olhar e vai falar: 'Putz, eu
  gosto dessa organização'"*.

## Em aberto

- Projeto conjunto proposto em 20/10: sem data, janela fecha no fim de
  outubro.
- MCP: chat streaming vs rotas por objetivo — ele sem opinião formada.
- Plano/conta de tokens (renovação dia 20) e CLI: compromissos de msilva de
  20/10, ainda sem issue no Linear.
- Bugsnag: verificar se a integração é a oficial.
- A revisão de 2026-10-08 não tem página — só `PRO-1005`; e o raw de
  2026-09-22 segue sem ingest (é o único raw de reunião não ingerido).

## Fontes fora da wiki

- Slack `#projetos-arquitetura-de-agentes`: 2026-08-19, 08-31, 09-04, 09-16,
  10-02. `#privado-pulso`: 2026-09-23. `#projetos`: 2026-09-25. DM
  Luís↔Matheus: 2026-09-11, 09-22, 10-01, 10-08.
- `raw/Agente de portfólio - 2026_09_22 15_00 GMT-03_00 - Anotações do Gemini.md`
  (diarização parcial: Luís remoto tem rótulo próprio; Matheus, Gabi e
  Carol dividem o mesmo microfone).
- `raw/2026-10-20 Luis - Matheus (18h19 walkthrough do repo dos agentes) - Transcript.md`
  e `raw/2026-10-20 Luis - Matheus (18h42 arquitetura, ADR e tokens) - Anotações do Gemini.md`.
- Linear `PRO-1005` (Reorganização do código do Fluxo Agêntico), criada
  2026-10-09, concluída 2026-10-14.
