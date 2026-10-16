# Estado da construção

Atualizado: 16/10/2026
Fase atual: 1 — em execução (15 de 19 tasks concluídas com aceite; SPEC-1-005 com CA-01/02/03/05 fechados — falta apenas a 2faac03b, job de repontuação).

Tasks concluídas com aceite humano (14–16/10/2026): SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da); SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7); SPEC-1-003 completa (c7240db4, 0574671d, 899395c4); SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923); SPEC-1-005 CA-01/02 (3b93879f) e CA-03/05 (77a1aa6e, v0.0.72 5eb62a2 — pontuacoes append-only + histórico por produto; idempotência confirmada com o José como design).

Task extra "edição de lead direto no detalhe" (decisão do José, 16/10) IMPLEMENTADA (v0.0.80, 96994d7): migrações 0024/0025 (leitura aberta + updateRule autenticado em empresas/pessoas) + hook cadastro_editar (REQUEST hook — onRecordUpdateRequest; o model hook não tem e.auth) + seção Editar no LeadDetail (nome, segmento, UF, cidade, contato ×3, observações + setor/porte da empresa vinculada; etapa fora do editor — muda pelo fluxo). TDD: backend 26/26 (EDITAR-ACL + EDITAR-EFEITO), rules 65/65. Prova: vendedor edita lead/empresa exclusiva própria; alheio/compartilhada negados; admin tudo; mudar setor → recalcular → linha nova no histórico. Publicada 14:30Z. Aguardando teste humano.

Próxima task da SPEC-1-005: 2faac03b (repontuar os contatos ainda não abordados — SCORE-VERSAO-JOB), aguardando autorização para análise.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Os dois projetos de origem são submódulos (funil-mkt: evandromp70/funil-mkt---vendas-simbiosia-dmbzn8jh9).
