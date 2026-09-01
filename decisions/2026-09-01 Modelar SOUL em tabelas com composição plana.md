---
type: decision
status: active
updated: 2026-09-01
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

- O dashboard precisa permitir escolher fragments, ordenar a composição,
  editar o texto específico do profile e visualizar a SOUL efetiva.
- Ainda falta decidir a tecnologia concreta do banco e onde o renderer roda.
- Ainda falta definir como uma versão é promovida para uso por um agente.
- Ainda falta decidir quais fragments iniciais existirão e escrever seu
  conteúdo real.
- A eventual aplicação em A10/A14 é trabalho futuro, não parte desta decisão.

## Relacionado

- [[Como deve funcionar o molde de agente]]
- [[Agent Harness Template]]
- [[Desenho do agente LangGraph para A10+A14]]
