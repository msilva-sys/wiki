---
type: meeting
status: active
updated: 2026-08-18
date: 2026-08-18
attendees: [Gabrielle Ferreira, Matheus Silva]
source: "raw/Matheus _ Gabrielle - 2026_08_18 11_04 GMT-03_00 - Anotações do Gemini.md"
transcription_confidence: low
tags: [1-1, linear, process, farol, n8n, tokens, templates, proxy, handover]
aliases: [1-1 2026-08-18]
---

# 1:1 Matheus / Gabrielle (2026-08-18)

Second 1:1, 11:04, **30 minutes 41 seconds**. A screen-share walkthrough of how the
area runs work in Linear, followed by a debrief on the Gabriel consultation.

Timing matters. Gabrielle is **on leave from 2026-08-24** for ~2.5 weeks
([[2026-08-14 1-1 Matheus - Gabrielle]]), and msilva is restructuring the proxy
backlog on this board. This is the last scheduled 1:1 before that window.

> [!note] Source replaced — rebuilt against the transcript on 2026-08-18
> This page was **first written from a version of the raw file containing only
> Gemini's summary blocks** (*resumo* / *próximas etapas* / *detalhes*). msilva then
> downloaded the actual transcript to the same path, and this page was rewritten
> against it. Same situation as the 2026-08-14 1:1's `.docx` → `.txt` swap.
>
> **The rewrite was not cosmetic.** The transcript contains material the summary
> omitted entirely — most importantly that **Gabriel switched LLM providers**, which
> confounds the cost comparison the wiki had been treating as clean, and that **the
> n8n citizen flows are intended to go behind the proxy**. It also *contradicts* the
> summary in one place: the summary presented the review split as decided; the
> transcript has Gabrielle saying explicitly that nothing is settled. Corrections are
> called out at each point below.

> [!warning] Transcription quality — attribution is collapsed
> Gemini attributes **every line to "Gabrielle Ferreira"**, including msilva's own
> questions and commitments. Same defect as
> [[2026-08-14 1-1 Matheus - Gabrielle]]. Attribution below is inferred from content;
> where a line is msilva's it is marked. The audio is also poor — *Linear* comes
> through as "liner"/"líner"/"Lin", *Claude* as "Cloud", *Jira* as "giro", *issue* as
> "iso"/"nicho", *milestone* as "marstone"/"mystone", *Airtable* as "Air Table"/"AT",
> *Anthropic* as "tropic". Quotes below are verbatim including the garbling, with
> bracketed reconstructions.

## Decisions

- **n8n execution saving gets turned on for production runs** —
  [[2026-08-18 Save n8n execution logs for audit]]. Already changed on at least one
  flow during the call.
- **The `Fronte de Negócios` project was split**, and its stabilization milestone
  regrouped — done 2026-08-17, described here. See
  [[Linear Project Structure]].

**Not a decision, though the summary presented it as one:** the product-feedback /
code-review split. Gabrielle's own words are *"não tem um o o martelo martelo batido
de qual que é o melhor fluxo"* — nothing has been hammered down. Recorded as a
stated preference at
[[2026-08-18 Product feedback in Linear, code review in Git]], which was **demoted
from a decision** on this basis.

## Action items

- [ ] **Gabrielle** — send the Linear project link **and** the folder of `.md`
      templates/docs, zipped if needed: *"vou te mandar tanto o link do projeto que
      ele tá, mas também te passo a pasta."* msilva asked for the PRDs or access.
- [ ] **msilva** — migrate everything off Jira **today**. His own commitment:
      *"eu vou então hoje eu vou pegar tudo lá do [Jira] e botar para ele[s] para
      vocês terem uma ideia melhor das coisas."* Fourth time this has been recorded.
- [ ] **msilva** — turn on execution saving on the remaining `RR*` flows
      ([[2026-08-18 Save n8n execution logs for audit]]).
- [ ] **msilva** — ask Luís what he dislikes about his own templates before adopting
      them: *"Vai também depois perguntar a ele o que que ele achou que tá ficando
      ruim."*
- [ ] **msilva** — connect the proxy's Linear project to the existing proxy Slack
      channel (offered, not assigned).

## Open questions

- **How many n8n flows hit Airtable, and who holds which key?** The blocker on
  putting them behind the proxy. Gabrielle: *"a gente não tem noção de quantos são e
  cada pessoa tem sua própria chave."* This is now the proxy's largest unknown — see
  the section below.
