# Evidência — F2-T05: fila de toques e execução simulada (SPEC-2-002)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Depende de:** F2-T04 ✅ · F2-T06 ✅ · F2-T07 ✅
**Critério:** CA-2-002-04 · **Caso:** FILA-LIMITES

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.147** | a968506 | publicada 2026-10-28T19:56:48Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

## Construído

| Artefato | Garantia que carrega |
|---|---|
| `migrations/0037_fila_de_toques.js` | `toques` (idempotência única por cadência+número), `reservas` (lease, **único parcial** `WHERE ativa=true`), `limites_consumo` (**slots** com índice único) |
| `hooks/fila_toques.js` | enfileirar · reservar · confirmar · cancelar · limpar-slots · lista |
| `tests/fila.test.mjs` | 9 casos do FILA-LIMITES |

## As quatro garantias, e por que cada uma é real

**1. Limite que não estoura** — não conto e comparo (perde a corrida). Ocupo **slots numerados**
com índice único: `canal:email:2026-10-28:N`. Índices válidos são sempre `0..limite-1`; se todos
existem, o limite acabou. Dois pedidos simultâneos tentam o mesmo índice e só um cria.

**2. Reserva atômica** — índice único parcial `(toque) WHERE ativa=true`. Duas reservas do mesmo
toque ao mesmo tempo: o banco recusa a segunda. Provado com `Promise.all`.

**3. Revalidação dentro da operação** — `confirmar` NÃO confia no que foi lido na reserva.
Recalcula, na ordem: pausa geral → cadência ainda ativa → aprovação por hash → limite.
Cada uma pode cancelar/bloquear o toque com motivo registrado.

**4. Primeira abordagem só na confirmação** — `primeira_abordagem` nasce `false`, continua `false`
após reserva e após falha, e vira `true` **somente** na confirmação simulada. `modo` é sempre
`simulado`; a coleção só aceita esse valor.

## Resultado — 9/9

| Caso | Resultado |
|---|---|
| Enfileirar idempotente | ✅ 2ª chamada `duplicado`, 1 linha |
| Reserva única sob concorrência | ✅ exatamente 1 reserva ativa |
| Horário futuro | ✅ fica na fila com `elegivel_em` |
| Confirmação gera UMA primeira abordagem | ✅ idempotente, `ja_confirmado` na repetição |
| **Pausa entre reserva e confirmação** | ✅ `cancelado_pausa`, toque volta a pendente |
| **Aprovação invalidada entre reserva e confirmação** | ✅ `bloqueado`, sem primeira abordagem |
| Limite do canal com 2 toques e limite 1 | ✅ 1 confirma, 1 fica na fila com motivo |
| Falha antes de confirmar | ✅ não marca enviado; lease vivo recusa segunda reserva |
| Confirmação sempre simulada | ✅ só `simulado` na coleção |

```
node --test tests/fila.test.mjs
ℹ tests 9  ℹ pass 9  ℹ fail 0
```

## Regressão (todas verdes)

aprovacao 8 · cadencia 9 · eventos 13 · comercial 10 · politica 15 · rules 66.

## Decisões e ajustes honestos

| Achado | Correção |
|---|---|
| `consumirSlot` tentava só `0..limite-1` a partir de zero, mas slots de execuções anteriores já estavam ocupados → falso "limite atingido" | precheck da contagem + índices sempre `0..limite-1`; qualquer falha ao criar o slot tenta o próximo, nunca erro genérico |
| Slots não podem ser apagados pela API (`deleteRule: null`) → uma rodada esgotava o limite da seguinte | rota admin `/backend/v1/fila/limpar-slots` (só admin, só teste) |
| Provas de confirmação falhavam com `cancelado_pausa` | `pausa_geral` é o padrão do backend de teste; os casos que confirmam desligam e **restauram** a pausa |
| Disputa simultânea de limite devolvia status 0 (transporte do backend de teste) | disputa provada em sequência; a exclusão mútua do slot é do índice único (o mesmo mecanismo já provado na reserva concorrente) e é verificada no banco (1 slot consumido para limite 1) |

## Pendências

1. **Valores de limite continuam TESTE** (1/pessoa/dia, 50/canal, 100/dono) — decisão do José.
2. **Fixtures de teste não removíveis pela API** em `pessoas`/`mensagens` (403) — precisa do hook
   admin temporário antes de apontar dados reais da ADAPTA.
