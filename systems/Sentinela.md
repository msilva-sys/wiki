---
type: system
status: active
updated: 2026-10-06
aliases: [sentinela, SentinelaMODE, /sentinela:sentinela]
tags: [security, governance, homologação, cloudflare]
---

# Sentinela

Checagem de segurança e homologação de projetos da LiveMode. Peça central do
**Programa de Governança e Segurança em IA** (dono: Carolina Bezerra, projeto
Linear `Guia de um Builder`, P-PRO-16 — ainda sem página própria na wiki, ver
[[Guia de um Builder]]).

Fonte: repositórios `livemode-org/guia-de-um-builder` e
`livemode-org/sentinelamode` (lidos via GitHub em 2026-10-06 — não há cópia
em `raw/`).

## O que é — duas faces, um nome

| | Plugin `/sentinela:sentinela` | Portal `sentinela.livemode.space` |
|---|---|---|
| Onde roda | na máquina de quem construiu, na pasta do projeto | web |
| O que faz | **mede** — lê o código-fonte e testa o ambiente publicado | **guarda e mostra** — relatório, PDF, inventário |
| Quando | 1ª passada no início (mesmo com o projeto vazio); 2ª passada antes de o link circular | sempre, depois de existir relatório |

Não são dois produtos — são dois momentos do mesmo ciclo (`homologacao.md`
§12.5 do repo acima).

## Correção à minha própria nota anterior (2026-10-06)

O artefato `liveauth-vs-sentinela.html` (raiz desta wiki, 2026-09-22) descrevia
o Sentinela como uma checagem **"rodada pelo time de TI... depois do
deploy"**. Isso não é mais como o contrato de homologação descreve (v2.9.0,
divulgado como obrigatório na empresa): **quem roda é a própria pessoa que
construiu o projeto**, não um time auditando depois — e são **duas
passadas**, não uma, a primeira já valendo com o projeto vazio. O resto do
comparativo daquele artefato segue valendo (LiveAuth autentica o usuário
final; Sentinela audita se o projeto está seguro antes de ir ao ar).

## O que mede

Calcula a **TAG** (`baixa` · `media` · `alta`) a partir de três condições —
continuidade (C1), confidencialidade (C2, incluindo dados sensíveis via
C2-P), cross-área (C3) — e é quem **emite o valor oficial**: uma TAG
calculada pela skill `config-projeto` ou pelo teste público (`teste.html`)
sem passar pelo Sentinela é autodeclarada, não o registro da empresa.

Cobre tecnicamente: segredo salvo no projeto/histórico, dado sensível
exposto, proteção de acesso (trava de domínio) ativa antes do link circular.
**É o inventário** da empresa (item H12 do contrato) — quando passa, oferece
o endereço oficial `.livemode.space`.

## Status operacional (via Linear, 2026-10-05)

66 sites checados (03/07–30/09); 8 em grau C com achado grave aberto, ainda
não roteados para correção (ver
[PRO-876](https://linear.app/projetos-livemode/issue/PRO-876)); 63 fora do
catálogo da Vitrine, em revisão um a um (ver
[PRO-857](https://linear.app/projetos-livemode/issue/PRO-857)).

## Relação com outros sistemas

- [[LiveAuth]] — produtos diferentes do mesmo ciclo de vida de produto:
  LiveAuth autentica o usuário final; Sentinela audita se o projeto (LiveAuth
  incluso) está seguro antes de ir ao ar. Comparativo completo em
  `liveauth-vs-sentinela.html` (raiz da wiki).
- [[Gustavo Cruz]] se comprometeu a rodar o Sentinela no Hub de Automações
  Fiscais como mitigação de força bruta/rate limiting (ver
  [[2026-09-30 LiveAuth - Discovery com Gustavo Cruz (Hub Fiscal)]]) — não
  confirmado se já rodou.

## Open questions

- A página do programa-mãe (`Guia de um Builder`) ainda não existe nesta
  wiki — este é o primeiro fio dele a virar página própria.
- Se o Hub de Automações Fiscais do Gustavo já passou pela checagem.