- **Did the 11-centavos run and the $7 run use the same model?** Gabriel switched
  provider between them. Nobody knows. See the correction below.
- **Which template does Luís consider bad, and why?** He is already rewriting them.
- **Does Luís put his review comments back into Linear or only Git?** Gabrielle
  didn't know: *"Eu só não sei te dizer se quando ele pegava alguma coisa da Yasmin e
  ele voltava se ele botava no git."*
- **Is the tracking platform an Airtable app?** Strongly implied (see below) but not
  stated outright. If so it is an Airtable consumer and a proxy candidate, and nobody
  connected those.

## Linear's structure, walked end to end

**Initiative = the product. Project = a slice of value delivery inside it.**

> *"A gente tá tratando no final a iniciativa como o produto, o sistema, a solução em
> si e aqui projetos como um pedaço eh uma parte daquela iniciativa."*

Her worked example is [[LiveScript]]: the initiative is the product, and the version
that went to production plus each subsequent release are its projects.

Full write-up: [[Linear Project Structure]].

### The Airtable initiative has three projects, and Luís created all three

> *"se tiver o próprio Air Table aqui governando confiabilidade, o proxy ele tá dentro
> dele. Então aqui quando a gente entra nessa iniciativa, a gente tem três projetos
> que o Luís tinha criado."*

1. **Build the Airtable proxy** — [[Airtable Proxy]].
2. **Expand the proxy to other apps beyond LiveScript** — *"expandir esse proxy para
   outros apps para além do L[ive]Script."*
3. **A Livemode data hub.**

The initiative's purpose, in her words: *"tudo isso é voltado para garantir, né,
govern[ança e con]fiabilidade dos dados que hoje a gente consome do A[irtable]"* —
so **governance *and reliability*,** not governance alone. Worth keeping both words:
reliability is what the World Cup data loss was about.

> [!question] The data hub — she is explicitly guessing, four times over
> *"um data hub que ele tava criando da Live Mode que não sei se tem mais detalhes
> aqui […] Banco de dados intermediári[o] […] eu lembro do [Luís] ter falado comigo
> sobre isso pouco. Eu acho que é a ideia de ir migrando do air table. Pode ser. Sei.
> Eu acho que é isso. Deve ser."*
>
> So: an **intermediate database**, and the idea is **migrating off Airtable** —
> second-hand from Luís and hedged at every step. **Ask Luís, not Gabrielle.**
>
> **This is very likely the wiki's already-recorded Phase 2**: the merged
> proxy + LiveScript-stabilization programme whose phase 2 moves data that doesn't
> need to be in Airtable into a separate database
> ([[2026-08-10 Onboarding Técnico - Matheus]], [[LiveScript]]). [[Airtable Proxy]]
> records that phase as **absent from the roadmap** — it isn't absent, it is a
> sibling project. *(unverified: the identification is this vault's inference from
> two hedged descriptions matching.)*

### Farol is an initiative too

> *"quando a gente vai também, por exemplo, no farol, que é o que o [Luís] tá tocando
> agora com a Yasmin, dentro dele a gente tem o primeiro projeto, que seria esse
> farol […] pegando a lá as AP[I]s de cada plataforma, juntando tudo isso no banco. E
> a gente teria a V2, que é uma evolução dele […] pegando os dados da bronze, botando
> na ouro, na prata, botando ali uma camada conversacional de com IA."*

So V2 is a **second project inside the Farol initiative**, not a vague future. See
[[Farol]].

### Agent Flow should be its own initiative — Gabrielle's answer

> *"talvez quando você for fazer o de agente do fluxo agêntico, talvez você iria criar
> uma iniciativa e aí talvez cada parte ali seria um projeto, enfim, acho que talvez
> depois você entender melhor como ele dividir."*

Hedged three times with *talvez*, but it is her answer to a question the wiki had
listed as open: **[[Agent Flow]] gets its own initiative, with each part a project.**
The four-part per-agent spec frame already on that page maps onto it directly.

## The Linear plan — and msilva already hit the cap

Gabrielle, to msilva: *"Você tinha, você tomou um rate limit, né? […] não podia mais
criar."* **He had already hit the issue ceiling trying to create issues.** That is
what prompted the plan discussion, and it makes the cap concrete rather than
theoretical.

