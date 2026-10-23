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

> **Correção (2026-10-22, segunda rodada):** a API key foi obtida e a
> instância foi lida inteira. Os números desta seção e das seguintes são da
> amostra. Os números exatos estão em "Segunda rodada", no fim da página.
> Duas correções importantes: as credenciais são **940**, não "200+", e as
> chaves de Airtable são **130**, não ~30. Além disso, **873 dos 1.256 fluxos
> são fechados ao MCP** (`availableInMCP=false`), não só os 4 do grupo H.

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

## Segunda rodada, 2026-10-22 — instância inteira via API

A rodada 1 fechou com 62% de cobertura e uma pergunta aberta: o que tem nos
outros 38%. Esta rodada lê a instância inteira.

### Cobertura e método

**Primeira tentativa, via MCP.** Enumerou 1.213 dos 1.256 workflows. Mas
**835 deles têm `availableInMCP=false`**: o MCP lista o fluxo, mas não lê o
conteúdo. **Correção da rodada 1:** lá parecia que só 4 fluxos estavam
fechados ao MCP (grupo H). Na verdade, a maioria está fechada. A cobertura de
*conteúdo* da rodada 1 foi bem menor que os 62% de *metadados*
`(unverified: quanto menor, não medido)`. A carga em paralelo também gerou
"Too many requests" e derrubou o conector. Tentativa abandonada.

**Segunda tentativa, via API REST.** API key de curta duração, criada por
msilva, usada em ritmo baixo. Baixado:

- 1.658 workflows (1.256 vivos + 402 arquivados), ou seja, **100% dos não
  arquivados**;
- 940 credenciais (só metadados, sem segredo);
- a última execução de cada ativo.

**Análise.**

1. Script determinístico sobre os 1.658: gatilhos, trava no nó, segredo
   escrito no JSON, uso de credencial, higiene.
2. Agentes leram **289 fluxos ativos**: os que têm entrada de rede sem
   credencial nativa, ou um segredo literal detectado. A leitura foi sobre
   digests com os segredos redigidos.
3. Todo achado médio ou pior passou por verificadores céticos independentes.

O script é heurístico. Ele marca falso positivo e deixa passar segredo (ver
"Segredos literais"). Os números dele são triagem, não veredito.

| Medida | Rodada 1 (MCP, amostra) | Rodada 2 (API, 100%) |
|---|---|---|
| Workflows lidos | 781 de 1.256 (62%) | 1.256 de 1.256, mais 402 arquivados = 1.658 |
| Fechados ao MCP | 4 | 873 vivos (1.256 − 383); 835 de 1.213 na tentativa via MCP |
| Credenciais | 200+ (teto da ferramenta) | **940** |
| Chaves de Airtable | ~30 | **130** (128 do tipo Airtable + 2 HTTP com "airtable" no nome; 68 usadas por ativos) |
| Ativos | 433 (amostra) | **624** (632 inativos) |
| Ativos parados há 6 meses ou mais | 98 (4 há mais de 1 ano) | **145** (5 há mais de 1 ano) |
| Cópias/testes | 76, 20 deles ativos | **236** vivos, **47** ativos |
| Ativos expostos ao MCP | 198 (amostra) | **249** (383 no total) |
| Webhooks | 41 citam no nome, 26 ativos auditados, 2 abertos confirmados | **282** fluxos ativos com entrada de rede (webhook, formulário, Slack, MCP); 269 nós de webhook ativos sem trava no nó; **58** sem trava confirmados como médio ou pior |
| Ativos sem descrição | 306 (amostra) | 455 (tentativa via MCP; a API não informa) |
| Contas donas de credencial | 53 | a API não informa; vale o 53 |

### Higiene (metadados de toda a instância)

- **624 ativos**, 632 inativos e 402 arquivados.
- **145 ativos** sem edição há 6 meses ou mais; 5 deles há mais de um ano.
- **236 vivos** com cara de cópia, teste ou rascunho no nome; **47 ativos**.
- **383 expostos ao MCP** da instância, 249 deles ativos.
- Workflows espalhados por **78 projetos** distintos. Como team projects não
  estão licenciados (`teamProjectsEnabled: false`, visto na rodada 1), são
  todos projetos pessoais.
