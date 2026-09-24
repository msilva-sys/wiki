---
type: synthesis
status: active
updated: 2026-09-24
date: 2026-09-24
tags: [auth, security, liveauth, trava-de-dominio]
aliases: [inventário trava de domínio, impacto liveauth, inventário de auth]
---

# Trava de domínio e autenticação — inventário para o LiveAuth

Levantamento pedido por msilva para medir o impacto de uma eventual
migração para o [[LiveAuth]]: quais projetos, além do
[[Agent Flow|Fluxo Agêntico]], já implementam algum tipo de gate de
autenticação hoje. Pesquisa via `gh` na conta `tech-livemode`,
2026-09-24, cobrindo a org GitHub `livemode-org` (34 repos) e os
repositórios pessoais da própria conta `tech-livemode` (59 repos).

## Método (e uma lacuna descoberta no caminho)

Primeira rodada usou `gh api search/code` (busca de código do GitHub) por
termos como `trava-de-dominio`, `_middleware.js`, `DOMINIO_PERMITIDO`.
msilva achou por fora, na Vercel, um deploy (`tasks-projetos`) com
`/login` que a busca não retornou — o arquivo era `middleware.ts`, não
`.js`, e mesmo `filename:middleware.ts` deu zero resultado pra um arquivo
que existia no repo. **A busca de código do GitHub tem lacuna de
indexação real** (provável recência do push), não é confiável sozinha
pra esse tipo de varredura.

Segunda rodada: listagem direta da raiz de cada repo (API de conteúdo,
não busca) nos 34 + 59 repos, procurando `middleware.js`, `middleware.ts`,
`functions/`, `vercel.json`. Mais lento, mas não depende de indexação.
Cada acerto foi lido por inteiro pra confirmar se é de fato um gate de
auth (alguns `functions/` eram proxy reverso ou Cloud Functions sem
relação com autenticação, descartados: `hub-administrativo` é um proxy
reverso pra outros Pages projects, `status-salas` só redireciona domínio
antigo, `whatsapp-image-decryptor/functions` é Cloud Function de
descriptografia de imagem).

**Correção, mesma sessão**: essa segunda rodada também tinha lacuna —
só olhava a **raiz** do repo por `middleware`/`functions`/`vercel.json`.
msilva apontou um caso real que ela classificou errado: **LiveScript**
(`livemode-roteiros-nextjs`) tem login em produção
(`roteiros.livemode.space/auth/signin`) mas não usa `middleware.ts`
nenhum — o gate é feito num `app/(auth)/layout.tsx` (client-side,
redirect) + validação de token do Firebase nas rotas de API (server-side,
`withApi`, 401/403), documentado em `docs/architecture/ADR-007-firebase-
auth-authentication.md`. **Terceira rodada**: grep de palavra-chave
(`auth`, `signin`, `login`, `passport`) na árvore inteira de arquivos
(`git/trees/…?recursive=true`) de todo repo antes classificado como "sem
gate" — achou mais 5 casos reais (detalhados abaixo) que a segunda rodada
também tinha perdido. Também rodada uma checagem de `firebase.json` na
raiz de todo repo (Firebase Hosting não deixa rastro em `vercel.json`);
não achou nenhum caso novo além do LiveScript — os outros resultados
eram repos vazios (placeholder), confirmado por leitura direta.

Mesmo essa terceira rodada não é garantidamente exaustiva — um gate
embutido direto num `index.html` estático sem nenhum framework, ou uma
hospedagem fora de Vercel/Cloudflare/Firebase, não deixaria nenhum dos
sinais buscados. Ver Open questions.

## Padrão 1 — skill `trava-de-dominio` (Google OAuth por domínio, 5 deploys)

Fonte: `livemode-brain/skills/trava-de-dominio`. Cada instalação é
isolada — credencial OAuth do Google e `SESSION_SECRET` próprios por
projeto, sem registro central, falha aberta se faltar secret.

| Repo (org) | Adaptador |
|---|---|
| `projetos-vitrine` | Vercel (`middleware.js` + `api/auth/*`) |
| `projetos-hub-interno` | Vercel |
| `livemode-pulso` | Vercel |
| `guia-de-um-builder` | Cloudflare Pages (`functions/_middleware.js`) — único com trava extra no CI que bloqueia deploy sem os 3 secrets |
| `vitrine-ia-lmarques` | Cloudflare Pages |

