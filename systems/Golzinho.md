---
type: system
status: active
updated: 2026-10-16
aliases: [golzinho, live content, goal-webhook, pipeline de gols]
tags: [opta, cazetv, supabase, realtime, live-content]
---

# Golzinho

Robô que acompanha todos os jogos transmitidos e, a cada gol, posta no Slack
(`#golzinho-alertas`, `C09HK7TLWBW`) o alerta já com a arte do jogador puxada do
Drive e a legenda gerada por IA. É a cara do projeto **Live Content**, idealizado
por Ana Zacour com Peçanha e João Kropf, depois com Roberta Rodrigues e Isabella
Lima (anúncio no `#general`, 2025-10-03).

O alvo do produto não é "ter dado de futebol" — é **chegar antes do replay**:

> Com a Opta, temos um delay de no máximo 30 segundos. Pensando em uma
> transmissão, o timing é basicamente ter tudo pronto quando o replay do evento
> está passando.
> — Peçanha, `#general`, 2025-10-03

Importa pra [[Opta]] porque o banco que ele alimenta é o que a [[Opta API]] expõe
como "dados próprios" em `/v1/ourGoals`, `/v1/ourMatches` e nos WebSockets — sete
meses depois de o banco já existir.

## A arquitetura

```
         pergunta de forma contínua
Opta ◄──────────────────────────────── [produtor — NÃO IDENTIFICADO]
                                                │ grava
                                                ▼
                                   Supabase: tabelas goals e matches
                                                │
          ┌──────────────┬──────────────────────┼──────────────┐
          ▼              ▼                      ▼              ▼
    goal-webhook    goal-clipper          live-content     Opta API
   (o robô em si)  (clipe de vídeo)        (telas)       (/ourGoals, /ws)
```

A tabela é o ponto de encontro: o gol entra uma vez e três sistemas reagem juntos,
cada um fazendo uma coisa. **Não é cache** — se fosse, espelharia o schema da Opta,
teria TTL e não teria canal de push. A persistência existe porque a Opta é pull
(nunca avisa nada) e o produto precisa de push.

## Quem lê (verificado nos repos, 2026-10-16)

| Repo | Papel | Como é acionado |
| --- | --- | --- |
| `tech-livemode/goal-webhook` | O Golzinho propriamente dito. Legenda com Gemini, arte do Drive, post no Slack | Database Webhook do Supabase: `POST` a cada `INSERT`/`UPDATE` em `goals` |
| `tech-livemode/goal-clipper` | OCR do placar na transmissão + FFmpeg; corta clipe de 14s (9s antes, 5s depois do gol), calibrando o atraso do broadcast | Assinatura Realtime em `goals` |
| `tech-livemode/live-content` | Next.js: telas de gols, partidas, captions e tweets | Leitura sob demanda |
| `tech-livemode/opta-api` | Expõe leitura e repassa o Realtime adiante | Assinatura Realtime |

Nenhum deles escreve em `goals`/`matches` — conferido linha a linha. O
`goal-webhook` só grava `captions`, `assets` e um gol-dummy de teste.

O `goal-webhook` só republica quando `scorer` **ou** `confirmed` muda: é o
mecanismo de corrigir no Slack um gol anulado depois do alerta.

## As tabelas

- **`matches`** — calendário, placar, estádio, flag `live`, e os horários **reais**
  de início de cada tempo (`first_half_start`, `second_half_start`), que são o que
  permite ao clipper achar o gol na gravação.
- **`goals`** — minuto e `minute_sec` (precisão de segundo), autor, tipo
  (`G`/`OG`/`PG`), placar logo após o gol, `confirmed` (`yes`/`no`/`disallowed`) e
  `additional_comments`.

Os IDs são os mesmos da Opta: `id` de partida serve como `fx` nos feeds, e
`tournament_calendar` é o `tmcl` da edição.

## Riscos

- **O produtor é um ponto cego.** Ninguém mapeou o processo que escreve nas
  tabelas, e ele não tem monitoramento. Se parar, `/ourGoals` e `/ourMatches`
  seguem respondendo `200` com dado velho; o WebSocket fica mudo — e silêncio é
  indistinguível de "nenhum gol ainda". Ver
  [[Quem escreve nas tabelas goals e matches]].
- **Três clientes acessam o Supabase direto**, com credencial do banco, fora do
  log de auditoria `opta-proxy`. O controle de acesso que a [[Opta API]] constrói
  não alcança esse caminho.
- **Há um arquivo `service-account.json` versionado na raiz do `goal-webhook`.**
  Só o nome foi observado na árvore do repo; o conteúdo **não** foi lido de
  propósito. Se for chave real, é credencial em histórico de git. Verificar.

## Histórico

| Data | O quê |
| --- | --- |
| 2025-09-22 | Primeiro commit do `goal-webhook` |
| 2025-09-26 | Canal `#golzinho-alertas` criado |
| 2025-10-03 | Live Content anunciado no `#general` |
| 2026-02-05 | Volta depois de uma parada (*"OOOOOOOO GOLZINHO VOLTOOOOOU"*) |
| 2026-04-14 | A [[Opta API]] passa a expor as mesmas tabelas por HTTP e WebSocket |
| 2026-04-24 | Último commit do `goal-webhook` (troca do modelo Gemini) |
| 2026-10-16 | Em operação — alertas de Libertadores, Ligue 1, Serie A e Bundesliga no canal |