- Sem descrição: 455 ativos (tentativa via MCP). O agregado da API diz 624,
  mas isso é um artefato, porque a API não traz o campo.

**Execução.** A instância guarda só ~1 dia de histórico. Nesse período:

- Idade da última execução: 215 ativos rodaram em até 1 dia, 1 em até 30
  dias, e 408 não têm registro. Total: 624.
- Status da última execução: 106 sucesso, **117 erro**, 2 aguardando, 1
  rodando, 398 sem registro.
- Os 408 sem registro **não querem dizer "nunca executou"**. Muitos são
  agendados semanais ou webhooks que ninguém chamou no dia.
- `(unverified: os dois campos divergem em 10 fluxos, 398 × 408; a causa não
  foi apurada.)`
- Dos 226 ativos com status registrado, mais da metade (117) terminou a
  última execução em erro `(unverified: pode ser uma falha isolada, não
  quebra permanente)`.

### Credenciais

**940 credenciais. Nenhuma é gerenciada ou global (0).** Cada uma mora no
projeto pessoal de alguém.

| Família | Qtd. | Tipos |
|---|---|---|
| Google Workspace | 321 | Sheets 87 (+17 de gatilho), Gmail 87, Drive 63, conta de serviço 19, Docs 16, OAuth genérico 13, Calendar 12, Admin 7 |
| Slack | 153 | `slackApi` 117, OAuth 36 |
| Airtable | 128 | PAT 117, API key legada 9, OAuth 2. Mais 2 credenciais HTTP com "airtable" no nome, contadas na linha HTTP: 130 no total |
| HTTP genéricas (opacas) | 119 | Header 92, Bearer 12, Basic 8, Custom 5, Query 2 |
| LLM | 95 | OpenAI 72, Gemini/PaLM 14, Anthropic 6, Groq 2, Cohere 1 |
| Banco e infraestrutura | 25 | Supabase 9, Postgres 8, LDAP 3, Redis 2, SSH com senha 2, SFTP 1 |
| WhatsApp | 21 | Evolution 18, WhatsApp oficial 3 |
| Outros | 78 | YouTube 14, BigQuery 11, **API do próprio n8n 7**, SMTP 7, OAuth2 genérico 7, Monday 7, Notion 6, GitHub 4, Entra 2, Linear 2 e mais 11 tipos com 1 cada |

- **328 credenciais não são usadas por nenhum fluxo vivo.** Outras 212 só
  aparecem em fluxos inativos. Total sem uso em fluxo ativo: 540.
- **Airtable: 130 chaves, 68 usadas por fluxos ativos.** A ordem de grandeza
  da rodada 1 (~30) estava 4× abaixo. Concentração: um único PAT do Airtable
  serve 33 fluxos ativos, e outro serve 19. Mesmo padrão no Drive (29),
  Sheets (27 e 24), BigQuery (25 e 23), OpenAI (25), Monday (21) e Slack
  (21). Vazou uma, cai tudo junto.
- As 7 credenciais da API do n8n dão acesso à própria instância `(unverified:
  escopo e dono não conferidos)`.

### Pontos de entrada de rede (ativos)

**Pela configuração do nó** (script, 337 nós de entrada em 282 fluxos
ativos):

| Gatilho e trava no nó | Nós ativos |
|---|---|
| Webhook sem auth | 269 |
| Webhook com Header Auth | 40 |
| Formulário sem auth | 11 |
| Webhook com Basic Auth | 8 |
| Gatilho nativo do Slack (assinado pelo provedor) | 6 |
| Servidor MCP sem auth | 3 |

**Pela trava de verdade** (agentes, nos 289 ativos lidos):

| Trava real | Fluxos |
|---|---|
| Nenhuma | **127** |
| Só UUID no caminho (capability URL) | 61 |
| Sem entrada de rede (entraram na leitura só por segredo literal) | 49 |
| Outra checagem dentro do fluxo (campo do corpo, forjável) | 22 |
| Segredo literal comparado num `IF` | 18 |
| Login Google feito à mão no fluxo | 7 |
| Assinatura do provedor verificada | 4 |
| Trava nativa no nó | 1 |
| **Total** | **289** |

**Os 3 servidores MCP sem autenticação.** São 3 ativos com MCP Server
Trigger sem auth (4 contando os inativos). Os três estão abertos de fato:

