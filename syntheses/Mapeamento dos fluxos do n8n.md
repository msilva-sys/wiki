---
type: synthesis
status: active
updated: 2026-10-22
date: 2026-10-22
aliases: [mapeamento n8n, inventário n8n, fluxos do n8n]
tags: [n8n, automação, auth, governança, liveauth, credenciais]
---

# Mapeamento dos fluxos do n8n

Levantamento pedido por msilva em 2026-10-22, logo depois da correção de
[[LiveAuth]] sobre consumidores reais. Motivo: o inventário de
[[Trava de domínio e autenticação — inventário para o LiveAuth]] só varreu
**repositórios do GitHub** — nenhuma automação de n8n entrou na conta dos 21
apps, embora o n8n seja hoje, em volume, a maior superfície de automação da
empresa. Também é a contraparte direta do
[[Inventário de Processos e Ferramentas]], que ataca justamente chave de API
espalhada por área.

Fonte: servidor MCP do n8n (`n8nops.livemode.com`), acessado em 2026-10-22
com a conta `tech@livemode.com`.

## Método e limite de cobertura — ler antes dos números

Dois tipos de número aqui, não confundir:

- **Totais exatos**, lidos do campo `count` do próprio n8n (ele conta tudo
  que casa com o filtro, independente de quantos itens devolve).
- **Números de amostra**, calculados sobre os workflows que consegui
  enumerar de fato.

A ferramenta `search_workflows` do MCP **não tem paginação** — aceita
`limit` (máx. 200), mas não tem offset nem cursor. Para enumerar, usei 6
ordenações diferentes (`updatedAt` asc/desc, `createdAt` asc/desc, `name`
asc/desc), 200 cada, e deduplicei: **781 workflows únicos de 1.256**, ou
**62% de cobertura**. Os 38% restantes não foram lidos. Filtro por letra
não resolve: `query=a` sozinho casa 1.020 workflows, muito acima do teto de
200.

Para fechar os 100% seria preciso a **API REST do n8n** (`/api/v1/workflows`
com `cursor`), que pagina de verdade — precisa de uma API key da instância,
que não tenho.

`(unverified: a amostra de 62% não é aleatória — é enviesada para extremos
de data e de ordem alfabética. Proporções calculadas sobre ela, como "% de
ativos", não devem ser extrapoladas sem ressalva.)`

## Escala (totais exatos)

| Medida | Valor |
|---|---|
| Workflows na instância | **1.256** |
| Workflows na raiz do projeto da conta TI (sem pasta) | 1.036 |
| Execuções em ~1,5 dia (2026-10-21 até 2026-10-22 16h) | **38.694** |
| Credenciais visíveis | **200+** (teto da ferramenta; o total real pode ser maior) |
| Contas distintas donas de credencial | **53** |
| Tags cadastradas | 54, cobrindo ~150 usos — tagueamento é exceção, não regra |

As 38.694 execuções em um dia e meio são dominadas por um punhado de
workflows: *Tradutor - Arquivo para Categoria* (sub-workflow, chamado em
modo `integrated`) e a esteira *Detector de Novos Vídeos - Drive Changes
API* → *Sync Incremental - Brasileirão / Ligas Internacionais - Drive →
Portal*. Volume de pipeline de vídeo, não de uso humano.

## Governança: não existe projeto de time

`teamProjectsEnabled: false` — **team projects não estão licenciados nesta
instância**. Consequência: todo workflow e toda credencial moram em
**projetos pessoais**. As credenciais estão espalhadas por **53 contas
diferentes** — contas de pessoa (`hamaral@`, `asodre@`, `ydutra@`…) e
caixas de time (`time_financeiro@`, `juridico@`, `opec@`,
`atendimento_comercial@`, `administrativo@`…).

Isso é o mesmo padrão que o LiveAuth existe para corrigir, um nível acima:
não é cada app reimplementando login, é cada pessoa guardando a própria
credencial de produção dentro do projeto pessoal dela, numa ferramenta
compartilhada.

A amostra viu **43 pastas distintas**, mas `search_folders` no projeto da
conta TI lista só 12 — as outras são de projetos pessoais de terceiros.
Nenhuma convenção de nomes comum entre elas (`🎲 Data Engineering`,
`📊 Opta Workflows`, `⚽ Golzinho`, `PanJunior`, `LFU`…).

## Credenciais (amostra de 200, por tipo)

