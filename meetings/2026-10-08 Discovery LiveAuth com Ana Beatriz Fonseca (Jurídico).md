---
type: meeting
status: stable
date: 2026-10-08
updated: 2026-10-08
attendees: [Matheus Oliveira da Silva, Ana Beatriz Fonseca]
aliases: [discovery jurídico, Ana Beatriz LiveAuth]
tags: [liveauth, jurídico, discovery]
---

# Discovery LiveAuth com Ana Beatriz Fonseca (Jurídico) — 2026-10-08

> Fonte: `raw/Ana Beatriz _ Matheus - 2026_10_08 13_59 GMT-03_00 - Anotações
> do Gemini.md`. Transcrição automática, confiança baixa em vários trechos
> (termos técnicos saem corrompidos — ex. "versel"/"verset" = Vercel,
> "Bit Hub" = GitHub, "FAL C" = provavelmente Vercel também). Parafraseado
> onde a transcrição é ambígua, não corrigido silenciosamente.

## Decisions

(nenhuma — conversa de discovery, nada fechado.)

## Commitments

- Matheus vai dar um overview do [[Sentinela]] pra Ana quando ela quiser
  (ela estava de férias, não conhece a ferramenta ainda apesar de já
  constar na lista de skills da empresa).
- Matheus vai compartilhar os achados desta conversa com Gabrielle e
  Carolina, como insumo do discovery do [[LiveAuth]].

## Open questions

- Se a migração do hub jurídico (`livemode-juridico`) pra conta da
  organização (GitHub e Vercel) está de fato completa — Ana diz ter
  terminado "agora" (hoje), não reconferido no código.
- Quem mais no financeiro (Kauan, Letícia) já tem modelo de acesso em
  camadas que vale estudar antes de fechar o desenho do LiveAuth.

## Facts stated

- Ana Beatriz usa "público" no sentido de **audiência ampla**, não
  exposição na rede: o hub jurídico roda num endereço web público,
  protegido só por trava de domínio Google (`@livemode.com`) — o mesmo
  padrão já catalogado como `livemode-juridico` em
  [[Trava de domínio e autenticação — inventário para o LiveAuth]]
  (Padrão 2). O acesso de fato restrito (jurídico, às vezes financeiro) é
  uma camada de convenção/processo por cima disso, não uma trava técnica
  adicional hoje.
- Ana Beatriz: o financeiro está mais avançado nisso; indica Kauan e
  Letícia (time de FP&A) como boas referências de projetos com níveis de
  acesso.
- Ana Beatriz: está construindo o hub jurídico pra centralizar acesso a
  contratos (hoje em artifacts do Claude compartilhados por e-mail
  individual; ela é a única administradora). Luís Fernandez deu acesso ao
  GitHub do projeto originalmente; depois ela passou a usar a Vercel.
  Teve problema de fragmentação de conta Vercel (cada time contrata a
  própria, não existe uma da organização) — falou com Carolina sobre
  isso. Só "agora" (hoje) terminou de importar o projeto Vercel pra conta
  da organização, e teve que mover o GitHub da conta pessoal
  `tech-livemode` pra `livemode-org`.
- Ana Beatriz: o cuidado extra com dados sensíveis atrasa a entrega dos
  próprios projetos do jurídico — mas é deliberado, pelo tipo de dado
  (contratos, valores).
- Ana Beatriz: o financeiro já cometeu o mesmo erro que o Fluxo Agêntico
  corrigiu — restringir só por domínio `@livemode.com`, sem
  granularidade — e já corrigiu isso.
- Ana Beatriz: Luís Fernandez já encontrou uma informação vazada no
  código do jurídico uma vez; desde então ela sempre revisa o que produz
  com Claude.
- Ana Beatriz: já fazia auditoria de segurança com uma skill dentro do
  Claude antes de o Sentinela existir; hoje usa o Sentinela, que já está
  disponível pra todo mundo.
- Ana Beatriz: não quer ser a primeira a implementar isso do zero —
  prefere ver o que outros times/áreas já fazem antes de avançar.
