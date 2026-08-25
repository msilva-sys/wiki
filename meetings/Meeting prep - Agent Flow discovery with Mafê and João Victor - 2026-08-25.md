---
type: meeting-prep
status: active
updated: 2026-08-25
date: 2026-08-25
aliases: [mafe joao prep, intake discovery mafe joao]
tags: [agents, discovery, meeting-prep, a1, a10, intake]
---
# Agent Flow discovery com Mafê e João Victor — 2026-08-25

## Por que essa reunião

Executa dois action items de [[2026-08-24 Agent Flow discovery with Carol]] —
mapear como o trabalho de cada um chega, já que nenhum dos dois está em
sistema central. Serve **três propósitos ao mesmo tempo** agora, não dois:

| Propósito | Onde encaixa |
|---|---|
| Fonte de dados do **A10 Portfolio** — o time se comprometeu a começar por aqui | [[2026-08-24 Start Agent Flow with A10 Portfolio]] |
| Mapeamento de canal real de intake — os dois atuam como **primeiro contato**, o que A1 Receptor Universal deveria cobrir | [[Agent Flow]], [[Fluxo Agêntico project instruction]] |
| **Novo, 2026-08-24**: Mafê é a instância humana do front de **Enablement/A4 Teacher** — entender a prática real dela antes de desenhar A4 | [[Maria Fernanda Lemos]] |

## O que já sabemos, entrando

| Pessoa                            | Papel                                                                                                           | Gap conhecido                                                                                                                                                                                 | Fonte                                                                                                          |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| [[Maria Fernanda Lemos]] ("Mafê") | **Analista Pleno, Produto, UX & Adoção de IA** — camada *Habilitadores de IA*; também PM em projetos de produto | Trabalho de habilitação não vive em **nenhum sistema** rastreado (hipótese: é o registro de interações de apoio, uma responsabilidade formal do cargo, que não está sendo feito centralmente) | Carol (gap), cargo colado por msilva do Hub interno (`Brain`, `pessoas/maria-fernanda-mafe`), ambos 2026-08-24 |
| [[João Victor Andrade]]           | Onboarding CRM (N8N/Monday da Yasmin, mapa de arquitetura CRM)                                                  | Backlog de demandas CRM em **planilha pessoal**, não centralizado                                                                                                                             | Carol, [[2026-08-24 Agent Flow discovery with Carol]]                                                          |

Carol: *"nunca foi prioridade nossa"* mapear exatamente tudo que o time faz —
não é um gap por acidente, é ausência de pressão até agora.

> [!important] Mafê não é só uma fonte de dado não-rastreado — ela é o A4 Teacher de hoje
> O cargo dela (*Habilitadores de IA*) é literalmente o segundo dos "três
> fronts" que [[Agent Flow]] descreve, e suas responsabilidades batem quase
> ponto a ponto com a especificação de A4 Teacher: apoio reativo, escalado
> ao nível de letramento de quem pede, sem construir pela pessoa. Ela
> também tem uma função de tipo A6 ("corporatização": identificar quando
> uma solução vira corporativa). Isso muda o peso da conversa com ela — não
> é só "onde está seu backlog", é "como você realmente faz enablement hoje".
> Ver [projects/Agent Flow.md](projects/Agent%20Flow.md).

## Perguntas — mapeamento pra A10 (dados/backlog)

| Pergunta | Por quê |
|---|---|
| Onde exatamente vive o registro de cada demanda hoje (planilha, memória, Slack, nada)? | Definir a fonte concreta que A10 teria que ler |
| Qual a cadência/volume — quantas demandas por semana, mais ou menos? | Sem isso A10 não tem o que agregar |
| O que já é "fechado" vs. "em andamento" vs. "represado" nesse controle informal? | Testar se dá pra extrair estado sem virar trabalho extra pra eles |
| Topariam registrar isso em algum lugar comum (Linear ou outro), ou é fricção demais? | Testa a recomendação da Carol — não precisa ir tudo pro Linear, mas precisa de *algum* mecanismo mínimo de intake |

## Perguntas — Mafê especificamente, mapeamento pra A4 Teacher

| Pergunta | Por quê |
|---|---|
| O registro de "interações de apoio" que o cargo prevê — você faz isso hoje, em algum lugar? Se sim, onde; se não, por quê? | Testa direto a hipótese de que é essa a peça sem sistema, não o trabalho de PM |
| Como você decide quanto apoio dar — o que muda entre uma área "letrada" e uma que não é? | Insumo direto pro diagnóstico de maturidade L0–L3 que a instrução prevê pro A4 |
| Como você reconhece que alguém "já anda sozinho" e para de precisar de você? | É o critério de sucesso do front, segundo a própria Gabrielle (destravar → passo atrás) |
| Quando você identifica que uma solução individual deveria virar corporativa, o que acontece depois — pra quem você leva isso? | Sobreposição com a função de corporatização do A6 — testar se existe hoje um caminho real |
| Quando a demanda passa do seu entendimento e você aciona a "camada técnica" — quem é essa camada, na prática, e como é esse acionamento? | Ponto real de roteamento humano hoje; insumo pro desenho de A1/A2 nesse desvio |
| Seu trabalho de PM em projetos de produto é rastreado em algum lugar (Linear)? | Separa o que já está coberto do que de fato é o gap |

## Perguntas — mapeamento pra A1 (canal de entrada)

| Pergunta | Por quê |
|---|---|
| Por onde as demandas chegam até vocês — DM, e-mail, reunião, alguém chegando pessoalmente? | É exatamente o que A1 Receptor Universal precisa cobrir; o histórico aqui é de fragmentação de canal (bot anterior morreu por isso) |
| Alguém pede pra vocês sem querer que "vire tarefa" — só uma dúvida, um "destrava isso rápido"? | Testa se existe uma classe de demanda que nem deveria virar item de backlog (relevante pro corte trivial/complexo do A2) |
| Quando uma demanda chega errada/incompleta, o que vocês fazem — voltam a pergunta, tentam adivinhar, escalam? | Insumo real pro critério de classificação do A2 (tipo/escopo/complexidade/risco) |
| Existe uma demanda que "sumiu" ou foi esquecida por não ter ficado registrada em lugar nenhum? Exemplo concreto ajuda | Casos reais de custo do não-rastreamento — força argumentativa pro A1+A10 |
> [!tip] Ver [[2026-08-25 Agent Flow discovery with Mafê]] para o que de fato saiu
> A reunião rodou só com Mafê — a metade com João Victor não aconteceu
> junto, ficou para uma conversa separada.

## Depois da reunião

- Atualizar [[Maria Fernanda Lemos]] e [[João Victor Andrade]] com o que saiu.
- Fan-out esperado: [[Agent Flow]] (A10 data sources, A1 open question sobre
  escuta passiva), possivelmente [[What should the Agent Flow research phase study]].
- Se render achado durável, considerar `ingest` como reunião completa —
  `meetings/2026-08-25 Agent Flow discovery with Mafê e João Victor.md`.