- **MCP da Matriz:** confirmado como médio. Consulta a Matriz de Eventos com
  fórmula livre, usando o token do Airtable da empresa.
- **[OPTA] MCP Server:** sem auth confirmado, mas impacto baixo. Só lê
  dados da Opta.
- **Um terceiro, de nome genérico ("My workflow"):** classificado como baixo
  e não verificado.

### Segredos literais em fluxos

**Script (heurístico):** **191 fluxos vivos** têm algo com cara de segredo
escrito no JSON; **98 deles estão ativos**. Por tipo de trecho: parâmetro
literal 638, bearer literal 319, token do Slack 216, JWT 34, chave de API do
Google 8, webhook de entrada do Slack 7, chave OpenAI 3, PAT do Airtable 2,
chave privada 2, chave Anthropic 1. **Isso é contagem de trechos, não de
segredos confirmados.** O mesmo segredo repete em vários nós, e a heurística
de "parâmetro literal" é a mais ruidosa.

**Agentes, nos 289 ativos lidos:** 117 com segredo real, 7 falsos positivos
do script, 165 sem segredo. O script errou para os dois lados:

- 7 falsos positivos descartados;
- os agentes acharam mais fluxos com segredo real (117) do que o script tinha
  marcado em todos os ativos (98). O script deixa passar, por exemplo, chave
  dentro de nó de código.

Um fluxo conta como "real" se tiver pelo menos um segredo real. Entre esses
117 está a chave anon do Supabase, que é **pública por desenho**. Quase
sempre ela aparece junto do segredo da função de sincronização, que é o
risco de verdade `(unverified: quantos dos 117 só têm chave anon)`.

**Segredos reais por sistema.** A tabela conta menções, não fluxos: um fluxo
pode citar vários sistemas.

| Sistema | Menções | Observação |
|---|---|---|
| Supabase | ~33 | 13 são chave anon (pública); 13 são o segredo da função que **grava e apaga** vídeos do portal; o resto é apikey/JWT, um deles provavelmente service role |
| APIs internas e tokens de entrada de webhooks | ~29 | portais, ingestão de dashboards, chatbot, alertas do Itaú, Double Check, dashboard da OPEC |
| Slack (bot, SCIM, webhook de entrada) | ~17 | inclui SCIM que **cria usuários** |
| Evolution / WhatsApp | ~14 | envio pelo número da empresa |
| LLM e IA | ~14 | Groq, Gemini, AssemblyAI, OpenAI, OpenRouter e outros |
| InHire | ~12 | e-mail e senha de login, provavelmente da mesma conta `(unverified)` |
| GLPI | ~9 | App-Token e token de usuário |
| Templated.io | ~6 | renderização paga |
| Busca paga (Serper, SerpAPI, GNews, Apify) | ~5 | |
| Airtable, Microsoft Entra, Admin do Google, DocuSign, Opta, API-Football, Caju | 2–3 cada | Entra: cria e desativa usuários; DocuSign: **chave privada RSA** |
| Monday, Notion, Salú, Zabbix, UniFi, Short.io, LiveSuite | 1 cada | Zabbix roda script em servidor; UniFi é conta de **admin de rede** |
| Sessões de navegador (Claude Teams admin, ChatGPT) | 2 | cookie de sessão escrito no fluxo |

### Achados verificados

**Como os números fecham.** Os agentes classificaram 289 fluxos.

- Severidade: 2 críticos, 29 altos, 149 médios, 101 baixos, 8 sem risco.
- Status: 167 confirmados, 75 refutados ou rebaixados, 47 baixos não
  verificados. O verificador só roda em médio ou pior.
- São **180 de severidade média ou pior**. Destes, **158 estão confirmados**
  (2 críticos, 29 altos, 127 médios), e é essa a lista abaixo.
- Os **22 médios restantes** estão com status *refutado ou rebaixado*: o
  verificador contestou parte do achado, ou rebaixou de alto para médio. Por
  isso ficaram fora da lista.
- Dos 167 confirmados, **9 são baixos ou sem risco**: foram confirmados
  depois de rebaixados.
