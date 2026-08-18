---
type: decision
status: draft
updated: 2026-08-18
date: 2026-08-18
decided_by: nobody — stated as a preference by Gabrielle Ferreira
source: "[[2026-08-18 1-1 Matheus - Gabrielle]]"
tags: [process, linear, git, review, product]
---

# Product feedback in Linear, code review in Git

> [!danger] Not a decision — demoted 2026-08-18, the same day it was filed
> This page was first written from a version of the raw file containing **only
> Gemini's summary**, which presented the split as settled (*"Feedback de produto foi
> centralizado exclusivamente na plataforma"*). The actual transcript says the
> opposite:
>
> *"também não tem um o o martelo martelo batido de qual que é o melhor fluxo. Acho que
> talvez um funcione melhor. Acho [que faz] sentido a gente […] concentrar os reviews e
> coisas relacionado ao produto aí e a parte [de] desenvolvimento lá[,] de reviews."*
>
> **"Não tem martelo batido"** — nothing has been hammered down. What exists is a
> reasoned preference from msilva's manager, offered in answer to his direct question,
> alongside an explicit statement that the team is still working out the right flow.
>
> `status: draft` and `decided_by: nobody` reflect that. The page is kept rather than
> deleted because the *practice* below is real and observed, and because several pages
> already link here. **Deleting or moving it out of `decisions/` needs msilva's call.**

## The stated preference

| Kind of feedback | Where Gabrielle thinks it belongs |
|---|---|
| Product behaviour, business rules | **Linear**, in the issue's comments |
| Strictly technical, code-level | **Git** |

msilva's question was what prompted it: *"a minha dúvida era mais se os feedbacks e
revis[ões] eram centralizados lá no bit[bucket] ou aí."*

## The practice actually observed — this part is not in doubt

Gabrielle described what she did the day before with Luís's work, and it is concrete:

> *"ele jogou várias tarefas que ele tava numa branch lá e ele subiu pra produção, que
> no caso seria pra homologação. E aí eu validei, tipo, fazendo testes e tudo mais. E
> aí o que tava certo já jogava para aprovado[,] que não, eu voltava para ele olhar e
> já botava o fluxo de comentários ali."*

- **Gabrielle is the product reviewer.** Not an unnamed role — her.
- The target is **homologation**, not production. Worth being precise about.
- Her method is interface testing: *"eu entrar lá na interface"*. Approve, comment, or
  send back.
- **Luís reviews Yasmin's work differently** — *"ele acaba olhando mais o git"* —
  because he is checking what was actually implemented, not whether it behaves.

So the split isn't a policy imposed on two people; it is **two reviewers with
different questions naturally using different tools.** That is a more durable reason
for the arrangement than a rule would be, and it is why the preference is probably
right even though it isn't decided.

## Two mechanical facts that follow

- **Linear comments do not reach Git.** *"isso daqui ele não vai pro Git […] ele tem a
  descrição novamente do que que é, mas ele não tem os comentários. Os comentários
  estão lá nas tarefas."* The PR carries the description and nothing else, so **the
  review history genuinely lives in two places** with no sync. Anyone reconstructing
  why something was built a certain way has to check both.
- **Subissue ↔ PR is not 1:1.** *"Cada subi[ssue] dessa é uma PR. Depende. Aqui ele fez
  uma PR só para todas elas."* Which means per-subissue product validation can sit on
  top of a single PR, and Gabrielle validated per subissue anyway.

## Why it still matters to msilva

- **His proxy work will eventually pass through a product validator** — not yet, since
  the proxy is pre-production and has no interface to test, but the *company-wide
  infrastructure* framing ([[Airtable Proxy]]) means it acquires product-facing
  behaviour eventually.
- **Issue descriptions are what a non-engineer reads.** They are the input to
  validation, which raises the bar on the restructured proxy issues. Luís's Markdown
  templates ([[Linear Project Structure]]) are the intended aid — though he is
  rewriting them because he dislikes how they turned out.
- **Git review remains msilva's own** for the proxy, per
  [[2026-08-14 No mandatory PR review while the proxy is pre-production]]. Nothing here
  reopens that.

## How it reads against the PR-review decision

The area removed mandatory human PR review while the proxy is pre-production. Read
alongside this page, review did not disappear — it **moved and specialized**: the
technical gate relaxed, a product gate emerged in practice. Two decisions about two
different risks.

**But be careful with that framing now.** The product gate is not a decision, it is
what Gabrielle happens to do, and she is on leave from 2026-08-24. Whether it persists
while she is away is untested.

## Bearing on [[Agent Flow]]

The architecture's premise is *humans as strategic approvers and quality refiners*
([[Fluxo Agêntico project instruction]]). This is that premise observed: strategic
approval is product's, in Linear; quality refinement is technical, in Git. An agent
submitting work has to know which gate it is aiming at — different reviewers,
different vocabularies, different rejection criteria.

For **A2**, whose output is a ticket, the gate is the Linear comment thread, which the
Slack integration can already deliver to a channel.

## Open questions

- **Does the return path have a state, or only a comment?** Matters for anything
  computing progress from the board.
- **Does Luís put review comments back into Linear or only Git?** Gabrielle didn't
  know: *"eu só não sei te dizer se quando ele pegava alguma coisa da Yasmin e ele
  voltava se ele botava no git."*
- **Does a product rejection require a Git revert?** Unstated, and it is the seam
  between the two surfaces.
- **Who validates while Gabrielle is on leave?** The role is her.
- **Should this page live in `decisions/` at all?** msilva's call.
