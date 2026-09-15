---
type: meeting
status: stable
updated: 2026-09-15
date: 2026-09-15
attendees: [Matheus Silva, Gabrielle Ferreira]
transcription_confidence: low
aliases: [discovery a1 a2 gabi, reunião gabrielle a1 a2]
tags: [agent-flow, agents, a1, a2, gabrielle, discovery, portfolio, airtable, priorizacao]
---

# Discovery A1 e A2 com Gabrielle — 2026-09-15

Continuação de [[Meeting prep - Discovery A1 e A2 com Gabrielle - 2026-09-15]].
Objetivo duplo: discovery do projeto **A1 & A2** no Linear (PRO-543/544) e,
aproveitando a conversa, PRO-595 (regras de priorização de portfólio que
Gabrielle já tem, levantadas por Carolina em
[[2026-09-14 Carolina - Matheus (critérios A10-A14)]]).

**Confiança de transcrição baixa** — mesmo problema de diarização da
reunião com Carolina (2026-09-14): quase toda fala vem rotulada
"Gabrielle Ferreira", incluindo trechos que pelo conteúdo são claramente
do msilva (ex.: *"Não, eu já vou te pedir, eu preciso desse cálculo"*,
*"Eu acho que o projeto seria tranquilo"*). Atribuição abaixo por
inferência de conteúdo, não por rótulo confiável.

## Decisions

Nenhuma decisão formal fechada — reunião de discovery, sem fechamento.

## Commitments

- msilva vai marcar a reunião com Carolina, Luís e Gabrielle pra definir os
  critérios novos de priorização de portfólio — PRO-595.
- msilva vai pensar em formatos de exposição do A1 receptor (bot num canal,
  menção em DM, encaminhamento) e compartilhar com Gabrielle.

## Open questions

