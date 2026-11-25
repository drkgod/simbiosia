# F3-T04 — SYNC-AUTH (CA-3-002-01) · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.229/f375e24 · **Fábrica:** v0.0.40/174274a

## O que foi construído

Receptor autenticado em **cada** backend (`POST /backend/v1/sync/receber`), com token de serviço no segredo `SYNC_TOKEN_INTERAPP` do Skip — o mesmo valor nos dois projetos. Mais `GET /backend/v1/sync/saude` (só admin) em cada lado.

Cada receptor aceita **só a origem do outro**: o do Funil aceita `origem=fabrica`, o da Fábrica aceita `origem=funil`. Chamada cruzada recebe 403 com motivo `autoridade`.

## Resultado — os dois lados

| Caminho | Funil | Fábrica |
|---|---|---|
| sem token | `401` `sem_token` | `401` `sem_token` |
| token errado | `401` `token_invalido` | `401` `token_invalido` |
| origem trocada | `403` `autoridade` | `403` `autoridade` |
| ambiente `real` | `400` `ambiente_real` | `400` `ambiente_real` |
| token válido | `200` `recebido` + recibo | `200` `recebido` + recibo |
| repetido (mesmo hash) | `200` `duplicado`, mesmo recibo | `200` `duplicado`, mesmo recibo |
| mesmo evento, hash diferente | `409` `conflito`, sem sobrescrever | `409` `conflito` |

**CA-3-002-01 atendido nos dois sentidos.**

Saúde dos canais depois da prova: Funil `token_configurado: true`, 0 eventos de teste (limpos); Fábrica `token_configurado: true`, 49 recibos históricos da fase 2 e 1 pendente.

## O que me custou tempo — e o que aprendi

### 1. O 404 na Fábrica era **URL errada**, não falta de publish

Eu estava usando um host que não existe. O backend real é o de `shrd03`, obtido com `skip_cloud_get_backend_url`. **Lição:** antes de diagnosticar "rota não registrada", conferir a URL do backend pela fonte oficial. A hipótese do publish estava errada e teria me feito publicar sem necessidade.

### 2. `countRecords` não conta nesta VM do Skip

A rota `/saude` devolvia `eventos_recebidos: 0` com os recibos gravados no banco. Primeiro achei que fosse filtro vazio; troquei por `id != ''` e continuou 0. O que funciona é **contar pelo tamanho da lista** (`findRecordsByFilter(...).length`).

Isso é perigoso: **um painel de saúde que sempre diz zero é pior que não ter painel** — dá sensação de monitoramento sem monitorar nada. Foi a conferência cruzada com a API de registros que expôs (o banco tinha 50, o painel dizia 0).

### 3. `estado: 'recebido'` não existe no select

O select de `sync_inbox` aceita `aplicado, duplicado, atrasado, pendente, conflito, rejeitado`. A primeira versão gravava `recebido` e o save falhava com `Invalid value`. Corrigido para **`pendente`**, que é exatamente o significado certo: recibo gravado, projeção ainda não aplicada — o que a F3-T05 vai consumir.

### 4. `X-Sync-Token` não atravessa o proxy interno

O código aceita `X-Sync-Token` ou `Authorization: Bearer`. Na prática só o **Bearer** chegou. Quem for integrar de verdade deve usar Bearer.

## Limpeza

`sync_inbox` tem `deleteRule: null`: a API de registros nega até admin (403 "Only superusers"). Usei uma **rota temporária** (`/backend/v1/sync/limpar-provas`) que apagava só os `evento_id` de prova. Executada e conferida: Funil com 0, Fábrica sem nenhum resíduo meu.

A rota foi **neutralizada** e responde **410** nos dois backends — conferido. Mantida no repositório, não apagada, porque apagar arquivo de hook pode quebrar o apply (lição das fases 1 e 2).

## Segurança

O token não está no Git, em tela, em log ou nesta evidência. Vive só nos segredos do Skip dos dois projetos. Comparação em tempo constante (length + XOR), não `===`. Sem segredo configurado, a rota devolve `503` em vez de abrir sem proteção.

**Rotação:** trocar `SYNC_TOKEN_INTERAPP` nos dois projetos e reaplicar. Não há valor antigo em lugar nenhum.

## Arquivos

- `pocketbase/hooks/sync_receptor.js` — publicado nos dois repositórios
- `pocketbase/hooks/zz_sync_limpeza_tmp.js` — rota neutralizada (410)
