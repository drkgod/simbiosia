# Estado da construção

Atualizado: 16/10/2026
Fase atual: 1 — em execução (14 de 19 tasks concluídas com aceite; SPEC-1-001/1-002/1-003/1-004 COMPLETAS; SPEC-1-005 com CA-01/02 fechados e 77a1aa6e implementada, aguardando teste humano).

Tasks concluídas com aceite humano (14–15/10/2026): SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da); SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7); SPEC-1-003 completa (c7240db4, 0574671d, 899395c4, v0.0.55 e135610); SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923, v0.0.67 7145c37); SPEC-1-005 CA-01/02 (3b93879f, v0.0.70 0986c2f, com correção 1: aprovar régua = create rascunho + update aprovada).

Task 77a1aa6e (proteger cálculo e histórico, CA-1-005-03/05) IMPLEMENTADA em 15/10/2026 (v0.0.72, 5eb62a2): migração 0023 (pontuacoes append-only — createRule/updateRule/deleteRule null, leitura dono/admin) + histórico das notas por produto no LeadDetail (todas as versões, sem mistura; score legado e qualificações manuais preservados). TDD: backend 24/24 (3 novos), rules 65/65. Prova funcional: PATCH forjado de valor/faixa → 403; CREATE forjado direto → 403; score legado 55 preservado após repontuar; histórico por produto sem mistura. Publicada 20:53Z. Aguardando teste humano.

Próxima task elegível: 2faac03b (repontuar os contatos ainda não abordados — SCORE-VERSAO-JOB), aguardando autorização para análise.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Os dois projetos de origem são submódulos (funil-mkt: evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9).
