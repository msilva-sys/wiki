---
type: meeting
status: stable
date: 2026-10-20
updated: 2026-10-21
attendees: [Matheus Oliveira da Silva, Luís Fernandez]
source: "raw/2026-10-20 Luis - Matheus (18h42 arquitetura, ADR e tokens) - Anotações do Gemini.md"
source2: "raw/2026-10-20 Luis - Matheus (18h19 walkthrough do repo dos agentes) - Transcript.md"
aliases: [1-1 20 de outubro, conversa da ADR, papo do token]
tags: [agent-flow, arquitetura, adr, claude-code, tokens, harness]
---

# 2026-10-20 Matheus - Luís (arquitetura dos agentes, ADR e tokens)

Evento de calendário único, 18:00–19:00, Sala Handebol + Meet, gravado em
**duas partes**: 18h19 (13 min, walkthrough do repo dos agentes) e 18h42
(32 min, a conversa sobre método). Última conversa técnica longa com Luís antes
da saída dele — ver [[2026-10-07 Divisão de projetos na transição de Luís]].

> [!warning] Diarização colapsada — toda atribuição aqui é inferida
> Os dois arquivos em `raw/` atribuem **100% das falas** a "Matheus Oliveira da
> Silva", inclusive as inequivocamente de Luís (*"Você é o desenvolvedor mais
> experiente do time agora, cara"*, *"por que que o Luiz ganhou o cocozão?"*).
> As Anotações do Gemini herdaram o erro e atribuem a msilva a crítica que Luís
> fez ao trabalho dele — o resumo automático daquele arquivo **não deve ser
> lido como fonte de atribuição**. Tudo abaixo é reatribuído por inferência de
> contexto (quem explica o código é msilva; quem questiona e diagnostica é
> Luís) e marcado `(unverified: diarização colapsada)`. Mesmo tipo de problema
> já registrado em [[2026-08-25 Bossabox AI transformation discovery]] e no 1:1
> de 2026-09-10.

> [!note] Correções de atribuição — 2026-10-21
> Verificação adversarial dos 20 claims desta página contra os dois raws,
> registrada em [[O que Luís acha do projeto dos agentes]]: 18 confirmados como Luís.
> Ajustes: (1) a pergunta *"Em que momento o desenho do quadro virou a
> solução atual?"* (Open questions) é paráfrase de dois trechos (00:04:58 e
> 00:24:10), não verbatim — só *"muito mais filosófico"* é literal; (2)
> *"brain"* é palavra de msilva, não diagnóstico de Luís; (3) *"talvez
> esteja muito bagunçado"* (00:17:22) é provavelmente msilva, sobre o
> próprio doc de reorganização; (4) *"provavelmente devolveria uma melhor"*
> (pasta `infra`) não é verbatim. Nada muda no sentido dos Decisions.

## Decisions

- **Loop vs. workflow não é eixo do molde de agente.** msilva trouxe a
  distinção como obstáculo para chegar a um molde único (loop expõe as
  ferramentas e o modelo as chama até atingir o objetivo; workflow executa
  passo a passo). Luís encerrou: *"isso pouco importa pro desenho que a gente
  fez [...] é como, dentro dele, ele vai funcionar"* e *"esse teu argumento
  inicial do workflow ou looping, ele pra mim é zero dentro dessa discussão"*.
  Fecha uma dúvida que estava aberta em
  [[Como deve funcionar o molde de agente]].
  `(unverified: diarização colapsada)`
- **A arquitetura da solução passa a ser artefato no repositório, não contexto
  de conversa.** Diagnóstico de Luís aceito por msilva na própria conversa: a
  arquitetura principal hoje vive no *brain* dele, acessível ao agente só
  quando ele concede acesso, e não como arquivo versionado no repo.

## Commitments

- **msilva** vai falar com Gabrielle e Carolina **em 2026-10-21** sobre o plano
  da conta: a conta pessoal dele renova dia 20 em 200 USD, valor que ele não
  pretende usar sozinho no mês seguinte; quer migrar para um plano
  compartilhado entre ele e Luís, ou para a conta da empresa. Ainda **não
  existe issue no Linear** para isso — ver nota ao final.
