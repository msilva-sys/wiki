---
type: synthesis
status: draft
updated: 2026-10-23
date: 2026-10-23
tags: [liveauth, auth, automation, governance]
---

# Como LiveAuth pode autenticar sistemas e automações

Proposta de evolução levantada na query de msilva em 2026-10-23. Não é
capacidade implementada nem decisão de ampliar o escopo.

## Ponto de partida documentado

[[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]]
centraliza login humano e autorização por grupo, e exclui explicitamente
auth app-to-app. A API do [[Airtable Proxy]] usa chave por app; integrar
seu `/dashboard` ao LiveAuth não altera essa autenticação de máquina.
[[LiveAuth]] registra produção concluída, mas nenhum consumidor real,
na checagem de 2026-10-07; isso não foi reconferido nesta query.

[[2026-10-07 Daniel - Matheus (Inventário de Processos e Governança de Auth)]]
registra fragmentação de auth e incidentes de automações/chaves esquecidas.
A união com [[Inventário de Processos e Ferramentas]] segue sem desenho
acordado. A proposta abaixo é uma possibilidade de conexão entre as frentes.

## Proposta

Dar identidade própria a cada serviço ou automação, por ambiente, com dono,
finalidade, recursos permitidos e ações autorizadas. Uma identidade por
fluxo só oferece isolamento se o executor também isolar suas credenciais;
uma única credencial compartilhada por todo o n8n identifica apenas o n8n.

Exemplo hipotético: `n8n-fiscal-prod` pode ler os dados fiscais permitidos
via proxy; não pode escrever nem acessar bases de outro processo.

O executor prova sua identidade ao emissor; recebe access token curto,
limitado à API de destino (`aud`) e às permissões aprovadas. A API valida
assinatura, emissor, destinatário, validade e permissão necessária antes de
executar cada operação. O token de handoff de login humano não serve para
esse fluxo. O LiveAuth poderia manter cadastro e política sobre um
provedor de identidade existente, sem implementar um servidor OAuth próprio.

OAuth 2.0 Client Credentials atende clientes confidenciais que operam em
nome próprio ([RFC 6749, seção 4.4](https://www.rfc-editor.org/rfc/rfc6749.html#section-4.4)).
Onde disponível, identidade da plataforma/federação evita uma chave estática;
o GCP documenta federação e credenciais curtas em
[Identities for workloads](https://docs.cloud.google.com/iam/docs/workload-identities).
A aplicabilidade aos executores da Livemode precisa ser verificada.

Separar execução autônoma de execução em nome de pessoa: a primeira usa
permissões da automação; a segunda precisa de delegação explícita,
preservando identidade da pessoa e limites da delegação. Client Credentials
não representa autorização de um usuário.

## Limites e governança

- APIs internas precisam adotar a validação e aplicar as permissões.
  Só cadastrar uma automação no LiveAuth não protege seu destino.
- APIs externas precisam aceitar o emissor, ou continuar usando credenciais
  próprias do fornecedor, guardadas em gerenciador de segredos. Um proxy
  pode aplicá-las sem entregá-las ao fluxo, se houver motivo para isso.
- Bloquear a identidade impede novas emissões; tokens já emitidos podem
  valer até expirar. Revogação imediata exige consulta de estado ou outro
  mecanismo explícito de invalidação no destino.
- Auditoria deve atribuir chamadas à automação, ambiente, destino e ação,
  com resultado e correlação da execução, sem registrar tokens.
- Limites de custo, cotas, alertas e desligamento operacional complementam
  auth; um bot autorizado ainda pode executar trabalho indevido ou caro.

## Primeiro recorte a validar

Escolher uma automação real e uma API interna, com permissão inicialmente
de leitura. Provar identidade isolada, acesso permitido, negação de escrita
e de acesso a outro recurso, atribuição em logs e prazo efetivo de
revogação. Para o proxy, manter o escopo de bases do PAT já existente;
novos scopes internos exigiriam enforcement adicional e decisão separada.
Só depois decidir se o LiveAuth deve assumir esse papel para outros fluxos.

## Relacionado

- [[LiveAuth]]
- [[Inventário de Processos e Ferramentas]]
- [[Airtable Proxy]]
