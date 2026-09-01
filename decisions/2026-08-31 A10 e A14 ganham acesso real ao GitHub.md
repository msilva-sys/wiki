---
type: decision
status: active
updated: 2026-09-01
date: 2026-08-31
aliases: [github access a10 a14, repo_tools, acesso ao repositório]
tags: [agents, agent-flow, a10, a14, langgraph, github, linear]
---

# A10 e A14 ganham acesso real ao GitHub

Testando o A14 no chat, o agente respondeu **"não tenho acesso ao
repositório"** — literalmente verdade: o único sinal de GitHub que existia
até então era achar um PR que citasse o identifier de uma issue
(`github_client.list_prs_referencing`), útil só como "sinal de código"
ligado a uma issue específica, sem nenhuma navegação real do repo.

## Decisão

Ambos os agentes ganham acesso real ao GitHub, cobrindo quatro
capacidades: navegar arquivos/código, histórico de commits, status de
PRs/CI, e métricas agregadas do repo. Tracked em
[PRO-488](https://linear.app/projetos-livemode/issue/PRO-488).

## Como o repo é resolvido

Sem dict mantido à mão por projeto. `linear_client.get_project_repos`
lê o campo nativo `Project.externalLinks` do Linear (introspecção
confirmou o schema) e resolve `"org/repo"` completo por regex sobre a
URL — **o link cadastrado no Linear é fonte de verdade, org incluída**,
não só o nome do repo contra um `GITHUB_ORG` fixo. Qualquer projeto que
tenha (ou venha a ter) um link do GitHub nos seus recursos do Linear
funciona automaticamente, sem tocar em código. Link quebrado ou ausente
não levanta exceção — os tools devolvem `{"error": ...}` e o LLM repassa
a limitação real.

## Decisões técnicas menores

- **Sem Checks API.** A permissão "Checks" não existe pra fine-grained
  PAT nesse contexto (confirmado tentando cadastrar) — status de CI vem
  só da Commit Status API legada (`/commits/{sha}/status`), que é o que a
  maioria dos CIs (GitHub Actions incluído) também popula.
- **`ref` como escape hatch.** `main` está quase vazio nesse repo — todo
  trabalho real fica em `langgraph`, não mergeado. `list_repo_contents`,
  `get_file_content`, `list_commits` e `get_repo_metrics` aceitam um
  `ref` opcional em vez de assumir a branch padrão.
- **`list_branches` faltou na primeira passada** — só apareceu testando
  de verdade: um trace real do Langfuse (puxado direto via API pública do
  Langfuse, sem tool dedicado) mostrou o modelo chamando
  `list_project_repos` corretamente e então dizendo que não tinha tool
  pra listar branches. Corrigido no mesmo dia, commit separado
  (`e376031`) em vez de amend.

## Correção 2026-09-01 — wiring de repo extraída do domínio do agente

Preocupação levantada revendo o código: `a10/tools.py` e `a14/tools.py`
misturavam tools de domínio genuíno (`calculate_assignee_workload`,
`calculate_progress`) com a wiring de acesso a repo — formatação de
resposta do `github_client` quase idêntica nos dois arquivos, e
tratamento de "sem repo" (`_no_repo`) repetido. Extraído pra
`repo_tools.py` (raiz do repo, ao lado de `github_client.py` e
`linear_client.py`): funções puras que recebem `repo` já resolvido e
devolvem o dict formatado. Cada `tools.py` continua responsável só pela
parte que difere de verdade entre os dois agentes — **como** o repo é
resolvido (A10: por issue, via `issue.project_id`; A14: uma vez por
projeto, no fechamento de `create_tools`) — e delega o resto pro módulo
compartilhado. Commit `4968e20`.

## Trade-off aceito

Ambiguidade de múltiplos repos por projeto não tem tratamento especial —
sempre usa `repos[0]`. Aceitável hoje dado o mapeamento 1:1 real; se
algum projeto ganhar mais de um repo linkado, os tools já aceitam um
`repo` explícito como override, mas nenhum agente hoje decide sozinho
qual usar.

## Ligação com o resto do projeto

Instância do design geral do A10/A14 fechado em
[[Desenho do agente LangGraph para A10+A14]] (toolset determinístico pros
critérios computáveis). Ver [[Agent Flow]] pro contexto do projeto.
