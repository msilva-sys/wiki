---
type: synthesis
status: active
updated: 2026-11-20
date: 2026-11-19
aliases: [curadoria de skills, skill_curator, skill curator, esteira de skills, ci de skills]
tags: [livestry, agent-flow, skills, design-doc, github]
---

# Design Doc — Curadoria de skills pelo LiveAgents

- Status: rascunho, 2026-11-19
- Repositórios afetados: `livemode-livestry`, `fluxo-agentico` (LiveAgents),
  `livemode-claude-skills` (só recebe PRs, nada muda nele)
- Ponto de partida: LiveStry, ADR 0003 (`livemode-livestry/docs/adr/0003-proposta-de-skill-por-pr.md`)
  e `DESIGN.md`, Interfaces, bloco 5

Toda skill proposta pelo LiveStry passa pelo agente `skill_curator`, no LiveAgents,
antes de chegar à revisão humana. Só a skill aprovada vira PR, e quem abre o PR
continua sendo o LiveStry, chamado de volta pelo LiveAgents. Quem propôs recebe
pelo Slack o resultado da curadoria, a abertura do PR e o desfecho dele.

## Contexto

Hoje `propose_new_skill` e `propose_skill_update` validam o formato e abrem um PR
direto no `livemode-claude-skills`, na branch `propose/<name>`. A revisão humana é
a única porta para o conteúdo. A pessoa que propôs não fica sabendo de nada depois
da chamada (Open Issue "A pessoa não acompanha a proposta", no `DESIGN.md`).

O LiveAgents já tem as peças para o trabalho assíncrono:

- fila `queue_entries` acordada pelo despacho `POST /run/<chave>` (ADR 0042);
- transporte do Slack (ADR 0020);
- porta de leitura do GitHub (ADR 0046);
- efeitos declarados com ledger (ADR 0014).

## Objetivos

- Nenhuma skill chega ao PR sem passar pelo agente curador, inclusive ajustes em
  proposta que já está em revisão humana.
- Quem propôs, e todo mundo que contribuiu, é avisado em cada passo.
- O LiveStry continua sem estado e continua sendo o único que escreve no
  repositório de skills.

## Não-objetivos

- Substituir a revisão humana. O curador filtra o que chega ao revisor; o merge
  continua sendo a porta.
- Curadoria de PR aberto direto no GitHub, fora do LiveStry.
- Outros critérios além de duplicação e gatilho, por enquanto.
- Veredito com ressalvas. Por enquanto é só passa ou reprova.
- Reenvio automático de curadoria ou de callback que falhou.

## Fluxo

```mermaid
sequenceDiagram
    participant P as Pessoa (cliente MCP)
    participant S as LiveStry
    participant A as LiveAgents (skill_curator)
    participant G as GitHub (livemode-claude-skills)
    participant K as Slack

    P->>S: propose_new_skill / propose_skill_update
    S->>S: valida formato, ficha e diretorias; fixa origem
    S->>A: POST proposta + skill resultante + e-mail (secret)
    A->>A: guarda na fila skill_curator_events e acorda a rodada
    A-->>S: 202
    S-->>P: "A skill está sob processo de revisão. Ao fim, você será avisado no Slack."
    A->>G: lê o catálogo do main (duplicação)
    A->>A: julga: passa ou reprova
    alt reprova
        A->>K: DM aos contribuidores, com os motivos
    else passa
        A->>S: POST proposta aprovada (secret)
        S->>S: valida de novo contra a base atual
        S->>G: commit na branch propose/<name>; abre o PR se não houver
        S-->>A: PR criado, atualizado ou sem mudança
        A->>K: DM: passou na curadoria
        A->>K: DM: PR aberto (só se criado agora)
    end
    G->>A: webhook pull_request closed
    A->>K: DM: publicada (merged) ou recusada (fechado sem merge)
```

## Decisões

