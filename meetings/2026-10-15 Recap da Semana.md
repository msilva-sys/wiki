---
type: meeting
status: stable
date: 2026-10-15
updated: 2026-10-16
attendees: [Gabrielle Ferreira, Maria Fernanda Lemos, Yasmin Macedo, Matheus Oliveira da Silva, Camila Sande]
aliases: []
tags: [recap, semanal, skills, playwright, automacoes, linear]
---

# 2026-10-15 Recap da Semana

Rito retrospectivo semanal do time. Fonte:
`raw/2026-10-15 Recap da Semana.html` (ata e transcrição do Gemini, ~26
min).

> [!warning] Atribuição não confiável nesta fonte
> A diarização do Gemini quebrou: **a transcrição inteira rotula todas as
> falas como [[Gabrielle Ferreira]]**, embora o conteúdo mostre várias
> pessoas falando. A seção "Detalhes" da ata atribui melhor, mas as
> "Próximas etapas" trocam nomes — um item aparece como de "Pedrinho" nas
> etapas e de "Mateus" nos detalhes.
>
> Abaixo só é atribuído o que dá para confirmar pelo conteúdo (projeto
> citado, issue do Linear correspondente). O resto fica sem dono nomeado.

## Decisões

- **O desenvolvimento do [[Pulse]] internacional fica com Pedrinho.**
  Única decisão marcada como alinhada na ata. Primeira entrega focada no
  mínimo, em cima do protótipo já construído, montando estrutura
  replicável depois para o Brasil.

## Commitments

De msilva (confirmados pelo conteúdo e pelas issues correspondentes):

- Alinhar com [[Luís Fernandez]] o padrão de projeto, para que a
  estruturação das skills siga a norma do time.
- Encerrar os testes do erro da Expresso no [[Farol]] e analisar os dados
  para decidir se aciona o fornecedor. Rodando desde terça (2026-10-13);
  encerramento previsto para 2026-10-16.

De terceiros, ainda sem retorno visível:

- **João e Luiz** devem entregar suas skills para que [[Gabrielle Ferreira]]
  faça o cruzamento de duplicadas e descrições divergentes — é o que trava
  a segunda etapa do inventário de skills.
- **Gabrielle** vai montar um repositório centralizado para as skills do
  time, discutir com Lucas os requisitos de taxonomia contemplando
  internacional, e apresentar prazos de roadmap para atendimento e OPEC.
- **Pedrinho** deve enviar a divisão planejada entre taxonomia e o projeto
  do Pulso internacional — cobrado na reunião, ainda não enviado.
- **O grupo** deve testar as skills umas com as outras, inclusive a de
  leitura de automações do n8n.

## Perguntas em aberto

- **Repositório único ou separado para documentação de automações?**
  Levantado sobre manter num só repo a documentação do n8n e a do Airtable.
  Encaminhamento foi começar separado, com padrão de título, usando o do
  Airtable como guia de estrutura. Não fechado.
- **Testes do [[LiveScript]] com ou sem Airtable junto?** msilva pensou em
  testar incluindo o Airtable; a configuração que já existia no LiveScript
  é teste de interface apenas. Depende de conversa com Luís para entender a
  razão e decidir se os outros projetos seguem o mesmo caminho.

## Fatos declarados

- **Limite de automações**: cerca de 100 mil automações por mês por
  workspace, limite que o time já bateu. Motivou desligar automações
  quebradas ou inativas e otimizar as de alta frequência. *(Não confundir
  com o cap mensal de chamadas de API do Airtable, de ordem semelhante —
  ver [[Airtable Rate Limits]].)*
- **Suporte agora abre tarefa automaticamente no Linear** — nenhuma tarefa
  nasce mais no Slack. As tarefas trazem label de interna ou externa e
  quem pediu, bastando a pessoa responsável se atribuir. Ver
  [[Linear Project Structure]].
- **Ciclos do Linear são automáticos** (explicado a quem tinha dificuldade
  de pôr prazo): ciclo de uma semana; tarefa em progresso não concluída
  passa sozinha para o ciclo seguinte, e tarefa concluída em ciclo
  posterior é puxada para o atual.
- **Estudo de expansão do [[LiveScript]] para Portugal** (Gabrielle):
  cobriu direitos, contratos, competições, estoque e vendas por marca;
  indicou poucos impactos, mitigados com tags de segregação de base e
  variável corporativa nos formulários. Em tese o fluxo brasileiro já
  serve.
- **Inventário de automações** (Gabrielle): feito no Taylor e no n8n, com
  desligamento do que estava quebrado ou inativo; correção das falhas em
  curso, documentação em repositório Git prevista para a semana seguinte,
  com rotina automatizada via Claude no Airtable para monitorar e abrir
  tarefa em caso de erro.

## O que msilva reportou

- **Protótipo com Playwright** para testes automatizados do [[LiveScript]],
  com o objetivo de criar testes sem exigir conhecimento avançado de
  configuração. Quatro ou cinco skills criadas em volta disso.
- **Proposta de unificar as skills do time num único repositório Git como
  plugins** — a pessoa dá um comando e o Claude puxa os plugins
  relacionados, sem precisar baixar nada de novo; atualizar uma skill vira
  PR no repositório. Nasceu da fricção de querer distribuir cinco skills de
  uma vez. Encosta em `PRO-903` e no
  [[Desenho de um Skills Registry corporativo]].
- **Reorganização da base de código do agente** com Luís — organização de
  diretórios e lógica, buscando transparência. Corresponde a `PRO-1005`,
  concluída em 2026-10-15.
- **Skill de construção de projeto quebrada em subskills** por
  complexidade, em vez de um padrão burocrático único: uma skill geral
  entende o projeto e direciona para outras — configurar ambiente,
  configurar agentes. Travada no padrão de projeto a alinhar com Luís.
- **Dependabot** abrindo PRs automáticos para vulnerabilidades em
  bibliotecas, sendo revisados.
- **Reunião com [[Ana Beatriz Fonseca]]** para alinhar o fluxo de
  preenchimento de dados no Airtable; depois do OK dela, o agente pode ser
  configurado para produção. Ver [[LiveAuth]].