- Conta de fechamento: 109 baixos ou sem risco = 47 não verificados + 9
  confirmados + 53 refutados ou rebaixados. 75 refutados ou rebaixados = 22
  médios + 53 baixos.
- `(unverified: a distribuição de status por severidade é deduzida por
  diferença; a lista recebida só traz os confirmados.)`

**Críticos e altos (31, todos confirmados).** Fluxos com nome de pessoa estão
descritos pelo que fazem.

| Sev. | id | Fluxo | Problema | Correção | Status |
|---|---|---|---|---|---|
| Crítico | Yih1Uh0C6S7mwcFU | Fluxo 100% (onboarding de colaborador) | Dois webhooks públicos sem trava criam contas no AD, Google, Slack e GLPI e gravam senha no Airtable. Tokens do Slack (inclusive SCIM), do GLPI e do Admin do Google estão escritos no fluxo | Header Auth no cadastro; assinatura do Slack nas respostas; credenciais nativas e rotação; parar de gravar senha | confirmado |
| Crítico | eG4ZQZg6a2iDhguO | Inicial (onboarding de colaborador) | Webhook público sem trava cria contas no AD e no Google com licença, envia senha e abre chamado. Tokens do GLPI escritos no fluxo | Header Auth (a automação do Airtable envia o header); credencial nativa e rotação | confirmado |
| Alto | Rhu4FyHSKKIE1l2Y | CadastroCaju | Webhook sem trava cadastra colaborador no benefício e grava no RH. Senhas e client_secret de 4 empresas escritos no fluxo | Header Auth; credenciais nativas e rotação | confirmado |
| Alto | a6xK8DXwMB9R5pQS | Recebimento de respostas e avaliação de currículo | Webhook sem trava cria candidato, gasta LLM, manda DM e baixa URL arbitrária | Header Auth no provedor do formulário; validar o domínio da URL | confirmado |
| Alto | S9VZb1UxgnLRmgX1 | teste Onboarding Portugal | client_secret do Entra (cria usuários) e tokens do GLPI no código; entrada protegida só por UUID; senha padrão fixa | Credenciais nativas e rotação; Header Auth; senha aleatória por onboarding | confirmado |
| Alto | ZzyfJKNSGwWOawkR | 4- Consolida Lives (MM) | Chave da Evolution escrita em 6 nós, apesar de já haver credencial nativa | Usar só a credencial; rotacionar; desligar certificado inválido | confirmado |
| Alto | ZcAwEhqLElQn9pMu | Offboarding Portugal | client_secret do Entra (desativa usuários, lê Intune) e tokens do GLPI no código; entrada só por UUID | Credenciais nativas e rotação; Header Auth | confirmado |
| Alto | wiaJYI1qGx46iK6e | Central Olímpica, rotina agendada de pesquisa e escrita | Tokens de Airtable e Notion (com escrita) e chaves de Serper e Gemini escritos no fluxo | Credenciais nativas; rotacionar todas | confirmado |
| Alto | uW9x313JxBD06KUS | RR_CazeTV_01_Principal_Simplificado | Eventos do Slack sem verificação de assinatura; dá para forjar e mudar o estado do reconhecimento de receita | Verificar X-Slack-Signature antes de qualquer nó | confirmado |
| Alto | ui87P6ClKMybJIGk | Acompanhamento Docusign | Chave privada RSA do DocuSign no código; quem lê o fluxo gera token e baixa contratos | Mover para credencial ou cofre; rotacionar no DocuSign | confirmado |
| Alto | 2an3u2dKCOUOsL3I | ADM QR CODE | Webhook sem trava abre chamado e posta no Slack e no WhatsApp; tokens de Slack e GLPI no fluxo | Header Auth; credenciais nativas e rotação | confirmado |
| Alto | JVhQHcbdmVAJWHMf | Aprovação - Redes | Webhook da Evolution sem validação: simula mensagem, gasta LLM, posta em grupo; chaves no fluxo | Header Auth na Evolution; credenciais nativas e rotação | confirmado |
| Alto | ZjQzvlkIsnC7LSZe | Placar - Slash Command | Slash command sem assinatura do Slack dispara upload, render e post; JWT do Supabase no fluxo | Assinatura do Slack; credencial nativa e rotação | confirmado |
| Alto | 3xpBqbohJg01T6zG | My workflow 328 | Senha do InHire em claro (dados de candidatos); webhook só por UUID | Credencial nativa; trocar a senha; Header Auth | confirmado |
| Alto | V3GK9JVWjl91BkqT | Zabbix Script Slack | Dois webhooks sem trava: qualquer um posta alerta, gasta LLM e **reinicia serviço em servidor**; token do Zabbix no fluxo | Assinatura do Slack; Header Auth no Zabbix; credencial e rotação | confirmado |
| Alto | neFhwyalUuNKxo2e | Envio - Agendamento de Entrevista | Webhook sem trava manda WhatsApp pelo número do Recrutamento para telefone vindo do corpo; chave da Evolution no fluxo | Header Auth; credencial nativa e rotação | confirmado |
| Alto | 1Va9eAZZDtgf62LT | CRM Livemode — Auditoria Semanal | Token do Monday em 5 nós; dá acesso a negociações, valores e contatos | Credencial nativa; rotacionar | confirmado |
| Alto | 1sDstJEhpIV1e6h1 | Forms - Estágio | Senha da conta do InHire no nó de login | Credencial nativa; trocar a senha | confirmado |
| Alto | 02V5Jc3UqEXEaajO | Coletar Usuários Claude Teams | Cookies de sessão de admin e PAT do Airtable com escrita no fluxo; webhook solto | Credenciais nativas; rotacionar sessões e PAT; apagar o webhook | confirmado |
| Alto | kYtm52nAaNPBwZok | AVD - Comunicados | O nó "Verificar Assinatura" sempre aprova; qualquer um manda mensagem em nome do bot para qualquer funcionário (phishing) | HMAC de verdade sobre o corpo bruto, ou Header Auth; 401 se falhar | confirmado |
| Alto | 1t308Vcym7ShQ9N2 | GPT de Agências | Webhook sem trava devolve agências, contatos, negociações e e-mails do Monday | Header Auth no GPT chamador | confirmado |
| Alto | Uk4H7tmisf8OBlkg | Pipeline Frame.io — INGEST | Sem credencial; CORS não barra chamada direta; cria projetos e URLs de upload na conta da empresa | Header Auth, chamado por backend atrás do LiveAuth | confirmado |
| Alto | UDwbvuCkyd7GMCc0 | Aging Contas a Receber | Dois GET públicos servem painel e JSON de contas a receber (clientes, valores) | Painel atrás do LiveAuth; detalhe com Header Auth ou sessão | confirmado |
| Alto | o3pasZtQRUSeFg6I | Marcar demissional na Salú | Chave da Salú em 4 nós (dados de colaborador, ação de demissão); webhook só por UUID | Credencial nativa e rotação; Header Auth | confirmado |
| Alto | MyqwpY7Rkf7rKWGx | [OPTA] CALL OPTA API | Webhook sem trava chama qualquer endpoint da Opta com a chave da empresa e devolve a resposta | Header Auth; lista permitida de endpoints | confirmado |
| Alto | LFUuXHM9UOMIB7V7 | Post diário de Instagram com envio por WhatsApp | Chave da Evolution fixa no nó, apesar da credencial nativa | Usar só a credencial; rotacionar | confirmado |
| Alto | NzlMgE4ssiHRYFXi | My workflow 390 | Webhook só por UUID reprova candidato e manda e-mail para qualquer endereço; senha do InHire no fluxo | Credencial e troca de senha; Header Auth; remover nó de teste | confirmado |
| Alto | 9GqNnRp5VGrEg8Cd | Slack Interactivity Router | Sem assinatura do Slack: aprova fluxos pendentes e faz o n8n chamar endereço arbitrário com SSL desligado | Assinatura do Slack; lista permitida de domínios de retorno | confirmado |
| Alto | tL34di7AeMshiXWB | plataforma talentos | 4 webhooks sem trava mandam WhatsApp com texto livre para qualquer número | Header Auth; não aceitar texto livre de fora | confirmado |
| Alto | yVxhlJrycO7D3Lvk | Listar NFs Smartdata - 4 | Webhook sem trava e CORS aberto devolve as notas fiscais de qualquer e-mail informado | Login (LiveAuth) no app chamador; e-mail vindo da sessão; tirar o CORS aberto | confirmado |
| Alto | UlbqbF5UerpNVIy4 | Descobrir ID de grupo de Wpp | Chave da Evolution no cabeçalho do nó | Credencial nativa e rotação; desativar (é de uso pontual) | confirmado |

