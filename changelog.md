# Histórico da construção

## 17/10/2026 18:04 — Task bf09d372 (filtros por coorte e produto) IMPLEMENTADA (skip v0.0.99)

Página Listas ganhou card Filtros (coorte + produto-alvo — só produtos ativos), coluna Membros com contagem por lista (getContagens: 1 consulta agregada; RLS do backend filtra o que o vendedor não é dono) e coluna "Nota — {produto}" nos membros da lista aberta (getNotasProduto: nota mais recente por lead de pontuacoes, append-only). Filtro vazio = estado vazio com contagem zero. Provas: rules 66/66; backend 35/35 — LISTAS-FILTROS (contagem por lista, filtro coorte/produto no backend, nota por lead, RLS de vendedor). Lição da task: a rota scoring/calcular exige régua aprovada — o 1º teste falhou sem régua; corrigido criando régua no padrão provado (create rascunho → update aprovada). Prova de clique: filtros renderizam, contagem "4 de 4", coluna Membros presente. Publicada 9c3ac61.

## 17/10/2026 17:58 — Task 2e26d650 FECHADA — aceite humano do José ("Funcionou")

Teste na /listas aprovado. CA-1-006-01 completo. Próxima task da SPEC-1-006: bf09d372 (filtros por coorte e produto).

## 17/10/2026 17:45 — Task 2e26d650 (listas unificadas) IMPLEMENTADA (skip v0.0.97–98)

Migração 0027: listas expandida (coorte, produto_alvo, filtros json, fonte, status, ambiente; backfill idempotente das listas legadas → fonte=legado, ambiente=producao — DECISÃO DO CLIENTE) + deleteRule de lista_membros liberado para admin. lista_membros JÁ EXISTIA (0016). Serviço listas.ts + página Listas.tsx + rota /listas + nav. Correção durante a tarefa: $app não existe em migração — usar o app do callback (QA pegou). Provas: rules 66/66; backend 34/34 (LISTAS-UNIFICADAS). Prova de clique completa. Publicada e86c82c.