> *"dentro do plano gratuito, a gente tem uma barreira de 250, se não me engano, 150,
> né? Quantidade de [issues], só que a gente tá no trial do business, então por
> enquanto a gente não tá batendo nessa barreira. […] só que ele acaba, tipo, em 22
> dias. A gente tá testando, entendendo aqui o que faz sentido ou não, pra gente
> entender qual o plano faz sentido a gente assinar e se faz sentido assinar."*

Two things to hold: **22 days → 2026-09-09**, and **"se faz sentido assinar"** — the
purchase is genuinely undecided, not pending paperwork.

Her preference: *"eu particularmente eu gosto do liner, eu acho que a gente vai
continuar nele."*

> [!warning] The trial lapses while she is on leave
> 22 days from 2026-08-18 is **2026-09-09**; Gabrielle returns **2026-09-10**. The
> trial expires the day before she is back, msilva is migrating the `AIRTABLEGC`
> backlog into that workspace today, and **he has already hit the free cap once.**
>
> Nobody stated this in the meeting; it falls out of the calendar. Raise it before
> 2026-08-24 — the person who decides the plan is the person leaving.

## The review flow — and Gabrielle is the product reviewer

msilva's question was where feedback is centralized: *"a minha dúvida era mais se os
feedbacks e revis[ões] eram centralizados lá no bit[bucket] ou aí."*

The answer describes what she did the previous day with Luís's work:

> *"ele jogou várias tarefas que ele tava numa branch lá e ele subiu pra produção, que
> no caso seria pra homologação. E aí eu validei, tipo, fazendo testes e tudo mais. E
> aí o que tava certo já jogava para aprovado[,] que não, eu voltava para ele olhar e
> já botava o fluxo de comentários ali."*

- **The "person from product" is Gabrielle herself.** This answers an open question
  the earlier version of this page had raised.
- *"pra produção, que no caso seria pra homologação"* — it is **homologation**, not
  production.
- Her flow is interface testing: *"eu entrar lá na interface"*. Luís's review of
  Yasmin's work, by contrast, *"ele acaba olhando mais o git"*.
- **Subissue ↔ PR is not 1:1.** *"Cada subi[ssue] dessa é uma PR. Depende. Aqui ele
  fez uma PR só para todas elas."*
- **Linear comments do not reach Git.** *"isso daqui ele não vai pro Git […] ele tem a
  descrição novamente do que que é, mas ele não tem os comentários. Os comentários
  estão lá nas tarefas."*

> [!important] Correction — this is a preference, not a decision
> The summary-only version of this page filed the split as a decision. The transcript
> is explicit that it isn't:
>
> *"também não tem um o o martelo martelo batido de qual que é o melhor fluxo. Acho
> que talvez um funcione melhor. Acho [que faz] sentido a gente […] concentrar os
> reviews e coisas relacionado ao produto aí e a parte [de] desenvolvimento lá[,] de
> reviews."*
>
> A reasoned preference from the manager, offered in answer to a direct question —
> which is worth recording — but *"não tem martelo batido"* is as clear a
> not-yet-decided marker as a transcript gives.
> [[2026-08-18 Product feedback in Linear, code review in Git]] was demoted
> accordingly.

## Templates — two kinds, and Luís already dislikes his

msilva asked whether there is a skill standardizing issue format. There is:

- **Per-type templates**: *"o Luís, ele criou uma[s] templates […] para cada tipo de
  [issue], tá? Se é bug, se é spike, enfim"* — plus epics. Markdown files:
  *"é um MD com […] o template realmente, né, de como criação[:] foi um bug, um
  épico."*
- **A structural template**: *"como dividir as tarefas em M[ilest]on[e], em épicos, em
  [issues] e sub[issues], que é um pouco mais de contexto de como que a gente tá
  tratando cada uma dessas coisas."*
- Gabrielle has her own version and rates it poorly: *"eu criei também o meu, só que o
  meu eu não achei que tá tão bom."*
- There is a further `.md` **relating the project to Linear and to the skills**.

> [!warning] Luís is rewriting them because he doesn't like how they came out
> *"ele tava ajustando um pouco porque ele não tava gostando muito dos templates de
> como tavam. Vai também depois perguntar a ele o que que ele achou que tá ficando
> ruim."*
>
> This matters for [[Agent Flow]], where A2's output shape has been modelled on
> Packer's external ticket template. The in-house templates exist **and their author
> is dissatisfied with them** — so "read the in-house ones" is not the whole answer;
> the interesting artifact is Luís's diagnosis of what's wrong with them.