**Médios (127 confirmados), por categoria:**

| Categoria | Médios | O que tem dentro | Correção padrão |
|---|---|---|---|
| Segredo literal no fluxo | 53 | 9 sincronizações do portal de vídeos com o segredo da função que grava e apaga; tokens de bot do Slack em subfluxos e chamados; senha do InHire em formulários de estágio; tokens do GLPI em relatórios agendados; chaves de LLM, Templated e Evolution | Credencial nativa e rotação |
| Sem trava | 45 | dados de RH (admissão com CPF e conta bancária, carta de aviso prévio, homologação); esteira de CRM Airtable↔Monday; Central de Externas; relatórios disparáveis em loop; site de cartões corporativos; checklist de manutenção; upload de fatura do Itaú | Header Auth; LiveAuth onde há humano |
| Capability URL (só UUID) | 18 | exceção de SLA; cronograma contábil (posta link de redefinição de senha em canal); CRM vindo do Monday; formulários internos (nota de débito PJ, upload de dados do TikTok) | Header Auth; LiveAuth; token assinado de uso único |
| Integração sem assinatura | 9 | eventos e cliques do Slack (férias, chamados, tarefas, intake do RR, bot de viagens), webhook do Monday | Verificar X-Slack-Signature |
| Trava fraca inline | 1 | recebimento de nota fiscal: só confere que existe um "Bearer" | Validar o JWT de verdade, ou Header Auth |
| MCP sem auth | 1 | consulta à Matriz de Eventos | Auth no MCP Trigger, ou o padrão MCP do LiveAuth |

