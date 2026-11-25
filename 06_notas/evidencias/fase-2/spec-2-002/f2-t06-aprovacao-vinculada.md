# Evidência — F2-T06: aprovar cada mensagem de relacionamento (SPEC-2-002)

**Data:** 28/10/2026 · **Executor:** Jarbas · **Depende de:** F2-T03 ✅ · F2-T04 ✅ · F2-T07 ✅
**Critérios:** CA-2-002-03 · **Casos:** APROVACAO-VINCULADA · APROVACAO-UI-E2E (parcial)

## Versões

| App | Versão | Hash | Produção |
|---|---|---|---|
| Funil (50182) | **v0.0.143** | 670b074 | publicada 2026-10-28T19:04:04Z |
| Fábrica (50248) | v0.0.32 | d69cbcb | inalterada |

## Construído

| Artefato | Papel |
|---|---|
| `migrations/0036_mensagens_e_aprovacoes.js` | coleções `mensagens` e `aprovacoes` + índice único parcial (uma aprovação válida por mensagem) |
| `hooks/mensagens_aprovacao.js` | criar · editar · aprovar · revogar · pode-executar |
| `src/pages/Aprovacoes.tsx` | tela real `/aprovacoes` (lista, editar, aprovar, revogar, "posso executar?") |
| `tests/aprovacao.test.mjs` | 8 casos do CA-2-002-03 |

## Princípio implementado

A aprovação é **selo sobre hash de conteúdo**, não booleano. O hash é sempre calculado no servidor;
hash vindo do cliente é ignorado (provado). `pode-executar` **recalcula** o hash do conteúdo atual e
compara com o aprovado — não confia em flag gravada na edição.

## CA-2-002-03 — APROVACAO-VINCULADA: 8/8 ✅

| Prova | Resultado |
|---|---|
| Aprovar conteúdo A → executa | ✅ `aprovacao_valida` |
| Editar A→B → aprovação invalidada | ✅ `invalidouAprovacao: true`, volta a pendente |
| Reaprovar B → vale de novo | ✅ 1 aprovação válida (índice parcial) |
| Trocar canal → invalida | ✅ |
| Revogar → invalida e volta a pendente | ✅ |
| Operador (papel vendedor) tenta aprovar | ✅ 403 |
| Aprovar NÃO envia | ✅ `enviado: false`, 0 despachos registrados |
| Hash do cliente ignorado | ✅ servidor recalcula |

```
node --test tests/aprovacao.test.mjs
ℹ tests 8  ℹ pass 8  ℹ fail 0
```

## CA-2-002-03 — APROVACAO-UI-E2E: executado por navegador real

Tela `/aprovacoes` em produção, mensagem de fixture, clique real:
```
1-INICIAL:            (vazio — ainda não verificado)
2-APOS_APROVAR_aviso: "Aprovação registrada para este conteúdo. Nada foi enviado."
3-APOS_APROVAR_exec:  PODE EXECUTAR: aprovacao_valida
4-APOS_EDITAR_aviso:  "Conteúdo alterado: a aprovação anterior deixou de valer."
5-APOS_EDITAR_exec:   NÃO PODE EXECUTAR: sem_aprovacao_valida
```
**Parcial:** o trecho "repetir com vendedor alheio" foi provado no BACKEND (403), não repetido na UI
por esgotamento de orçamento da execução. Usuário sintético criado (`vendedor.ui.*`, papel vendedor)
para completar quando retomado.

## Causa raiz de uma saga de 6 tentativas (vale registro)

`getNumber()` **não existe** no Record do PocketBase → `TypeError: Object has no member 'getNumber'`
→ 500 no aprovar. Só apareceu depois de expor o erro no corpo da resposta. Corrigido para
`Number(record.get('campo') || 0)` nos dois hooks. **O mesmo bug latente estava no hook de cadência**
(linha da regra de prioridade) e foi corrigido no mesmo apply — teria quebrado quando a regra fosse
configurada.

Colateral: a migração 0036 não constava aplicada após o primeiro apply (que falhou no build);
o índice do ordinal falhou em silêncio. Reaplicada e confirmada em `skip_cloud_list_migrations`.

## Achados abertos

1. **Limpeza de fixtures não funciona pela API** — `pessoas` e `mensagens` têm `deleteRule: null`;
   deletar dá 403 "Only superusers". As suítes passam mas deixam registros `MARCA`/`APR-`/`PROBE`
   no backend de teste. Precisa do hook admin temporário (padrão já validado em 22/10) antes de
   apontar dados reais da ADAPTA.
2. **Teste de UI do vendedor alheio** pendente (backend já prova).
