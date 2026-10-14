# Estado da construção

Atualizado: 14/10/2026
Fase atual: 1 — em execução (tasks 1 e 2 concluídas, com teste humano aprovado; task 3 autorizada e em implementação).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Sistema único com Funil MKT como base; Fábrica como módulo de reuso. Task 20c86bd2 (SPEC-1-001, CA-01/04) concluída com teste humano aprovado. Task 490ec843 (SPEC-1-001, CA-03, SEM-EXTERNO) CONCLUÍDA (v0.0.30, ref a0ac0e2; testes 7/7 regras e 5/5 backend; gate de UI com aviso visível e respiro). Task d3e7d6da (ACL-NEGATIVA): plano apresentado e AUTORIZADO pelo José; implementação em andamento — RED escrito no clone (contas sintéticas operador1/operador2, negações cruzadas, owner/role imutáveis, config/despachos só-admin); execuções ajustadas ao rate limit do backend de demonstração.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: concluir RED da d3e7d6da, migração 0010 + dono do lead pai nos hooks de IA, GREEN, publicação e teste humano. Uma task por vez, com prova e confirmação humana.
