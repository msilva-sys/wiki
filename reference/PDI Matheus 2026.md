---
type: reference
status: active
updated: 2026-10-13
aliases: [PDI, meu PDI]
tags: [pdi, carreira]
---

# PDI Matheus 2026

Espelho em markdown do painel (`pdi-da-area`, skill). Painel vivo:
https://claude.ai/code/artifact/e46d94a6-53e9-4b5b-9977-7f297dc61033

Liderança: Gabrielle Ferreira · Revisão a cada 15 dias (desvio do padrão
bimestral da área) · **v2 — validada com Gabrielle (13/10/2026)**.

## Foco 1 — Comunicar decisões antes de agir
Princípio: *"Antes de decidir, trago as opções."*

| Ação | 30d | Evidência | Meta 60d |
|---|---|---|---|
| F1.1 Trazer opções comparadas antes de decisão técnica relevante | ✓ | mensagem assíncrona ou tópico no 1:1 com as opções comparadas, registrado antes do código | zero decisões técnicas relevantes implementadas sem essa checagem prévia |
| F1.2 Comunicar decisões em geral de forma assíncrona e frequente | ✓ | atualização assíncrona (Slack/Linear) na mesma semana da decisão | nenhuma decisão relevante chega ao 1:1 ou ao Recap como novidade |

## Foco 2 — Desenvolver visão de produto
Princípio: *"Antes de desenhar a solução, entendo o problema de negócio."*

| Ação | 30d | Evidência | Meta 60d |
|---|---|---|---|
| F2.1 Explicitar o "porquê de produto" antes da proposta técnica — como ela se conecta/escala com a arquitetura da empresa, buscando ativamente pessoas/áreas que ajudem a entender o que ainda não conheço *(ampliada v2)* | | seção/nota no discovery, anterior à proposta técnica, cobrindo porquê de produto, arquitetura e, quando aplicável, conversa ativa | 2 projetos + 1 conversa ativa registrada com outra área/pessoa |
| F2.2 Participar ativamente de conversas de produto | | contribuição de produto registrada na ata/notas da reunião | 2 reuniões com contribuição registrada |
| F2.3 "Vender" um projeto próprio em termos de valor de negócio | | apresentação ou documento curto, focado em valor | 1 apresentação ou documento feito |

## Foco 3 — Dar feedback direto *(novo v2, separado de F1.3)*
Princípio: *"Falar o difícil direto à pessoa, sem esperar ser perguntado."*

| Ação | 30d | Evidência | Meta 60d |
|---|---|---|---|
| F3.1 Dar ao menos um feedback direto a um colega | ✓ | registro de quando e a quem, na aba Registro | 1 feedback direto dado |

## Rituais *(revisado v2 — Luís sai da empresa, ritual fundido no Recap)*
- **Toda semana, no Recap da Semana** — trazer atualização de decisão/produto;
  dizer se tomei alguma decisão sem avisar antes; dizer se ouvi mais que
  direcionei numa descoberta. Tags: F1.1 · F1.2 · F2.1 · F2.2

## Papel de Gabrielle neste PDI
- Validou a v1 e ajustou a v2 na conversa de 13/10/2026.
- Participa dos checkpoints quinzenais do PDI.
- É o contato de produto e comunicação no Recap da Semana, onde os Focos 1 e
  2 são acompanhados.

## Checkpoints
Validação 13/10 · 30 dias 12/11 · CP1 13/10 · CP2 27/10 · CP3 10/11 ·
CP4–5 24/11.

## Feedbacks de origem

**Autoavaliação guiada** (07/10/2026) — *"Minha cadeira exige sobretudo
autonomia e proatividade — entrego ambos. Minha maior dificuldade
ultimamente é ter visão de produto."* Começar: compartilhar mais decisões no
geral. Parar: decidir stack sozinho sem comunicar (Hono.js/Cloudflare,
LiveAuth). Continuar: proatividade (LiveAuth, LiveStry, proxy). Na conversa:
dificuldade de visão de produto no [[Agent Flow]]; ganho de velocidade com IA
confirmado, nada ainda passado ao time; nunca deu feedback difícil direto;
próximo degrau nomeado pelo próprio — "saber me comunicar e vender melhor
meus projetos".

