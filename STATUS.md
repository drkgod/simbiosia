# Estado da construção

Atualizado: 14/10/2026
Fase atual: 1 — em execução (tasks 1 e 2 concluídas, com teste humano aprovado; task 3 implementada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Sistema único com Funil MKT como base; Fábrica como módulo de reuso. Task 20c86bd2 (SPEC-1-001, CA-01/04) concluída com teste humano aprovado. Task 490ec843 (SPEC-1-001, CA-03, SEM-EXTERNO) CONCLUÍDA (v0.0.30, ref a0ac0e2). Task d3e7d6da (ACL-NEGATIVA, CA-02) IMPLEMENTADA (v0.0.34, ref f581d6f): migrações 0010/0011 (owner e role imutáveis, filhas pelo dono do lead pai, sistema_config/teste_despachos só-admin), dono do lead pai verificado nos 3 hooks de IA, testes 10/10 backend + 7/7 regras. Aguardando teste humano.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da d3e7d6da (vendedor vê só o dele; admin vê tudo; vendedor não troca owner nem role; config/despachos só-admin). Uma task por vez, com prova e confirmação humana.
