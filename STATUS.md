# Estado da construção

Atualizado: 21/10/2026
Fase atual: Fase 1 ENCERRADA (22/22 tasks com aceite). Fase 2 EM EXECUÇÃO: SPEC-1-007 (identidades e projeções locais entre apps).

SPEC-1-007: 199009b0 (CONTRATO-V1, CA-01) FECHADA com aceite do José (21/10). 0cda0d05 (PROJECAO-LOCAL, CA-02) IMPLEMENTADA E PROVADA (21/10): Funil 12/12 (projeções literais empresa/pessoa/produto/nota; trava de autoridade 0031 — updateRule null em produtos/pontuacoes; v0.0.108/0495a0d); Fábrica 9/9 (estado_comercial + protecao_global na coleção comercial_projetado; PATCH direto negado; v0.0.26/3064db4). Causa raiz corrigida: Fábrica não tinha campo role em users (migração 0010 + seed admin). Próximo: teste humano → fechar 0cda0d05 → acc7d79f (última da SPEC). Provas: 06_notas/evidencias/fase-1/spec-1-007/0cda0d05-provas.md.

Pendências de limpeza (registradas, não bloqueiam): fixtures de prova no banco (append-only — resíduo permanece); 152 conflitos antigos + fixtures TESTE da Fase 1; cadastro→lead (criar lead a partir da pessoa).

Histórico da Fase 1 (22/22): SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da); SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7); SPEC-1-003 completa (c7240db4, 0574671d, 899395c4); SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923); SPEC-1-005 completa (3b93879f, 77a1aa6e, 2faac03b); SPEC-1-006 completa (2e26d650, bf09d372, 196f75e2 — DEMO-F1). Task extra "edição de lead" fechada com aceite (16/10).

Repositório operacional privado: https://github.com/drkgod/simbiosia. Os dois projetos de origem são submódulos (funil-mkt: evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9).
