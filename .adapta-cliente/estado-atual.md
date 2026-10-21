# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: 0cda0d05 (PROJECAO-LOCAL, SPEC-1-007) — IMPLEMENTADA E PROVADA (12/12 Funil + 9/9 Fábrica), aguardando teste humano
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007, CA-1-007-02)
- etapa: aguardando_teste_humano
- analise: .adapta-cliente/analises/0cda0d05-projecao-local.md
- teste_humano: pendente (roteiro enviado ao José: projeções nos 2 apps, duplicado, PATCH negado)
- verificacao_automatica: passou (Funil 12/12; Fábrica 9/9; contrato 8/8 nos 2 repos)
- aprendizado: Fábrica não tinha campo role em users (causa raiz do 400) — corrigido na 0010; leads não tem ambiente (lookup por nome_empresa); PocketBase normaliza data (2026-10-14 15:00:00.000Z); sync_inbox/empresas append-only (evento_id único por corrida nos testes)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.108/0495a0d) + 50248 (v0.0.26/3064db4)
- skip_publicacao: funil publicada:0495a0d 2026-10-21T20:21:16Z; fabrica publicada:3064db4 2026-10-21T20:44:10Z
- pendente_github: submódulos (espelho no fim da SPEC)
- ultima_acao: causa raiz do 400 corrigida (campo role + seed admin na Fábrica); prova da Fábrica 9/9
- proxima_acao: aguardar teste humano do José para fechar a 0cda0d05; depois acc7d79f (RECONCILIACAO)
- atualizado_em: 2026-10-21T22:00:00-03:00
