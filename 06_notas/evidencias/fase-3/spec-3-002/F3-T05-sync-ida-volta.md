# F3-T05 — SYNC-IDA-VOLTA (ida) · CONCLUÍDA

**Data:** 25/11/2026 · **Funil:** v0.0.233/0c11909 · **Fábrica:** v0.0.46/ea3be7b

## O que foi construído

**Fábrica (lado de saída):** `migration 0011_sync_outbox` + `hooks/sync_outbox_fabrica.js`.

Três rotas, separadas de propósito:

| Rota | Para quê |
|---|---|
| `POST /backend/v1/sync/enfileirar` | monta o envelope v1 e grava no outbox |
| `POST /backend/v1/sync/despachar` | envia o que está pendente, com retry |
| `GET /backend/v1/sync/outbox-saude` | fila por estado, último envio, travados |

Gravar e enviar são passos distintos porque têm riscos distintos: o enfileirar tem de ser rápido e nunca perder; o despachar pode falhar e ser repetido. Até 5 tentativas com backoff de 1, 2, 4, 8 e 16 minutos; depois `esgotado` e o admin vê em `travados`. Recusa definitiva (400/403/409/422) não gasta tentativa: repetir não resolveria.

**Funil (lado de entrada):** `hooks/sync_receptor.js` v2 — aplica a projeção, não só grava recibo.

Cadastro (empresa/pessoa), catálogo (produto) e nota (pontuação) entram como projeção **somente leitura**, por `sync_global_id`. Produto projetado fica `ativo: false`: catálogo é autoridade da Fábrica, e vender é decisão comercial do Funil. Nota é snapshot literal, nunca recalculada, e `pontuacoes` é append-only.

## Prova — 10 de 10

| Caso | Resultado |
|---|---|
| A · cadastro de empresa nova | `aplicar`, projeção criada |
| B · mesmo evento e mesmo hash | `duplicado`, nada muda |
| C · evento já aplicado com payload novo | `conflito`, não sobrescreve |
| D · revisão que não avança | recusado (não reativa projeção) |
| E · cadastro de pessoa | `aplicar`, projeção criada |
| F · catálogo aprovado | `aplicar`, produto projetado |
| G · nota sem referência projetada | `pendente`, com motivo |
| H · hash divergente do payload | `rejeitado` |
| I · origem trocada no envelope | `403` autoridade |
| J · outbox → despachar → enviado | `enfileirado` + `enviado` |

Depois da prova: Funil com `sync_inbox` zerado (`em_dia: true`); Fábrica com outbox zerado (`em_dia: true`). Rotas de limpeza neutralizadas em **410** nos dois backends, conferido; receptor e despachar seguem vivos (401 sem token).

## O que me custou tempo — três defeitos reais

### 1. `JSON.stringify` num campo `json` do Skip vira array de bytes

Gravei `reg.set('payload', JSON.stringify(payload))` e o campo voltou como `[123,34,99,...]` — um `[]uint8`. O receptor recebia aquilo como `payload` e recusava com "payload ausente ou não é objeto". Cinco despachos perdidos antes de eu inspecionar o campo com uma rota temporária.

**Regra:** campo `json` do Skip recebe o **objeto**, nunca a string. O runtime serializa sozinho. Se a leitura encontrar array de bytes, é sinal de gravação errada, não de corrupção.

### 2. Recibo `rejeitado` virava `duplicado` no reenvio

Meu primeiro evento morreu rejeitado por causa do defeito acima. Quando corrigi a gravação e reenviei, o receptor respondeu `duplicado` — porque comparava só o hash, e o hash batia. O evento estava preso para sempre num recibo **sem efeito nenhum**. `rejeitado` não é fato aplicado; o reenvio tem de poder entrar. Corrigido: `hashAnterior === hash && estadoAnterior !== 'rejeitado'`.

### 3. Empresa já existente no Funil quebrava a projeção

A prova de cadastro falhou com `GoError: ambiente: Value must be unique; cnpj_norm: Value must be unique` — 500. O índice único em `(ambiente, cnpj_norm)` barrava a segunda empresa com o mesmo CNPJ: havia uma empresa da prova anterior já no banco. Corrigido adotando o registro existente pelo CNPJ e escrevendo o `sync_global_id` nele, em vez de tentar criar outro. Adotar identidade é melhor que duplicar, e é o que a F3-T07 vai precisar de qualquer forma.

## Evento determinístico

`evento_id` = `fabrica:<ambiente>:evento:<hash(tipo|entidade|revisão)>:<tipo>:<entidade>:r<revisão>`.

Determinístico de propósito: reenfileirar o mesmo fato com a mesma revisão e o mesmo payload produz o mesmo `evento_id`, e o receptor responde `duplicado`. É isso que torna o retry seguro. A entidade entra na semente porque, sem ela, duas empresas na mesma revisão colidiam — peguei isso antes de aplicar.

## Segurança

`sync_outbox`: `createRule: null` e `deleteRule: null` — só hook interno escreve, ninguém apaga histórico pela API. Admin lê e pode reprocessar. O token continua só no segredo `SYNC_TOKEN_INTERAPP`, comparado em tempo constante. Nenhum payload vai para log: só `evento_id` e tipo.

## O que ficou para a F3-T02

Os dois hooks e a migração estão aplicados e provados no Skip. A publicação deles nos repositórios dos apps entra junto com o restante do código da Fábrica na **F3-T02**, que está aberta exatamente por isso (app sem commit desde 22/10). Não é pendência nova: é o mesmo lote.

## Achado para a F3-T06

A Fábrica **não tem** coleção de produtos nem de pontuações — só `empresas`, `pessoas` (via cadastro), `listas`, `regras_icp` e `comercial_projetado`. Ou seja: `catalogo_aprovado` e `pontuacao_snapshot` hoje só podem partir de dados que o Funil já tem. A ida de catálogo e nota depende de a Fábrica ganhar essas fontes, e isso é decisão de escopo, não de código. Registrado em `06_notas/pendencias-fase-3.md`.
