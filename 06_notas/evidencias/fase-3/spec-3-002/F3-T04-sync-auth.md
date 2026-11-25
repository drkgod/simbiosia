# F3-T04 — SYNC-AUTH (CA-3-002-01)

**Data:** 25/11/2026 · **Funil:** v0.0.225/a0ccabc · **Fábrica:** v0.0.35/530b704

## O que foi construído

Um receptor autenticado em **cada** backend (`POST /backend/v1/sync/receber`), com token de serviço no segredo `SYNC_TOKEN_INTERAPP` do Skip — o mesmo valor nos dois projetos. Mais `GET /backend/v1/sync/saude` (só admin) em cada lado.

Cada receptor aceita **só a origem do outro**: o do Funil aceita `origem=fabrica`, o da Fábrica aceita `origem=funil`. Chamada cruzada recebe 403 com motivo `autoridade`.

## Resultado no Funil — 5 de 5 caminhos

| Caminho | Resposta |
|---|---|
| sem token | `401` · `token de serviço ausente` (`sem_token`) |
| token errado | `401` · `token de serviço inválido` (`token_invalido`) |
| origem errada (funil no receptor do Funil) | `403` · `origem não autorizada neste receptor` (`autoridade`) |
| token correto | `200` · `acao: recebido` + `recibo_id` gravado |
| repetido (mesmo hash) | `200` · `acao: duplicado`, mesmo recibo, nada alterado |
| mesmo evento_id com hash diferente | `409` · `acao: conflito`, `hash_anterior`, sem sobrescrever |

**CA-3-002-01 atendido no lado do Funil.**

## A Fábrica não respondeu — pendência

As três chamadas ao receptor da Fábrica devolveram **`Not Found`**, inclusive com token válido. O apply foi verde nos dois (setup, staticAnalysis, build, integrations e test todos ok). Três hipóteses, na ordem em que eu investigaria:

1. **rota não registrada até um publish** — é o padrão conhecido do Skip: hook novo pode não subir até um `skip_project_publish`, e a versão publicada pode ser anterior ao apply;
2. **URL errada** — a que eu usei é a do backend interno; vale conferir com `skip_cloud_get_backend_url` do projeto 50248;
3. **falha silenciosa de deploy** — o apply pode reportar sucesso com o hook fora do ar no processo que atende.

O que falta, exatamente: rodar `skip_project_publish` no 50248 e repetir as três chamadas. Se continuar `Not Found`, conferir a URL e a lista de hooks registrados.

**Por isso a F3-T04 fica `[/]`, não `[x]`:** o critério CA-3-002-01 pede recusa nos dois sentidos, e só um lado está provado.

## Detalhes de implementação que valem registro

- **Comparação em tempo constante** no token (`length` + XOR por caractere), não `===`. Errado por diferença de tempo é o suficiente para descobrir token por tentativa.
- **Falha fechada sem segredo**: sem `SYNC_TOKEN_INTERAPP` o canal devolve `503` em vez de abrir sem proteção.
- **`X-Sync-Token` não chegou** em nenhum teste pelo proxy interno; `Authorization: Bearer` funcionou. O código aceita os dois, mas na prática só o Bearer passou. Registrado para quem for integrar de verdade.
- **`estado` do recibo**: o select de `sync_inbox` aceita `aplicado, duplicado, atrasado, pendente, conflito, rejeitado` — **não** `recebido`. A primeira versão usou `recebido` e deu `Invalid value`; corrigida para `pendente`, que significa "recibo gravado, projeção ainda não aplicada" e é o que a F3-T05 vai consumir.

## Segurança

O token **não** está no Git, em tela, em log ou nesta evidência. Vive só nos segredos do Skip dos dois projetos. Rotação: trocar o segredo nos dois projetos e reaplicar.
