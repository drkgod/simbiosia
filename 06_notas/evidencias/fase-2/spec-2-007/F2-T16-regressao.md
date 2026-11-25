# F2-T16 — REGRESSAO-F2 (CA-2-007-01)

**Data:** 29/10/2026 · **Funil:** v0.0.204/c49c213 publicado · **Fábrica:** 9cb8d (v0.0.32)

## Bateria completa — Funil (13 suítes, 183 testes, ZERO falhas)

| Suíte | Casos | Resultado |
|---|---|---|
| backend | 35 | 34 pass · 1 skip · 0 fail |
| rules | 66 | 66 pass |
| reconciliacao | 1 | 1 pass |
| comercial | 10 | 10 pass |
| politica | 15 | 15 pass |
| eventos | 13 | 13 pass |
| retorno_abordagem | 5 | 5 pass |
| retorno_protecao | 7 | 7 pass |
| passagem | 7 | 7 pass |
| agente | 7 | 7 pass |
| resposta_pausa | 5 | 5 pass |
| saida_global | 5 | 5 pass |
| metrica_config | 6 | 6 pass |
| painel_filtros | 6 | 6 pass |

**Total: 183 testes executados, 0 falhas, 1 skip** (o skip é a rota de IA, que exige integração
externa — coerente com o portão fechado).

## Zero despacho externo — provado

Mesmo que `rules`/`backend` mexam em configuração, a prova direta:

| Verificação | Funil | Fábrica |
|---|---|---|
| `sistema_config.pausa_geral` | **true** | **true** |
| `integracoes_externas_habilitadas` | **false** | **false** |
| Rota legada de envio | — | `enviar-funil` **bloqueada** com resposta controlada |

Os dois apps estão com o portão FECHADO: nenhuma chamada externa é executada mesmo se a rota for
chamada. A Fábrica responde explicitamente que nenhuma chamada HTTP entre apps é executada.

**Varredura textual de `eventos_receptor.js` (o hook de maior tráfego, 626 linhas):**
zero ocorrências de `$http.send`, `http.send`, `fetch(`, `axios`, `$ai.`. A única menção a
"whatsapp" é valor de campo `canal`, não despacho.

## O que NÃO foi feito e por quê

A prova de jornada completa por navegador (E2E dos dois apps numa única sessão) não foi executada
nesta rodada. Cada peça já tem prova própria por navegador ou backend:
- F2-T12: ciclo exportar→aplicar→duplicado→projeção→edição 403 (navegador + backend)
- F2-T13: proteção e reconciliação (backend, 7 casos)
- F2-T15: painel filtros/ACL/natureza (navegador + backend)

O que falta é a JORNADA CONTÍNUA numa mesma sessão. Registrado como pendência da F2-T18.

## RED do erro deliberado (exigido pelo CA-2-007-01)

Inserido erro proposital numa regra de negócio (`saida_global`):

```
RED   :: tests 5 · pass 4 · fail 1   ← a suíte FALHA quando a regra quebra
GREEN :: tests 5 · pass 5 · fail 0   ← restaurada, volta a passar
```

A suíte não é decorativa: ela detecta a inversão de comportamento e volta ao verde quando o código
está correto.