## Claude creates the Linear structure autonomously

The most consequential thing in the meeting for [[Agent Flow]], and it went by as an
aside describing routine practice:

> *"eu primeiro pego […] alguma documentação ali do projeto em si, mas de produto,
> PRDs, etc. E aí eu chego no [Claude] com ele aqui. E aí depois que eu faço isso, eu
> peço para ele jogar tudo lá pro [Linear]. E aí ele […] cria tanto a descrição do
> projeto […] ele próprio já cria aqui os milestones e aí […] dentro dos milestones vai
> subir [issues]. Enfim, ele cria as tarefas sozinho."*

So: **documentation and PRDs go in, and Claude writes the project description,
milestones and issues into Linear by itself**, using the templates. Each issue's
subissues *"ele já trouxe a partir do template lá que ele usou."*

Related: Luís authors issue context in Linear with Claude's help and leaves comments
for Gabrielle from inside it — *"são informações que ele conversou lá com o próprio
chat e achou que fazia sentido botar como históri[a]. Aí aqui ele abriu um comentário
para mim por dentro lá do próprio [Claude]."*

And Linear has a **native description-improving agent**: *"quando você bota a
descrição aqui, você tem o agentezinho[que] trata a descrição para dar uma melhorada."*

> [!important] This is the second running agent-like system in the area, and it is the
> one msilva should copy today
> [[AI status reporting on Linear]] was filed as *the only* agent-like system actually
> running here. That is now wrong: **backlog authoring is agent-driven too**, and
> unlike the status readout it is uncontroversial and in daily use by the manager.
>
> Directly operational: msilva has to migrate the `AIRTABLEGC` backlog **today**, and
> the proxy already has the input this workflow expects — `airtable-proxy-design.md`
> and `doc/STATUS_proxy_airtable.md`. **The established method for doing his action
> item is to hand the design docs to Claude and have it write the milestones and
> issues**, using Luís's templates. He does not need to invent a process.
>
> For [[Agent Flow]] it is evidence about A2: an agent writing well-formed tickets
> from context is not a research question here, it is Tuesday.

## Teams, and the centralized board

**Teams are the workspace/permission boundary.** *"a gente tem os times que são como se
fosse o workspace […] A gente só criou esses dois aqui separados porque são com
[freela] para ele não ter acesso a todos os nossos outros projetos."* The proxy's
project sits inside the main *projetos* team.

**Initiatives cut across teams**: *"os times é mais como se fosse você dando acesso […]
Mas a iniciativa ela olha pro todo projeto, ela não vai ser ligada dentro."*

### The tracking platform is probably an Airtable app — and its subtask blindness is deliberate

The board the team reviews weekly is **not Linear**. It pulls *from* Linear:

> *"Ele puxa todos os projetos que estão classificados como em andamento. E aí dentro
> de cada projeto você consegue selecionar se você quer buscar a fonte do
> [backlog] do liner, porque nem todos os projetos estão no liner ainda. […] ele puxa
> aqui todos os milst[ones] que tem lá no liner e as [tasks] dentro dele."*

And on how it is fed: *"no final ele tá puxando do Air Table, então só você botar API
do Air Table ou pelo MCP do próprio [Claude]"*.

> [!important] Two findings, and the second closes the biggest open question on
> [[AI status reporting on Linear]]
> **1. It is an Airtable consumer.** If the platform reads Airtable, it is a candidate
> to sit behind the [[Airtable Proxy]] — and nobody in the meeting connected the
> project msilva owns to the tool the team uses to watch his project. *(unverified:
> "ele tá puxando do Air Table" is one clause in a garbled passage. Confirm before
> building on it.)*
>
> **2. The subtask blindness is a design choice, not a defect.**
> *"ele traz a nível de [issue], ele não traz a nível de sub[issue], que é para poder
> também não ficar tão confuso aqui. A gente precisa só ideia geral […] visibilidade
> do todo."* And it is already slated to change: *"Eu vou depois liberar aqui também
> para conseguir clicar e abrir sub[issues]."*
>
> The wiki had this as a mechanical defect of unknown origin, possibly unfixable, with
> "what is it mechanically?" as the standing question. The answer: **an in-house board
> Gabrielle controls, aggregating at issue level on purpose to reduce noise, with
> drill-down planned.** The flat-issues advice still holds for now — but it is
> adapting to a temporary setting, not to a broken system.

