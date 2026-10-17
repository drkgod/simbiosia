# Histórico da construção

## 17/10/2026 17:45 — Task 2e26d650 (listas unificadas) IMPLEMENTADA (skip v0.0.97–98)

Migração 0027: listas expandida (coorte, produto_alvo, filtros json, fonte rfb/planilha/api/misto/legado, status rascunho/em_preparacao/pronta, ambiente teste/producao; backfill idempotente das listas legadas → fonte=legado, ambiente=producao — DECISÃO DO CLIENTE registrada na análise) + deleteRule de lista_membros liberado para admin (desvincular não apaga lead/histórico). lista_membros JÁ EXISTIA (0016, da task de importação) — índice único (lista, lead, ambiente) mantido. Serviço listas.ts (getListas/getMembros/criarLista/vincularLead/desvincular/vincularImportados idempotente). Página Listas.tsx (criar lista, tabela com status/fonte/ambiente, painel de membros com Ver contato → LeadDetail e Desvincular) + rota /listas + link na navegação. Correção durante a tarefa: $app não existe em migração — usar o app do callback (QA pegou). Provas: regras 66/66; backend 34/34 (LISTAS-UNIFICADAS: mesma pessoa em 2 listas com IDs únicos, índice único recusa repetição, navegação no mesmo backend, desvincular preserva lead, vendedor não vê lista alheia). Prova de clique: criar lista, vínculo por membro, navegação lista→contato→notas no mesmo login/backend. Publicada e86c82c.

## 16/10/2026 21:55 — Task 2faac03b FECHADA — SPEC-1-005 COMPLETA

Teste humano aprovado pelo José ("Funcionou" — régua v2 aprovada, job de repontuação executado, marcador (atual) no histórico; reteste após a correção do parse JSON ok). Skip v0.0.96 (0041c7b) no ar.

## 16/10/2026 23:42 — Correção: "[object Object]" is not valid JSON ao abrir a régua (skip v0.0.96)

Campos JSON do PocketBase (criterios, faixas, regras_eliminatorias, respostas) chegam JÁ desserializados pelo SDK; a UI fazia JSON.parse em cima do objeto. Correção: jsonTolerante no editor de régua e na entrevista.
