# Análise — task 2e26d650: Preparar as listas na Fábrica e a visão no Funil

**SPEC:** SPEC-1-006 (listas unificadas e demonstração) · **CA:** CA-1-006-01 · **TDD:** LISTAS-UNIFICADAS
**Data:** 17/10/2026 · **Estado:** implementada (v0.0.98), aguardando teste humano

## Descoberta principal

`lista_membros` JÁ EXISTIA (migração 0016, da task de importação) com índice único (lista, lead, ambiente) e RLS no padrão ACL. A task então reduziu-se a: expandir `listas` com o contrato da SPEC, liberar delete de membro para admin (desvincular), criar serviço/página/rota e provar.

## Implementado

1. Migração 0027: listas + coorte/produto_alvo/filtros/fonte/status/ambiente; backfill idempotente (legado → fonte=legado, ambiente=producao — DECISÃO DO CLIENTE); deleteRule de lista_membros = admin.
2. Serviço src/services/listas.ts: getListas/getLista/getMembros/criarLista/vincularLead/desvincular/vincularImportados (idempotente por lista_origem).
3. Página src/pages/Listas.tsx: criar lista (nome/descrição/coorte/fonte/ambiente), tabela de listas, painel de membros com Ver contato (→ /leads/:id) e Desvincular; estado vazio com contagem zero.
4. Rota /listas + link "Listas" no Layout (entre Importar e Produtos).
5. Teste LISTAS-UNIFICADAS (backend): mesma pessoa em 2 listas com IDs únicos; índice único recusa repetição; navegação membro→lead→pontuações no mesmo login; desvincular preserva lead; vendedor não vê lista alheia.

## Provas

- QA Skip verde (v0.0.97 falhou na migração por $app; v0.0.98 corrigida com app do callback).
- Regras 66/66; backend 34/34 (LISTAS-UNIFICADAS incluída).
- Prova de clique: criar lista TESTE Lista UI → vínculo de lead → Ver contato → LeadDetail com notas/histórico; fixture de teste removida após a prova.
- Publicada e86c82c em 2026-10-17T17:45:17Z.

## Riscos/notas

- Filtros por coorte/produto são a task SEGUINTE (bf09d372) — não duplicados aqui.
- Fábrica (50248) intocada; nada de enviar-funil.
- Espelho fase.md no GitHub defasado (quadro do portal é sincronizado à parte pelo consultor).
