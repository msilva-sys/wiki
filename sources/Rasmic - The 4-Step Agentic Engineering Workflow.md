---
type: source
status: stable
updated: 2026-10-20
source: "raw/The 4-Step Agentic Engineering Workflow I Use to Ship Real Apps Ras Mic.md"
url: "https://www.youtube.com/watch?v=8rEAkEvhhcQ"
aliases: [rasmic, software factory 4 steps, 4-step workflow, isolate build prove ship]
tags: [agents, skills, harness, workflow, code-review, prior-art]
---

# Rasmic — "The 4-Step Agentic Engineering Workflow I Use to Ship Real Apps"

Transcript de vídeo no YouTube: entrevista conduzida por **Peter** com
**Michael** (canal Rasmic, ~100 mil inscritos), full stack engineer que também
toca uma consultoria construindo apps e agentes para terceiros.

**Data de publicação não determinável** — o transcript não tem cabeçalho de
data, o nome do arquivo não carrega uma, e o `birth` do arquivo em `raw/` é
`2026-10-20`, dia do download, não da publicação.

> [!warning] Nomes de modelo corrompidos pela transcrição automática
> O transcript chama os modelos de *"Astra"*, *"Fable"*, *"Luna"*, *"Soul"*,
> *"GBT 5.6"*, *"Grock 46"* e *"Composer"*. A transcrição mistura nomes reais
> com ruído de fonética — e algumas afirmações (ex.: *"com Fable, toda vez que
> falo de segurança, sou jogado pro Opus 5"*) dependem de qual modelo é qual.
> **Citado abaixo como está no original, sem tentar desembaralhar.** Qualquer
> claim específico de modelo nesta página é `(unverified: nome de modelo
> possivelmente corrompido na transcrição)`.

## A tese: "software factory" não é o loop autônomo

A definição corrente de software factory — dar a tarefa e deixar rodar até o
fim — ele descarta: *"it usually is a slop cannon."* O motivo é mecânico, não
estético: *"one bad wrong assumption in the beginning just destroys
everything."* Experiência própria citada: deixar rodando a noite toda pra
construir um jogo e perder tudo por uma assunção errada no início.

A definição que ele usa no lugar: **software factory é o workflow, o domain
knowledge e o gosto da pessoa empacotados num `AGENTS.md` + um punhado de
skills**, *"model and harness agnostic"*. O `AGENTS.md` dele não descreve a
estrutura do código — *"the models are so good now they can use their read
tools and figure out how things are structured"* — descreve **em que ponto do
fluxo chamar qual skill**.

Contraste explícito com Ralph loop e com `/go`: *"you're leaving a lot of gaps
for the agent to fill itself, and sometimes they'll make good assumptions
[...] but sometimes it could be terribly wrong."*

## Os quatro passos

| Passo | Skill chamada | Autoria | O que faz |
|---|---|---|---|
| 1. **Isolate** | `new-feature` | dele | Cria um git worktree a partir de `main` antes de escrever qualquer código |
| 2. **Build** | `code-structure` | dele | Escreve código numa service layer architecture |
| 3. **Prove** | `evidence-driven-testing` / `before-and-after` | dele / Vercel | Produz evidência de que o bug existe e de que a correção funciona |
| 4. **Ship** | `grep-loop` / `unslop` | Greptile / um eng. da Cursor | Itera o PR até o code review agent dar 5/5 |

### 1. Isolate

A skill é curta e declarativa: *"start a new task in an isolated git worktree
branched off main so multiple agents can work on the same repo in parallel
without conflicts. Use at the beginning of every new feature, fix or task
before writing any code."* O corpo dela é essencialmente um how-to de git
(`fetch origin`, criar o worktree a partir da raiz do repo, verificar) — ele
admite que git nunca foi o forte dele e que a skill foi gerada com IA.

O ganho é paralelismo real: cinco abas de terminal rodando agentes ao mesmo
tempo sem um pisar no outro.

**A ressalva está dentro da própria skill**: *"worktrees do not isolate shared
resources"* — porta do dev server, banco compartilhado. O código fica isolado;
os recursos, não.

### 2. Build

`code-structure` manda escrever em **service layer architecture**, com dois
espaços separados:

- **Orchestration layer** — dona do *why* e do *when*: regras de negócio,
  transições de estado, checagens de autorização.
- **Service layer** — dona do *how* reusável: funções, SDK, input explícito,
  retorno estruturado.

Dois motivos declarados, nesta ordem:

1. **Corta função duplicada** — *"this limits duplicate functions, which agents
   love to write: the same function again and call it in different places."*
2. **Fica legível pra humano** — o nome da função basta: `create manage
   sandbox`, `prepare repo`, `detect package manager`, `install dependencies`.
   Justificativa dele: *"if I for some reason have to get in and read and write
   code like it's 2024, then this should make it at least easy for me to
   understand."*

Ele reconhece que a escolha arquitetural é discutível (*"a lot of people debate
this"*) — o argumento não é que service layer é a arquitetura certa, é que uma
arquitetura **nomeada e prescrita** dá ao agente um padrão a seguir no lugar de
inventar um por feature.

### 3. Prove

Duas skills, por ordem de preferência:

- **`evidence-driven-testing`** — grava **vídeo** (via ffmpeg) do estado
  *antes*, reproduzindo o bug, e do estado *depois*, com a correção. Depende de
  ffmpeg na máquina; ele não testou no Windows. Combina com agentes de nuvem
  (Cursor Cloud, Devin) e com o fato de o Linear permitir atribuir uma issue a
  um agente, não só a uma pessoa: o ticket de bug do time de produto vira,
  direto, um agente obrigado a **provar que o bug existe** antes de corrigir —
  *"cuz sometimes it just might have been user error."*
- **`before-and-after`** (*"a skill I stole from Vercel"*) — screenshot do
  antes e do depois. Quando não há UI pra fotografar, **número**: o exemplo
  dado é uma navegação que saiu de 850 ms para 60 ms. Quando nem isso, testes
  e estatísticas.

O ponto não é o formato da evidência, é que o agente tenha que produzir alguma:
*"it sort of forces the agent to prove that the solution it made works — I've
seen traces where it'll do the before and after and be like 'oh, the changes
aren't really good', and then it'll continue on working."*

### 4. Ship

`grep-loop` é skill da **Greptile** (code review agent), trazida inteira pro
repo dele. O que ela faz, lido do próprio arquivo: *"iteratively improves PR
until Greptile gives a 5/5 confidence score with zero unresolved comments.
Triggers Greptile review, fixes all actionable comments, pushes, re-triggers
review, and repeats."*

O mecanismo é o que torna esse loop diferente dos loops que ele rejeita: **o
score de um agente externo é o critério de parada.** Feedback abaixo de 5/5
reentra no passo 2 (escreve código) e passa de novo pelo passo 3 (prova), então
empurra. Teto: 5 iterações no default da skill, 10 na versão dele.

Dado de calibração dele: *"most models — your Fables, even Astra — usually the
first PR they make is a four out of five."* Ou seja, o loop quase sempre dispara
pelo menos uma vez.

Também usa `unslop`, de um engenheiro hoje na Cursor. Observação dele: *"Astra
doesn't really follow this — it tends to skip unslop."*

## O passo que ele nunca delega

**O teste manual dele mesmo.** *"Even though this is supposedly a software
factory, it's very much human in the loop — I am testing my features."*

O propósito declarado dos passos 3 e 4 é **encurtar** esse review, não
eliminá-lo: *"what I'm trying to do with evidence-driven testing and before and
after and GP loop is we're trying to build trust [...] so it makes manual review
as little, as short of a time as possible."* E a razão de haver review manual é
produto, não engenharia: *"when you're building an application for people, a
little bit of empathy is necessary for the user."*

**O rigor é proporcional a quem usa o software.** Ele mantém dois regimes
declarados:

- App que outras pessoas usam, ou cliente: o fluxo inteiro, mais developers
  humanos do time revisando o código.
- Ferramentinha pessoal (um serviço de imagem entre máquinas, um screenshotter):
  *"I just wrote /go. It worked. It was good enough for me. I don't care for
  the Greptile score."*

Pra projeto pessoal ele chega a desaconselhar o fluxo todo — *"just have fun"* —
com uma exceção: **se tem gente usando e tem dinheiro envolvido, security audit
é obrigatório.** O caso dele é o sistema de pagamento que construiu pra própria
igreja; o audit achou um problema em mecanismo de recibo que *"would have been
detrimental"*.

## Ele não escreve spec

Planejamento é conversa por voz, 5 a 30 minutos de ida e volta com o agente, e
**o histórico da conversa é a spec**: *"planning for me isn't the agent writing
the spec and me reviewing the spec and me saying 'go build it'. Planning for me
is me and the agent discussing."* Ele atribui isso a como pensa (*"I have to
read out loud, I have to hear it"*) e não propõe como prática geral.

**A exceção é o que precisa sobreviver à conversa.** Quando o trabalho tem um
setup que ele não quer executar agora, pede ao agente um markdown dentro do
próprio repo (`imessage/setup.md`) — *"for certain things that I want to
remember, I want to come back to, I want the agent to come back to, I will ask
it to create files within the codebase itself."* Motivo declarado: *"I'm a very
forgetful person."*

Isso é o oposto direto do que [[How to Write an Effective Software Design Document]]
e a skill `design-doc` deste vault assumem (documento antes de implementar),
**mas não é o oposto da tese de instruções-em-arquivo** de
[[Packaging as skills]]: ele escreve arquivo exatamente quando o contexto
precisa durar além da sessão. A discordância é sobre *spec de implementação*,
não sobre persistência.

## Modelo: não usar state-of-the-art pra tudo

*"Don't fall into this trap of having to use the state-of-the-art for
everything. I find, in fact, the really big models can do really dumb things
for minuscule tasks."* Dois corolários dele:

- **State-of-the-art fica pra orquestrar e revisar**; modelo menor executa.
  *"A lot of the smaller models are getting really, really good."*
- Modelo grande em tarefa pequena custa duas vezes: token e tempo.

Exemplo próprio fora de código: declaração de imposto feita com um modelo
barato, com outro modelo rodando como subagente pra conferir o trabalho do
primeiro — uma divisão executor/verificador igual à dos passos 2–4 acima.

Ele aposta em **model routers** como peça faltante. Encosta no `cross-model
review` já registrado em [[Claude Code Working Habits]]: a prática de msilva é
planejar com um modelo e revisar com outro; aqui o eixo de corte é por
**tarefa**, não só por etapa.

## Ceticismo com best practice de lab

*"You kind of have to be a little bit skeptical about some of the best
practices coming from the labs, because the engineers who work in the labs
just have unlimited tokens."* Mais o conflito de interesse direto: *"at the end
of the day they are a business, and they want you to spend more money."*

Eco quase literal do argumento de
[[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] — duas vozes
externas independentes dizendo que a prescrição de quem tem token ilimitado não
transfere pra quem tem limite. Ele puxa daí uma consequência que Akita não puxa:
suporte filosófico a **open weight models rodáveis na própria máquina**, como
seguro contra depender do subsídio.

## Skills somem conforme o modelo melhora?

Ele sustenta os dois lados, e a divisão é útil:

- **Some**: skill que era *descritor* de algo a que o modelo já tem acesso.
  *"Explaining stuff to the agent that it already has access to — that's for
  sure gone. You shouldn't do that."* Candidato dele à primeira baixa:
  `code-structure`, se o modelo passar a escrever código elegante sozinho.
- **Fica**: skill que codifica **como você trabalha** e o seu gosto.
  *"Skills before were descriptors of the things that the model had access to,
  versus specific workflows."*

Previsão dele: o que mata `new-feature` e `grep-loop` não é o modelo, é o
**harness** — *"I do see a future where maybe not the model but the harness
will start to incorporate better engineering practices."*

Hábito que ele mantém por causa disso: **a cada modelo novo, rodar primeiro sem
`AGENTS.md` e sem skill nenhuma**, pra medir o modelo cru, e só depois reinjetar
o software factory.

## Cron

Nos apps de cliente com exigência alta (um deles com empresas de governo), roda
**security audit agendado todo dia** em Cursor Cloud agent. Ele quer migrar isso
pro próprio home lab (uma Linux box + um Mac).

Segundo cron, por dor própria: **faxina**. A máquina dele chegou perto de ficar
sem memória; a causa eram *"3.000 something worktrees"* acumulados — o custo de
operação do passo 1 que a skill do passo 1 não cobre.

## O que isso toca nesta wiki

- **[[Packaging as skills]]** — sétima fonte independente da tese, e refina ela:
  a divisão descritor-vs-workflow dá um critério pra *qual* skill sobrevive à
  próxima geração de modelo.
- **[[Agent Harness Template]]** — os quatro passos caem limpo no eixo
  guias/sensores de [[Harness engineering for coding agent users]]: 1–2 são
  guias (feedforward), 3–4 são sensores (feedback), sendo o 4 um sensor
  *inferencial* (outro LLM julgando) com critério de parada numérico.
- **[[Claude Code Working Habits]]** — roteamento de modelo por tarefa.

## Perguntas em aberto

- **O score de 5/5 é critério de parada confiável?** O loop para quando um
  agente de review se declara satisfeito. Nada no fluxo testa se o 5/5 da
  Greptile correlaciona com código que funciona — e o próprio autor mantém
  review manual depois, o que sugere que ele também não acredita totalmente.
  Equivale ao problema de *behaviour harness* que Böckeler chama de
  *"the elephant in the room"*.
- **Quanto do fluxo depende de PR + GitHub + um agente de review pago?** Os
  passos 3 e 4 assumem essa infra inteira. Não há versão declarada do fluxo pra
  quem não tem.
- **Nenhum número.** A página toda é prática relatada por uma pessoa, sem
  benchmark, sem taxa de retrabalho, sem custo. Ao contrário de
  [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]], que traz
  dados próprios, aqui a evidência é a própria experiência do autor —
  explicitamente apresentada como tal, não como medição.
