---
type: meeting-prep
status: active
updated: 2026-09-02
date: 2026-09-02
attendees: [Matheus Silva, Carolina Bezerra]
tags: [onboarding, 1-1]
---

# Prep — 1:1 Matheus × Carol (2026-09-02)

Pedido dela na DM (2026-08-28): trazer os combinados do onboarding nas 4
categorias abaixo, e pensar em feedback positivo/negativo das primeiras
semanas e do gerenciamento. Rascunho puxado do estado atual da wiki
([[Airtable Proxy]], [[Agent Flow]], [[index]]) — revisar antes de levar.

## Em andamento → vai pra Setembro

| Item | Onde está |
|---|---|
| Auth do proxy (app-key, além do app-id atual) | camada seguinte à identidade por VPN, ainda não iniciada |
| F3 — Proxy em produção validado c/ LiveScript | projeto Linear ativo, 6 milestones |
| PRO-90 — NEG/load balancer/DNS | bloqueado, faltam identificadores de rede |
| Pulumi state backend (Cloud vs. GCS) | indefinido |
| Rotação das 5 credenciais expostas | purge do git já feito (2026-08-21); rotação em si ainda não — só msilva pode fazer |
| A10 Portfolio + A14 PM Agent (PoC → produção) | SOUL, anticorruption layer, memória via Postgres, digest por projeto e harness de avaliação (Langfuse) já rodando; cron proativo revertido pra status update agregado, ainda não commitado |
| Linear Business trial | expira 2026-09-09, um dia antes da Gabrielle voltar — decisão de compra é dela |

## Não iniciado, mas ainda prioridade

| Item | Por quê segue prioridade |
|---|---|
| PRO-517 — revisitar com Luís a objeção de desenhar memória compartilhada entre agentes | Luís pediu pra não desenhar isso antes de assentar as entidades (2026-08-20); ainda não voltei nisso com ele |
| Acumulação de sinal *entre* agentes ("memória do sistema agêntico") | única parte da lacuna de memória que segue sem dono, mesmo com SOUL já em produção |
| PRO-62 / PRO-96 — migração do lado LiveScript pro proxy | adiado, não abandonado (regra confirmada 2026-08-26) |
| token-terminating auth, OTel/OTLP sobre BigQuery, Cloud Run min=1 | próximos candidatos extraíveis do proxy, ainda não abertos como issue |

## Novo que acho que precisamos cobrir

- Generalizar o padrão DTO≠domínio (anticorruption layer) além do Linear — hoje só existe pra Linear (`domain.py`/`linear_adapter.py`), e A10/A14 vão crescer pra outras fontes.
- [[Bossabox Engagement]] — acompanhar esse discovery externo de perto; o método deles (12 estágios, +40 agentes) mira quase as mesmas dores do Agent Flow.
- Repassar o e-mail verificado da trava de domínio pro backend (`x-lm-user-email`) — ficou em aberto sem prazo desde 2026-08-28.

## O que perdeu o sentido

- A5 Watcher como candidato a "primeiro agente" ou foco especial — deprioritizado (2026-08-24), é só mais um dos 14.
- Identificação de app por header `X-App-Id` — abandonada em favor de identificação por URL path.
- Self-serve tooling pra onboarding de apps novos no proxy — Luís optou pelo caminho manual, sem ferramenta, até existir demanda real.

## Feedback — primeiras semanas e gerenciamento

Não preenchi com conteúdo pronto — isso é reflexão sua, não algo pra eu
inventar. Pontos de partida, se ajudar:

- **Positivo**: o que destravou rápido? (ex.: autonomia total no Linear,
  acesso a repositório concedido sem fricção, "traz opções antes de decidir"
  como combinado claro)
- **Negativo**: onde faltou clareza ou você ficou travado esperando decisão de
  outra pessoa? (ex.: PRO-82/83/76 ficaram um tempo bloqueados em Luís; a
  tensão não resolvida entre "proxy é 100% seu job" vs. "Agent Flow é o
  destaque de agosto")
- **Sobre o gerenciamento da Carol/Gabrielle**: cobertura durante a licença da
  Gabrielle, ritmo dos check-ins, algo que faria diferença pra próxima fase.
