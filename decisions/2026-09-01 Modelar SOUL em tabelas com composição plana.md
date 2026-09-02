---
type: decision
status: active
updated: 2026-09-02
date: 2026-09-01
aliases: [SOUL relacional, soul_versions, soul_composition]
tags: [agent-flow, agents, soul, database, composition]
---

# Modelar SOUL em tabelas com composição plana

## Decisão

msilva decidiu em 2026-09-01, durante uma discussão em chat, que a SOUL dos
agentes deve ser uma **fonte comportamental independente** do `system_prompt`
mantido no Langfuse. O `system_prompt` continua específico da capacidade ou
tarefa; a SOUL descreve como o agente se comporta entre diferentes tarefas e
canais.

A primeira versão será simples e composable:

- persistida em tabelas, não em arquivos YAML;
- conteúdo de cada versão como texto direto, sem uma árvore de atributos
  aninhados;
- composição plana entre profiles e fragments;
- relacionamento representado por join table, não por array de componentes;
- ordem de composição explícita;
- versões imutáveis e referências sempre presas a uma versão exata.

Nenhuma implementação nos agentes A10/A14 foi autorizada nesta operação; esta
página registra somente o desenho decidido.

## Modelo V1

```sql
create table soul_versions (
  id          uuid primary key,
  name        text not null,
  version     integer not null,
  kind        text not null, -- fragment | profile
  content     text not null,
  created_at  timestamp not null,
  created_by  text not null,

  unique (name, version)
);

create table soul_composition (
  profile_version_id   uuid not null references soul_versions(id),
  component_version_id uuid not null references soul_versions(id),
  position             integer not null,

  primary key (profile_version_id, component_version_id),
  unique (profile_version_id, position)
);
```

Cada linha de `soul_versions` contém somente identidade, versão, tipo e um bloco
textual. `soul_composition` declara quais fragments formam um profile e em que
ordem devem ser aplicados.

Exemplo conceitual:

```text
livemode-core@1
→ evidence-first@1
→ a10@1
= SOUL efetiva do A10
```

O conteúdo próprio do profile é aplicado depois dos fragments ordenados. O
agente aponta para uma versão específica do profile, não apenas para seu nome.

## Regras de composição

- Somente `profile` aparece como pai em `soul_composition`.
- Somente `fragment` aparece como componente.
- Fragments não compõem outros fragments no V1; não há recursão nem árvore de
  herança.
- Uma edição cria nova linha em `soul_versions`; versões existentes não são
  alteradas.
- Atualizar um fragment não muda agentes automaticamente. A adoção exige uma
  nova versão do profile com novas linhas na join table.
- A ordem é `position` crescente; depois vem o `content` do próprio profile.
- O runtime deve registrar no trace a versão do profile e as versões dos
  fragments usados.

## Relação com o Langfuse e o harness

[[Agent Harness Template]] posiciona `Soul.md` dentro do Harness, ao lado de
Skills, Tools e MCP. Esta decisão troca o arquivo sugerido no template por uma
fonte relacional versionada, mas preserva a separação conceitual.

O Langfuse pode receber a SOUL efetiva renderizada e seus identificadores para
observabilidade. Ele não é, por esta decisão, a fonte de verdade da SOUL. Seu
`system_prompt` e a SOUL possuem autoria, versionamento e ciclo de vida
separados, ainda que o harness precise combiná-los no contexto enviado ao
modelo.

## Por que este recorte

O objetivo é preservar composição e reutilização sem construir agora um
framework de personalidade, um schema dinâmico de atributos ou uma cadeia de
herança. O conteúdo textual mantém a edição straightforward para um futuro
dashboard; a join table dá ordenação e integridade referencial sem esconder a
composição num array ou JSON.

## Consequências e questões restantes

- ~~O dashboard precisa permitir escolher fragments, ordenar a composição,
  editar o texto específico do profile e visualizar a SOUL efetiva.~~
  **Implementado** — ver seção abaixo.
- ~~Ainda falta decidir a tecnologia concreta do banco e onde o renderer
  roda.~~ **Resolvido**: Postgres (`POSTGRES_URL_NON_POOLING`), mesmo banco
  do checkpointer de chat (`langgraph.checkpoint.postgres.PostgresSaver`);
  renderer roda dentro do próprio backend FastAPI (`soul/render.py`),
  chamado pelos agentes em runtime, não um serviço separado.
- ~~Ainda falta definir como uma versão é promovida para uso por um
  agente.~~ **Resolvido**: `soul/store.py::promote()`. Ver seção abaixo.
- Ainda falta decidir quais fragments iniciais existirão e escrever seu
  conteúdo real — `soul/seed.py` semeia um bootstrap idempotente em
  `development` (não decide o conteúdo definitivo de produção).
- ~~A eventual aplicação em A10/A14 é trabalho futuro, não parte desta
  decisão.~~ **Feito**: `a10/agent.py` e `a14/agent.py` chamam
  `load_active_soul(agent_key, environment)` e compõem `<soul>`+`<task>`
  antes de cada invocação (analítica e de chat).

## Implementação — auditada diretamente no código, 2026-09-02

Não fazia parte desta decisão original; registrado aqui porque é o
desdobramento direto dela, achado revisando o repo `livemode-fluxo-agentico`
(branch `langgraph`) a pedido de msilva, sem sessão de chat própria — ver
[[Agent Flow]] pro callout equivalente na página-mãe.

- **Schema V1 saiu como desenhado**, com reforços que a decisão não
  antecipava: `soul_versions` é imutável por **trigger de banco**
  (`soul_versions_no_update`, não só convenção de código); `soul_composition`
  e `soul_bindings` têm **trigger de checagem de `kind`** (profile só compõe
  fragment, binding só aponta pra profile) — a integridade referencial que a
  decisão descrevia em prosa virou constraint de banco.
- **`soul_composition_set`** — tabela adicional não prevista aqui: marca uma
  composição como finalizada mesmo quando vazia, distinguindo "nunca
  configurada" de "configurada sem fragments". Um profile só pode ser
  promovido depois de finalizado — impede mutar a composição de uma SOUL já
  ativa por baixo dos panos.
- **Dashboard existe** (`frontend/src/pages/soul-page.tsx` + `soul/api.py`),
  atrás do mesmo login `@livemode.com` do resto do produto
  (`auth_gate.py`). Leitura (`list_versions`, `get_composition`,
  `list_bindings`, `preview`) exige só sessão; escrita (`create_version`,
  `set_composition`, `promote`) exige `require_admin`.
- **Promoção** (`soul/store.py::promote`) troca o binding ativo e grava uma
  linha em `soul_promotions` (quem, quando, versão anterior/nova) — rollback
  é o mesmo endpoint apontando pra uma versão anterior, não uma operação
  separada. Promover para `environment="production"` exige
  `confirm_production=true` explícito no request — trava que a decisão
  original não previa.
- **Runtime**: `compose_system_prompt` (`soul/render.py`) junta `<soul>` (a
  SOUL efetiva) com `<task>` (o prompt do Langfuse) em texto simples, com
  precedência explícita documentada em docstring — guardrails de código
  sempre vencem, a SOUL só governa tom/postura, nunca os critérios da tarefa.
  Cada trace do Langfuse carrega `soul_profile`/`soul_profile_version`/
  `soul_fragment_versions` como metadata, cumprindo o requisito de
  observabilidade que a decisão original já pedia.

## Relacionado

- [[Como deve funcionar o molde de agente]]
- [[Agent Harness Template]]
- [[Desenho do agente LangGraph para A10+A14]]
- [[Agent Flow]]
