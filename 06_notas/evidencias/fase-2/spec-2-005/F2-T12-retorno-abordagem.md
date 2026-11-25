# F2-T12 — RETORNO-ABORDAGEM (CA-2-005-01)

**Data:** 28/10/2026 · **Funil:** v0.0.186/0249dce publicado
**Suíte:** `tests/retorno_abordagem.test.mjs` · **5/5 verde** · **RED confirmado**
**Regressão:** resposta_pausa 5/5 · saida_global 5/5 · eventos 13/13 · passagem 7/7

## O que foi construído

| Peça | O que faz |
|---|---|
| `POST /backend/v1/comercial/registrar-abordagem` | Grava o FATO comercial da primeira abordagem, com data literal, revisão e ambiente TESTE. |
| `GET /backend/v1/comercial/exportar-fixture` | Emite a fixture local (documento) para aplicação MANUAL na Fábrica. Nenhum HTTP entre apps. |
| `eventos_receptor` (tipo `toque_confirmado`) | Confirmação simulada grava `primeira_abordagem_em` no lead; o PRIMEIRO instante é preservado. |
| `comercial_exportacoes` (migração 0046) | Log do que foi exportado, com hash — duplicata não substitui fato válido. |

## Provas (por critério)

```
✔ confirmação registra a primeira abordagem com data literal (ambiente teste, fonte funil)
✔ reprocessar NÃO move a data — "primeira" quer dizer primeira
✔ sem data confirmada não há fato (agendamento/aprovação não confirmam; modo real recusado)
✔ fixture leva a identidade GLOBAL, nunca o id local do PocketBase
✔ registrar abordagem não recalcula pontuação (comercial é domínio próprio)
```

## Decisões de desenho

**A exportação não faz HTTP.** A rota só MONTA o documento; quem aplica é o operador, no backend
TESTE da Fábrica, via `sync_aplicar` (herdado da Fase 1). Isso mantém a regra "nenhum HTTP entre
apps" e deixa o transporte fora do sistema.

**Identidade global, nunca id local.** O que viaja é `sync_global_id` (ou o id como fallback
canônico) — IDs de PocketBase e usuários locais não atravessam como relações.

## Defeitos encontrados e corrigidos

1. **`leads` não tem campo `ambiente`** — filtrar por ele derrubava a exportação com 500
   ("unknown field ambiente"). O escopo TESTE vem da pessoa, verificada evento a evento.
2. **Formato de data** — o backend devolve `2026-10-27 13:00:00.000Z`; comparar string bruta
   falhava. O teste compara INSTANTE, que é o que importa.

## Pendência

**RETORNO-UI-E2E não foi provado nesta rodada** (orçamento da sessão). O backend está provado;
falta a prova de navegador: confirmar toque no Funil → exportar fixture → aplicar no TESTE da
Fábrica → conferir origem/data/revisão e edição proibida. Registrado como pendência aberta.
