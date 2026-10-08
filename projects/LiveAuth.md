---
type: project
status: active
updated: 2026-10-08
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
  pro endereço novo) — N/A, nenhum app estava conectado ainda.
- **Integração com LiveAuth** ([P-PRO-32](https://linear.app/projetos-livemode/project/integracao-com-liveauth-e78af1e2fd54)) — **Backlog**, não iniciado
  de fato. É o trabalho do lado do proxy do Airtable pra virar o primeiro
  consumidor real: PRO-712 (cadastrar o proxy como app conectado), PRO-715
  (sessão própria pós-login), PRO-716 (bloquear `/dashboard` por grupo) —
  as três ainda `Backlog`. **Continua valendo**: nenhum consumidor real do
  LiveAuth hoje, consistente com
  [[Trava de domínio e autenticação — inventário para o LiveAuth]].
- **LiveAuth - Melhorias** ([P-PRO-35](https://linear.app/projetos-livemode/project/liveauth-melhorias-2cc8c0893747)) — **Backlog**, não iniciado. Papel
  leitura/escrita por membro de grupo (PRO-730/731/733), histórico
  auditável (PRO-734), access log (PRO-735), Sentinela checando auth nos
  apps (PRO-689) — motivado pelo Console de Publicação, que hoje mantém
  sua própria lista de quem edita num YAML.

**Resumo**: LiveAuth está em produção de verdade (hospedagem definitiva,
MCP incluso) — só falta conectar o primeiro consumidor real (Airtable
Proxy `/dashboard`), que é trabalho represado, não bloqueado por nada
técnico listado aqui.

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
jeito, fora o Fluxo Agêntico — **21 apps** no total, nenhum consumidor
do LiveAuth hoje. Mais 1 app confirmado **aberto de propósito**
(`copa2026-labelling`, banco Firebase com regra pública) e 2 candidatos
não confirmados por código (`painel-salas`, `livemode-brain`). Achado
notável: `livemode-projects-management` já tem autorização por grupo
própria (`authz.ts`), o precedente mais próximo do objetivo do LiveAuth
achado no levantamento. Ver
[[Trava de domínio e autenticação — inventário para o LiveAuth]].

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
granularidade. Segundo discovery independente (depois de Gustavo Cruz e
Marina Ferrão) a confirmar a mesma dor.

**Possível fechamento de gap do inventário**: Ana diz ter terminado hoje
de migrar o Vercel e o GitHub do hub jurídico pra conta da organização
(`livemode-org`) — se confirmado, resolve o achado "repo fantasma na org"
que o inventário registra desde 2026-09-24. Não reconferido no código
ainda.

Referências novas pro discovery: Kauan e [[Letícia]] (financeiro/FP&A),
ainda sem contato direto.
