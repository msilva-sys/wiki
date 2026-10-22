---
type: project
status: active
updated: 2026-10-22
aliases: [liveauth, liveauth poc]
tags: [auth, security, liveauth, proxy]
---

# LiveAuth

> Related: [[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]] · [[Airtable Proxy]]

Autorização por grupo compartilhada entre apps internos da Livemode. Já em
produção no Linear — iniciativa **LiveAuth**, quatro projetos (POC,
produção, integração, melhorias — ver Estado abaixo), lead msilva, team
`Projetos-livemode`. O desenho completo (objetivo, interfaces, segurança,
open issues) vive na synthesis linkada acima; esta página acompanha o
estado de execução.

## Estado, 2026-10-07 (checado direto no Linear)

**Achado ao atualizar esta seção**: um projeto inteiro de deploy em
produção foi criado e concluído em 2026-09-24, no mesmo dia em que esta
página registrava "falta issue de deploy" — nunca tinha sido trazido pra
wiki. A iniciativa **LiveAuth** no Linear hoje tem quatro projetos:

- **LiveAuth - POC** ([P-PRO-29](https://linear.app/projetos-livemode/project/liveauth-poc-c6dfa1c702b0)) — **Completed**, 2026-09-24. PRO-711 (login
  restrito a @livemode.com), PRO-714 (login único decide quem entra),
  PRO-713 (tela do grupo `admin-proxy`), PRO-717 (skill de auditoria de
  auth) — as quatro `Done`.
- **LiveAuth em produção** ([P-PRO-34](https://linear.app/projetos-livemode/project/liveauth-em-producao-de0c066c4b57)) — **Completed**, mesmo dia
  2026-09-24. Migrou a hospedagem pra Cloudflare (acompanhando a migração
  geral da Livemode), tíquete de login passou de memória pra Firestore
  (a nova hospedagem não garante processo único), conexão com agentes de
  IA via MCP migrada junto (PRO-755), login validado ponta a ponta
  (PRO-727). Único item **Canceled**: PRO-728 (migrar apps já conectados
  pro endereço novo) — N/A, nenhum app estava conectado ainda. *(Conferido
  no lint de 2026-10-22: cancelado às 12h22 de 2026-09-24; o commit que
  conecta o Fluxo Agêntico é das 16h14 do mesmo dia e já nasce no endereço
  de produção.)*
- **Integração com LiveAuth** ([P-PRO-32](https://linear.app/projetos-livemode/project/integracao-com-liveauth-e78af1e2fd54)) — **Backlog**, não iniciado
  de fato. É o trabalho do lado do proxy do Airtable pra virar o primeiro
  consumidor real: PRO-712 (cadastrar o proxy como app conectado), PRO-715
  (sessão própria pós-login), PRO-716 (bloquear `/dashboard` por grupo) —
  as três ainda `Backlog`. **Corrigido em 2026-10-22**: o que estava aqui —
  "nenhum consumidor real do LiveAuth hoje", apoiado em
  [[Trava de domínio e autenticação — inventário para o LiveAuth]] — está
  errado desde o dia em que foi escrito. O proxy do Airtable de fato segue
  sem conectar, mas o [[Agent Flow|Fluxo Agêntico]] já é consumidor real em
  produção; ver "Consumidores reais" abaixo.
- **LiveAuth - Melhorias** ([P-PRO-35](https://linear.app/projetos-livemode/project/liveauth-melhorias-2cc8c0893747)) — **Backlog**, não iniciado. Papel
  leitura/escrita por membro de grupo (PRO-730/731/733), histórico
  auditável (PRO-734), access log (PRO-735), Sentinela checando auth nos
  apps (PRO-689) — motivado pelo Console de Publicação, que hoje mantém
  sua própria lista de quem edita num YAML.

**Resumo**: LiveAuth está em produção de verdade (hospedagem definitiva,
MCP incluso) e já tem consumidor real — o [[Agent Flow|Fluxo Agêntico]],
por dois caminhos (ver abaixo). O que segue represado é o `/dashboard` do
Airtable Proxy: trabalho não iniciado, não bloqueado por nada técnico
listado aqui.

## Consumidores reais, 2026-10-22 (correção)

Correção levantada por msilva em 2026-10-22: esta página e o inventário
vinham afirmando "nenhum consumidor real do LiveAuth" desde 2026-09-24.
**Errado** — o [[Agent Flow|Fluxo Agêntico]] é consumidor real desde o
mesmo dia em que o inventário foi escrito. Conferido direto no código de
`livemode-fluxo-agentico` nesta sessão, não por relato:

- **Login de navegador, desde 2026-09-24** (commit `f4b69ae`, *feat(auth):
  migra login pro LiveAuth e fecha rotas de leitura sem sessao*, PRO-717):
  o `auth_gate.py` não roda mais OAuth próprio — redireciona pro
  `/api/authorize` do LiveAuth e valida o tíquete em
  `/api/authorize/verify` (`apps/api/src/core/infra/auth_gate.py:92-118`).
  App id `fluxo-agentico`, apontando por default pra URL de produção
  `https://liveauth.livemode-projetos.workers.dev`
  (`apps/api/src/core/infra/settings.py:87-88`).
- **Servidor MCP protegido, desde 2026-09-28** (commit `f8d6fdd`,
  *feat(mcp): expoe agentes via MCP com auth LiveAuth*, PRO-762): o Fluxo
  Agêntico é *resource server*, o LiveAuth é o authorization server. O
  token é verificado localmente por JWT ES256 + JWKS
  (`/.well-known/jwks.json`), com `audience` igual à URL do MCP cadastrada
  como `resource` do app (`apps/api/src/core/infra/mcp.py:52-107`). Do
  lado do LiveAuth é o PRO-755 (commit `e7cb622`) + a skill
  `liveauth-protect-mcp` (`06fe3d8`), com a assinatura do `access_token`
  corrigida no runtime do Workers (`c752aac`).

Ou seja: o primeiro consumidor real não foi o `/dashboard` do proxy do
Airtable, como o design doc previa — foi o Fluxo Agêntico, e por dois
caminhos (gate de navegador e MCP). *(unverified: não abri a URL de
produção nesta sessão; a evidência é o código e o default de configuração
apontando pra produção.)*

**Consequência pro discovery**: o argumento "ninguém usa ainda" não vale
mais — existe caso em produção pra mostrar nas conversas com as áreas,
inclusive o caso MCP, que é exatamente o "login centralizado para agentes
de IA" que o LiveAuth queria provar.

**Por que a wiki errou**: o inventário de 2026-09-24 classificou o Fluxo
Agêntico como "auth própria em `auth_gate.py`" — verdade até aquele dia, e
a migração entrou no mesmo dia. A atualização de 2026-10-07 leu o Linear,
~~onde esse trabalho está registrado sob o projeto do Fluxo Agêntico
(PRO-717/PRO-762), não sob os projetos da iniciativa LiveAuth~~ — e não
reconferiu o código. *(Corrigido no lint de 2026-10-22, conferido no
Linear: só o PRO-762 é do projeto Infra do Fluxo Agêntico. O PRO-717 é da
LiveAuth - POC — a skill de auditoria listada acima — e é o número que o
commit `f4b69ae` cita. A pista estava sob a iniciativa LiveAuth, com título
de skill, não de migração. Unverified: por que o commit cita o PRO-717;
provável que fechar as rotas de leitura abertas tenha saído da
auditoria.)*

## Open questions (herdadas da synthesis)

Nome "LiveAuth" fechado com Luís, falta validar com a Gabi; dono da infra
segue indefinido — ver a lista completa na synthesis. **Projeto Firebase
já resolvido** (2026-09-24): o repo `livemode-org/livemode-liveauth` já
existe, `.firebaserc` aponta pro projeto dedicado
`livemode-liveauth-42ca1`, não o `livemode-roteiros-dev` compartilhado.

## Impacto de uma futura migração

Inventário de apps com auth própria feito em 2026-09-24, a pedido de
msilva. **Quatro rodadas de correção no caminho** — cada uma achada por
uma pergunta direta de msilva ou por checagem mais funda: busca de
código do GitHub tinha lacuna de indexação; varredura de árvore só
olhava raiz; grep de palavra-chave só olhava nome de arquivo, não
conteúdo (perdeu auth embutida direto em `index.html`). Total final:
**20 deploys** reimplementando gate de e-mail/domínio cada um do seu
jeito, fora o Fluxo Agêntico — **21 apps** no total. (Dizia aqui "nenhum
consumidor do LiveAuth hoje"; **corrigido em 2026-10-22** — o próprio
Fluxo Agêntico migrou no mesmo 2026-09-24, ver "Consumidores reais"
acima.) Mais 1 app confirmado **aberto de propósito**
(`copa2026-labelling`, banco Firebase com regra pública) e 2 candidatos
não confirmados por código (`painel-salas`, `livemode-brain`). Achado
notável: `livemode-projects-management` já tem autorização por grupo
própria (`authz.ts`), o precedente mais próximo do objetivo do LiveAuth
achado no levantamento. Ver
[[Trava de domínio e autenticação — inventário para o LiveAuth]].

**Fora do GitHub, 2026-10-22**: a auditoria dos webhooks em
[[Mapeamento dos fluxos do n8n]] achou a página de Ranking de Executivos do
CRM com login Google e cookie de sessão reimplementados dentro de um fluxo
— o 21º caso de reimplementação, primeiro fora de repo. Pelo menos 6
webhooks servem página pra humano e são os candidatos naturais ao LiveAuth;
para o resto, a synthesis sugere trava no nó do n8n.

## Discovery — casos de uso levantados, 2026-09-30

Em [[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]]:

- **[[Gustavo Cruz]]**, área fiscal, opera o "Hub de Automações Fiscais" —
  dashboards sobre dados do ERP TOTVS, travados só por senha, acessíveis
  externamente, sem rate limiting, vulneráveis a força bruta. Caso real do
  problema que o LiveAuth quer resolver; ainda não decidido se migra pro
  LiveAuth ou só recebe mitigação via Sentinela por ora.
- **[[Marina Ferrão]]** (atendimento), via Gustavo: planilha do núcleo criativo
  que outros times precisam ler mas só o time dono pode editar — requisito
  de granularidade de permissão por time, ainda sem desenho técnico.

### Follow-up direto com Marina Ferrão, 2026-10-02

Em [[2026-10-02 Discovery LiveAuth com Marina Ferrão (Atendimento)]] (contato
passado por Gabrielle): a dor real do atendimento é compartilhamento de
Drive/planilha com **clientes externos** (fora do domínio @livemode) —
controlado hoje por convenção (pasta por cliente, cadastro de e-mail por
e-mail), não por código, motivado por um vazamento de link real durante a
Copa. **msilva reconheceu em tempo real que isso pode ser um problema
diferente do que o LiveAuth resolve** — LiveAuth hoje restringe login a
`@livemode.com`, não endereça compartilhamento com conta externa. Sem
resolução; fica como open issue de escopo, não extensão de requisito.

## Conversa com Daniel Robillotta (Inventário de Processos), 2026-10-07

Em [[2026-10-07 Daniel - Matheus (Inventário de Processos e Governança de Auth)]]:
msilva demonstrou a POC pra [[Daniel Robillotta]], que lidera um
mapeamento paralelo de processos/ferramentas/API keys a pedido do CFO
[[Zoca]]. Acordaram seguir as duas frentes separadas por ora, com plano de
unir depois — ainda sem desenho de como. Reafirmado como open issue: validar
a POC com Carolina e Gabrielle antes de ampliar o uso. Ver
[[Inventário de Processos e Ferramentas]].

Na mesma conversa, msilva citou seu próprio caso de chave de API
compartilhada (Fluxo Agêntico, provável) correndo risco de ficar sem
crédito por ser de uso exclusivo de LLM com vários agentes — exemplo
concreto do problema que o inventário do Daniel também ataca.

## Discovery com o jurídico ([[Ana Beatriz Fonseca]]), 2026-10-08

Em [[2026-10-08 Discovery LiveAuth com Ana Beatriz Fonseca (Jurídico)]]:
**o hub jurídico dela não é um app novo — é o `livemode-juridico`** já
catalogado em
[[Trava de domínio e autenticação — inventário para o LiveAuth]] (Padrão
2, domínio checado em `app/page.tsx`). "Não temos projetos públicos", na
fala dela, é sobre **audiência** (quem ela deixa acessar), não exposição
de rede — o hub roda num endereço público, protegido só por trava de
domínio Google.

**Validação independente do argumento central do LiveAuth**: o financeiro
já cometeu e corrigiu o mesmo erro que motivou
[[2026-08-28 Trava de domínio no Fluxo Agêntico via Google OAuth]] —
restringir só por domínio `@livemode.com` não é suficiente, precisa de
granularidade. Segundo discovery independente a confirmar a mesma dor,
depois do de Gustavo Cruz — o de Marina Ferrão apontou outro problema (ver
acima).

**Possível fechamento de gap do inventário**: Ana diz ter terminado hoje
de migrar o Vercel e o GitHub do hub jurídico pra conta da organização
(`livemode-org`) — se confirmado, resolve o achado "repo fantasma na org"
que o inventário registra desde 2026-09-24. Não reconferido no código
ainda.

Referências novas pro discovery: Kauan e [[Letícia]] (financeiro/FP&A),
ainda sem contato direto.
