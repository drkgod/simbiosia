# Estado da construção

Atualizado: 15/10/2026
Fase atual: 1 — em execução (13 de 19 tasks concluídas com aceite; SPEC-1-001/1-002/1-003/1-004 completas; 3b93879f implementada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Tasks concluídas com aceite humano:
- SPEC-1-001 completa (20c86bd2, 490ec843, d3e7d6da) — fundação, acessos e ambiente.
- SPEC-1-002 completa (ea6db5dc, 3b736d7d, f5668cd8, 1b7f6fc7) — cadastro único e proteção.
- SPEC-1-003 completa (c7240db4, 0574671d, 899395c4) — importação das bases (v0.0.55, e135610).
- SPEC-1-004 completa (7183cf59, 46c0fd51, b0c25923) — produtos e réguas editáveis (v0.0.67, 7145c37).

Task 3b93879f (pontuação por produto com motivos, CA-1-005-01/02) IMPLEMENTADA em 15/10/2026 (v0.0.69, 2ccd3a4): motor puro scoring-core + gerador + migração 0022 + hook calcular + seção no LeadDetail; rules 65/65, backend 21/21, prova funcional da fixture da SPEC ok. Aguardando teste humano. Próxima: 77a1aa6e (proteger cálculo e histórico).

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.
