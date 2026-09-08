---
name: finish-day
description: Commita e dá push nos repositórios em que trabalhei hoje (mudanças pendentes ou commits locais de hoje ainda não pushados), com confirmação antes de agir. Use quando eu disser "finish-day", "fecha o dia", "fecha os repos" ou /finish-day.
---

Varre os repositórios abaixo, mostra o que encontrou, **espera confirmação**,
e só então comita e dá push no que foi confirmado.

## Raízes a varrer

- Cada subpasta de primeiro nível de `C:\Users\msilva\projects\` que tenha
  `.git` — descubra dinamicamente, não hardcode nomes.
- `C:\Users\msilva\Documents\work`
- `C:\Users\msilva\Documents\projetos-wiki`

## Critério "trabalhei hoje"

Um repo entra na lista se, na data de hoje (contexto da sessão):
- `git status` mostra algo pendente (staged, unstaged ou untracked), **ou**
- existe commit local de hoje ainda não pushado (`git log @{u}..HEAD` — ou,
  sem upstream, o commit mais recente do repo — com data de hoje).

Repos sem nada disso: ignore, sem mencionar no relatório.

## Passo 1 — levantamento

Para cada repo candidato, rode `git status` (e, se houver merge/rebase em
andamento, HEAD destacado ou conflito, marque como "pular" — nunca tente
resolver). Monte uma lista curta, um repo por linha: nome, o que tem
pendente (N arquivos mudados / N commits locais não pushados), e se vai
precisar de commit, push, ou os dois.

## Passo 2 — confirmação

Mostre essa lista a msilva e **pergunte antes de agir** (via
`AskUserQuestion`, multiSelect com os repos candidatos, todos pré-selecionados
por padrão) — ele pode confirmar todos, tirar algum da lista, ou cancelar. Não
comite nem dê push em nenhum repo antes dessa confirmação.

## Passo 3 — por repo confirmado

1. Se há mudanças pendentes:
   - `git diff` para ver o que mudou; confira se algum arquivo parece
     credencial/segredo (`.env`, chaves, tokens) antes de stagear — se achar,
     pare e avise, não commite.
   - `git add` só os arquivos relevantes (nunca `-A` cego se houver algo
     suspeito na lista).
   - Gere a mensagem de commit a partir do diff usando a skill
     `caveman-commit` (Conventional Commits comprimido).
   - `git commit`.
2. Se o branch atual tem upstream: `git push`. Sem upstream: **não** crie um
   automaticamente — pule o push e relate "sem upstream configurado".
3. Nunca `--force`, nunca mexer em outro branch além do atual.

## Relatório final

Lista curta, um repo por linha: nome do repo, o que foi feito (commit+push /
só commit / só push / pulado e por quê). Sem prosa.