`client-intelligence` e `livemode-template` têm a skill copiada mas não
instalada (sem gate ativo na raiz). O próprio [[Agent Flow|Fluxo
Agêntico]] usa o mesmo modelo mental mas com implementação própria em
`auth_gate.py`, não a skill (`livemode-fluxo-agentico`, ver
[[2026-08-28 Trava de domínio no Fluxo Agêntico via Google OAuth]]).

## Padrão 2 — Auth.js/NextAuth própria, domínio restrito via callback `signIn` (5 deploys)

Cada um reimplementa o mesmo gate (Google provider + checar sufixo do
e-mail em `signIn()`) do zero, em código próprio — nenhum usa a skill:

| Repo | Conta | Detalhe |
|---|---|---|
| `escala-producao` | pessoal | `ALLOWED_EMAIL_DOMAIN`, hard gate no callback |
| `cazetv-posts-app` | pessoal | mesmo padrão, `ALLOWED_EMAIL_DOMAIN` |
| `ai-enablement-hub` | pessoal | + rota admin restrita por `role` no token |
| `sistemas-visuais-hub` | pessoal | + acesso total restrito a uma allow-list de pessoas nomeadas, + exceção pra convidados externos numa única rota |
| `livemode-juridico` | pessoal (não a org — ver abaixo) | domínio checado em `app/page.tsx` (Node runtime), middleware só confere presença de cookie |

Estes já têm **RBAC por pessoa/rota** dentro do próprio app (admin vs.
viewer, allow-lists nomeadas) — mais granular que o modelo do LiveAuth
hoje (grupo binário por `app_id`, ver Non-goals do design doc). Se
migrarem, perdem essa granularidade a menos que criem um grupo por role.

## Padrão 3 — sessão Firebase própria (4 deploys)

| Repo | Conta | Detalhe |
|---|---|---|
| `tasks-projetos` | pessoal | Firebase Auth client + Admin SDK, cookie de sessão verificado no server, role (`admin`/`viewer`) por `ADMIN_EMAILS`. Middleware só confere presença do cookie (Edge Runtime não roda o Admin SDK) |
| `livemode-roteiros-nextjs` (**LiveScript**) | pessoal | achado por msilva (não pela busca) — sem middleware nenhum: gate client-side em `app/(auth)/layout.tsx` + token do Firebase validado nas rotas de API (`withApi`, 401/403), domínio checado no client (`hd` hint + checagem pós-login). Documentado em ADR-007 |
| `livemode-video-downloader` | org | Firebase Auth, login próprio; único com **infra do auth em Terraform** (`infra/tofu/modules/firebase-auth`) |
| `livemode-projects-management` | org | "Console de Publicação" — restringe quem pode publicar quais projetos na Vercel sem dar acesso à conta. Firebase Auth (domínio checado em `identity.ts`, `endsWith` contra truque de subdomínio) + **autorização por grupo própria** (`authz.ts`: `admins` globais + `deployer_groups` por projeto, config-driven, RF-03/04/05 documentados no PRD) |

`livemode-projects-management` merece destaque: é o precedente mais
próximo do objetivo do LiveAuth achado neste levantamento — grupo decide
quem pode fazer o quê em qual projeto — só que construído e preso a um
app só, sem ambição de ser compartilhado. Mesma técnica de "checagem
barata no edge/client, checagem real no server" nos quatro, e é o mesmo
modelo mental do próprio design do LiveAuth (Blocking Function +
verificação de assinatura).

## Padrão 4 — Supabase Auth (2 deploys)

| Repo | Conta | Domínio confirmado? |
|---|---|---|
| `caz-tv-escala-hub` | pessoal | sim — `ALLOWED_DOMAIN = "livemode.com"`, guard de rota client-side (TanStack Router `_authenticated`), tem flag de bypass pra dev (`VITE_DISABLE_AUTH`) |
| `live-content` | pessoal | não achei no código lido — sessão via Supabase, redireciona pra `/auth/signin` sem sessão, mas nenhuma checagem de sufixo de e-mail identificada |

## Padrão 5 — segredo único compartilhado, não domínio (3 deploys)