| Tipo | Qtd. | Nota |
|---|---|---|
| `airtableTokenApi` + `airtableApi` | **29** | PATs separados; mais 1 PAT do Airtable guardado como `httpBearerAuth` → **30 chaves de Airtable** |
| `slackApi` + `slackOAuth2Api` | 29 | |
| `gmailOAuth2` | 21 | |
| `googleSheetsOAuth2Api` (+ trigger) | 21 | |
| `httpHeaderAuth` / `httpBearerAuth` / `httpCustomAuth` / `httpBasicAuth` | **23** | genéricas e opacas — o tipo não diz o que protegem |
| `openAiApi` + `anthropicApi` + `groqApi` + `googlePalmApi` | **19** | chaves de LLM |
| `googleDriveOAuth2Api` | 13 | |
| `evolutionApi` | 6 | WhatsApp |
| `postgres` + `supabaseApi` | 5 | bancos (Supabase CazéTV, People Hub/RH, Copa) |
| `ldap` | 1 | diretório corporativo |
| `sshPassword` | 1 | acesso a servidor por senha |
| outros (YouTube, Calendar, Docs, Notion, Monday, BigQuery, Entra, GSuite Admin, Apify, Linear, SMTP…) | ~30 | |

Nenhuma credencial é `isGlobal` ou `isManaged` — não há credencial
gerenciada centralmente; cada uma é um segredo solto num projeto pessoal.

**As 30 chaves de Airtable são o achado mais direto**: é exatamente o
problema que o [[Airtable Proxy]] e a distribuição central de chaves querem
resolver, e que a wiki já registrava desde 2026-08-18 como "um número
desconhecido de fluxos cidadãos, cada autor com a própria chave". O número
agora tem ordem de grandeza: **~30**. Vários nomes são pessoais ou
temporários (`tokensophia`, `temp-arthur`, `yasmin`, `liderrh`), o que
indica chave emitida para uma pessoa e nunca rotacionada.

## O que os fluxos fazem (totais exatos por busca em nome/descrição)

| Busca | Workflows |
|---|---|
| `slack` | 86 |
| `airtable` | 74 |
| `CRM` | 54 |
| `webhook` | 41 |
| `form` | 37 |
| `agente` | 23 |
| `whatsapp` | 21 |
| `chat` | 12 |
| `copy` | 10 |

O maior bloco nomeado é **CRM** (54), uma esteira comercial sobre Monday +
Airtable. Depois vêm as integrações de Opta/vídeo e as rotinas
administrativas (chamados de manutenção que notificam WhatsApp via Evolution
API, escalas, NF, contas a pagar).

## Sinais de risco e de dívida (amostra de 781)

- **433 ativos** contra 348 inativos. Ou seja: a instância não é um
  laboratório — mais da metade do que existe está ligado.
- **98 workflows ativos sem nenhuma edição há 6+ meses** (4 deles há mais de
  um ano). Rodam sozinhos, ninguém toca. É a definição de automação órfã:
  quando quebra ou vaza, não há dono óbvio.
- **76 workflows com cara de teste, cópia ou rascunho** no nome (`copy`,
  `TESTE`, `rascunho`, `v2`, `[BACKUP]`) — **20 deles ativos**. Cópia ligada
  é risco duplo: usa credencial de produção e ninguém lembra que existe.
- **306 ativos sem descrição nenhuma** — o que o fluxo faz só se descobre
  abrindo o canvas.
- **198 ativos estão expostos como ferramenta de MCP** (`availableInMCP`),
  isto é, acessíveis a um agente de IA que se conecte à instância.
- **41 workflows citam webhook** no nome/descrição. Webhook de n8n é uma URL
  pública; a trava, quando existe, é uma credencial de header. As 23
  credenciais HTTP genéricas provavelmente são isso — não confirmado fluxo a
  fluxo.

## Por que isso importa pro LiveAuth

1. **A superfície real de auth é maior do que os 21 apps catalogados.** O
   inventário via GitHub nunca ia achar isso: nenhum desses 1.256 fluxos tem
   repositório. Ver [[Trava de domínio e autenticação — inventário para o LiveAuth]].
2. **O problema aqui não é login de usuário, é credencial de serviço.** O
   LiveAuth, como está, não endereça: ele decide *quem entra num app*, não
   *qual segredo um robô carrega*. Mesma distinção que apareceu no discovery
   com atendimento (compartilhamento externo) — é problema vizinho, não o
   mesmo.
3. **Mas é exatamente a frente do [[Inventário de Processos e Ferramentas]]**,
   o mapeamento paralelo puxado pelo CFO. Este levantamento é insumo direto
   pra conversa de unificar as duas frentes, que ficou "pra depois" em
   2026-10-07.
