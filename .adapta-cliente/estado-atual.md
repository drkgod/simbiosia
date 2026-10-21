# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: 199009b0 (CONTRATO-V1, SPEC-1-007) — IMPLEMENTADA EM 2 APPS (Fábrica v0.0.22/044ceff + Funil v0.0.105/e345972, publicadas 21/10 19:35), aguardando 1 correção de reavaliação de recibo pendente + teste humano
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007, CA-1-007-01)
- analise: 06_notas/evidencias/fase-1/spec-1-007/ (a criar no próximo ciclo)
- etapa: fase_2_em_execucao (199009b0)
- teste_humano: pendente — após correção da reavaliação de recibo pendente
- verificacao_automatica: passou (contrato.test.mjs 8/8 nos DOIS repos; fonte e fixture idênticas byte a byte; prova funcional da rota: pendente/duplicado/rejeitado corretos)
- aprendizado: JSVM — declarações top-level não visíveis em callbacks (fonte inline no callback); auth.role é undefined no JSVM (usar getString('role')); hooks podem ficar desatualizados na instância (recarga com apply)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.105) + 50248 (v0.0.22)
- skip_publicacao: fábrica publicada:044ceff 2026-10-21T19:35:50Z; funil publicada:e345972 2026-10-21T19:35:56Z
- pendente_github: espelho dos submódulos (push direto falha por auth; sobe no próximo envio)
- ultima_acao: 199009b0 implementada nos 2 apps (contrato v1 + fundação da Fábrica + coleções sync + rota aplicar-fixture). Prova funcional: pendente/duplicado/rejeitado corretos
- proxima_acao: (1) CORRIGIR reavaliação de recibo pendente no sync_aplicar (SPEC fluxo 3 — hoje recibo pendente trava e caminho aplicar devolve duplicado); (2) reprovar caminho aplicar com mapeamento; (3) teste humano do José; (4) pendências pós-Fase 1: cadastro→lead e limpeza
- atualizado_em: 2026-10-21T19:40:00-03:00 (199009b0 implementada em 2 apps)
