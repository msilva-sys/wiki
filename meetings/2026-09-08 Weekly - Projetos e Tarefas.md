---
type: meeting
status: stable
updated: 2026-09-09
date: 2026-09-08
attendees: [Carolina Bezerra, Maria Fernanda Lemos, João Victor Andrade, Camila Sande, Matheus Silva]
aliases: [weekly 08/09, weekly remarcação]
tags: [linear, process, project-management, airtable-proxy, portfolio]
---

# 2026-09-08 Weekly - Projetos e Tarefas

13:59, ~22 min. Weekly de status da área, remarcada. Conduzida por
[[Maria Fernanda Lemos]] passando projeto a projeto. **Luís e Gabriel
ausentes** (indispostos), o que adiou a definição do Fronte de Negócio.
Reunião curta — *"o vídeo foi mais curtinho, 20 minutos"* — por ter menos
gente.

> [!warning] Atribuição colapsada entre Mafê e msilva
> O Gemini fundiu os dois microfones no bloco do proxy: a fala que começa
> *"Então, eh, Mateus, proxy do table"* está inteira sob o rótulo de
> **Maria Fernanda Lemos**, mas o conteúdo a partir de *"o PR eu já
> conversei com Le na semana que passou"* é claramente de **msilva** —
> ele é quem configurou as datas, quem gerencia as issues, quem fala do
> deploy. Mesmo padrão já registrado em [[2026-08-25 Farol - Dados]] e
> [[2026-08-19 1-1 Matheus - Gabrielle]]. As atribuições abaixo foram
> feitas por conteúdo, não pelo rótulo da transcrição.

## Decisions

- **API da Gary adotada como banco de imagem padrão** dos projetos.
  Integração concluída após conversa técnica na sexta (04/09); começou
  pelos projetos de redes. [[Maria Fernanda Lemos]].
- **Fronte de Negócio fica em reavaliação, sem decisão** — precisa de mais
  conversa. O desenvolvimento foi entregue e testado, mas o time de
  planejamento levantou questões sobre a implementação no dia a dia (querem
  exportar arquivo pra outro produto, ou trazer o processo atual pra
  dentro). Carol já ouviu duas pessoas do planejamento; falta sentar com o
  Luís pra decidir **se considera entregue e abre outra feature, ou se
  ajusta o cronograma**. O planejamento testa a ferramenta até o fim desta
  semana.

## Commitments

- **Carolina Bezerra**: sentar com o Luís pra definir escopo e cronograma
  do Fronte de Negócio; cobrar o Pedrinho pra abrir as pendências técnicas
  do [[Orca Next Version]] numa nova versão e fechar a tarefa anterior.
- **Maria Fernanda Lemos**: avisar a Gabrielle que o painel de portfólio
  está lendo o projeto errado do proxy (ver *Facts stated*); pegar status
  real do [[Farol]] com a Yasmin; tocar a automação de abertura de pedidos
  de arte de telão com Ana Domingos, envolvendo Camila.
- **João Victor Andrade**: apresentar o funil de creators à Débora e
  treiná-la no Monday; ajustar o dashboard de receita conforme validação
  da [[Júlia]]; estruturar as automações nativas de preenchimento
  retroativo de datas; falar com o Esbarai pra achar quem de fato usa a
  view de planejamento no Monday; migrar o acompanhamento do ClickUp pro
  Linear — **feito no mesmo dia**, ver
  [[2026-09-08 Overview de Linear com João Victor]].
- **Camila**: terminar o segundo bloco do curso da Cloud e mandar feedback
  pra Carolina.

> [!note] A ata do Gemini errou uma atribuição
> As "Próximas etapas" do Gemini listam *"[Mateus] Validar Proxy: Entrar
> em contato com Gabi para verificar a integração do sistema com o Proxy
> Airtable."* Na transcrição quem se compromete é a **Mafê**: *"Eu vou
> dar um toque também na Gabi, porque o nosso sistema tá olhando para o
> proxy do air table e não para onde você tá fazendo o gerenciamento das
> tarefas."* Não é um compromisso de msilva.

## Open questions

- **Os projetos de proxy no Linear deveriam ser um só?** Carol, olhando a
  lista: *"eu acho que esses três aí deveriam ser uma coisa só."* Mafê
  concordou (*"é a mesma coisa pelo que eu tô entendendo"*), referindo-se
  a *Proxy do Airtable*, *Proxy expandido para outros apps* e *Proxy em
  produção validado c/ LiveScript*. **Conflita com a estrutura vigente**,
  que veio do Luís — ver [[Linear Project Structure]] para a tensão
  completa. Não decidido na reunião; o Luís, que criou a estrutura, estava
  ausente. **Resolvido depois, em 2026-09-09**:
  [[2026-09-09 Manter os projetos de proxy separados no Linear]] — as duas
  partes falavam de coisas diferentes (frente de trabalho × nome confuso), e
  prevalece a separação.
