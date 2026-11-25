# F3-T06 — SYNC-IDA-VOLTA (volta) · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.236/2cb5325 · **Fábrica:** v0.0.49/4a00754

## O que foi construído

A volta é o espelho da ida, com a autoridade invertida: o Funil manda, a Fábrica recebe.

**Funil (saída):** `migration 0050_sync_outbox` + `hooks/sync_outbox_funil.js`.

| Rota | Para quê |
|---|---|
| `POST /backend/v1/sync/enfileirar` | envelope cru, para qualquer tipo que o Funil emita |
| `POST /backend/v1/sync/enfileirar-comercial` | **atalho de uso real**: monta os fatos a partir do LEAD |
| `POST /backend/v1/sync/despachar` | envia o pendente, retry 1/2/4/8/16 min |
| `GET /backend/v1/sync/outbox-saude` | fila por estado, último envio, travados |

A rota `enfileirar-comercial` é o que faz a volta servir para alguma coisa: o operador marca a primeira abordagem ou o pedido de saída, e o fato entra na fila sem ninguém montar envelope na mão. Ela lê o lead, resolve a identidade global da pessoa, o contexto comercial e a revisão do domínio, e enfileira `estado_comercial` e/ou `protecao_global`.

**Fábrica (entrada):** `sync_receptor.js` v2 — aplica em `comercial_projetado` como projeção somente leitura.

## Prova — 9 de 9

| Caso | Resultado |
|---|---|
| A · primeira abordagem | `aplicar`, projeção criada |
| B · mesmo evento e hash | `duplicado` |
| C · revisão 2 | `aplicar`, mesma projeção |
| D · proteção bloqueia | `aplicar`, `pedido_saida` |
| E · revisão velha | recusada (não desbloqueia) |
| F · motivo fora do select | `aplicar`, sem derrubar |
| G · Funil emitindo cadastro | `rejeitado` por autoridade |
| H · origem `fabrica` no receptor da Fábrica | `403` |
| I · outbox → despachar → enviado | `enfileirado` + `enviado` |

**Conferência do estado final na Fábrica:** `primeira_abordagem_em = 2026-11-20 10:00:00` — a data ORIGINAL, não a da revisão 2 (que trazia 01/12). `bloqueio_global: true`, `motivo: pedido_saida`, `comercial_revisao: 5`. A regra "primeira quer dizer primeira" sobreviveu ao transporte.

Depois da prova: outbox do Funil zerado e `em_dia: true`; 7 recibos e 1 projeção de prova apagados na Fábrica. Rotas de limpeza em **410** nos dois, conferido; despachar e receptor vivos (401 sem token).

## A regra que mais importa aqui

**Ausência de evento nunca desbloqueia.** A proseção nasce com `bloqueio_global: false`, mas quem já está bloqueado só sai do bloqueio com um evento de proteção de revisão MAIOR dizendo `bloqueio_global: false`. Um lado que voltou vazio não libera ninguém. É a defesa contra o acidente mais caro deste tipo de sistema: perder a sincronização e voltar a abordar quem pediu para sair.

## Dois cuidados que o código já embute

**Primeira abordagem não se move.** Se a projeção já tem data, ela fica, mesmo com revisão maior. Reprocessar não reescreve o passado — a data do primeiro contato é um fato, não um estado.

**Motivo do bloqueio é `select`.** `relacionamento` ou `pedido_saida`; qualquer outro valor derrubaria o save com 500. O código valida antes de gravar e cai no caso "bloqueado sem motivo classificado" em vez de falhar. Testei com `motivo: outro_motivo` justamente para provar isso.

## Sobre a fase 2 no banco da Fábrica

A saúde da Fábrica mostra 1 pendente e 4 conflitos. **Não são desta task**: são de 29/10, provas `G-F13-*` da fase 2, criadas antes desta rodada. Deixei como estão — apagar histórico de prova de outra fase sem decisão seria pior que conviver com ele. O `em_dia: false` reflete esses registros antigos, não a sincronização de agora.

## O que ficou para a F3-T02

Os hooks e a migration estão aplicados e provados no Skip. A publicação nos repositórios dos apps entra no lote da **F3-T02**, junto com o restante do código da Fábrica — mesma situação da F3-T05, não é pendência nova.

## A decisão de escopo segue aberta

O catálogo e as notas continuam sem fonte na Fábrica (achado da F3-T05, em `pendencias-fase-3.md`). A volta não toca nisso: comercial, proteção e primeira abordagem **são** do Funil, e agora chegam à Fábrica por HTTP.
