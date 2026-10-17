# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: 2e26d650 (listas na Fábrica e visão no Funil) — IMPLEMENTADA (v0.0.98/e86c82c publicada 17/10 17:45), aguardando teste humano
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-listas-unificadas-e-demonstracao.md (SPEC-1-006, CA-1-006-01)
- analise: .adapta-cliente/analises/2e26d650-listas-unificadas.md
- etapa: aguardando_teste_humano
- teste_humano: pendente (roteiro: navegação Listas → criar lista → vincular lead → Ver contato → conferir nota no LeadDetail)
- verificacao_automatica: passou (QA Skip verde; regras 66/66; backend 34/34 — LISTAS-UNIFICADAS verde; prova de clique: criar lista, vínculo, navegação lista→contato→notas no mesmo backend)
- aprendizado: registrado ($app não existe em migração — usar o app do callback; lista_membros já existia na 0016 e não foi recriada)
- sistema: funil-mkt
- skip_projeto: 50182
- skip_versao: e86c82c (v0.0.98)
- skip_publicacao: publicada:e86c82c em 2026-10-17T17:45:17Z
- pendente_github: espelho do submódulo funil-mkt (push direto falha por auth; sobe no próximo envio)
- ultima_acao: 2e26d650 implementada: migração 0027 (listas expandida: coorte/produto_alvo/filtros/fonte/status/ambiente + backfill legado=producao; delete de lista_membros liberado para admin), serviço listas.ts (vínculo idempotente de importados), página Listas.tsx + rota /listas + nav. lista_membros JÁ EXISTIA (0016) — não recriada. Prova de clique completa (criar lista → vínculo → Ver contato → notas)
- proxima_acao: aguardar teste humano do José
- atualizado_em: 2026-10-17T17:55:00-03:00 (implementação)
