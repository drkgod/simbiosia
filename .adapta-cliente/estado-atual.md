# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007) — IMPLEMENTADA, prova do Funil 12/12; lado Fábrica com impedimento (role do usuário) — em_correcao
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007, CA-1-007-02)
- etapa: em_correcao (impedimento: create de sync_mapeamentos na Fábrica devolve 400 — provável role do usuário jose@ não preenchida; correção mapeada)
- analise: .adapta-cliente/analises/0cda0d05-projecao-local.md
- teste_humano: pendente (Funil testável agora: fixture projeta empresa/pessoa/produto/nota literal; repetir não duplica; PATCH negado)
- verificacao_automatica: parcial (Funil 12/12; contrato 8/8 nos 2 repos; Fábrica aguarda correção de role)
- aprendizado: JSVM — fonte inline no callback; role via getString('role'); leads NÃO tem campo ambiente (lookup por nome_empresa); sync_inbox/empresas append-only (resíduo permanece — usar evento_id único por corrida nos testes)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.108/0495a0d) + 50248 (v0.0.24/ded36bb)
- skip_publicacao: funil publicada:0495a0d 2026-10-21T20:21:16Z; fabrica publicada:ded36bb 2026-10-21T20:21:22Z
- pendente_github: submódulos (espelho dos hooks/migrações/testes novos — fonte executável é o Skip)
- ultima_acao: 0cda0d05 implementada nos 2 apps; prova do Funil 12/12; prova da Fábrica travou no create do mapeamento (400)
- proxima_acao: corrigir role do usuário na Fábrica → reexecutar prova da Fábrica → teste humano dos dois lados
- atualizado_em: 2026-10-21T21:45:00-03:00