`world-cup-picks` e `Monitor-de-Dados` (pessoal): HTTP Basic Auth,
usuário/senha únicos via env var, não por pessoa. `copa-audiencia`
(`reports-app`, pessoal): mesma ideia com implementação própria — senha
única, cookie assinado HMAC-SHA256, gate no entry do servidor Nitro,
comentado no próprio código como decisão deliberada ("no-op se
`APP_PASSWORD` não setada — rollback trivial"). Modelo de segurança
diferente dos outros — mais parecido com a "senha compartilhada" que o
`guia-de-um-builder` documentou ter abandonado (ver `governanca.md`
desse repo, trava de domínio por e-mail substituiu senha única em
2026-09-22). Os três falham aberto se o segredo não estiver configurado.

## Padrão 6 — Express + Passport, servidor próprio (1 deploy)

`portal-audiencia-programacao` (pessoal): não é Next.js — um `api/
index.ts` só, rodando como função da Vercel, com Express + Passport
(`GoogleStrategy`), sessão via `cookie-session`, domínio checado no
callback da strategy (`ALLOWED_DOMAIN = 'livemode.com'`), headers de
segurança (`helmet`, HSTS) configurados manualmente. Arquitetura mais
distante dos outros padrões — nenhum framework de auth compartilhado.

## Sem gate detectável (não é prova de ausência)

Têm `vercel.json` mas nenhum `middleware`/`functions`/sinal de auth
achado na árvore inteira do repo — `bq-quality`, `content-pulse`,
`copa2026-labelling`, `dashboard_ao_vivo_copa`, `narradores-cztv-web`,
`social-media-thumb-collector`. **Três repos saíram desta lista na
correção** (`caz-tv-escala-hub`, `copa-audiencia`,
`portal-audiencia-programacao`) — tinham gate real que só apareceu numa
varredura mais funda; ver Método. Não confirma ausência de proteção nos
6 restantes — pode haver Vercel Deployment Protection configurado só no
painel (não aparece no repo), ou o conteúdo pode ser deliberadamente
público (ex. picks de Copa do Mundo). Não investigado a fundo; relevante
pro escopo da issue PRO-717 (skill que aponta apps com dado sensível sem
autenticação).

## `livemode-juridico`: repo fantasma na org

`livemode-org/livemode-juridico` existe mas está **vazio**. O app real
mora em `tech-livemode/livemode-juridico` (conta pessoal, Padrão 2
acima). Mesmo tipo de fragmentação de ownership que a trava de domínio
já tem — custódia não migrou junto com o nome do repo.

## LiveAuth já tem repo e projeto Firebase definido

`livemode-org/livemode-liveauth` já existe (Cloudflare Worker,
`wrangler.jsonc`, skill `liveauth-connect` já no repo). `.firebaserc`
aponta pro projeto **`livemode-liveauth-42ca1`** — dedicado, não o
`livemode-roteiros-dev` compartilhado com o LiveScript. **Resolve o Open
Issue "qual projeto Firebase" na synthesis de design** — ver
[[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]].

## Implicação para o LiveAuth

Superfície de apps com auth/domínio próprio identificados: **17 deploys**
(5 skill `trava-de-dominio` + 5 Auth.js própria + 4 Firebase própria + 2
Supabase + 1 Express/Passport), fora os 3 de segredo único compartilhado
(modelo diferente, não comparável) e o Fluxo Agêntico — **18 apps** no
total já resolvendo esse problema, cada um à sua maneira, nenhum
consumidor do LiveAuth. O design doc do LiveAuth cobre migração do Fluxo
Agêntico como não-objetivo por ora, mas não menciona nenhum dos outros
17 — se um dia virar migração, não é troca de um lugar só. Pelo menos 3
apps já têm autorização mais granular que o modelo atual de grupo
binário por `app_id` do LiveAuth: `sistemas-visuais-hub` e
`ai-enablement-hub` (RBAC por pessoa/role), e sobretudo
`livemode-projects-management` (grupo por projeto, `deployer_groups`) —
o precedente mais próximo do próprio objetivo do LiveAuth, vale uma
leitura direta do `authz.ts` de lá antes de fechar o modelo de grupo
final.

## Open questions

- Não confirmado se algum dos 17 apps tem plano de virar consumidor do
  LiveAuth.
- `live-content` (Supabase) — não confirmado se restringe por domínio ou
  não.
- 6 repos com `vercel.json` sem gate detectável — não investigado se têm
  proteção fora do repo (Vercel Deployment Protection) ou se são
  intencionalmente públicos.
- **Método ainda não é garantidamente exaustivo** (ver correção na seção
  Método) — um gate embutido direto num `index.html` estático, ou fora
  de Vercel/Cloudflare/Firebase, não deixaria nenhum dos sinais
  buscados. Se surgir mais algum caso, seguir o mesmo padrão desta
  correção: registrar inline, não reescrever silenciosamente.
- Escopo desta pesquisa: conta `tech-livemode` (org + pessoal). Não
  cobre outras contas/orgs GitHub da Livemode, se existirem.
