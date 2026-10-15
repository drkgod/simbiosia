# Estado da construção

Atualizado: 15/10/2026
Fase atual: 1 — em execução (8 de 19 tasks concluídas com aceite; SPEC-1-001 e SPEC-1-002 completas; c7240db4 concluída; 0574671d implementada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Tasks concluídas com aceite humano: SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da), SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7) e c7240db4 (SPEC-1-003, prévia da base, CA-1-003-01/04, v0.0.48 ref 6b91544).

Task 0574671d (importar a base e mostrar o relatório por linha, CA-1-003-02) IMPLEMENTADA em 15/10/2026 (v0.0.50, ref 825971e): migração 0016 (importacoes/importacao_linhas/lista_membros), hook confirmar (linha a linha com checkpoint, identidade contra a base do ambiente, repetir devolve o relatório sem duplicar, só admin) e UI /importar com confirmação e relatório por linha. Prova: rules 36/36, backend 16/16, prova funcional da rota (6 linhas → 1 nova/2 repetidas/2 incompletas/1 erro; reconciliação ok; repetição sem duplicar; vendedor 403). Aguardando teste humano.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da 0574671d (tela /importar); depois a task 899395c4 (retomar e desfazer importação sem duplicar contatos). Uma task por vez, com prova e confirmação humana.