- Quais critérios de priorização de portfólio a área vai usar daqui pra
  frente. A fórmula ponderada do GPT antigo de Gabrielle (ver apêndice)
  caiu em desuso segundo ela mesma e não deve servir de base — PRO-595.
  A **filosofia** por trás (testar pequeno antes de estruturar, "projeto
  não é autorização pra agir") não foi descartada por ela e pode seguir
  como insumo pro desenho do A2/A7, independente da fórmula de score.
- Vale manter um cálculo ponderado (fórmula com pesos por critério) ou
  simplificar? Só faz sentido manter pesos diferentes se o time ainda
  quiser isso — em aberto, decisão da reunião futura com Carol e Luís.
- Como demandas soltas (que não são um projeto) entram no Linear sem
  forçar todo pedido a virar "Projeto"? Gabrielle aponta a aba/status
  **Triagem** como candidata — não testada, não confirmada.
- Ferramenta própria da Gabrielle (a que ela chama de app/"Wikly") ainda
  cria tarefa no Airtable em vez do Linear quando alguém abre uma tarefa
  em seu nome — bug/gap identificado na própria conversa, não corrigido.
- Por que a conta do GPT Business consome crédito ao ser acessada via
  Codex — pendência tangencial, checar com TI.

## Facts stated

- Gabrielle: hoje a área recebe demanda por duas fontes que ainda não
  passam pelo Linear — tarefas soltas continuam sendo criadas no Airtable
  (inclusive pela ferramenta dela, que aponta pra lá e não pro Linear), e
  mensagem direta no Slack, sem registro em lugar nenhum.
- Gabrielle: todo o histórico de projetos anterior ao "marco zero" da
  migração pro Linear ([[2026-08-14 Migrate project management from Jira
  to Linear]]) — backlog nunca tocado, projetos concluídos, abandonados —
  vive só no Airtable, nunca foi migrado.
- Gabrielle: o board de priorização de portfólio (antigo) tinha uma coluna
  de "hold" com motivo explícito registrado (dependência de projeto
  anterior, falta de gente disponível, ou pedido do próprio solicitante
  pra não priorizar agora).
- Gabrielle: ela mesma construiu um GPT customizado — **"priorizador de
  projetos"**, https://chatgpt.com/gpts/editor/g-6967b8ab3140819197cda61702e3a006
  — prompt completo colado por msilva no apêndice abaixo. Quatro critérios
  ponderados (**peso 2** só pra Valor pro Negócio; Escalabilidade, Economia
  de Tempo e Complexidade de Desenvolvimento todos **peso 1**, escala
  1/3/5, Complexidade invertida — baixa complexidade pontua 5) mais uma
  filosofia central de fundo: "testar primeiro é dar autonomia", projeto
  não é autorização pra agir, prefere matar rápido a escalar cedo. Rodava
  toda quinta antes da reunião de priorização (ela, Carol, Luís, Arthur);
  trazia nota sugerida por critério, o grupo validava/ajustava.
- Gabrielle: a fórmula de score caiu em desuso — não deve servir de base
  pra conversa nova com Carol e Luís. A filosofia de fundo (testar antes
  de estruturar) não foi descartada por ela — é o comportamento que ela
  elogiou no próprio GPT (sugerir teste pequeno antes de projeto
  corporativo), possível insumo pro A2/A7 independente da fórmula.
- Gabrielle: canal oficial hoje é `#resolveaqui-livemode` ("resolve
  aqui"), mas é minoria do volume real — maioria ainda manda mensagem
  direta no Slack. Existiu um formulário antigo na plataforma "Fill",
  removido de propósito quando as áreas passaram a resolver mais sozinhas
  (pra reduzir pedido indiscriminado) — isso, sem querer, também apagou o
  conhecimento de qual é o canal oficial hoje.
- Gabrielle: ideia inicial pra expor o A1 receptor — bot hospedado na
  Vercel, conectado a uma skill do Claude que acessa o Slack. Formato
  ainda não decidido (canal que só encaminha vs. bot mencionável em DM).

## Notable quotes

- Gabrielle: *"A gente não tem um local meio que unificado para receber
  essas demandas."*
- Gabrielle, sobre os critérios antigos: *"isso daqui meio que caiu em
  desuso, então não acho que a gente deveria usar isso daqui como base."*

## Depois da reunião

Achados aqui ainda não foram levados pro Linear — msilva revisa antes de
qualquer atualização em PRO-543/544/595.

## Anexo — prompt completo do GPT priorizador (colado por msilva, 2026-09-15)

Copiado da aba Configure do GPT
(https://chatgpt.com/gpts/editor/g-6967b8ab3140819197cda61702e3a006),
depois da reunião, a pedido de msilva. Verbatim, não editado:

> Você é um Especialista Sênior em Priorização de Projetos Estratégicos e Operacionais.
> Atue como alguém que participa de comitês executivos, mas com forte viés de
> descentralização, aprendizado rápido e uso responsável de estrutura.
> Lembre-se sempre: projeto não é autorização para agir — é consequência do aprendizado.
>
> CONTEXTO CULTURAL (NÃO É REGRA, É JEITO DE PENSAR)
> A organização identificou riscos claros:
> - Estrutura cedo demais para problemas simples
> - Projetos grandes para aprendizados pequenos
> - Centralização excessiva do que poderia ser testado de forma autônoma
> - Pessoas esperando "projeto" para agir
> - Alto custo de agenda antes de testar algo simples
>
> PRINCÍPIO FUNDAMENTAL
> "Testar primeiro é dar autonomia."
> Se dá para testar sozinho, a pessoa testa.
> Estrutura vem depois do aprendizado, não antes.
>
> CRITÉRIOS DE PRIORIZAÇÃO (USE APENAS SE PASSAR PELA LENTE)
> Avalie somente se fizer sentido virar projeto.
>
> 1) Valor para o Negócio (Peso 2)
> - Alto (5): impacto direto e relevante em receita, eficiência, risco ou posicionamento
> - Médio (3): suporte a iniciativas maiores ou melhoria operacional relevante
> - Baixo (1): impacto indireto, exploratório ou aprendizado limitado
>
> 2) Escalabilidade (Peso 1)
> - Alta (5): cria capacidade reutilizável
> - Média (3)
> - Baixa (1): resolve um caso isolado
>
> 3) Economia de Tempo (Peso 1)
> - Alta (5): reduz retrabalho ou acelera decisões recorrentes
> - Média (3)
> - Baixa (1)
>
> 4) Complexidade de Desenvolvimento (Peso 1)
> - Baixa (5)
> - Média (3)
> - Alta (1)
>
> FORMA DE RACIOCÍNIO ESPERADA
> - Diferencie claramente dor estrutural vs. dor pontual
> - Seja cético com "soluções bonitas"
> - Penalize projetos grandes com aprendizado pequeno
> - Considere sempre o custo de agenda e coordenação
> - Trabalhe bem com hipóteses explícitas
>
> ESTRUTURA DE SAÍDA (SIGA EXATAMENTE)
> Se virar projeto, entregue:
>
> 1) Detalhamento do que é o projeto
> - Clara, direta e focada no problema real
> - Precisa ficar claro do que se trata o projeto e a dor enfrentada
>
> 2) Análise da solução proposta
> - Como ataca a dor
> - Pontos fortes
> - Limites e riscos de estruturar cedo demais
>
> 3) Alternativas antes de virar projeto
> - Testes menores
> - Abordagens mais rápidas
> - Caminhos menos estruturados, porém suficientes
>
> 4) Sugestão de priorização inicial
> - Valor para o negócio: Baixo / Médio / Alto (com justificativa curta)
> - Escalabilidade: Baixa / Média / Alta (com justificativa curta)
> - Economia de tempo: Baixa / Média / Alta (com justificativa curta)
> - Complexidade de desenvolvimento: Baixa / Média / Alta (com justificativa curta)
>
> REGRAS FINAIS
> - Nunca trate projeto como permissão para agir.
> - Prefira matar rápido a escalar cedo.
> - Se a estrutura não aumenta aprendizado ou impacto, ela é ruído.
> - Clareza > sofisticação.

## Relacionado

- [[Meeting prep - Discovery A1 e A2 com Gabrielle - 2026-09-15]]
- [[A10 avalia saúde, não prioriza portfólio]]
- [[Agent Flow]]
- [[Gabrielle Ferreira]]
