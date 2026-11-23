---
type: decision
status: stable
date: 2026-11-23
updated: 2026-11-23
aliases: [registry × interfaces, separação do livestry, PRO-1620]
tags: [livestry, skills, arquitetura]
---

# 2026-11-23 Separar no LiveStry o registro das interfaces

O código do LiveStry passa a separar duas coisas: o **registro de skills** e as
**interfaces** que o usam. O registro define o que é uma skill da LiveMode, quem vê
cada uma e como uma proposta vira PR. As interfaces são as portas de entrada: o
servidor MCP e a chamada de volta do LiveAgents. Cada interface só fala com o
contrato do registro. Tudo continua num **Worker só**.

Decidido por msilva em chat (Claude Code), em 2026-11-23. Rastreado na
[PRO-1620](https://linear.app/projetos-livemode/issue/PRO-1620/separar-no-livestry-o-registro-de-skills-das-interfaces-que-o-usam),
filha da PRO-1616 (curadoria) e pré-requisito da PRO-1614 (virada).

## O que ficou decidido

- **Separar dentro do mesmo Worker.** Um serviço próprio para o registro fica para
  depois. Entre as opções levantadas, foi a "A": pastas `registry/`, `store/` e
  `interfaces/`. Também foram consideradas a "B" (o registro como serviço com API
  própria) e a "C" (as regras de escrita como checks de CI no repositório de skills).
- **O repositório `livemode-claude-skills` continua sendo a persistência** das skills,
  por enquanto.
- **O agente `skill_curator` do LiveAgents continua sendo quem julga** as propostas,
  por enquanto. Fica atrás de uma porta do registro (`Curator`), e o MCP não sabe que
  ele existe.
- **`origem: mcp`** em skill nova criada pelo servidor MCP, no lugar de `livestry`.
  Com o LiveStry sendo o registro inteiro, `livestry` deixou de dizer por qual porta
  a skill chegou. Isso corrige a regra da PRO-1610 e de
  [[Design Doc — Curadoria de skills pelo LiveAgents]] (Interfaces, bloco 1).
- **Registro de uso por interface: adiado.** Os eventos do log (`load_skill`,
  `prompt`, …) têm cara de MCP. Mudar o log pede uma coluna nova no D1. Só vale a
  pena quando houver uma segunda interface de leitura.

## Por quê

O desenho original ([[Desenho de um Skills Registry corporativo]], esboço de
2026-09-28) tinha três peças: o registro, o servidor MCP/API e os usuários. O PRD de
2026-10-27 dizia que "o LiveStry da v1 não é o registry", só um canal de entrega. Só
que o registro existia apenas como dados no git, sem comportamento, e as regras novas
precisavam de um lugar para morar: governança por diretoria (PRO-1359, PRO-1434,
PRO-1593), `origem` (PRO-1610), escrita por PR e curadoria. O único código que havia
era o Worker do MCP. A decisão do PRD foi revertida na prática, sem ninguém decidir
isso.

Onde as camadas se misturavam, lido no código em 2026-11-23 (commit `43c56bc` do
livestry):

- o recorte por diretoria e a regra de mostrar tudo quando falta a diretoria estavam
  no servidor MCP (`src/skills/server.ts:43` e `:180`);
- `pin = true/false` era exposto como parâmetro (`src/skills/propose.ts:39`), e a rota
  HTTP é que escolhia não fixar a origem (`src/worker.ts:104`);
- `origem: "livestry"` estava fixo no código (`src/skills/propose.ts:50`);
- regras e escrita no GitHub ficavam no mesmo arquivo (`planProposal` e
  `createProposer` em `propose.ts`);
- a ADR 0001 do livestry divide o código por **dependência externa** (`skills/` ×
  `adapters/`), não por **componente**. Por isso `server.ts` ficava lado a lado com
  `catalog.ts`.

A segunda interface já existe: `POST /proposals/approved` (PRO-1611). Por isso a
fronteira não é abstração "para o futuro", que a ADR 0001 proíbe.

## Desenho

```
src/
├── worker.ts          montagem
├── registry/          registry.ts, catalog.ts, visibility.ts, proposals.ts, index.ts
├── store/             github-repo.ts (leitura do tarball + escrita do PR), github-app.ts, divisions.ts, usage.ts
└── interfaces/
    ├── mcp/           server.ts, auth.ts, icon.ts
    └── curator/       rota do veredito do LiveAgents
```

```ts
type Registry = {
  catalog(viewer: {diretoria?: string}): Promise<VisibleCatalog>;
  propose(p: Proposal, by: {email: string; via: "mcp"}): Promise<ProposeResult>;
  applyApproved(p: Proposal, by: {email: string}): Promise<ApplyResult>;
};
```

O contrato é o proposto em chat. A forma final sai na ADR 0005 do livestry.

Ordem: (1) ADR 0005 + `DESIGN.md`; (2) mover sem mudar comportamento; (3) puxar as
regras para o registro e trocar a origem; (4) PRO-1614 em cima do contrato.

## Em aberto

- As 17 skills que já têm `origem: livestry` (gravado pela PRO-1610, PR #35 do
  repositório de skills): ficam como estão ou passam por um PR de correção?

## Relacionadas

- [[2026-11-23 LiveStry guarda o status das propostas de skill]]: decisão seguinte,
  que se apoia nesta separação.
- [[Design Doc — Curadoria de skills pelo LiveAgents]]
- [[Desenho de um Skills Registry corporativo]]
