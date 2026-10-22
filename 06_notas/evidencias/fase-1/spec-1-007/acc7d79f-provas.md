# Provas — Task acc7d79f (CONTRATO-RECONCILIACAO, SPEC-1-007, CA-1-007-03)

**Data:** 22/10/2026 · **Executor:** Jarbas · **Status:** PROVADA — 14/14 Funil + 12/12 Fábrica; aguardando teste humano

## Casos TDD provados (ambos os apps)

| Caso | Funil | Fábrica |
|---|---|---|
| duplicata: mesmo evento id/hash repetido → duplicado, sem nova projeção | ✔ | ✔ |
| atraso: rev1 depois de rev2 → atrasado; ponteiro permanece rev2 | ✔ | ✔ |
| conflito: mesmo id/revisão com payload diferente → conflito (não sobrescreve) | ✔ | ✔ |
| sem mapeamento → pendente; corrigido → MESMO evento aplicado 1x; retomada não duplica | ✔ | ✔ |
| fusão (identidade_alias): alias gravado, ativo, aponta para o sobrevivente | ✔ | ✔ |
| pausa de cadências: cadência agendada do contexto fundido → cancelada (revisão humana) | ✔ | n/a |
| reversão (identidade_revogacao): rev superior, IDs originais e histórico preservados | ✔ | ✔ |
| evento antigo (rev1) após revogação (rev2) → atrasado/duplicado: NÃO reativa alias | ✔ | ✔ |

Testes: tests/reconciliacao.test.mjs (Funil) e tests/reconciliacao-fabrica.test.mjs (roda do funil-mkt com PB_URL da Fábrica). Fonte pura scripts/domain/simbiosia-reconciliacao.js idêntica nos 2 apps.

## Correções do ciclo (5 + fix de escopo)

1. Comparação de revisão por CHAVE DE NEGÓCIO (ambiente, origem, domínio, entidade) — não só por evento_id.
2. Recarga do hook (instância desatualizada).
3. Recibo 'duplicado' também conta na chave de negócio.
4. Nota projetada IDEMPOTENTE por chave (lead+produto+versão+dados_hash).
5. Busca por chave SEM limite (recibo da corrida atual visível).
6. Fix de escopo no hook da Fábrica (alias usava variável fora de escopo → 500).

## Versões no ar (22/10/2026 11:46Z)

- Funil (Skip 50182): v0.0.118 (58773c6)
- Fábrica (Skip 50248): v0.0.29 (c603535)

## Regressão

contrato.test.mjs 8/8 nos 2 repos; projecao-local 12/12 Funil.

## Pendências

1. Teste humano (roteiro enviado ao José).
2. Espelho dos hooks/migrações/testes nos submódulos do GitHub (fim da SPEC).
3. Limpeza: resíduo de fixtures de prova (append-only) — UI admin.