**Tasks outside projects get a personal board.** A second tab, for work beyond
projects — her example is *"a ajuda que você tava dando lá pro Gabriel."* Plus a
per-user filter (*"tudo que tá no seu nome, ele fica filtrado"*) and an **inbox** for
comment mentions.

## Restructuring projects — two fixes, not one

> *"esse do Fronte […] ele tava com sei lá, 2% 3% concluído, acho que se 6% alguma
> coisa assim. Por quê? Porque […] tinham o Milestone que ele era tudo que tava no
> backlog que não era da estabilização, que era já de evolução."*

**Fix 1 — split the project.** *"a gente criou um outro projeto de [Front] evolução e
jogou tudo para cá. Então isso já dá um aspecto mais real […] [da] porcentagem de
conclusão do projeto."*

**Fix 2 — break up the single milestone.** This one the earlier version of this page
missed entirely:

> *"a gente também tava com todos esses [milestones] de estabilização, todas as tarefas
> de estabilização como [um] [milestone] só. Então a gente também não conseguia, por
> exemplo, a Carol olhar, ter visibilidade de onde que a gente tava e a gente ontem
> reagrupou."*

One giant milestone hid where the work stood from **Carol** specifically. Regrouped
2026-08-17; now *"a gente já consegue saber melhor o que realmente tá travado, [o]
que a gente já andou."*

**Neither fix touched the agent.** Both fixes are board shape. And the method is
explicitly unsettled and open to msilva's input:

> *"a gente tá muito nesse processo de entender o melhor fluxo de como criar as coisas
> no Lin[ear]. Então, não tem muito também um certo nem um errado […] pode ficar à
> vontade[,] cara[,] [se] ter algum insi[ght], alguma maneira que você tá achando que
> não tá funcionando[,] de pegar e de mudar."*

That is a standing invitation to change the conventions, not just follow them.

## Slack integration, offered concretely for the proxy

> *"dá para você conectar lá no canal do que já existe do proxy e aí botar para ele,
> tipo, ah, toda vez que você tiver um update, ele post[a] ou sempre que você mover uma
> tarefa."*

