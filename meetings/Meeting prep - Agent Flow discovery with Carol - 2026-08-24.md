---
type: meeting-prep
status: active
updated: 2026-08-24
date: 2026-08-24
aliases: [carol prep, agent flow discovery carol, carol discovery]
tags: [agents, carol, discovery, meeting-prep, a5, a6, a10, diagram]
---
# Agent Flow discovery with Carol — 2026-08-24

Artefato pra conduzir a conversa: o diagrama, o que cada agente faz segundo
a instrução do projeto, e o que Luís e a Gabi já disseram sobre cada um.

## Diagrama — [[Fluxo Agêntico diagram]]

![[fluxo agêntico diagrama 0.png]]

Sólido = fluxo principal · pontilhado = contexto/feedback.

## Os 14 agentes

| Agente | Descrição ([[Fluxo Agêntico project instruction]]) | Luís acha | Gabi acha |
|---|---|---|---|
| **A1** Receptor Universal | Captura de qualquer canal (Slack, Monday, email, formulário, webhook, alerta de sistema); normaliza; enriquece via A6/A13. Meta < 10s | **A1+A2 é um agente só** — "para mim eles são a mesma coisa" (2026-08-20). Tudo passa pelo classificador por ora, sem exceção por agente | Dúvida se precisa ser dois agentes ou um (2026-08-19) |
| **A2** Classificador & Decisor | Análise multi-dimensional (tipo, escopo, complexidade, risco); roteia para um dos 3 ramos | Mesma fusão do A1 | Mesma dúvida do A1 |
| **A3** Executor | Orquestra bugs/automações/manutenção; cria sub-agentes sob demanda; escala pra humano se falhar; recebe feedback do A5 | **A3 = A9, mesmo agente** (2026-08-20, reverte divisão anterior); sub-agentes de dev são escopo do harness do projeto, não da arquitetura (2026-08-19) | A3 é o rápido/operacional, A9 o de trás do discovery completo (2026-08-19) — divisão que o Luís depois reverteu |
| **A4** Teacher | Ensina áreas a se virarem sozinhas; diagnostica maturidade L0–L3; tutoriais personalizados; acompanha execução | Reabre se é agente de verdade ou só skill, pelo teste **agir/informar** (2026-08-20); segue distinto do Discovery porque o output difere — resposta a uma pessoa vs. material bruto pra decisão | Padrão dela (2026-08-18): destravar → dar um passo atrás → ajudar sob demanda; sucesso = a pessoa andar sozinha |
| **A5** Watcher | Saúde a cada 5min, uso a cada hora, relatório a cada 24h; detecta incidente e oportunidade; alimenta A3 | Não deveria ser escopado pelo proxy (2026-08-18, afinado 2026-08-19). **Despriorizado como candidato a primeiro agente, 2026-08-24** — [[2026-08-24 Deprioritize A5 Watcher as first-agent candidate]] | Alcance do proxy (Orca) era o argumento de utilidade mais forte (2026-08-18); dois caminhos propostos — A) por projeto, B) consolidador (2026-08-19) |
| **A6** Curator | Memória institucional, aprendizado contínuo, corporatização (redundância entre áreas), inteligência estratégica — hub, todo agente se conecta | **Não é agente autônomo por ora** — se é só skill, é skill; se é só memória a serviço de outra parte, não precisa de agente dedicado (2026-08-20). Mas organizar bem a memória é "talvez a peça mais importante" do projeto | Nomeou as 4 funções ao vivo; sinalizou que pode ser demais pra um agente só (2026-08-19) |
| **A7** Discovery | Discovery autônomo via chat; gera PRD completo (histórias, requisitos, arquitetura, estimativa, riscos, MVP-vs-completo); consulta A10/A11/A12; aprovação humana só no fim | "Convergir achados num PRD" pode ser etapa/agente separado do Discovery em si — não fechado (2026-08-20) | Precisa de contexto em vários níveis (produto, portfólio, empresa, Watch); A8 ganha papel de checar se o contexto já é suficiente (2026-08-19) |
| **A8** Orchestrator | Roda o projeto aprovado — setup, monitoramento diário, riscos/bloqueios, escopo, status a stakeholders, replanejamento, go-live | **A8+A9 ≈ "o próprio Claude Code"** — orquestrador que sobe sub-agentes sob demanda já é isso (2026-08-20) | Novo papel: lê o BRD do A7 e julga se já há contexto suficiente antes de seguir (2026-08-19) |
| **A9** Developer | Do PRD à produção; cria sub-agentes; stack controlada (React, Python, Node, APIs conhecidas, N8n, Vercel/Replit — sem Go); 3 tentativas, sempre entrega algo funcional | Mesmo agente que A3; sub-agentes de dev são escopo do harness do projeto (2026-08-19); A8+A9 ≈ Claude Code (2026-08-20) | "O revisor trabalhante de produção" — executor específico do pipeline de projetos, atrás do discovery completo, distinto do A3 (2026-08-19) |
| **A10** Portfolio | Analisa backlog e capacidade; detecta itens travados, priorização desalinhada, gargalos, escopo descontrolado; colabora com A11/A12; "sugere, nunca executa" | É preocupação real e distinta, não "inteligência transversal" genérica (2026-08-20); relata que a própria Gabrielle começaria por aqui, pela dor de visibilidade dela; pediu protótipo em LangGraph pra comparar com Claude puro | Em aberto se a ferramenta de intranet da Carol já é isso (escopo empresa-toda, redundância/anomalia) (2026-08-19); dor dela (visibilidade entre projetos) é por que começaria aqui, relatado pelo Luís (2026-08-20) |
| **A11** Product | Análise de uso real (DAU/WAU/MAU, features usadas, tempo por tela, erros, abandono); "investiga antes de recomendar" | Também "coisa realmente separada", não intel. transversal genérica (2026-08-20) | A10 alimenta o A11, que analisa uso real e produz os relatórios que o A10 mostra (2026-08-19) |
| **A12** Data Gov | Soft enforcement (alerta, permite) pra dado sensível/formato errado/baixa qualidade; hard enforcement (bloqueia) pra API externa não autorizada, export de compliance crítico, deleção de base inteira | Trata A12 (com A13) como **"harness"** — camada de guardrail, não agente que raciocina (2026-08-20) | Resolvido como validação de uso de dado (ex.: fonte tem que vir do repositório de competições); dúvida se esse time é dono disso, ou se é da fundação/plataforma (2026-08-19) |
| **A13** Deduplication | Detecta e bloqueia trabalho duplicado; alimenta contexto pro A1 antes de entrar no fluxo | **O único dos 5 que cabe como "inteligência transversal"** de fato (2026-08-20); tratado como harness junto com A12 | Em aberto se é o mesmo agente que A10 (sobreposição de detecção de redundância) (2026-08-19) |
| **A14** PM Agent | Acompanha do pedido à entrega; garante visibilidade de status; comunica proativamente; ponto de contato automatizado | Linear já posta release note no Slack sozinho quando um projeto sobe de versão — infra que o A14 (ou algo mais simples) já poderia usar (2026-08-19); terceira voz (com Carol e Gabrielle) apontando **A10+A14** como ponto de partida (2026-08-20) | Camada de log/relatório observando a coluna do projeto, produzindo status a stakeholders pedindo aprovação (2026-08-19) |

Fold no [[Agent Flow]], sínteses e `index.md` depois — não durante.
Divergência com Gabi/Luís → registrar, não escolher vencedor.
