---
type: meeting
status: stable
updated: 2026-10-05
date: 2026-10-05
attendees: [Matheus Silva, Carolina Bezerra]
transcription_confidence: medium
aliases: [feedback reports a14 carol, revisão status updates a14]
tags: [agent-flow, agents, a14, carolina, status-update, feedback-produto]
---

# 2026-10-05 Carolina - Matheus (feedback reports A14)

Fonte: transcrição enviada por e-mail pela Carolina a msilva em 2026-10-05,
09:40 (`raw/carol-matheus.pdf`). Speaker A = Carolina, Speaker B = msilva
(confirmado por msilva). Diarização consistente, mas a transcrição troca
palavras (ex.: "Línea"/"Leaner" = Linear, "malha"/"Marliston" = milestone,
"isho" = issue, "Clodio" = Claude).

Revisão de produto dos **status updates semanais do A14** (publicação
semanal de [[Agent Flow]], `PRO-667`). Carolina leu dois reports: o do
projeto dela (~20 issues, tocado sozinha, sem milestones) e o do projeto
"guia de um builder" (milestones M1–M6, M6 "engajamento da vitrine").

## Decisions

Nenhuma decisão formal. Carolina deu recomendações e deixou a discussão de
mérito do estágio de ciclo de vida para Gabrielle.

## Commitments

- msilva: pausar a publicação automática do A14, regenerar os reports da
  última rodada com o feedback da Carolina e do Luís, reenviar ao grupo; se
  não convergir, reduzir escopo do agente. **Ajustado por msilva
  (2026-10-05, depois da reunião)**: não vai reenviar a última rodada — vai
  pedir ao pessoal que revise o preview do report no dashboard —
  [PRO-860](https://linear.app/projetos-livemode/issue/PRO-860/pausar-a-publicacao-semanal-do-a14-e-reenviar-a-ultima-rodada-com-o).
- msilva: investigar os sinais errados apontados na revisão (lista em
  *Facts stated*) —
  [PRO-861](https://linear.app/projetos-livemode/issue/PRO-861/investigar-sinais-errados-nos-status-updates-do-a14-prioridade-atraso)
  (mapeamento, não correção direta, por escolha de msilva).
- Carolina: vai incluir milestone na skill de criação de projeto.
- Carolina: vai mandar o áudio a msilva (pra ele passar no Claude) e o
  resumo à Gabrielle.

## Open questions

- **Usar ou não o estágio de ciclo de vida** (Planejamento/Construção/
  Evolução/Manutenção…, `PRO-669`/`PRO-704`)? Carolina listou três
  caminhos: (1) usar só o status nativo do Linear, (2) tirar a variável do
  agente, (3) o agente inferir o estágio pelo contexto. Ela não colocaria na
  v1 (risco de inferência errada, ninguém sabe o que cada estágio significa),
  ou só como frase de alerta com evidência. msilva defendeu o valor (ex.:
  projeto em manutenção com vazão baixa não preocupa). Mérito fica com
  Gabrielle.
- **Milestone deve ser obrigatório?** msilva: faz sentido se o time
  padronizar estruturação de projeto. Carolina vai pôr na skill, mas a regra
  não foi fechada.
- **Agente "pré-requisito"** de completude de dados (avisa que falta
  milestone/estimate, sem análise estratégica) — segundo Carolina, Gabrielle
  ia pedir a msilva; ainda não pediu. Carolina separa duas etapas: alguém
  que orienta/barra desde o começo, e outro que analisa.
- **Sistemas em manutenção** (ORCA, LiveScript) não ficam como projeto ativo
  no Linear (dados como completos). Carolina acha que precisam de **outro
  agente**, com outros parâmetros, não do A14.
- **Análise do GitHub** dentro do A14 ou num agente à parte? msilva cogitou um
  agente de "code quality".
- O que o agente quis dizer com "não foram retornados dados semanais" (msilva
  também não sabia).

## Facts stated

- msilva: o Linear não deixa adicionar valores ao status de projeto, por
  isso ele criou o campo de estágio próprio e um de-para que sincroniza com o
  status nativo (ex.: "Evolução" → In Progress). Os projetos do fluxo não
  tinham estágio setado, daí o "estágio não declarado" nos reports.
- Carolina: no Airtable antigo havia mais etapas de status (in progress,
  teste, homologação/validação do usuário).
- Carolina: projeto em manutenção, do jeito que o time classifica hoje, é
  projeto entregue/completo — não fica aberto no Linear. Status de projeto é
  "status de product manager": medir saúde do que está acontecendo, não do
  que já foi entregue.
- msilva: milestones alimentam lead time e aderência ao prazo no A14.
- msilva: quando a issue tem link pro GitHub, o agente busca o PR (ex.: um PR
  aberto há ~12 dias no [[Pulse]]).
- msilva: Luís já tinha dado feedback de que os reports estão **expositivos,
  não propositivos** (falta veredito) e confusos de ler.
- msilva: o report já é instruído a ter três tópicos — posição, trajetória,
  julgamento (`PRO-666`) —, mas não saiu agrupado assim; ele não sabe por quê.
  O julgamento foi adicionado na véspera, depois de feedback de outra pessoa.
- **Sinais errados conferidos ao vivo** (viraram `PRO-861`):
  - prioridade urgente: das três issues citadas (443, 441, 380), só a 380
    estava certa — 441 já concluída, 443 não é urgente;
  - prioridade do projeto reportada como "sem prioridade", mas está Medium
    (parece ter lido um estado anterior);
  - M6 "concluído 1 dia após o alvo", mas entregue no dia 20 com alvo no dia
    30. Hipóteses levantadas: compara com target date, ou contou uma tarefa
    concluída que entrou no milestone depois de fechado;
  - vazão zero em quatro semanas porque nenhuma issue tem estimate.
- **Feedback de formato (Carolina)**:
  - dividir em três blocos: (1) o que o agente não conseguiu medir por falta
    de dado — deve "tender a zero" com o tempo; (2) alertas e progresso;
    (3) sugestões;
  - tirar o "resumo" por enquanto (repetitivo);
  - quebrar linha por milestone — no celular fica ilegível;
  - "nenhuma movimentação" deveria ser alerta, não neutro;
  - separar Linear de GitHub, e no GitHub separar PRs de commits (ela nota
    que o Claude não commita sozinho se não pedir);
  - start/target date e progresso são úteis; histórico de prioridade não;
  - talvez uma classificação de risco por milestone;
  - parear alerta com evidência (ex.: M5 em 40%, 20 dias sem ninguém tocar,
    data já vencida → sugestão: backlog ou mudar data/alocar esforço).

## Notable quotes

- Carolina: *"Se você colocar ele num agente e vir pra mim falando que o
  status é XPTO, se eu não sei o que significa, não vai me servir de nada."*
- Carolina: *"O não ter nada deveria indicar alguma coisa pra ele."*
- Carolina: *"O ideal é que eu consiga bater o olho aqui e falar assim:
  esse projeto aqui tem tarefa aberta há não sei quanto tempo, deixa eu dar
  uma olhada nele."*

## Relacionado

- [[2026-09-14 Carolina - Matheus (critérios A10-A14)]] — revisão anterior,
  dos critérios.
- [[Fronteira A10×A14 (informação e métricas)]]
- [[Linear Project Structure]]
- [[Carolina Bezerra]]
- [[Agent Flow]]