4. **Os 198 fluxos expostos via MCP** são o caso de uso que o LiveAuth já
   sabe proteger — é o mesmo desenho do servidor MCP do
   [[Agent Flow|Fluxo Agêntico]] (resource server + JWT ES256 + JWKS). Vale
   checar como o n8n autentica hoje quem consome esses fluxos como
   ferramenta.

## Auditoria dos webhooks, 2026-10-22

Segunda passada, pedida por msilva logo depois do mapeamento: dos 41 fluxos
que citam webhook, **26 estão ativos**. Classifiquei os 26 lendo o
`triggerInfo` de cada um (que diz se o nó exige credencial) e, nos casos
decisivos, o JSON completo dos nós. Webhook de n8n é URL pública em
`https://n8nops.livemode.com/webhook/<path>` — não há rede fechada na
frente.

**Paths e segredos não são reproduzidos aqui** (esta wiki é um repositório
git). O que vai abaixo é o padrão de proteção de cada fluxo, não como
chamá-lo.

### A. Trava no próprio nó do n8n — 4 fluxos, 6 endpoints

O n8n recusa a requisição antes de executar qualquer coisa. É o único grupo
em que a trava não depende do autor do fluxo ter lembrado de checar.

- Aging de Contas a Pagar (Supabase → HTML): header próprio, nos 5 endpoints.
- Painel Tênis 2026 (dados de entrega): header próprio.
- Central de Eventos — Melhorias, no endpoint que o Supabase chama: header.
- Painel de Automações: Basic Auth nos dois endpoints.

### B. Checagem dentro do fluxo, segredo fixo no JSON — pelo menos 6

Primeiro nó depois do webhook é um `IF` comparando um header ou um
parâmetro de query com **uma string literal escrita no próprio workflow**.
Funciona, mas:

- o segredo está em texto puro no JSON do fluxo — e, portanto, em todo
  export, backup e cópia do workflow, além de visível a qualquer pessoa com
  acesso de leitura ao n8n (inclusive por MCP, foi assim que eu li);
- quando o segredo vai na query (`?t=`, `?k=`), ele vaza em log de acesso,
  histórico de navegador, `Referer` e link compartilhado;
- não há rotação, expiração nem revogação — trocar o segredo significa
  editar o fluxo e avisar quem usa.

Confirmados lendo o código: dashboard operacional da OPEC (token na query) e
liberação de acesso de visitantes (header). Declarados na própria descrição,
não reconferidos nó a nó: consulta de status de comissão de talentos, API do
Double Check, animação de rally do tênis, chat da Central OKR.

### C. Capability URL — o identificador é o token