| Questão | Decisão |
| --- | --- |
| Quem escreve no GitHub | **O LiveStry**, como hoje. Só muda o momento: depois da aprovação, chamado de volta pelo LiveAgents. A lógica do bloco 5 fica onde está |
| Credencial do GitHub | A App `livemode-livestry` continua só no Worker do LiveStry. O LiveAgents só recebe o webhook, com o webhook secret da App, e lê o `main` pela porta da ADR 0046 |
| Quando o PR é aberto | **Só depois que a skill passa na curadoria.** Até lá a proposta vive na fila do LiveAgents |
| Ajuste em proposta com PR aberto | **Passa de novo pela curadoria** antes de virar commit no PR |
| Critérios | **Duplicação e gatilho**, por enquanto |
| Veredito | **Passa ou reprova**, com os motivos |
| Reprovação | Nada vai para o GitHub. Os contribuidores recebem os motivos por DM. Corrigir é propor de novo pela conversa |
| Ajuste reprovado em proposta com PR aberto | O PR fica como estava; os contribuidores recebem os motivos |
| Quem é avisado | **Todo mundo que contribuiu** com a proposta (e-mail de cada chamada) |
| Canal dos avisos | DM no Slack |
| Falha do curador ou do callback | Entrada fica `failed` na fila. Alerta em [#alertas](https://livemode.slack.com/archives/C0CHE9QTEAX). Quem propôs não é avisado |
| Proposta só na fila, sem PR | O LiveStry não a enxerga. Continua lendo a branch, como hoje, só para o erro "skill ainda em revisão" |
| Nomes | Agente `skill_curator`, no padrão da ADR 0002 do Fluxo Agêntico (Interfaces) |
| Autenticação entre os dois | **Um secret compartilhado**, nos dois sentidos, por enquanto |
| Contra o que se julga duplicação | O `main` do `livemode-org/livemode-claude-skills`, lido no momento da curadoria pela porta da ADR 0046 |
| Registro de onde a skill veio | **`metadata.origem`** no `SKILL.md`, com a interface que criou a skill (`livestry`). Gravado pelo LiveStry na criação e nunca mais alterado (Interfaces, bloco 1). As skills de hoje recebem `origem: livestry` num PR único. Daqui em diante, skill sem o campo conta como `unknown` |

## Interfaces

### 1. LiveStry → LiveAgents: proposta

O LiveStry mantém as validações de hoje (entrada, regra da tool contra o `main`,
juiz do catálogo, ficha e diretorias) e devolve os erros na hora. O que passa vai
para o LiveAgents em vez de ir para o GitHub.

`POST /api/skill_curator/proposals`, com
`Authorization: Bearer <secret compartilhado>`.

| Campo | Origem |
| --- | --- |
| `kind` | `new` ou `update`, pela tool chamada |
| `name`, `group`, `group_description?`, `summary`, `files`, `delete?` | Entrada da tool, já validada. Voltam iguais no callback |
| `skill` | A pasta da skill resultante (`[{path, content}]`), que o LiveStry já monta no passo 3 da validação. É o que o curador julga, sem refazer a aplicação em Python |
| `proposer_email` | `sub` do token do LiveAuth, nunca da entrada |

**Origem da skill.** Antes de enviar, o LiveStry fixa `origem` na ficha em
`metadata` do `SKILL.md`, por cima do que o modelo tiver escrito. Só mexe quando o
`SKILL.md` está entre os `files`; sem ele, vale o da base.

| Caso | Valor gravado |
| --- | --- |
| `propose_new_skill` | `livestry` |
| `propose_skill_update`, skill no `main` com `origem` | O valor do `main`. Nunca muda depois da criação |
| `propose_skill_update`, skill no `main` sem `origem` (criada por PR direto) | Nenhum. O campo é tirado se o modelo tiver posto |

As skills que existem hoje recebem `origem: livestry` num PR único no repositório
de skills, antes desta mudança entrar. Daqui em diante, quem lê o campo trata a
ausência como `unknown`: é a skill que entrou sem dizer de onde veio.

Há precedente: com o Airtable fora do ar, o LiveStry já reescreve `areas` no
`SKILL.md` (`anyArea`, em `skills/propose`). O catálogo ignora campos da ficha
que não conhece, então `origem` não muda o que é servido. O LiveAgents não
confere o campo: cada interface é responsável pelo seu.

Resposta `202` assim que a entrada está na fila. Falha no LiveAgents vira erro na
tool ("não foi possível enviar a proposta agora; tente de novo"), com só o status
no log, como os erros do GitHub hoje.

Resposta da tool à pessoa: "A skill está sob processo de revisão. Ao fim, você
será avisado no Slack." Texto a revisar.

### 2. Fila

Fila `skill_curator_events` na `queue_entries` (ADR 0042), no padrão da
`portfolio_analyst_events`. A rodada tem a chave do agente: `POST /run/skill_curator`
no worker. O `payload` é o corpo da requisição acima. A chave de idempotência é
um hash do `payload`: o mesmo envio repetido não entra duas vezes. A rota guarda e
acorda a rodada pelo despacho.

As entradas da mesma skill são curadas em ordem, uma por vez.

### 3. Curadoria

1. Lê o catálogo do `main` (nome e descrição de cada skill) pela porta de leitura
   do GitHub.
2. O agente julga a `skill` recebida em dois critérios:
   - **Duplicação:** outra skill do catálogo já cobre o mesmo pedido.
   - **Gatilho:** a descrição faz a skill ser carregada no pedido certo, e só nele.
3. Devolve `{veredito: passa | reprova, motivos: [...]}`, validado contra o schema.

Os critérios ficam no prompt e na skill do agente no Langfuse, para calibrar sem
deploy (ADR 0003 do Fluxo Agêntico).

### 4. LiveAgents → LiveStry: proposta aprovada

Só quando a curadoria passa. `POST /proposals/approved` no LiveStry, com o mesmo
secret, e o `payload` da entrada como corpo.

O LiveStry roda o mesmo caminho de hoje, a partir da tabela de estados do bloco
5: valida de novo contra a base atual, faz o commit em `propose/<name>` e abre o
PR se não houver. O commit e o PR levam o `proposer_email` da entrada, como hoje
levam o e-mail do token. O LiveStry não fixa `origem` de novo: já veio fixado.

| Resposta | O que o LiveAgents faz |
| --- | --- |
| `200 {pr: "created"}` | DM de aprovação e DM de PR aberto. Entrada `done` |
| `200 {pr: "updated"}` ou `200 {pr: "unchanged"}` | DM de aprovação. Entrada `done` |
| `422` com motivos (a validação mudou: outra proposta entrou no `main`, o grupo sumiu) | DM aos contribuidores com os motivos, como numa reprovação. Entrada `done` |
| Outro erro ou sem resposta | Entrada `failed`, alerta em #alertas |

Repetir o callback é seguro: se o tree resultante é igual ao da base, o LiveStry
não grava de novo ("Repetir não grava de novo", bloco 5). Do lado do LiveAgents,
a chamada é um efeito declarado (ADR 0014), sem confirmação humana, porque a
pessoa já confirmou na conversa.

O que mudou no `main` entre a curadoria e o callback, fora da pasta da skill, não
passa pelo curador de novo. É o mesmo limite de hoje ("Não protege contra versão
desatualizada", bloco 5).

### 5. Webhook do GitHub

A App `livemode-livestry` entrega o evento `pull_request` em
`POST /api/skill_curator/webhooks/github`, no padrão do
`/portfolio_analyst/portfolio/webhooks/linear`, conferido pelo
`X-Hub-Signature-256` com o webhook secret da App. Só interessa `action: closed`
em branch `propose/*`:

- `merged: true` → aviso de publicada;
- `merged: false` → aviso de recusada na revisão.

### 6. Avisos

DM no Slack para cada contribuidor, achado pelo e-mail.

| Momento | Conteúdo |
| --- | --- |
| Curadoria reprovou, ou o LiveStry recusou no callback | Os motivos, e que basta propor de novo pela conversa |
| Curadoria aprovou | Que passou e segue para revisão humana |
| PR aberto | Que a skill está em revisão humana (só quando o PR é criado, não a cada commit) |
| PR mergeado | Que a skill foi publicada |
| PR fechado sem merge | Que a revisão recusou a skill |
| Curadoria ou callback falhou | Alerta em #alertas para o time rodar de novo |

Contribuidores são os `proposer_email` das entradas da fila para aquela skill,
desde o último desfecho (reprovação, merge ou PR fechado).

## Segurança

- **A chave da App continua só no Worker do LiveStry.** O LiveAgents tem só o
  webhook secret da App, que não escreve nada.
- **A rota `/proposals/approved` pula a curadoria.** Quem tem o secret abre PR com
  qualquer conteúdo em nome de qualquer e-mail. A revisão humana continua sendo a
  porta, como na v1. É a segunda rota do LiveStry fora do `/mcp` que aceita
  escrita, e a única sem token do LiveAuth.
- **Conteúdo da skill é entrada não confiável para o curador.** Uma skill pode
  tentar convencer o modelo a aprovar. O curador é filtro, não porta.
- **Secret compartilhado** entre LiveStry e LiveAgents, guardado no Bitwarden e
  como secret nos dois. Trocar por token de serviço do LiveAuth (levantamento da
  v2, item 8) quando houver.
- **Webhook do GitHub** só é aceito com assinatura válida.
- O e-mail vem do token do LiveAuth na proposta e volta igual no callback. Nenhum
  campo das tools aceita e-mail.

## Mudanças por repositório

**LiveStry**

- [ ] ADR nova, substituindo em parte a 0003: o PR deixa de ser aberto na chamada
      da tool e passa a ser aberto no callback, depois da curadoria.
- [ ] `skills/propose`: a tool manda ao LiveAgents; a escrita de hoje vira o
      handler de `POST /proposals/approved`.
- [ ] `metadata.origem` no `SKILL.md` enviado (Interfaces, bloco 1).
- [ ] Texto de saída das tools.
- [ ] `DESIGN.md`: bloco 5, rota nova, Dependencies (secret novo, LiveAgents como
      serviço externo), Security, e fecha a Open Issue "A pessoa não acompanha a
      proposta".

**Fluxo Agêntico**

- [ ] ADR para a troca com serviço externo por secret compartilhado (entrada e
      callback).
- [ ] Agente `skill_curator`: prompt, schema do veredito, suíte de avaliação.
- [ ] Pacote `agents/skill_curator`: rota de entrada, fila `skill_curator_events`,
      rodada `skill_curator`.
- [ ] Efeito do callback ao LiveStry; rota do webhook; avisos no Slack.

**Repositório de skills**

- [ ] PR único gravando `origem: livestry` na ficha de cada skill existente.

**GitHub App `livemode-livestry`**

- [ ] Assinar o evento `pull_request` e apontar o webhook para o LiveAgents, com
      webhook secret.

## Perguntas em aberto

- **Texto final** da resposta das tools e de cada DM. Fica para depois.
- **Check de CI exigindo `origem`** em skill nova que entra por PR direto, para
  nenhuma nascer `unknown`.

## Relacionadas

- [[Desenho de um Skills Registry corporativo]]: origem do LiveStry.
- [[A6 Curador deve padronizar e sinalizar defasagem de skills]]: ideia anterior de curadoria de skills por agente.
- [[Agent Flow]]: o LiveAgents (Fluxo Agêntico), onde o `skill_curator` mora.
