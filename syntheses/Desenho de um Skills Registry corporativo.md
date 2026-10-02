---
type: synthesis
status: active
updated: 2026-10-02
date: 2026-10-02
aliases: [skills registry, registro de skills]
tags: [skills, agent-skills, mcp, agent-flow, governance]
---

# Desenho de um Skills Registry corporativo

## A pergunta

Como resolver, na Livemode, os três problemas reais de skill compartilhada
entre o time: compartilhamento manual e defasado, manutenção que não
propaga (fix local não chega aos outros), e controle de qualidade
inexistente (caso concreto: a skill `pm-linear` não funciona direito)? O
ponto de partida foi um esboço de msilva (2026-10-02, desenhado à mão,
não salvo em `raw/`) com um "Skills registry" central, servidor MCP/API, e
usuários — cobrindo autoria, compartilhamento, auditoria, review, evals,
governança, analytics, busca vetorial/semântica.

## Como chegou aqui

Validação dos três problemas com msilva: compartilhamento hoje é zip
manual (defasagem, falta de praticidade, descoberta fraca — "não sabia que
existia skill pra esse fluxo"); manutenção não propaga pros usuários locais;
qualidade já falhou de verdade (`pm-linear`); escala é "todos" — não é
problema de poucas pessoas.

**Alternativa nativa descartada em parte**: o plugin marketplace do Claude
Code (`marketplace.json`, `/plugin install`, auto-update) resolve
distribuição e versionamento de graça, mas é mecanismo específico do Claude
Code — não existe equivalente em Cursor, Copilot, Gemini CLI etc. Como a
Livemode precisa ser **harness-agnostic**, isso derruba o marketplace como
solução única (docs: code.claude.com/docs/en/plugins/*).

**Formato do pacote de skill**: resolvido adotando a spec aberta
[agentskills.io/specification](https://agentskills.io/specification) —
`SKILL.md` com frontmatter (`name`, `description`, `license`,
`compatibility`, `metadata`, `allowed-tools`), carregamento progressivo
(metadata sempre carregada, corpo só na ativação). Confirmado como
cross-harness de verdade, não aposta: `agentskills.io/clients.md` lista
suporte nativo em ~45 produtos, incluindo Cursor, GitHub Copilot/VS Code,
Gemini CLI, Codex/ChatGPT, Goose, OpenHands, Roo Code. A spec **não define**
registry/distribuição — deixa esse espaço aberto de propósito, o que
confirma que a ideia do diagrama (registry + MCP) não compete com nada
existente.

**O problema de trigger**: dado um registry central exposto via MCP
(`search_skills`/`load_skill`), nada faz o modelo decidir chamar
`search_skills` espontaneamente — ele só age sobre o que já está no
contexto. Mecanismo real usado hoje (e observável nesta própria sessão):
metadata (nome+descrição) de toda skill é injetada passivamente no contexto
no início da sessão; só a ativação completa é uma tool call. Alternativas
mapeadas, por quanto dependem do modelo "lembrar sozinho": instrução fixa
em arquivo estático (`AGENTS.md`/`CLAUDE.md`), uma MCP tool por skill (sem
catálogo, mas escala mal), MCP "prompts" como slash command manual, híbrido
sync-local + registry pra cauda longa, recomendação fora do loop do agente
(bot no Linear/Slack), hook automático, e convenção cultural.

**Hook escolhido como solução**: roda antes do prompt chegar ao modelo,
fora da decisão dele — elimina o problema de trigger de vez. Risco
levantado por msilva: vendor lock-in. Investigado: 11 de 14 harnesses
pesquisados têm mecanismo equivalente (`SessionStart`/`UserPromptSubmit` ou
nome próximo), convergência de nomenclatura forte o bastante pra sugerir
imitação direta do Claude Code. **Não existe padrão cross-vendor para
hooks** — o Agent Plugins Spec (agent-plugins.org, TSC com Amazon/Cursor/
Microsoft/OpenAI/Vercel, sucessor do AGENTS.md/SKILL.md) exclui hooks
explicitamente do contrato portável, por serem "client-específicos demais".
Lock-in é real, mas administrável: lógica central de busca fica igual,
muda só um adapter fino por harness (schema JSON via stdin/stdout).

**Escopo final, decidido por msilva**: limitar a 4 harnesses — Claude Code,
Cursor, Codex/GPT, Gemini CLI. Todos os quatro confirmados na camada mais
forte da pesquisa (hook injeta contexto automaticamente, sem decisão do
modelo), sem gaps de documentação a cobrir — ao contrário de Windsurf/Goose/
OpenCode, que ficaram como "parcial, não confirmado".

## Arquitetura resultante

- Formato de pacote: `SKILL.md` per agentskills.io spec.
- Fonte de verdade: registry central (git ou DB), servido via MCP —
  `search_skills` (busca semântica/vetorial) + `load_skill` (corpo
  completo), com log de uso por chamada (resolve auditoria/analytics de
  graça).
- Trigger: hook nativo por harness (`SessionStart`/`UserPromptSubmit` ou
  equivalente), um adapter fino por um dos 4 harnesses escopados, chamando
  a mesma lógica central de busca.
- Gate de qualidade: ainda em aberto — `claude plugin eval` é early-access
  e dev-time, não serve como gate automático; `skills-ref validate` (lib
  de referência da spec) cobre só validação estrutural do frontmatter, não
  "a skill funciona certo".

## O que continua em aberto

- Schema exato do hook em cada um dos 4 harnesses (nome do evento, formato
  de `additionalContext`) — pesquisa inicial via resumo de doc, não leitura
  verbatim; precisa confirmação campo-a-campo antes de implementar.
- Gate de qualidade real pro caso `pm-linear` — nenhuma opção mapeada até
  agora resolve isso automaticamente.
- Governança/escopo por time — ainda não desenhado como o registry decide
  o que cada time vê.
- Não foi levado a Luís, Gabrielle, ou qualquer outra pessoa do time —
  raciocínio só entre msilva e Claude até aqui.

## Relacionado

- [[Packaging as skills]]
- [[A6 Curador deve padronizar e sinalizar defasagem de skills]]
- [[Agent Flow]]