Por categoria, os 158 confirmados ficam assim: **sem trava 58** (2 críticos,
11 altos, 45 médios), segredo literal 68, capability URL 18, integração sem
assinatura 12 (3 altos, 9 médios), trava fraca 1, MCP 1. Fora do recorte da
lista: 101 baixos e 8 sem risco, além dos 22 médios refutados ou rebaixados
explicados acima.

**Padrão de maior gravidade:** os dois críticos e 2 altos (onboarding e
offboarding de Portugal) são fluxos de **ciclo de vida de conta**: criam ou
desativam usuário no AD, no Entra ou no Google. Se contar também o cadastro
de benefício na admissão (Caju), são 3 altos.

**O que reaparece da rodada 1:**

- **Cartões corporativos** (E1): confirmado como médio. Página financeira sem
  login.
- **Checklist de manutenção** (E2): confirmado como médio. Posta em
  WhatsApp e Slack sem trava.
- **Exceção de SLA** (C): confirmado como médio, capability URL.
- **Cronograma contábil e entrada do CRM pelo Monday** (UUID no caminho):
  confirmados como médio. O cronograma tem um agravante novo: link de
  redefinição de senha postado em canal.
- **RR CazéTV, eventos do Slack** (G): agora **alto** no orquestrador
  principal, e médio no intake.
- **Webhook do YouTube** (H, fechado ao MCP na rodada 1): agora lido. Médio,
  grava qualquer arquivo no Drive.
- **Offboarding de Portugal** (F, "baixo risco" na rodada 1): agora **alto**.
  O risco não é a página, é o segredo do Entra e do GLPI no código.
- **Dashboard da OPEC e Double Check** (B): aparecem na contagem de segredos
  reais, mas não entre os médios ou piores confirmados `(unverified:
  severidade final)`.
- **Login Google feito à mão** (D): era 1 caso confirmado; os agentes acharam
  7.

**Aging, atenção:** o aging da rodada 1 era o de **contas a pagar**, que tem
Header Auth (grupo A). O alto desta rodada é outro fluxo, o de **contas a
receber**, sem trava. É novo.

**Novos:** os dois críticos de onboarding; os segredos de alto alcance no
código (chave privada do DocuSign, secrets do Entra, senhas do InHire e da
Caju, token do Monday, cookies de admin); o reinício de serviço via Zabbix; os
vazamentos de dado por webhook aberto (GPT de Agências, notas fiscais); o
envio de WhatsApp com texto livre; o roteador do Slack que chama endereço
arbitrário; e a verificação de assinatura de fachada.