- **msilva** vai avisar o time sobre o bloqueio da CLI (abaixo, em *Facts
  stated*), em vez de continuar contornando. Posição de Luís: *"não fica se
  virando aí, trabalhando de qualquer maneira — avisa"*. Também sem issue
  ainda.
- **Luís** propôs fazerem **um projeto junto** — pegar o desenho do quadro e
  implementá-lo a quatro mãos, como caixa inicial para desenvolver qualquer
  solução de agente. Compromisso dele, não de msilva; sem data marcada, e a
  janela é curta porque ele sai no fim de outubro.
- **msilva** combinou com Gabrielle expandir os valores de `stage` e montar um
  de-para para o Linear, que não tem alguns dos estágios necessários
  (mencionado no walkthrough como combinado já existente, não novo).

## Open questions

- **O MCP do Fluxo Agêntico deve expor chat ou rotas com objetivo?** Hoje o
  Claude abre um **chat streaming** com os agentes Portfólio e Projeto — tem
  acesso ao agente, não às ferramentas dele. msilva levantou que isso consome
  token duas vezes. A alternativa discutida foi criar rotas específicas no MCP
  (ex.: "analisa o projeto, dado o ID"), que preservariam identidade e
  capacidade do agente mas com objetivo delimitado, em vez de um chat que pode
  fazer qualquer coisa. Luís: *"não tenho opinião formada [...] me parece algo
  que a gente quer explorar [...] falta um pouco mais de pensar, planejar e
  talvez testar"*. Em aberto.
- **A pasta `core` é genérica de agente ou específica destes agentes?** Luís
  apontou que `outcomes` fala de milestone e delivery — domínio, não
  infraestrutura de agente — enquanto `core` deveria conter o que é comum a
  qualquer agente. *"Isso pra mim não me parece ser coisa genérica de agente,
  parece ser coisa específica dos agentes que a gente tá construindo."* Não
  virou tarefa; ele seguiu adiante para não travar o walkthrough.
- **A integração com o Bugsnag é a oficial ou inventada pelo modelo?** msilva
  não soube dizer como foi feita; Luís sugeriu conferir se o agente não criou
  um caminho próprio onde já existe um SDK. Não verificado.
- **Em que momento o desenho do quadro virou a solução atual?** Pergunta que
  Luís classificou como *"muito mais filosófica"* e deixou sem resposta: se a
  reorganização foi deliberada ou se, depois de chegar na solução atual, não
  havia como voltar. msilva reconheceu não ter entrado nesse nível.

## Facts stated

### O teste de portabilidade — o critério central da conversa

Luís propôs um teste concreto para saber se o molde de agente existe de fato:
*"quero pegar o teu projeto, deletar tudo que é específico de Linear e levar
para outra empresa. Dá para fazer isso?"* O resultado esperado seria sobrar
uma base clara de como um agente funciona, com interfaces explícitas, e só
~5% de exemplo específico de domínio.

Leitura dele ao olhar o código: não passa. *"Parece que tá tudo ainda
grudadão."* A crítica à pasta `core` (acima) é a mesma observação aplicada a um
diretório específico. `(unverified: diarização colapsada)`

Luís enquadrou isso como diagnóstico, não veredito — *"não é uma crítica
ainda"*, *"se a gente não conseguiu chegar nisso, por que que a gente não
conseguiu? E tá tudo bem"* — pedindo a explicação do porquê, não a correção
imediata.

### O diagnóstico: faltou fechar a arquitetura antes do código

*"A impressão que eu tenho é que você se empolgou para ver algo funcionando
antes de ter aquela base muito bem validada"* — e, segundo ele, é um padrão já
observado antes nos dois. Luís assumiu metade da responsabilidade: *"é mole meu
também de não ter falado assim: 'não desenvolve nada, me mostra a arquitetura
primeiro e aí depois você desenvolve'"*.

Contraexemplo dele, em curso: havia cerca de 20 dias com acesso ao código de
outro projeto (uma startup fora da Livemode) **sem ter escrito nada** —
testando, estudando e validando, à procura de uma opinião clara sobre a
arquitetura antes de pedir código ao modelo.

### ADR como regra sagrada

O método que Luís descreve usar:

