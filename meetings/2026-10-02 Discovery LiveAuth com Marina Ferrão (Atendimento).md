---
type: meeting
status: stable
updated: 2026-10-07
date: 2026-10-02
attendees: [Matheus Silva, Marina Souza Ferrão]
transcription_confidence: high
aliases: [marina ferrao discovery, liveauth atendimento]
tags: [liveauth, auth, discovery, atendimento]
---

# Discovery LiveAuth com Marina Ferrão (Atendimento) — 2026-10-02

Follow-up direto ao caso citado de segunda mão por Gustavo Cruz em
[[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]] — Gabrielle
("Gabi") passou o contato de Marina Souza Ferrão pra msilva. Boa diarização
(speaker labels reais, ao contrário das reuniões anteriores de LiveAuth).

msilva entra explicando o problema de autenticação/autorização (trava de
domínio @livemode, reimplementação por projeto); a conversa evolui pra um
retrato bem mais amplo do dia a dia do atendimento — e msilva percebe, ao
final, que boa parte do que surgiu é um problema diferente do que o LiveAuth
resolve (compartilhamento de arquivo/planilha com cliente externo, não gate
de app interno @livemode).

## Decisions

Nenhuma — reunião de discovery.

## Commitments

- msilva: compartilhar com Marina o que o time de projetos está focando,
  pra evitar duplicar esforço — pedido dela, não respondido ainda na
  própria call (queda de conexão no fim).

## Open questions

- Se o LiveAuth (gate de app interno, login @livemode) de fato serve o
  problema de Marina — que é majoritariamente compartilhamento de
  Drive/planilha com **clientes externos** (fora do domínio @livemode), não
  autenticação de usuário interno. msilva reconhece isso em tempo real na
  própria call, sem resolver.
- Futuro da interface de drag-and-drop que Gabrielle tentou construir sobre
  o Airtable pra distribuição de inserção — Marina não sabe se o projeto
  continua.
- O que o time de projetos vai compartilhar de volta com Marina sobre o
  que está em foco — combinado, não entregue ainda.

## Facts stated

- Marina: trabalha de São Paulo, não do Rio.
- Marina: atendimento fica dentro do comercial, lida direto com
  patrocinadores de inserção de mídia (CaséTV, entidades esportivas —
  Paulistão, Copinha). Diretor da área: **Mateus Favato**. "Redes de
  atendimento": Marina, Belém, Vitória Momi, Vittor — cada um com 3-4
  analistas/especialistas, cada um com portfólio de clientes.
- Marina: incidente real — durante a Copa, um link vazou pra audiência
  externa. Antes disso, planilhas de reporte (ex.: Itaú, consumida por 3
  empresas/agências diferentes ao mesmo tempo) usavam compartilhamento
  "qualquer um com o link", por necessidade de acesso multi-empresa. Depois
  do vazamento, política da empresa mudou pra cadastro de e-mail por
  e-mail de cliente — sem mais link aberto.
- Marina: hoje o controle de acesso é todo por convenção, não por código —
  um drive único compartilhado por todo o time de atendimento, pasta por
  cliente, cada atendimento organiza do seu jeito (alguns marcam "externo"
  no nome da pasta). Nunca houve problema de segurança além do vazamento
  citado.
- Marina: quer uma interface/dashboard de verdade pro cliente, em vez de
  acesso direto a planilha — "próximo passo" desejado, não construído.
- Marina: processo de distribuição de inserção é manual — time de ~30
  pessoas preenche planilha a partir do racional que o "PEC" fornece
  (estoque por competição); acesso restrito por regra de aba, não por
  código. Uma pessoa do time (citada como "Isaqui") automatizou parte da
  montagem do "mapa de inserção", puxando dados do estoque (PEC) e do hub
  de audiência.
- Marina: tentativa de automação mais ambiciosa ("Brand VAR") durante a
  Copa, feita pelo então "time de dados" (hoje "time de Intel"), não deu
  certo.
- Marina: maior dor real é falta de dashboard macro — hoje cada atendimento
  rastreia manualmente se a entrega está batendo a audiência vendida
  (pacing, risco de precisar bonificar inserção).
- Marina: usa Airtable como banco de dados (bom pra isso), mas acha ruim
  pra gestão de projeto/distribuição — testou um prototype de interface
  feito por Gabrielle (drag-and-drop sobre o Airtable) e achou travado/lento.
  Futuro do projeto incerto.
- Marina: outras automações já em andamento no time — site de upload de
  material com convenção de nome (feito por "Maria de Design"), site de
  envio de peças pelo cliente (feito por "Cácia") — ambos alimentam o
  Airtable com os links.
- msilva: está no terceiro mês na Livemode; time de projetos (Rio),
  trabalha com João (de Projetos, próximo ao comercial); mapeou o problema
  de auth/autorização tanto internamente quanto em outros times da
  Livemode.
- msilva, em tempo real: reconhece que a conversa revelou um problema
  "anterior" ao que ele veio mapear — ainda em discovery pra entender se
  são o mesmo problema ou não.

## Notable quotes

- Marina: *"A gente tem nossas regras de quem pode mexer em cada aba, mas
  eu acho que é um ponto de melhoria."*
- Marina: *"Vocês não precisam desenvolver algo do zero."*
- msilva: *"Eu tava pensando já num contexto, mas a gente tá talvez no
  anterior."*

## Relacionado

- [[LiveAuth]]
- [[Marina Ferrão]]
- [[Mateus Favato]]
- [[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]]