**A proxy Slack channel already exists.** Gabrielle also writes a weekly project
update by hand — project health plus a status note (*"ele traz aqui a saúde do
projeto"*), the most recent being the 7th — and frames it as her own practice rather
than a requirement.

## Debrief on Gabriel — three things the summary omitted

### 1. He switched LLM providers, and that confounds the cost figure

> *"ontem ele trocou o provider, ele tava usando [An]tropic[,] que trocou pro GPT[,] e
> o fluxo dele funcionou, ele falou que foi a primeira vez que funcionou."*

Gabrielle names the confound herself:

> *"fiquei sem saber […] não faço ideia do por[quê,] só [de mu]dado o provider melhorou[.]
> porque foi de 7 e aí eu não sei se foi só uma run que foi 7 para […] 11 centavos[,]
> então porque pode ser só isso ou pode ser que ele tava dando muito contexto."*

> [!danger] Corrects a claim this wiki has been carrying since 2026-08-17
> [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] records the
> $7-vs-11-centavos comparison as *"same flow, same day"* and draws
> **"cost is wildly variable, not inherent"** from it, which then propagated to
> [[Agent Flow]], [[Packaging as skills]] and
> [[Comparing the first-agent candidates]].
>
> **The two runs may not have used the same model.** A provider switch between them —
> one that also turned a never-working flow into a working one — is a large enough
> change that the cost delta cannot be attributed to run-to-run variance. The loop
> hypothesis is **not** refuted; it just lost its main piece of evidence.
>
> Gabrielle independently reached the same hypothesis for the same reason —
> *"Eu não achei que fosse[,] justificava[m] 7. A minha ideia era algum loop […] só
> que aí como eu não tinha esses lo[g]s de execução, fiquei perdido"* — so two people
> converged on it. That is worth something, but it is still a hypothesis, and the
> execution logs are what would settle it.

### 2. The budget anxiety was partly a misread of the mechanism

> *"ele falou que […] pro pessoal lá o budget o máximo é 20 […] é que o pessoal do TI
> eles recarregam $ por semana […] eles botam no workspace que é da empresa inteira e
> cada um tem sua [chave] e vai consumindo. E aí não é necessariamente um budget […]
> que tem que ser recarga pelo cartão."*

IT tops the company workspace up weekly; each person consumes against their own key.
The perceived hard $20 ceiling is not what it looks like. Her guidance to Gabriel:

> *"talvez é só alinhar que durante esse período de teste você vai gastar um pouco mais
> e que, tipo, a gente tá OK com isso, sua liderança tá OK com isso, até você
> estabilizar realmente o fluxo."*

**Leadership has explicitly authorized higher spend during stabilization.** That
qualifies the token-cost constraint on [[Agent Flow]]: it is a real design concern,
but it is not a hard budget gate during an experimentation phase.

### 3. The n8n logging defaults, exactly

> *"Quando a gente cria um fluxo aqui, ele tem por default […] não salvar as execuções.
> Eh, as falhadas ele tem por default, sim, e as que ocorrem em produção não. Então
> vocês tem que vir aqui e botar isso."*

**Failed executions are saved by default; successful production ones are not.** Path:
the three-dots menu → settings. Changed during the call for at least one flow —
*"agora tá salvando os dois porque a gente mudou."*

The symptom that had them both baffled: *"ele rodou lá o fluxo […] tava no execução[,]
tava running, aí quando terminava sumia."* A run vanishing **on completion** is
exactly what "don't save successful production executions" produces.

Also, on the **`conta tech`**: *"quando a gente loga com a conta tec, a gente consegue
ver o de todos"* — the tech login sees everyone's flows, with token input/output
counts per execution, *"[só] que um problema[:] a gente não consegue tipo filtrar por
usuário […] tem que pesquisar pelo nome ou você pode ir rolando aqui […] ele vem por
atualização."*

## The n8n flows are meant to go behind the proxy

Unprompted, at 00:28:09 — and it is the most directly useful thing in the meeting for
msilva's own project:

> *"Inclusive, conectando com a questão do proxy, a ideia é esses projetinhos eles
> passarem pelo proxy também […] todos que use[m Air]table[,] idealmente sim. Só que o
> problema é a gente não tem noção de quantos são e cada pessoa tem sua própria chave.
> Teria que cada pessoa entrar e dar individualmente a chave. […] a ideia era ser
> chave [centralizada]. […] a gente vai ter que entender melhor [a] maneira de aplicar
> isso aqui para dentro."*

Three facts, each new:

1. **The citizen-developer n8n flows are intended proxy consumers.** A consumer class
   the wiki had not recorded at all — distinct from the five named systems.
2. **Nobody knows how many there are.** Unknown cardinality is the blocker.
3. **Each person holds their own Airtable key.** So onboarding them means either every
   person surrendering a key, or the centralized key distribution msilva is already
   building.

That third point is direct validation of the current active task
([[Airtable Proxy]]: *app authentication + centralizing Airtable key distribution*) —
and it identifies the population that most needs it.

## Enablement is now a defined pattern

On the Gabriel consultation:

> *"A ideia é[:] tu ajudou ele destravar e ele vai andar sozinho. […] se ele travar em
> algum momento, ele procura a gente de novo e a gente ajuda[,] ajuda pontual."*

Unblock, then step back, then help on demand. That is the operating definition of the
enabler role [[Agent Flow]] proposes to automate as **A4 Teacher**, stated by the
person who owns the role. She also restated the sharing angle: Gabriel's team has n8n
access and the Claude skills, so extending coverage is a channel change —
*"para abranger o time também, para quando ele tiver fora"*, i.e. so it keeps working
when he is away.

## What this changes elsewhere

| Page | Change |
|---|---|
| [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] | **The provider switch confounds the $7-vs-11-centavos comparison.** |
| [[AI status reporting on Linear]] | Mechanism found: in-house board, issue-level **by design**, drill-down planned. No longer the *only* running agent system. |
| [[Airtable Proxy]] | n8n citizen flows as a consumer class; per-person keys; unknown count; the data hub is probably Phase 2. |
| [[Agent Flow]] | Own initiative (Gabrielle); Claude already authors Linear backlogs; budget tolerance during stabilization; A4 pattern stated. |
| [[Linear Project Structure]] | Milestone-granularity fix; teams vs initiatives; the invitation to change conventions. |
| [[2026-08-18 Product feedback in Linear, code review in Git]] | **Demoted** — *"não tem martelo batido"*. |
| [[2026-08-18 Save n8n execution logs for audit]] | Defaults corrected: failures *are* saved, production runs are not. |
| [[Farol]] | Farol is an initiative; V2 is its second project. |