Decisão de aprovar/reprovar exceção de SLA da Central de Eventos: o link
carrega o UUID do evento e a ação; não há autenticação. O autor documentou
isso numa sticky note dentro do fluxo ("o UUID do evento é o token, não
adivinhável"), e a janela é limitada ao status `aguardando_excecao`. É o
padrão de magic link: aceitável como escolha consciente, com a ressalva de
que **quem receber o link encaminhado aprova no lugar do gestor**, e não há
expiração nem registro de quem clicou — o histórico grava o gestor esperado,
não quem de fato acionou.

Três outros fluxos usam path aleatório (UUID) em vez de nome legível —
obscuridade, não trava: entrada do CRM vindo do Monday, notificações do
cronograma contábil, chat da Central OKR.

### D. Sessão própria com login Google — 1 confirmado

Página do Ranking de Executivos do CRM: cookie de sessão compartilhado com a
página de Cadência e redirecionamento pro login Google quando não há sessão.
É o único caso em todo o levantamento de **autenticação de usuário de
verdade** num webhook de n8n — alguém reimplementou à mão, dentro do fluxo,
o que o [[LiveAuth]] faz. A página irmã (Cadência) não foi reconferida nó a
nó.

### E. Sem trava nenhuma, confirmado no código — 2 fluxos

1. **Site de controle de cartões corporativos.** Quatro nós: webhook GET sem
   credencial → busca o HTML no Drive → baixa → responde. **Zero
   verificação.** Serve a página de controle de cartões corporativos, feita
   pra diretoria consultar. O path é um slug legível, não um UUID. Qualquer
   pessoa com a URL — ou que a adivinhe — abre.
2. **Registro de preenchimento do checklist de manutenção.** POST sem
   credencial, com `Access-Control-Allow-Origin: *`. Grava registro no
   Airtable e dispara mensagem nos grupos de WhatsApp (via Evolution) e nos
   canais de Slack de RJ e SP. Qualquer um pode inventar preenchimento e
   fazer o bot postar texto arbitrário nos grupos. O próprio autor
   documentou "site estático, sem login" — é escolha consciente, mas o efeito
   colateral (postar no WhatsApp/Slack da empresa) provavelmente não foi
   pesado.

### F. Sem trava, dados de baixo risco (aparentemente deliberado)

Acompanhamento de salas (JSON agregado de reuniões, descrito como "webhook
público"), ocupação de sala (free/busy de sala do Workspace) e a página HTML
do formulário de offboarding de Portugal.

### G. Integrações de terceiro sem verificação de assinatura aparente

Entrada de eventos do Slack do fluxo RR CazéTV, recepção de fotos do
WhatsApp via Evolution, e dois endpoints do Double Check (conclusão e ações
do Slack). Nenhum mostra validação de assinatura (`X-Slack-Signature` ou
equivalente) no nó de entrada — quem souber o path consegue injetar evento
falso. Não reconferi nó a nó os três últimos.

### H. Não inspecionáveis por aqui — 4 ativos

Bot de auditoria de TI (webhook → GPT → BigQuery), webhook do YouTube
(marcado com a tag **Uso Externo**), webhook de eventos de tarja e o download
de Getty do PMP têm `availableInMCP: false`, então o MCP recusa ler o fluxo.
Precisam de inspeção pela interface. O do YouTube é o único do levantamento
inteiro marcado como uso externo.

### O que isso diz pro LiveAuth

- **A trava padrão da empresa, em n8n, é segredo compartilhado** — header
  fixo, token na query ou UUID no link. Nenhum tem identidade de usuário:
  não dá pra dizer *quem* abriu o painel de contas a pagar, só que alguém
  tinha o header.
- **Um fluxo já reimplementou login Google com cookie de sessão à mão** (o
  Ranking do CRM). É o ~~22º~~ 21º caso de "cada um reimplementa auth do
  seu jeito" (os 20 do inventário; o Fluxo Agêntico deixou de contar ao
  migrar pro LiveAuth) e o primeiro achado fora do GitHub — reforça o
  argumento central do [[LiveAuth]] com um exemplo que ninguém tinha
  mapeado.
- **O caminho de menor esforço aqui não é migrar fluxo pro LiveAuth**, e sim
  oferecer a trava no nó (Header Auth com credencial do n8n, que já existe e
  é gerenciada pela plataforma) como padrão mínimo, e reservar o LiveAuth
  pros webhooks que servem **página pra humano** — hoje pelo menos 6: cartões
  corporativos, aging de contas a pagar, ranking e cadência do CRM, dashboard
  da OPEC, painel de automações.

### Ações sugeridas, em ordem

1. Fechar o site de controle de cartões corporativos (E1). É dado financeiro
   servido sem nenhuma verificação.
2. Decidir sobre o checklist (E2): manter aberto ou exigir um segredo — o
   risco é postagem forjada em canal da empresa, não vazamento.
3. Tirar os segredos literais de dentro dos fluxos (B), trocando por
   credencial de Header Auth do próprio n8n.
4. Inspecionar os 4 fluxos fechados pro MCP (H), a começar pelo marcado como
   uso externo.
5. Verificar assinatura nas integrações de Slack e WhatsApp (G).

## Open questions

- Os 38% de workflows não enumerados — precisa de API key da instância pra
  fechar com `/api/v1/workflows?cursor=`.
- Total real de credenciais: a listagem bate no teto de 200 e o `count` dela
  devolve só o que foi retornado, não o total.
- **Quem opera a instância**: existe dono de plataforma do n8n, ou cada área
  se vira? Ninguém apareceu como admin funcional no levantamento.
- ~~Quais dos 41 fluxos com webhook estão de fato abertos sem nenhuma
  trava.~~ **Respondido em 2026-10-22** — ver "Auditoria dos webhooks" acima.
  Resta inspecionar pela interface os 4 ativos que o MCP não lê, e reconferir
  nó a nó os fluxos do grupo B/G que só declaram a trava na descrição.
- As 30 chaves de Airtable: quantas ainda são válidas, quantas são de gente
  que saiu. Cruzar com o [[Airtable Proxy]] antes de propor migração.
- Se team projects (licença) resolveria metade do problema de governança
  sozinho, sem nenhum desenvolvimento — avaliar custo.

## Relacionado

- [[LiveAuth]]
- [[Trava de domínio e autenticação — inventário para o LiveAuth]]
- [[Inventário de Processos e Ferramentas]]
- [[Airtable Proxy]]
- [[Agent Flow]]