- **Qual aplicação vai apontar pro proxy** — LiveScript ou o front novo.
  Segue em aberto e **é decisão do Luís**, reafirmado aqui em fórum
  público. Mesma questão aberta de [[2026-09-04 1-1 Matheus - Luís]].
- **Status real do [[Farol]]**: entregue segundo Carol e Yasmin, equipe da
  Marina testando, mas o Linear mostra "em progresso" e a data de entrega
  registrada é 04/09. Esperar a Yasmin voltar.
- **O projeto de planejamento no Monday foi entregue ou ficou à deriva?**
  A Júlia passou a João Victor a percepção de que tinha parado; a Yasmin
  diz que foi entregue e finalizado, sem saber se estão usando. Carol deu
  o contexto que reconcilia: não é o CRM, é o **workflow da Monday**,
  construído a pedido antigo do time de planejamento — e ela também não
  tem visibilidade do uso diário. Provável problema de visibilidade, não
  de entrega.

## Facts stated

- **msilva**: o proxy tem meta de estar **integrado ao projeto do
  portfólio até 15/09**, com algum projeto apontando pra ele — *"seja live
  script, seja o front, a gente ainda não decidiu qual"*. Confirma em
  fórum público a data externa registrada em
  [[2026-09-04 1-1 Matheus - Luís]]; a meta interna de 10/09 (staging) não
  foi mencionada aqui.
- **msilva**: passou um tempo na semana anterior configurando as datas e o
  projeto no Linear — consistente com a auditoria de 2026-09-03 registrada
  em [[Airtable Proxy]].
- **msilva**, sobre o nome do próprio projeto: *"o nome tá errado. Seria
  validado com algum projeto"* — porque ainda não se sabe se é o
  LiveScript ou o front. O nome atual, *Proxy em produção validado c/
  LiveScript*, presume uma decisão que não foi tomada.
- **Maria Fernanda Lemos — o achado com mais consequência da reunião**: o
  painel interno de portfólio está lendo o projeto **`Proxy do Airtable`**,
  e não o `Proxy em produção validado c/ LiveScript`, onde msilva de fato
  gerencia as issues. *"O nosso sisteminha ele tá pegando do proxy do
  table e não pegando desse que você tá falando."* Ou seja: **o progresso
  real do proxy não aparece no painel que Carol e Gabrielle olham.** Ela
  vai avisar a Gabi. Ver [[Linear Project Structure]] para o mecanismo do
  painel.
- **Carolina Bezerra**: os projetos de **governança, treinamento e
  taxonomia** continuam parados desde a semana anterior; ela espera andar
  com algo nesta semana.
- **Carolina Bezerra**: no [[Orca Next Version]], as tarefas em aberto são
  pendências técnicas que o Pedrinho deveria ter movido pra uma nova
  versão e não moveu.
- **Gabriel**: a documentação do fluxo de audiência continua em andamento,
  mesmo status da semana anterior.
- **João Victor Andrade**: as automações do CRM já preenchem
  automaticamente `record ID`, data de criação e data de fechamento pra
  casos novos, com monitoria semanal dele; o desafio aberto é o
  **preenchimento retroativo**, e ele descobriu que dá pra fazer
  nativamente dentro da Monday, sem N8N nem integração externa.
- **João Victor Andrade**: desenhou na Monday um funil de fechamento de
  projetos com creators, pra substituir um processo que era 100% em
  planilha pela agência ESIP. Pedido original da [[Júlia]] quando ele
  entrou.
- **João Victor Andrade**: criou um dashboard interativo de receita na
  cloud conectando MCP do Airtable **e** da Monday, fragmentado por
  competição e período, a pedido da [[Júlia]] — que está validando os
  números.

## Notable quotes

> "Eu acho que esses três aí deveriam ser uma coisa só." — Carolina
> Bezerra, sobre os projetos de proxy no Linear

> "O nosso sisteminha ele tá pegando do proxy do table e não pegando
> desse que você tá falando." — Maria Fernanda Lemos

> "É, o nome tá errado. Seria validado com algum projeto." — msilva

## Referências

- Doc do Drive: *Weekly - Projetos e Tarefas | Remarcação - 2026/09/08
  13:59 GMT-03:00 - Anotações do Gemini*
  (`1L-bbmK9YV-bv8nAjTGAyDGy70CsB28Jzz2urI4-xxkw`), com resumo e
  transcrição completa. **Ainda não baixado pra `raw/`** — esta página foi
  escrita a partir do doc no Drive, não do arquivo local. Quando o arquivo
  chegar, trocar esta linha pelo caminho em `raw/`.
- [[Linear Project Structure]]
- [[Airtable Proxy]]
- [[2026-09-04 1-1 Matheus - Luís]]
- [[2026-09-08 Overview de Linear com João Victor]]
