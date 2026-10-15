# Estado da construção

Atualizado: 15/10/2026
Fase atual: 1 — em execução (13 de 19 tasks concluídas com aceite; SPEC-1-001/1-002/1-003/1-004 COMPLETAS; 3b93879f implementada, aguardando revalidação).

Tasks concluídas com aceite humano (14–15/10/2026): SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da); SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7); SPEC-1-003 completa (c7240db4, 0574671d, 899395c4, v0.0.55 e135610); SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923, v0.0.67 7145c37).

Task 3b93879f (pontuação por produto com motivos, CA-1-005-01/02) IMPLEMENTADA em 15/10/2026 (v0.0.69, 2ccd3a4): motor puro scoring-core + gerador + migração 0022 + hook calcular + seção no LeadDetail; rules 65/65, backend 21/21, prova funcional da fixture da SPEC ok (100/A, 0/D, 40/C, 100/A; idempotente; sem régua → pendente; vendedor 403).

Correção 1 (feedback do teste do José, v0.0.70, 0986c2f, publicada 20:24Z): o botão "Aprovar régua" mandava status aprovada no CREATE e o backend recusava ("Régua nova nasce como rascunho"). A UI agora faz create (rascunho) + update (aprovada) — o fluxo em dois passos que o contrato exige (é o que registra autor/data da aprovação). Provado na API: create rascunho ok, update aprovada ok (aprovado_por registrado), create já aprovada segue 400. Aguardando revalidação do José (aprovar régua e calcular pontuação).

Próxima task elegível: 77a1aa6e (proteger cálculo e histórico), aguardando autorização.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Os dois projetos de origem são submódulos (funil-mkt: evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9).
