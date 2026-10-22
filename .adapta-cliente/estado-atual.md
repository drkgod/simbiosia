# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: nenhuma — SPEC-1-007 COMPLETA (3/3); Fase 2 ENCERRADA; pendências pós-fase ZERADAS
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- etapa: cadastro→lead implementado e publicado (Funil v0.0.125/17075f0) — aguardando teste humano do José
- teste_humano: pendente (roteiro: Cadastro → aba Pessoas → "Ana Demo" já tem lead gerado; para novo teste: cadastrar pessoa → selecionar empresa → "Gerar lead"; conferir lead em Leads com rastro cadastro:)
- verificacao_automatica: passou (prova 5/5: criar/conteúdo/idempotência/pedido de saída 403/empresa inválida 400; prova de clique na UI: aviso "Lead criado a partir do cadastro (etapa: lista)" + lead no banco)
- aprendizado: publish é passo SEPARADO do apply (CDN só atualiza após skip_project_publish); empresas/pessoas no Funil têm deleteRule null — fixtures de prova só via hook admin temporário ($app.delete), removido após uso
- sistema: funil-mkt
- skip_projeto: 50182 (v0.0.125/17075f0) + 50248 (v0.0.32/d69cbcb)
- skip_publicacao: funil publicada:17075f0 2026-10-22T12:51:40Z; fabrica publicada:d69cbcb 2026-10-22
- pendente_github: nao (hook cadastro_gerar_lead no GitHub do submódulo; demais arquivos no clone local)
- ultima_acao: cadastro→lead no ar (hook + botão UI + provas); estado de teste deixado no ar (Ana Demo/Demo Comércio Ltda)
- proxima_acao: aceite do José no cadastro→lead; itens 2–3 da sugestão (fumaça com dado real; cobranças ADAPTA); definir próxima fase
- atualizado_em: 2026-10-22T10:05:00-03:00 (cadastro→lead no ar)