### O que muda para o LiveAuth

Finalidade da entrada, segundo os agentes, nos 289 fluxos:

| Finalidade | Fluxos | Quem resolve |
|---|---|---|
| Página para humano | 12 | **LiveAuth** |
| Formulário para humano | 22 | **LiveAuth** (ou convite com código, se o público for externo) |
| API robô-a-robô | 104 | Header Auth nativo do n8n |
| Callback de terceiro (Slack, Monday, Airtable, InHire, Evolution) | 97 | Assinatura do provedor; Header Auth onde não há assinatura |
| Servidor MCP | 3 | **LiveAuth**, no desenho que já existe |
| Não se aplica | 51 | Credencial nativa (o problema é segredo, não entrada) |

O "não se aplica" (51) não bate com "sem entrada de rede" (49) da tabela de
trava porque os campos são diferentes. Os 51 são os 49 sem entrada mais 2
que têm um nó de entrada sem uso real. Um deles está confirmado na lista: um
webhook solto, não ligado a nada. O outro é `(unverified)`.

- **Candidatos ao LiveAuth: 34 fluxos** com página ou formulário para humano,
  **28 deles abertos**. Entre os confirmados como médio ou pior, são 14 (4
  páginas e 10 formulários): aging de contas a receber, cartões
  corporativos, exceção de SLA, upload de fatura, checklist, orçamento de
  competições e outros. Alguns formulários servem público externo (PJ,
  inscrição em newsletter). Para esses, o LiveAuth (Google, só interno) não
  serve.
- **Caso intermediário:** um app no navegador chama o webhook direto, com
  CORS aberto (Frame.io, notas fiscais, autopreenchedor de roteiro, agenda
  da intranet, checklist). O humano está no app, não no n8n. A solução é
  LiveAuth no app e backend chamando o webhook com Header Auth `(unverified:
  ~10 casos, contagem manual sobre o texto dos achados)`.
- **Robô-a-robô: 201 fluxos.** O LiveAuth não entra. Header Auth nativo,
  assinatura do provedor e credencial nativa resolvem, e já existem no n8n.
- **Servidores MCP sem auth: 3.** É exatamente o caso que o LiveAuth já
  protege: resource server com JWT ES256 e JWKS, o mesmo desenho do
  [[Agent Flow|Fluxo Agêntico]].

**Três problemas distintos. Não misturar:**

1. **Quem é o humano** que abre a página ou o formulário → LiveAuth.
2. **Quem é o robô** que chama o webhook → Header Auth nativo ou assinatura
   do provedor. O LiveAuth não entra.
3. **Que segredo o robô carrega**: 117 ativos com segredo real no JSON e 940
   credenciais soltas em 78 projetos pessoais → credencial nativa, rotação e
   governança. O LiveAuth não resolve. É a frente do
   [[Inventário de Processos e Ferramentas]] e do [[Airtable Proxy]].

### Lacunas

- **Histórico de execução de ~1 dia.** "Sem registro" não é "nunca
  executou". A divergência de 10 fluxos (398 × 408) não foi apurada.
- **A API não informa descrição nem dono de credencial.** Para esses dois
  pontos valem os números do MCP: 455 ativos sem descrição e 53 contas donas
  de credencial.
- **O script é heurístico.** Ele marca falso positivo (7 descartados entre os
  289) e deixa passar segredo (os agentes acharam 117 contra 98 marcados). As
  contagens de trechos (638, 319, 216…) não são segredos confirmados. A
  contagem de "real" inclui chave anon do Supabase, que é pública.
- **A leitura por agente cobriu 289 dos 624 ativos.** Os outros 335 ativos,
  os 632 inativos e os 402 arquivados só passaram pelo script. Os arquivados
  não entram nos 191 com segredo `(unverified: quantos arquivados ainda
  carregam segredo no JSON)`.
- **22 médios refutados ou rebaixados.** O motivo de cada um está no
  resultado do workflow, mas não foi trazido para esta página.
- **"Aberto" quer dizer sem trava na configuração lida.** A abertura não foi
  provada com chamada ao endpoint.
