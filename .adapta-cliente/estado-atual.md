# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: acc7d79f (CONTRATO-RECONCILIACAO, SPEC-1-007) — PRÓXIMA (0cda0d05 FECHADA com aceite do José, "Funcionou" 21/10 18:41)
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-identidades-e-projecoes-entre-apps.md (SPEC-1-007, CA-1-007-03)
- etapa: aguardando_autorizacao (análise da acc7d79f)
- analise: .adapta-cliente/analises/0cda0d05-projecao-local.md (fechada); acc7d79f em análise
- teste_humano: 0cda0d05 aprovado pelo José; acc7d79f não iniciada
- verificacao_automatica: passou (0cda0d05: Funil 12/12; Fábrica 9/9; contrato 8/8 nos 2 repos)
- aprendizado: Fábrica não tinha campo role em users (causa raiz do 400) — corrigido na 0010; leads não tem ambiente (lookup por nome_empresa); PocketBase normaliza data (2026-10-14 15:00:00.000Z); sync_inbox/empresas append-only (evento_id único por corrida nos testes); trava de autoridade precisa de UI adequada (selo visível, sem jargão)
- sistema: funil-mkt + fabrica-listas
- skip_projeto: 50182 (v0.0.111/996d643) + 50248 (v0.0.26/3064db4)
- skip_publicacao: funil publicada:996d643 2026-10-21T21:40:19Z; fabrica publicada:3064db4 2026-10-21T20:44:10Z
- pendente_github: submódulos (espelho no fim da SPEC)
- ultima_acao: 0cda0d05 fechada com aceite; análise da acc7d79f em andamento
- proxima_acao: concluir análise da acc7d79f e apresentar plano; aguardar autorização
- atualizado_em: 2026-10-21T22:50:00-03:00 (0cda0d05 fechada)
