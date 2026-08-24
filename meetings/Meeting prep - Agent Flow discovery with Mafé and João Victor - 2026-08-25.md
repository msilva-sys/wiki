---
type: meeting-prep
status: active
updated: 2026-08-24
date: 2026-08-25
aliases: [mafe joao prep, intake discovery mafe joao]
tags: [agents, discovery, meeting-prep, a1, a10, intake]
---
# Agent Flow discovery com Mafé e João Victor — 2026-08-25

## Por que essa reunião

Executa dois action items de [[2026-08-24 Agent Flow discovery with Carol]] —
mapear como o trabalho de cada um chega, já que nenhum dos dois está em
sistema central. Serve **dois propósitos ao mesmo tempo**, não só um:

| Propósito | Onde encaixa |
|---|---|
| Fonte de dados do **A10 Portfolio** — o time se comprometeu a começar por aqui | [[2026-08-24 Start Agent Flow with A10 Portfolio]] |
| Mapeamento de canal real de intake — os dois atuam como **primeiro contato**, o que A1 Receptor Universal deveria cobrir | [[Agent Flow]], [[Fluxo Agêntico project instruction]] |

## O que já sabemos, entrando

| Pessoa | Papel | Gap conhecido | Fonte |
|---|---|---|---|
| [[Maria Fernanda Lemos]] ("Mafé") | — (não detalhado ainda) | Trabalho do dia a dia não vive em **nenhum sistema** rastreado | Carol, [[2026-08-24 Agent Flow discovery with Carol]] |
| [[João Victor Andrade]] | Onboarding CRM (N8N/Monday da Yasmin, mapa de arquitetura CRM) | Backlog de demandas CRM em **planilha pessoal**, não centralizado | Carol, mesma fonte |

Carol: *"nunca foi prioridade nossa"* mapear exatamente tudo que o time faz —
não é um gap por acidente, é ausência de pressão até agora.

## Perguntas — mapeamento pra A10 (dados/backlog)

| Pergunta | Por quê |
|---|---|
| Onde exatamente vive o registro de cada demanda hoje (planilha, memória, Slack, nada)? | Definir a fonte concreta que A10 teria que ler |
| Qual a cadência/volume — quantas demandas por semana, mais ou menos? | Sem isso A10 não tem o que agregar |
| O que já é "fechado" vs. "em andamento" vs. "represado" nesse controle informal? | Testar se dá pra extrair estado sem virar trabalho extra pra eles |
| Topariam registrar isso em algum lugar comum (Linear ou outro), ou é fricção demais? | Testa a recomendação da Carol — não precisa ir tudo pro Linear, mas precisa de *algum* mecanismo mínimo de intake |

## Perguntas — mapeamento pra A1 (canal de entrada)

| Pergunta | Por quê |
|---|---|
| Por onde as demandas chegam até vocês — DM, e-mail, reunião, alguém chegando pessoalmente? | É exatamente o que A1 Receptor Universal precisa cobrir; o histórico aqui é de fragmentação de canal (bot anterior morreu por isso) |
| Alguém pede pra vocês sem querer que "vire tarefa" — só uma dúvida, um "destrava isso rápido"? | Testa se existe uma classe de demanda que nem deveria virar item de backlog (relevante pro corte trivial/complexo do A2) |
| Quando uma demanda chega errada/incompleta, o que vocês fazem — voltam a pergunta, tentam adivinhar, escalam? | Insumo real pro critério de classificação do A2 (tipo/escopo/complexidade/risco) |
| Existe uma demanda que "sumiu" ou foi esquecida por não ter ficado registrada em lugar nenhum? Exemplo concreto ajuda | Casos reais de custo do não-rastreamento — força argumentativa pro A1+A10 |

## Cuidado ao registrar depois

- Anonimização padrão do Linear **não se aplica aqui** (é wiki, não issue) — mas
  ainda assim, preferir atribuir citações a "Mafé" / "João Victor" only quando a
  frase realmente precisar de dono; descrições de processo em geral não
  precisam de atribuição pessoal.
- Distinguir **o que eles disseram** de **conclusão do msilva** — mesma regra
  de sempre pra transcritos (`CLAUDE.md`, seção `ingest` — variante de reunião).

## Depois da reunião

- Atualizar [[Maria Fernanda Lemos]] e [[João Victor Andrade]] com o que saiu.
- Fan-out esperado: [[Agent Flow]] (A10 data sources, A1 open question sobre
  escuta passiva), possivelmente [[What should the Agent Flow research phase study]].
- Se render achado durável, considerar `ingest` como reunião completa —
  `meetings/2026-08-25 Agent Flow discovery with Mafé e João Victor.md`.