- A finalidade da entrada (humano, robô, callback) é inferida pelos agentes
  a partir do conteúdo do fluxo.

### Ações sugeridas, em ordem

1. **Revogar a API key usada nesta rodada.**
2. **Fechar os 2 críticos de onboarding.** Header Auth no webhook,
   assinatura do Slack, rotação dos tokens de GLPI, Slack (inclusive SCIM) e
   Admin do Google. Parar de gravar senha no Airtable.
3. **Rotacionar primeiro os segredos de maior alcance:** chave privada do
   DocuSign, os 2 secrets do Entra, senhas do InHire e da Caju, token do
   Monday, token do Zabbix, conta de admin do UniFi, cookies de sessão de
   admin. Rotação vem antes da migração, porque eles já estão expostos a
   quem lê o fluxo.
4. **Fechar os 29 altos.** Começar pelos que vazam dado (aging de contas a
   receber, notas fiscais, GPT de Agências) e pelos que agem fora (reinício
   via Zabbix, WhatsApp com texto livre, roteador do Slack, Opta, Frame.io).
5. **Header Auth nativo como padrão mínimo** nos webhooks robô-a-robô: os 58
   confirmados sem trava e os 18 capability URL (fonte: lista de achados
   confirmados, por categoria).
6. **Verificar a assinatura do Slack** nos 12 confirmados sem assinatura (3
   altos e 9 médios, mesma fonte). Vale um subfluxo único e reaproveitado.
7. **Páginas e formulários internos atrás do LiveAuth:** os 28 abertos, a
   começar pelos 14 confirmados (cartões, aging, exceção de SLA, fatura).
8. **Trocar segredo literal por credencial nativa:** 98 ativos marcados pelo
   script, 117 classificados pelos agentes como segredo real (68 confirmados
   como médio ou pior). Rotacionar cada um.
9. **Revisar as 328 credenciais sem uso em nenhum fluxo vivo.** Conferir os
   402 arquivados antes de apagar. Revisar também as 212 usadas só por
   fluxos inativos.
10. **Higiene:** desligar ou justificar as 47 cópias e testes ativos, os 145
    parados há 6 meses ou mais, e olhar os 117 com última execução em erro.
11. **MCP:** revisar quais dos 249 ativos expostos ao MCP precisam estar lá.
    Proteger os 3 servidores MCP sem auth no padrão do LiveAuth.
12. **Governança:** definir um dono de plataforma para o n8n, avaliar a
    licença de team projects e conferir o escopo das 7 credenciais da API do
    próprio n8n.

## Open questions

- ~~Os 38% de workflows não enumerados.~~ **Respondido em 2026-10-22**: a
  segunda rodada leu os 1.256 pela API REST.
- ~~Total real de credenciais.~~ **Respondido em 2026-10-22**: são 940.
- Os 22 achados médios que a verificação refutou ou rebaixou: o motivo de
  cada um está no resultado do workflow, não nesta página. Revisar antes de
  descartar.
- Os 335 ativos e os 632 inativos que só passaram pelo script: quantos têm
  segredo que a heurística não pega. Os agentes acharam 117 fluxos com
  segredo real contra 98 que o script marcou.
- Os 117 ativos cuja última execução terminou em erro: é quebra permanente
  ou falha pontual?
- **Quem opera a instância**: existe dono de plataforma do n8n, ou cada área
  se vira? Ninguém apareceu como admin funcional no levantamento.
- ~~Quais dos 41 fluxos com webhook estão de fato abertos sem nenhuma
  trava.~~ **Respondido em 2026-10-22** — ver "Auditoria dos webhooks" acima.
  A segunda rodada também leu os 4 que o MCP não lia e reconferiu os grupos
  B/G.
- As 130 chaves de Airtable (eram ~30 na rodada 1): quantas ainda são
  válidas, quantas são de gente que saiu. Cruzar com o [[Airtable Proxy]] antes de propor migração.
- Se team projects (licença) resolveria metade do problema de governança
  sozinho, sem nenhum desenvolvimento — avaliar custo.

## Relacionado

- [[LiveAuth]]
- [[Trava de domínio e autenticação — inventário para o LiveAuth]]
- [[Inventário de Processos e Ferramentas]]
- [[Airtable Proxy]]
- [[Agent Flow]]
