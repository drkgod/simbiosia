# Estado da construção

Atualizado: 14/10/2026
Fase atual: 1 — em execução (tasks 1 concluída; task 2 implementada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Sistema único com Funil MKT como base; Fábrica como módulo de reuso. Task 20c86bd2 (SPEC-1-001, CA-01/04) concluída com teste humano aprovado. Task 490ec843 (SPEC-1-001, CA-03, SEM-EXTERNO) implementada (v0.0.27, ref 2fce7e3): função pura recursoHabilitado, gate de UI nos botões de IA, testes SEM-EXTERNO verdes (contador 0 nas 3 rotas, controle positivo = 1, flags negadas a não-admin). Aguardando teste humano; depois a task de ACL (d3e7d6da).

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da task 490ec843 (botões de IA desabilitados em TESTE). Uma task por vez, com prova e confirmação humana.
