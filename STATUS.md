# Estado da construção

Atualizado: 21/10/2026
Fase atual: Fase 1 ENCERRADA (22/22 tasks com aceite — DEMO-F1 aprovada pelo José em 21/10, "Funcionou" 8/8 etapas). Fase 2 EM EXECUÇÃO: SPEC-1-007 (identidades e projeções locais entre apps).

SPEC-1-007 (Fase 2): 199009b0 (CONTRATO-V1, CA-01) FECHADA com aceite do José ("Pode prosseguir", 21/10/2026) — contrato v1 idêntico nos 2 repos (md5 fonte fb564fe7, fixture e7207512), testes 8/8 nos dois, ciclo pendente→aplicar→projeção→duplicado provado no Funil, reavaliação de recibo pendente corrigida (fluxo 3 da SPEC). Provas: 06_notas/evidencias/fase-1/spec-1-007/199009b0-provas.md.

0cda0d05 (PROJECAO-LOCAL, CA-02) IMPLEMENTADA (21/10): Funil provado 12/12 (projeções literais empresa/pessoa/produto/nota; trava de autoridade 0031 — updateRule null em produtos/pontuacoes); Fábrica publicada (v0.0.24, coleção comercial_projetado + hook de estado_comercial/protecao_global) com impedimento mapeado (create do mapeamento dá 400 — provável role do usuário; prova funcional pendente). Próximo: corrigir role na Fábrica → prova → teste humano dos dois lados. Provas: 06_notas/evidencias/fase-1/spec-1-007/0cda0d05-provas.md.

Pendências de limpeza (registradas, não bloqueiam): fixtures de prova no banco (sync_inbox/empresas append-only — resíduo permanece); 152 conflitos antigos + fixtures TESTE da Fase 1; cadastro→lead (criar lead a partir da pessoa).

Histórico da Fase 1 (22/22): SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da); SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7); SPEC-1-003 completa (c7240db4, 0574671d, 899395c4); SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923); SPEC-1-005 completa (3b93879f, 77a1aa6e, 2faac03b); SPEC-1-006 completa (2e26d650, bf09d372, 196f75e2 — DEMO-F1). Task extra "edição de lead" fechada com aceite (16/10).

Repositório operacional privado: https://github.com/drkgod/simbiosia. Os dois projetos de origem são submódulos (funil-mkt: evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9).
