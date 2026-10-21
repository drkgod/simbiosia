# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: 199009b0 (CONTRATO-V1, SPEC-1-007) — IMPLEMENTADA E PROVADA nos 2 apps (Fábrica v0.0.23/c133bdf + Funil v0.0.106/a0a7c0a, publicadas 21/10 19:44), aguardando teste humano do José
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007, CA-1-007-01)
- etapa: fase_2_em_execucao (199009b0, aguardando_teste_humano)
- teste_humano: roteiro: aplicar fixture sem mapeamento → pendente; aprovar mapeamento → MESMO evento reavaliado → aplicar com projeção; repetir → duplicado
- verificacao_automatica: passou (contrato.test.mjs 8/8 nos DOIS repos; fonte/fixture idênticas byte a byte; ciclo pendente→aplicar→projeção→duplicado provado no Funil)
- aprendizado: JSVM — fonte inline no callback; role via getString('role'); recibo pendente/rejeitado/atrasado deve ser REAVALIADO (SPEC fluxo 3); delete das coleções sync é admin-only (fixtures de prova ficaram no banco — limpar via UI)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.106/a0a7c0a) + 50248 (v0.0.23/c133bdf)
- skip_publicacao: fábrica publicada:c133bdf 2026-10-21T19:43:53Z; funil publicada:a0a7c0a 2026-10-21T19:44:00Z
- pendente_github: espelho dos submódulos (push direto falha por auth; sobe no próximo envio)
- ultima_acao: reavaliação de recibo pendente corrigida nos 2 apps; ciclo completo provado (evento 0004 aplicou com mapeamento e criou a empresa DEMO com identidade global; repetição = duplicado)
- proxima_acao: (1) teste humano do José na 199009b0; (2) limpeza das fixtures de prova (1 empresa DEMO + 1 mapeamento + recibos 0001-0004 — delete deu 403 via API); (3) depois: 0cda0d05 (PROJECAO-LOCAL); (4) pendências pós-Fase 1: cadastro→lead e limpeza
- atualizado_em: 2026-10-21T19:45:00-03:00 (199009b0 provada de ponta a ponta; aguardando teste humano)
