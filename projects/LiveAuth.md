---
type: project
status: active
updated: 2026-09-30
aliases: [liveauth, liveauth poc]
tags: [auth, security, liveauth, proxy]
---

# LiveAuth

> Related: [[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]] · [[Airtable Proxy]]

Autorização por grupo compartilhada entre apps internos da Livemode. POC em
andamento no Linear — iniciativa **LiveAuth**, projeto **LiveAuth - POC**
([P-PRO-29](https://linear.app/projetos-livemode/project/liveauth-poc-c6dfa1c702b0)),
lead msilva, team `Projetos-livemode`. O desenho completo (objetivo,
interfaces, segurança, open issues) vive na synthesis linkada acima; esta
página acompanha o estado de execução.

## Estado, 2026-09-24

Status Linear: **In Progress**. `startDate` 2026-09-23, `targetDate`
**2026-09-25** (amanhã).

- [x] PRO-711 — Restringir login do LiveAuth a contas @livemode.com (Blocking Function no Firebase Auth)
- [x] PRO-714 — Login único do LiveAuth decide quem entra em cada app parceiro
- [x] PRO-713 — Tela pra montar o grupo `admin-proxy` e gerenciar membros
- [ ] PRO-717 (In Progress, msilva) — skill separada que aponta apps com dado
      sensível sem autenticação, ou com login fora do padrão único (fora do
      LiveAuth e do Sentinela por enquanto)

**Falta issue de deploy em produção** — nenhuma das quatro cobre isso, e o
target date é amanhã. Sinalizado por msilva em 2026-09-24, sem issue criada.

## Open questions (herdadas da synthesis)

Nome "LiveAuth" fechado com Luís, falta validar com a Gabi; dono da infra
segue indefinido — ver a lista completa na synthesis. **Projeto Firebase
já resolvido** (2026-09-24): o repo `livemode-org/livemode-liveauth` já
existe, `.firebaserc` aponta pro projeto dedicado
`livemode-liveauth-42ca1`, não o `livemode-roteiros-dev` compartilhado.

## Impacto de uma futura migração

Inventário de apps com auth própria feito em 2026-09-24, a pedido de
msilva. **Quatro rodadas de correção no caminho** — cada uma achada por
uma pergunta direta de msilva ou por checagem mais funda: busca de
código do GitHub tinha lacuna de indexação; varredura de árvore só
olhava raiz; grep de palavra-chave só olhava nome de arquivo, não
conteúdo (perdeu auth embutida direto em `index.html`). Total final:
**20 deploys** reimplementando gate de e-mail/domínio cada um do seu
jeito, fora o Fluxo Agêntico — **21 apps** no total, nenhum consumidor
do LiveAuth hoje. Mais 1 app confirmado **aberto de propósito**
(`copa2026-labelling`, banco Firebase com regra pública) e 2 candidatos
não confirmados por código (`painel-salas`, `livemode-brain`). Achado
notável: `livemode-projects-management` já tem autorização por grupo
própria (`authz.ts`), o precedente mais próximo do objetivo do LiveAuth
achado no levantamento. Ver
[[Trava de domínio e autenticação — inventário para o LiveAuth]].

## Discovery — casos de uso levantados, 2026-09-30

Em [[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]]:

- **[[Gustavo Cruz]]**, área fiscal, opera o "Hub de Automações Fiscais" —
  dashboards sobre dados do ERP TOTVS, travados só por senha, acessíveis
  externamente, sem rate limiting, vulneráveis a força bruta. Caso real do
  problema que o LiveAuth quer resolver; ainda não decidido se migra pro
  LiveAuth ou só recebe mitigação via Sentinela por ora.
- **[[Marina Ferrão]]** (atendimento), via Gustavo: planilha do núcleo criativo
  que outros times precisam ler mas só o time dono pode editar — requisito
  de granularidade de permissão por time, ainda sem desenho técnico.
