# Estado da construção

Atualizado: 14/10/2026
Fase atual: 1 — em execução (task 1 publicada, aguardando teste humano).

Seis SPECs e 19 tasks abertas em 04_fase-atual/, com UUIDs preservados. Os cards continuam internos conforme retorno do portal; responsáveis/prazos não atribuídos por inferência.

Sistema único com Funil MKT como base; Fábrica como módulo de reuso. Task 20c86bd2 (SPEC-1-001) implementada e publicada no Skip (v0.0.19, ref 88790b4): migração 0008 (sistema_config + teste_despachos), despachos de IA instrumentados e neutralizados em TESTE, runners reais node:test, manifesto de versão e backup/restauração de fixtures sintéticas, selo AMBIENTE DE TESTE na UI.

Repositório operacional privado: https://github.com/drkgod/simbiosia. Publicação autorizada pelo consultor em 14/10/2026. Os dois projetos de origem são referenciados como submódulos; usar clone com --recurse-submodules.

Próxima ação: teste humano da task 20c86bd2 na URL de produção (badge TESTE, interceptação de IA com contador, prova de restauração). Uma task por vez, com prova e confirmação humana.
