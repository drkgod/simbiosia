# Prova do MCP — F3-T02 · MCP-PROVA

**Data:** 25/11/2026 · **Funil:** Skip 50182 · **Autor do MCP:** consultor da ADAPTA

## O que foi provado

Prova simples, como pedido: **criar uma chave, chamar `tools/list` e uma ferramenta de leitura**.
Executada contra o backend do Skip, não em teste isolado.

## Resultado

| Passo | Chamada | Resultado |
|---|---|---|
| 1 | `POST /api/collections/users/auth-with-password` (sessão de admin) | token obtido |
| 2 | `POST /backend/v1/keys` `{nome, escopo:"leitura"}` | **chave criada**, prefixo `sk_live_9907...`, aviso "não será exibida novamente", `ativa:true` |
| 3 | `POST /backend/v1/mcp` `tools/list` | **14 ferramentas** listadas |
| 4 | `POST /backend/v1/mcp` `tools/call` `leads_listar` | `{"total":0,"leads":[]}` — `isError:false` |
| 5 | `DELETE /backend/v1/keys/{id}` | **chave revogada**, `ativa:false` — "chamadas MCP com ela passam a devolver 401" |

## As 14 ferramentas de leitura que o escopo `leitura` expõe

`leads_listar` · `leads_obter` · `leads_contagem_etapas` · `listas_listar` · `contatos_listar` ·
`cadencias_listar` · `qualificacoes_listar` · `produtos_listar` · `produtos_obter` ·
`pontuacoes_consultar` · `contextos_consultar` · `metas_consultar` · `sistema_config_consultar` ·
`resumo_funil`

Com `leitura_escrita` somam-se 9 de escrita (`leads_criar`, `leads_atualizar`, `leads_mover_etapa`,
`listas_criar`, `listas_adicionar_membro`, `contatos_registrar`, `cadencias_criar_toque`,
`cadencias_atualizar_status`, `qualificacoes_registrar`) — **23 no total**. `admin` libera tudo,
incluindo `sistema_config`.

## O que a prova confirma e o que não confirma

**Confirma:** o endpoint responde JSON-RPC 2.0; a chave autentica por hash; o escopo `leitura`
lista e executa ferramenta de leitura; a revogação corta o acesso; o `ultimo_uso_em` foi atualizado
no registro da chave.

**Não confirma:** cada uma das 23 ferramentas individualmente. Só `leads_listar` foi chamada. Uma
tool que falhe por contrato errado não aparece nesta prova — aparece no primeiro uso real.

## Segurança observada

- A chave em claro **só existe na resposta da criação**; o registro guarda `key_hash` (SHA-256) e
  `prefixo`. Confirmado na listagem: `{"prefixo":"sk_live_9907..."}` sem hash nem chave.
- `createRule/updateRule/deleteRule` da coleção `api_keys` são `null` — só as rotas escrevem.
- A prova criou uma chave de nome `prova-f3-t02` e a revogou no fim. Nenhuma chave ficou ativa por
  causa desta prova.

## Como repetir

```bash
B="https://<funil>.internal.goskip.dev"
TOK=$(curl -s -X POST "$B/api/collections/users/auth-with-password" \
  -H 'Content-Type: application/json' \
  -d '{"identity":"<email>","password":"<senha>"}' | jq -r .token)
KEY=$(curl -s -X POST "$B/backend/v1/keys" -H "Authorization: Bearer $TOK" \
  -H 'Content-Type: application/json' \
  -d '{"nome":"prova","escopo":"leitura"}' | jq -r .chave)
curl -s -X POST "$B/backend/v1/mcp" -H "Authorization: Bearer $KEY" \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

Credencial e token não são registrados nesta evidência — apenas o comando com placeholders.
