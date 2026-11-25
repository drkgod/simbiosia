# Evidência — F2-T07: eventos e adaptadores locais de teste (SPEC-2-003)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Dependência:** F2-T01 ✅
**Critérios:** CA-2-003-01 (EVENTO-TESTE-AUTH) · CA-2-003-02 (EVENTO-IDEMPOTENTE) · CA-2-003-04 (SEM-EXTERNO-F2)

## Por que esta task antes da F2-T04

A F2-T04 depende de F2-T03 **e F2-T07**. F2-T07 depende só de F2-T01 (fechada). Construir F2-T07
primeiro destrava F2-T04, F2-T05, F2-T06 e F2-T08 de uma vez. F2-T04 fica registrada em espera.

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.135** | 30605a1 | publicada em 2026-10-28T16:10:20Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

QA do apply: setup ✔ · staticAnalysis ✔ · build ✔ · integrations ✔ · test (ran: false).

## Reuso em vez de recriação (degrau 2 da escada)

A SPEC manda **herdar** o contrato v1 da SPEC-1-007, não recriar. Verificado no código e **mantido**:

| Já existia | Cobertura |
|---|---|
| `sync_inbox`, `sync_mapeamentos`, `aliases_identidade` (migração 0028) | recibo, mapeamento de ator, alias |
| `validarEnvelope`, `hashEnvelope`, `jsonCanonico` | schema, campos proibidos, hash canônico |
| `decidirAplicacao` (duplicado/atrasado/pendente/conflito) | idempotência do contrato v1 |
| Tabela `AUTORIDADE` por tipo de evento | autoridade por origem |
| Portão de ambiente de teste + `teste_despachos` | SEM-EXTERNO da Fase 1 |

**Provado contra o Skip antes de mexer:** as três rotas de IA (`gerar-contato`, `sugerir-toque`,
`analisar-qualificacao`) respondem `interceptado: true`. Zero despacho externo.

## O que foi construído (as lacunas reais)

| Artefato | Papel |
|---|---|
| `migrations/0034_eventos_e_adaptadores.js` | coleções `eventos_teste` (receptor idempotente) e `adaptadores_teste` (auditoria das respostas sintéticas) |
| `hooks/eventos_receptor.js` | `POST /backend/v1/eventos/teste` — auth, payload fechado, hash, duplicado/conflito |
| `hooks/adaptadores_locais.js` | `POST /backend/v1/adaptadores/teste` — captação, enriquecimento e canal, todos sintéticos |
| **extensão no `sync_aplicar.js`** | **recusa `ambiente: 'real'`** — antes ele aceitava e mapeava para `producao` |

A última linha é a lacuna mais séria que encontrei: o adaptador da Fase 1 **aceitava modo real**,
contrariando o item 3 da SPEC-2-003. Corrigido com três linhas, sem tocar em autoridade nem hash.

## Resultado por critério

### CA-2-003-01 — EVENTO-TESTE-AUTH ✅
| Prova | Resultado |
|---|---|
| Evento válido aplica | ✅ `estado: aplicado` |
| Sem sessão | ✅ 401/403 |
| `modo: 'real'` | ✅ 400, nada gravado |
| `ambiente: 'real'` | ✅ 400, nada gravado |
| `owner` forjado no payload | ✅ 400 |
| `role` forjado no payload | ✅ 400 |
| Campo não previsto (payload aberto) | ✅ 400 |
| Referência inexistente | ✅ `estado: pendente`, sem efeito |

### CA-2-003-02 — EVENTO-IDEMPOTENTE ✅
| Prova | Resultado |
|---|---|
| Mesmo ID + mesmo hash, 2× | ✅ `duplicado`, mesmo `reciboId`, **1 registro no banco** |
| Mesmo ID + hash diferente | ✅ 409 `conflito`, informa `hashAnterior`, **não sobrescreve** |
| Rejeição de autoridade ao repetir | ✅ recusa não vira aplicação; depois válido aplica **uma vez** |

### CA-2-003-04 — SEM-EXTERNO-F2 ✅
| Prova | Resultado |
|---|---|
| Adaptador de canal | ✅ `modo: simulado`, `entregue: false` — nunca despacha |
| Adaptador com `modo: 'real'` | ✅ 400 |
| Captação/enriquecimento | ✅ fixtures determinísticas (mesma entrada → mesma saída) |
| Rotas finais de IA com pausa ativa | ✅ todas `interceptado: true`, **0 despachos** registrados |

```
node --test tests/eventos.test.mjs
ℹ tests 13
ℹ pass 13
ℹ fail 0
```

## Prova de que o runner não é decorativo

Assertion deliberadamente falsa (duplicado como aplicado):

```
assert.equal(r2.estado, 'aplicado', 'RED deliberado: deve falhar')
```

Resultado: **exit code 1**, `pass 0`, `fail 1`. Restaurado em seguida.

## Decisões tomadas dentro do que a SPEC deixou aberto

| Decisão | Por quê |
|---|---|
| Coleção `eventos_teste` separada de `sync_inbox` | `sync_inbox` é recibo de projeção entre apps; evento de canal é sintético de comunicação. Misturar esvaziaria o significado do recibo |
| Hash djb2 em vez de SHA | precisa ser determinístico, não criptográfico; o contrato v1 já tem o seu `hashEnvelope` |
| Papéis aceitos: admin, operador, vendedor | a SPEC pede "papel autorizado" sem listar; os três existem no sistema. Operador/dono nunca vem do payload |
| Respostas de adaptador literais no código | garante determinismo e prova que não há rede |
| `ocorrido_em` opcional | a SPEC o lista no evento; tornar obrigatório bloquearia evento simples de teste sem ganho |

## Riscos verificados

| Risco | Verificação |
|---|---|
| Reescrever contrato v1 e quebrar a Fase 1 | extensão de 3 linhas; autoridade, hash e decisão intactos |
| Migration com dados | aditiva: só cria duas coleções |
| Adaptador como porta para rede | nenhum `$http`, `$ai` ou SDK no arquivo |
| Idempotência frágil | índice único `(evento_id, ambiente)` **e** checagem de hash |
| Contador de despacho poluído | janela relativa ao início do teste (lição da F2-T01) |

## Pendência registrada

`tests/contrato.test.mjs` existe no clone desde a Fase 1 e **nunca rodou no runner**
(`scripts/test.sh` roda só rules e backend). É dívida antiga; a F2-T07 cobre os CAs por um runner
próprio (`eventos.test.mjs`), mas vale incluir o contrato no `test.sh` numa próxima passagem.
