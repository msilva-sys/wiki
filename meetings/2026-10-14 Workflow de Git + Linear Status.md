---
type: meeting
status: stable
date: 2026-10-14
updated: 2026-10-16
attendees: [Luís Fernandez, Gabrielle Ferreira, Yasmin Macedo, Matheus Oliveira da Silva, Carolina Bezerra]
aliases: []
tags: [git, linear, homologacao, cloudflare, vercel, processo]
---

# 2026-10-14 Workflow de Git + Linear Status

Proposta de fluxo de Git e de mapeamento de status no Linear, apresentada
por [[Luís Fernandez]]. Fonte:
`raw/2026-10-14 Workflow de Git + Linear Status.md` (ata e transcrição do
Gemini, ~58 min).

## Decisões

**Nenhuma sobre o fluxo de Git.** A própria ata do Gemini classifica o item
como **"Precisa de mais conversa"**. O que ficou acordado foi menor:

- **Projetos simples usam fluxo direto**, sem a estrutura exigida para
  sistemas complexos. Pedido de [[Carolina Bezerra]], aceito pelo grupo —
  cita [[Farol]] e apps de tarefa como exemplos.
- **Nova reunião será marcada** para apresentar o fluxo detalhado ao time.

## Commitments

Todos de [[Luís Fernandez]]:

- Listar os sistemas a migrar para a Cloudflare, priorizando os mais
  importantes.
- Montar a homologação do [[LiveScript]] na Cloudflare.
- Organizar nova reunião para apresentar o fluxo de Git ao time.
- Melhorar a representação gráfica do fluxo, incluindo o retorno de
  releases e hotfixes para desenvolvimento.
- Preparar material sobre os dois fluxos — o burocrático e o simples — e
  explicar a lógica por trás da adoção.

Do grupo:

- Definir os status das tarefas no Linear de acordo com o fluxo de
  homologação proposto.

## Perguntas em aberto

- **O fluxo completo é burocrático demais para o tamanho do time?** Questão
  levantada por [[Gabrielle Ferreira]] e não resolvida — é o motivo de o
  item ter ficado como "precisa de mais conversa".
- **As regras do Linear se estruturam por projeto ou por pessoa?** Debatido
  considerando limites de times e a necessidade de isolar configuração para
  projetos mais complexos. Sem conclusão.
- **A Vercel é abandonada por completo?** Luís expressou ceticismo —
  avaliou que manter projetos complexos na Vercel pode ser preferível,
  enquanto projetos simples criados na Cloudflare permanecem nela.

## Fatos declarados

- **Migração Vercel → Cloudflare** (grupo): apresentada como diretriz da
  empresa para conter custo, com cerca de **200 pessoas desenvolvendo
  sistemas de forma descentralizada**. Carolina defendeu governança mínima,
  o que fundamentou a escolha. *(Registro como fato declarado nesta
  reunião, não como decisão tomada nela — a diretriz vem de fora.)*
- **Estrutura de branches proposta** (Luís): `main` protegida, `release/*`
  para testes, `develop` para organização do trabalho, e branches de
  feature amarradas ao Linear. PR obrigatório para qualquer alteração.
- **Regra de fluxo** (Luís): atualizações correm sempre das branches mais
  importantes para as menos importantes — produção, release, develop,
  feature — garantindo que produção permaneça a versão mais estável.
- **Banco de homologação** (Luís): sandbox espelhado em produção, exigindo
  etapas manuais de deploy em paralelo ao código, como atualizar colunas no
  Airtable.
- **Testes** (Luís, respondendo a Gabrielle): a suíte roda localmente e nos
  estágios de revisão e integração contínua.
- **Resolução de conflito não deve ser automatizada** (Luís): revisão
  humana é indispensável; a prática recomendada é comunicação diária no
  Slack para que as pessoas tragam atualizações manualmente para suas
  branches. Scripts podem automatizar finalizações rotineiras de hotfix.
- **Separação de chaves por ambiente** (Luís): chaves de API e ferramentas
  de monitoramento como LogRocket e Bugsnag devem ser estritamente
  separadas entre produção, homologação e desenvolvimento, para evitar
  alarme falso.
- **Rastreabilidade** (Luís): vincular commits e PRs às chaves do Linear
  (citou `PRO-997` como exemplo) facilita investigação de falha e análise de
  crash. Tags do Git marcam a geração de versões.
- **Ambientes de preview na Vercel são trabalhosos** (Luís), o que leva o
  time a publicar direto em produção nos sistemas simples. Ele expressou
  ressalvas quanto a usar preview em sistemas críticos com múltiplos
  colaboradores, citando risco de conflito em bases de homologação e
  problemas com autenticação via Firebase ou SSO.

## Correção pelo código

Conferido no repo do [[LiveScript]] em 2026-10-16: **parte do fluxo
discutido já está implementada lá**. Os commits `c94dde5`
(`chore(vercel): auto-deploy release/* branches to staging`), `b1d3d27` e
`9461d9d` mostram branches `release/*` com deploy automático para staging
na Vercel, e `0a523af` adiciona tag em `main` com a versão do
`package.json` após cada push.

A reunião tratou homologação e `release/*` como proposta a debater; no
LiveScript já são prática. A divergência vale registrar — a conversa que
falta é sobre generalizar o fluxo para os outros projetos, não sobre
inventá-lo.
