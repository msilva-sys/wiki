---
type: meeting
status: stable
date: 2026-10-09
updated: 2026-10-13
attendees: [Luís Fernandez, Matheus Oliveira da Silva, Yasmin Macedo]
aliases: [Opta o que é isso]
tags: [opta, transição]
---

# Opta - O que é isso? — 2026-10-09

> Fonte: `raw/Opta - O que é isso_ - 2026_10_09 17_35 GMT-03_00 - Anotações do
> Gemini.md`, transcrição automática do Gemini. **Toda a fala está atribuída a
> um único locutor ("Luis Fernandez")** — o mesmo problema de diarização já
> visto em [[2026-08-25 Farol - Dados]] e na call do [[Bossabox Engagement]].
> O texto deixa claro que há pelo menos duas outras vozes (perguntas,
> respostas curtas tipo "sim"/"entendi", e o trecho final — "nós três vai
> lidar com a Gabi e Carol" confirma três pessoas na sala). Confirmado por
> msilva (2026-10-13): os outros dois presentes são ele e [[Yasmin Macedo]].
> Nada abaixo atribuído a "Matheus" ou "Yasmin" especificamente é
> reconstrução por contexto, não by diarização confiável.

Luís explica o histórico e funcionamento da [[Opta]] pra Matheus e Yasmin,
puxado pela divisão de projetos da sua saída
([[2026-10-07 Divisão de projetos na transição de Luís]]) — quer deixar o
sistema "não órfão" antes de sair.

## Decisions

Nenhuma decisão formal fechada nesta reunião — é contexto + lista de
melhorias que Luís joga pra fila de Matheus/Yasmin, pendente de aprovação de
[[Gabrielle Ferreira]] e [[Carolina Bezerra]] (ver Open questions).

## Commitments

- Luís vai conversar com [[Carolina Bezerra]] (e possivelmente
  [[Gabrielle Ferreira]]) pra achar quem é o responsável interno pelo
  contrato comercial da Opta.
- Luís sugere a Matheus e Yasmin apresentarem a Opta pro time **como dupla**
  — oportunidade de trabalharem mais próximos, não um pedido fechado ainda
  ("tô jogando um monte de coisa na fila de vocês").
- Meta de Luís antes de sair: algum dashboard da Opta no telão da sala de
  reunião (mesmo padrão do dashboard "Insights" do [[Airtable Proxy]]).

## Open questions

- Quem é o responsável interno pelo contrato comercial da Opta? Não se sabe
  hoje quem negocia/aprova mudança de contrato (ex: nova competição).
  Luís: "é meio nebuloso... não se sabe quem que é o responsável pelos
  contratos."
- Matheus/Yasmin podem agir direto nas melhorias listadas abaixo, ou
  precisam de aval de Gabi/Carol primeiro? Luís: não é pra sair fazendo
  sozinho, precisa aprovar com elas — mas ele é a favor e vai levar.
- Pode cachear resposta da Opta pra economizar chamada (mesma lógica que o
  time já pensa pro [[Airtable Proxy]])? Luís alerta que pode esbarrar em
  cláusula contratual que proíbe — não ficou claro qual cláusula
  (unverified: trecho confuso na transcrição, "eu tenho a Bí que não
  deixa... por contrato").
- O bot de chat interno que Luís mencionou (ver Facts) — ainda existe, dá
  pra reaproveitar movendo pro repo do MCP? Luís: "tem que ver."

## Facts stated

- **Histórico de ownership, por Luís**: [[Pessanha]] começou a assumir
  responsabilidade pela Opta antes de Luís chegar (já tinha saído da
  empresa quando Luís entrou). Depois [[Pedro Alves]] (ligado ao projeto
  [[Orca (CDE)]]) virou o dono informal — conhecia bem o sistema, era
  procurado por quem tinha dúvida. [[Vini]] também aprendeu bastante,
  principalmente durante a Copa, period em que Pedro desceu e os dois
  trocaram ideia de perto. Recentemente duas pessoas pediram ajuda a Luís
  sobre Opta — uma delas indicada pelo próprio Vini.
- **Contrato**: não é barato; mudar contrato (ex: pedir acesso a uma
  competição nova, tipo La Liga) é renegociação de contrato, não um simples
  gerar-de-chave — Pedro Alves teria citado valores perto de R$150.000,
  contratos de longo prazo. Luís não tem acesso aos detalhes do contrato.
  Recentemente mandou e-mail pro time da Opta tentando puxar essa
  responsabilidade, cc'd alguém (não tem certeza se [[Gabrielle Ferreira]]
  estava copiada). A Opta respondeu com uma chave de API por e-mail, mas
  sem contato de quem internamente cuida do contrato.
- **Chave de API única e compartilhada**: ao contrário das chaves de agente
  (geradas por pessoa, revogáveis individualmente), a Opta usa uma única
  chave compartilhada pela empresa inteira — comparada por Luís a "escova
  de dente compartilhada." Plano verbal de rotação: comunicar a empresa
  toda, dar ~30 dias pra migrar pra chave nova, depois ~60 dias desligar a
  chave antiga (quem não migrou simplesmente para de funcionar).
- **Arquitetura — três projetos separados**: **Opta API** (proxy que
  encapsula os endpoints da Opta que a empresa tem direito — não tem
  certeza se cobre todos), **Opta MCP** (expõe essa API internamente,
  muito usado), **Opta Docs** (documentação gerada por Luís via Claude,
  autenticada pra não vazar pra fora da empresa). Luís já cogitou unificar
  os três num monorepo, não fez — hoje é incômodo trabalhar com Claude Code
  cruzando os três repos.
- **Relatório de uso, feito por Luís a pedido de [[Maria Fernanda Lemos]]**
  (~um mês e meio antes desta reunião, i.e. por volta de 2026-08-25): no ar
  desde 30/03/2026; até 28/08/2026, 1556 requests em 31 dias. Principal
  consumidor: tráfego via agente (Claude/Cloud), +25% Node, 7% Python.
  Chamadas de ferramenta estáveis em ~230/mês.
  - `get_schedule`: **51% de sucesso em 246 chamadas** — taxa muito baixa;
    Luís acha que já corrigiu parcialmente, sem certeza.
  - `get_standings` (tabela de classificação): 100%.
  - `squads`: 97%.
  - Sem detalhamento de qual é o erro por trás das falhas — precisa
    investigar no nível do detalhe.
  - Lista de competições disponíveis é um JSON fixo, atualizado manualmente
    por Luís direto em produção — deveria ser dinâmico/vindo da própria API.
  - Log de uso só começou perto do 5º dia da Copa — não cobre todo o
    histórico real de uso.
  - Alguns erros 401 — provavelmente chave configurada errado por quem
    consome; tratando a Opta como produto, o certo seria achar essa pessoa
    e orientar.
  - **Credencial é hoje buscada direto do BigQuery, não é secret do GCP**
    (diferente do [[Farol]]) — falha de segurança/higiene apontada
    explicitamente por Luís como "um problema."
- **Visão de produto**: Luís quer que o time passe a tratar a Opta como
  produto próprio (mesmo discurso usado pro [[LiveScript]]) — tratar quem
  usa como cliente, dar visibilidade (dashboard no telão da sala de
  reunião, por onde passa gente importante da empresa), citar a Opta como
  case de sucesso do time em apresentações (do jeito que fizeram com
  LiveScript e [[Orca (CDE)]] numa apresentação técnica no mesmo dia,
  defendendo o uso de Airtable num curso).
- **Opta Docs, detalhe de conteúdo**: Luís gerou a documentação passando
  requisitos específicos pro Claude (não um prompt genérico) — pensada pra
  ensinar a usar a Opta via Claude Code / integrar numa aplicação, com lista
  de competições disponíveis. Tem uma seção de operação (como adicionar
  competição, gestão de chave) que ele não quer expor geral — ainda sem
  solução de como separar isso do conteúdo público.
- Luís menciona um projeto antigo (por volta de fevereiro) — um agente/bot
  de chat interno que abria um chat dedicado à Opta; caiu em desuso,
  ninguém usa mais hoje. Acha que dava pra mover isso pro repo do Opta MCP.
- Custos da Opta por chamada: cobrança fixa até um limite; acima disso,
  paga por faixa adicional. Pedro Alves teria feito algum cache seletivo de
  informação pra reduzir isso (nível de detalhe não entrado).
- Demo ao vivo no fim da reunião: perguntou a tabela do Brasileirão,
  próximos jogos do Flamengo e destaques do último jogo — o MCP trouxe
  dados (times, classificação, próximos confrontos) misturados com
  narrativa gerada (ex: "Neymar foi o nome do jogo, Jorginho foi o vilão")
  — a parte estatística vem da Opta, a narrativa é interpretação do
  modelo por cima do dado bruto, não 100% Opta.

## Notable quotes

- "A da ópta, até onde eu sei, é uma única e ela era a mesma compartilhada
  para todo mundo. Uma chava para uma empresa inteira." — Luís, sobre a
  chave de API.
- "Isso já somos nós assumindo o a opta como um produto nosso... a gente
  trata os nossos as pessoas que usam ela como os nossos clientes mesmo."
  — Luís.
- "Putz, isso aqui é um filho nosso, a gente precisa apoiar dele. É o que
  eu vou tentar fazer para ele não ficar órfão na minha saída." — Luís.
