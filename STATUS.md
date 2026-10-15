# Estado da construção

Atualizado: 15/10/2026
Fase atual: 1 — em execução (9 de 19 tasks concluídas com aceite; SPEC-1-001 e SPEC-1-002 completas; CA-01/02 da SPEC-1-003 entregues; 899395c4 implementada, aguardando teste humano — fecha a SPEC-1-003).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Tasks concluídas com aceite humano: SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da), SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7), c7240db4 (prévia da base, CA-1-003-01/04, v0.0.48 ref 6b91544) e 0574671d (importar e relatório por linha, CA-1-003-02, v0.0.50 ref 825971e).

Task 899395c4 (retomada + rollback, CA-1-003-03) IMPLEMENTADA em 15/10/2026 (v0.0.51, ref f66fd73): retomada no confirmar (lote processando devolve pendentes em vez de 409), rota reverter (rollback linha a linha, nada compartilhado é apagado, revisão 409), UI desfazer com confirmação. Prova: rules 43/43, backend 16/16, reversão funcional com zero restos, re-reversão recusada, vendedor 403, retomada de lote interrompido ok. Aguardando teste humano.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da 899395c4 (botão Desfazer este lote na /importar); depois a SPEC-1-004 (Fábrica de listas como módulo). Uma task por vez, com prova e confirmação humana.
