# Estado da construção

Atualizado: 14/10/2026
Fase atual: 1 — em execução (tasks 1-3 da SPEC-1-001 concluídas com aceite; task 1 da SPEC-1-002 implementada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Tasks concluídas: 20c86bd2 (ambiente de demonstração), 490ec843 (SEM-EXTERNO, v0.0.30), d3e7d6da (ACL, v0.0.34) — todas com teste humano aprovado. Task ea6db5dc (SPEC-1-002, cadastro único, CA-1-002-01) IMPLEMENTADA (v0.0.36, ref 0083166): migração 0012 (empresas/pessoas/pessoas_origens/bloqueios_contato/fusoes_cadastro + relations em leads), backfill rastreável (93 leads, 95 empresas, 11 pessoas), serviço cadastro.ts, tela /cadastro; CADASTRO-LEGADO verde (11/11 backend, 7/7 regras). Aguardando teste humano.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da ea6db5dc (tela /cadastro); depois a task IDENTIDADE (3b736d7d). Uma task por vez, com prova e confirmação humana.