1. Pega o desenho da solução.
2. Questiona o desenho com o modelo — se atende tudo que precisa.
3. Pede ao modelo que **descreva a solução** por escrito.
4. *"A partir daquele momento, aquilo ali é sagrado e ele não pode fugir
   daquilo. Aquilo é uma ADR."*

Quando o modelo aponta que algo não vai funcionar, a saída não é abandonar a
ADR: *"aí você bota um puxadinho [...] deixa marcado lá no outro ADR
específico e segue"*.

O diagnóstico específico sobre msilva: ADRs foram tentadas (*"eu comecei a
trabalhar com ADR aqui, mas mesmo assim em algum momento ele desandou"*), mas a
decisão não foi tornada inegociável, e a arquitetura principal **não está no
repositório** — está no *brain* de msilva, acessível ao agente só sob
concessão. A frase que fecha o argumento:

> *"Como é que ele vai seguir a arquitetura que você, como arquiteto, quer que
> ele siga, se você não tá disponibilizando a arquitetura para ele? [...] Tu tá
> acreditando que ele vai seguir o que você quer sem tu criar os guardrails
> corretos."* `(unverified: diarização colapsada; "guardrails" transcrito como
> "hardos")`

### Papel de arquiteto não inclui revisar todo o código

> *"Você, como arquiteto, você não vê tudo que o desenvolvedor júnior faz. 'Ah,
> mas eu reviso todo o código.' Não revisa, porque não é o papel do arquiteto.
> Papel do arquiteto é garantir que a arquitetura está sendo seguida — e você
> não está fazendo isso. Então você vai ter que revisar o código inteiro."*

A inversão é o ponto: revisar tudo é **sintoma** de arquitetura não
disponibilizada, não virtude profissional. Luís reconhece a mesma limitação em
si (*"eu também não vejo [cada coisa que ela faz]"*), mas a trata como parte
delegável, não como falha.

msilva havia dito, antes disso, que *"se eu escrevesse isso na mão, ia tá
melhor do que tá"*, perguntando se estava sendo petulante. Luís rejeitou o
enquadramento duas vezes: não é petulância, é não estar sabendo usar a
ferramenta — *"você tá admitindo que você não tá sabendo usar uma Ferrari"* —
e, mais tarde, reagiu com dureza à frase em si (ver *Notable quotes*).

### Token e plano de conta

Fatos levantados na conversa:

- msilva estava usando a conta **Humanas**, orientado por alguém no início
  (possivelmente Gabrielle — ele não lembra com certeza). Limite menor, janela
  estourando com frequência.
- Por causa disso, vinha usando **esforço médio** para desenvolver — a escolha
  do modelo estava sendo ditada pelo limite da conta, não pela tarefa.
- Na semana de 2026-10-13 trocou para a conta **tech** e os estouros pararam.
- A conta pessoal (200 USD, cartão próprio) **renova dia 20**.

O princípio de Luís, com o caminho que ele percorreu: começou na conta mais
barata do Cursor, foi subindo de plano a cada vez que batia no limite
(*"Gabi, tem que ir pro próximo"*) até chegar no plano de 200, onde a
produtividade dele disparou; está nele há seis meses e hoje nem usa tudo.

A regra que ele extrai é **vir de cima para baixo, não de baixo para cima**:

> *"Eu não vou botar o modelo mais burro possível para tentar fazer funcionar
> com o modelo mais burro, porque eu vou estar fazendo um monte de coisa que
> talvez eu não precise se eu estiver no próximo modelo. Então eu tô fazendo ao
> contrário: eu tô vindo de cima e tô descendo."*

Com uma ressalva de escala declarada por ele mesmo: *"pra 4, 5, 10, 20
funcionários dá para fazer isso? Não dá."* E uma janela temporal: *"enquanto os
tokens estão baratos [...] uma hora o preço vai pra realidade, a gente tem que
aproveitar."*

A cobrança sobre comunicação, separada do mérito técnico: *"em que momento você
me falou 'tô estourando token'?"* — msilva achou que era problema dele e foi se
virando. Resposta de Luís: *"tem que falar"*.

### O bloqueio da CLI

Há **mais de uma instalação do Claude Code** na máquina Windows de msilva — uma
do usuário e uma global. `cloud update` atualiza a instalação que **não** é a
chamada por default, então a CLI continua sem oferecer os modelos mais novos
(Fable e Opus 5.5 citados nominalmente). Pelo Zed/GUI o acesso funciona; pela
linha de comando, não.

TI foi acionada, pediu permissão de administrador, e **não resolveu mesmo com o
notebook fisicamente em mãos** — msilva deixou a máquina com eles, com tudo
aberto e as mensagens de erro à vista, durante uma reunião. Posição de Luís:
isso é bloqueio de trabalho, não algo para contornar em silêncio.

### Ultra Code

Recomendação de Luís, apresentada como consequência prática de ter a
arquitetura fechada: definir a arquitetura no nível de detalhe, discutir,
montar um plano, **ler o plano**, confirmar, e então deixar rodando de
madrugada. *"Uma coisa é você pedir para ele fazer do jeito que ele quer, outra
coisa é você direcionar pro jeito que você quer."* Ele faz isso rotineiramente
em projetos fora da Livemode, com execuções indo até 4–5h da manhã.

### Do walkthrough do repo (18h19)

- **MCP**: integra os agentes Portfólio e Projeto com Claude e Codex. O
  mecanismo é um **chat streaming** — o Claude conversa com o agente e pergunta
  o que ele sabe; **não** recebe as ferramentas do agente. msilva corrigiu a si
  mesmo nesse ponto durante a explicação. Origem: ideia da Carolina, feito às
  pressas e pouco testado desde então.
- **Por que não expor só as tools**: Luís articulou o argumento a favor do
  desenho atual — entregar tools ao Claude faz cada instalação ficar
  configurada de um jeito e **perde o "coração" de cada agente**, que é
  justamente o que se quer preservar.
- **Estrutura**: a separação domínio/infra já existe e incomoda menos que
  antes. `infra` é integração com terceiros (SQLAlchemy, FastAPI, Linear,
  Langfuse), sem domínio. Luís sugeriu desmembrar mais esses arquivos e
  observou que pedir ao modelo duas ou três organizações alternativas
  provavelmente devolveria uma melhor.
- **`review` aprovado**: a camada de decisão humana sobre o que o agente propõe
  — ele sugere um fato ou uma ação, não salva nem executa sozinho, e um
  sim/não humano decide — Luís considerou genuinamente genérica: *"pelo que
  você tá explicando, é para qualquer agente"*. Liga em
  [[2026-09-10 Memória de fatos do agente (agent_facts)]].
- **`agent_actions`** (agir sobre as próprias sugestões) está parado; msilva
  está na frente das docs com Gabrielle.
- **`stage`**: estágio de projeto ou iniciativa, existe porque o Linear não tem
  alguns dos valores necessários.

## Notable quotes

> *"Quero pegar o teu projeto, deletar tudo que é específico de Linear e levar
> para outra empresa. Dá para fazer isso?"* — Luís, propondo o teste de
> portabilidade

> *"A partir daquele momento, aquilo ali é sagrado e ele não pode fugir
> daquilo. Aquilo é uma ADR."*

> *"Papel do arquiteto é garantir que a arquitetura está sendo seguida — e você
> não está fazendo isso."*

> *"Talvez o mais importante de tudo não é o projeto, é a dificuldade que você
> está tendo de dar um direcionamento claro para ele. E quanto menos esforço
> você coloca para ele, maior é a chance dele cometer erro."*

> *"Essa tua frase do 'se eu escrevesse na mão ficaria melhor' — numa
> entrevista tu não passa. [...] E tu não pode aceitar isso."* — reação de Luís,
> registrada aqui porque é o tom real da conversa, não fanada para outras
> páginas

> *"Eu não sei ensinar as pessoas ainda."* — Luís, sobre a própria limitação,
> antes de propor o projeto conjunto

> *"Não economiza token. Você não tem que ficar economizando token."*

---

**Nota de rastreamento**: dois compromissos acima são de msilva e têm prazo
(a renovação da conta é imediata; o bloqueio da CLI já custou semanas), mas
**não foram abertos no Linear** — nenhum time/projeto foi confirmado para eles.
Ambos são candidatos a tarefa externa, não a issue de projeto. Decisão
pendente de msilva.
