# Estado atual — Adapta Cliente

- schema_version: adapta-cliente-state/v2
- task_id: bf09d372 (filtrar listas por coorte e produto) — IMPLEMENTADA (v0.0.99/9c3ac61 publicada 17/10 18:04), aguardando teste humano. Anterior 2e26d650 FECHADA ("Funcionou")
- executor: Jarbas (assistente)
- owner_informativo: José Aquino
- spec: 04_fase-atual/specs/spec-listas-unificadas-e-demonstracao.md (SPEC-1-006, CA-1-006-02)
- analise: .adapta-cliente/analises/bf09d372-filtros-listas.md
- etapa: aguardando_teste_humano
- teste_humano: roteiro bf09d372: /listas → filtrar por coorte e por produto-alvo → conferir contagem na coluna Membros → selecionar produto → conferir nota por membro na lista aberta
- verificacao_automatica: passou (QA verde; rules 66/66; backend 35/35 — LISTAS-FILTROS verde; prova de clique: card Filtros renderiza, contagem "4 de 4", coluna Membros presente)
- aprendizado: scoring/calcular exige régua aprovada (teste corrigido com régua no padrão 2 passos: create rascunho → update aprovada)
- sistema: funil-mkt
- skip_projeto: 50182
- skip_versao: 9c3ac61 (v0.0.99)
- skip_publicacao: publicada:9c3ac61 em 2026-10-17T18:04:52Z
- pendente_github: espelho do submódulo funil-mkt (push direto falha por auth; sobe no próximo envio)
- ultima_acao: bf09d372 implementada: card Filtros (coorte + produto-alvo, ativos), coluna Membros com contagens (1 consulta agregada), coluna "Nota — {produto}" nos membros (nota mais recente por lead de pontuacoes append-only), serviço getContagens/getNotasProduto
- proxima_acao: aguardar teste humano do José; depois 196f75e2 (demonstração da fase 1) fecha a SPEC-1-006 e a Fase 1
- atualizado_em: 2026-10-17T18:10:00-03:00 (bf09d372 implementada)
