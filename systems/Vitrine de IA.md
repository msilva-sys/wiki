---
type: system
status: active
updated: 2026-10-06
aliases: [vitrine, a vitrine, vitrine-ia-lmarques]
tags: [governance, sentinela, ai-adoption, catalog]
---

# Vitrine de IA

Catálogo público interno das iniciativas de IA construídas na LiveMode —
`https://vitrine.livemode.space/`. Atrás de login Google restrito a
`@livemode.com` (mesma trava de domínio catalogada em [[Trava de domínio e
autenticação — inventário para o LiveAuth]]), então não foi lida logada,
só por fonte (repo).

Fonte: repositório privado `livemode-org/vitrine-ia-lmarques` (lido via
GitHub em 2026-10-06 — não há cópia em `raw/`). Scaffold Google AI Studio
(Vite + React/TS), deploy Cloudflare Pages. `metadata.json` descreve o
propósito: *"O ecossistema colaborativo de IA da LiveMode: onde a inovação
de cada área se torna a solução de todos."*

## O que tem

- `CadastroModal.tsx` — cadastro de projeto/iniciativa no catálogo.
- `Ranking.tsx` — ranking entre as iniciativas cadastradas (curtidas/acesso).
- Um "concierge de IA" (achado no relatório de segurança abaixo, modelo
  `gpt-4o-mini`) — função exata ainda não lida no código, hipótese é busca
  ou recomendação assistida dentro do catálogo.

## Relação com o Sentinela

A Vitrine não calcula segurança sozinha — ela consome o veredito do
[[Sentinela]] via um contrato de integração dedicado (`CONTRATO-SENTINELA.md`
v2, ida/volta por polling do Sentinela, grau A/B aparecem aprovados, grau C
entra sem endereço). Detalhe completo do contrato está na página do
Sentinela, seção "Ponte com a Vitrine de IA", pra não duplicar.

## Checagem de segurança mais recente (`.sentinela-relatorio.md`, repo)

**Grau A.** Firebase legado removido, moderação de publicar/tirar do ar
exige segredo de admin conferido no servidor, nenhum segredo chega ao
navegador, login Google corporativo com sessão assinada. Único achado
aberto (baixo risco): as rotas de curtir, contar acesso e o concierge de IA
não têm rate-limit por pessoa — mitigado hoje por login restrito a
`@livemode.com` e teto de cota na conta de IA.

## Dono

Parte do **Programa de Governança e Segurança em IA** (dono: Carolina
Bezerra, projeto Linear `Guia de um Builder`) — mesmo programa-mãe do
Sentinela. O sufixo `lmarques` no nome do repo sugere autoria/manutenção por
um colega específico, não confirmado quem.

## Por que importa

A descrição do próprio `metadata.json` — "o que uma área constrói vira
solução de todos" — é quase literalmente a tese "single-player → multiplayer"
de [[Marketing teams are stuck in single-player Claude mode. Here's how to
go multiplayer]] (MKT1), só que aplicada a **projetos/produtos prontos**, não
a skills/capabilities reutilizáveis. É o paralelo institucional, já rodando,
do que o [[Desenho de um Skills Registry corporativo|LiveStry]] propõe fazer
pra skills — ver discussão em chat de 2026-10-06, ainda não fundida nessa
página.

## Open questions

- Quem de fato mantém o repo (`lmarques`) e se há relação formal com o
  LiveStry — não discutido com ninguém do time ainda.
- Função exata do "concierge de IA" dentro do catálogo.
- Se e quando o conteúdo logado do site (que não consegui ler) muda algo do
  que está descrito aqui a partir só do código-fonte.

## Relacionado

- [[Sentinela]]
- [[LiveAuth]]
- [[Desenho de um Skills Registry corporativo]]
- [[Marketing teams are stuck in single-player Claude mode. Here's how to go multiplayer]]