**Luís Fernandez** (1:1 de 01/10/2026 · norma fixada 18/08/2026) — três
violações da mesma norma no mesmo 1:1 (intake, Cloudflare Workers, Supabase).
Começar: trazer opções comparadas, comunicar async e frequente. Parar:
decidir e só avisar depois. Na conversa: time é equipe técnica, stack deve
ser simples e padronizada.

**Gabrielle Ferreira** (liderança · validação de 13/10/2026) — *"O quanto
menos você der de direcionamento do que a pessoa deve falar, melhor."*
Começar: criar metodologia de discovery (dor → como lida hoje → mundo ideal
→ como quer interagir) e buscar pessoas/áreas ativamente. Parar: focar só na
dor, sem entender como a pessoa quer interagir com a solução. Continuar:
autonomia e confiança técnica, sem ressalva da liderança. Na conversa: separa
F3; valida F2.3 como já suficiente; contexto da saída do Luís e do perfil
buscado pra vaga de tech lead (arquitetura + produto + IA estratégica) — ver
[[2026-10-13 Matheus - Gabrielle (PDI)]].

### De onde veio cada combinado, e o que ficou de fora

| Fonte | Virou | Onde / motivo |
|---|---|---|
| Autoavaliação (Começar, 07/10) — compartilhar mais decisões no geral | F1.2 | ação de comunicação assíncrona e frequente |
| Autoavaliação (Parar, 07/10) — decidir stack sozinho sem comunicar | F1.1 | ação de trazer opções antes de decidir |
| Luís, 1:1 de 01/10/2026 — três violações da norma no mesmo 1:1 | F1.1 · F1.2 | reforça as duas ações de comunicação do Foco 1 |
| Luís, norma fixada em 18/08/2026 | F1.1 | origem da norma que F1.1 formaliza |
| Autoavaliação (Cultura, 07/10) — nunca deu feedback direto | F3.1 | realocada pro Foco 3 na validação de 13/10 (antes F1.3) |
| Autoavaliação (Entrega, 07/10) — dificuldade de visão de produto, ex. Agent Flow | F2.1 | ação do porquê de produto |
| Autoavaliação (Entrega, 07/10) — mesmo ponto | F2.2 | ação de participar de conversas de produto |
| Autoavaliação (padrão, 07/10) — "vender melhor meus projetos" | F2.3 | ação de vender projeto em valor de negócio |
| Autoavaliação (Continuar, 07/10) — proatividade/autonomia | já é hábito | sem ação nova |
| Pergunta da frente técnica (07/10) | não | sem lacuna técnica clara neste ciclo |
| Validação 13/10 — separar o foco de feedback do Foco 1 | F3 (novo) | split de F1.3 num foco próprio |
| Validação 13/10 — escalar/conectar com a arquitetura + buscar pessoas | F2.1 | ação ampliada |
| Validação 13/10 — ação de "vender projeto" (F2.3) | já atendida | confirmada sem mudança |
| Validação 13/10 — ouvir mais / direcionar menos numa descoberta | ritual (Recap) | não dá pra quantificar de fora — vira pergunta fixa |
| Validação 13/10 — 1:1 semanal com o Luís | fora | ele sai da empresa; conteúdo fundido no Recap da Semana |

## Histórico de versões
- **v1** — autoavaliação guiada, 07/10/2026, pra validar com Gabrielle.
- **v2** — validada com Gabrielle, 13/10/2026: Foco 3 criado (separado de
  F1.3); F2.1 ampliada; ritual do 1:1 com Luís fundido no Recap da Semana.
