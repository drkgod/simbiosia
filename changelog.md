# Histórico da construção

## 22/10/2026 11:46 — Task acc7d79f (CONTRATO-RECONCILIACAO) implementada e provada — 14/14 Funil + 12/12 Fábrica

Fonte pura scripts/domain/simbiosia-reconciliacao.js idêntica nos 2 apps (aliasDaFusao, aliasRevogado, identidadeEfetiva, cadenciasParaPausar, decidirReconciliacao). Hook do Funil: identidade_alias/identidade_revogacao gravam alias local e PAUSAM (cancelam) cadências agendadas dos dois contextos fundidos — humano decide a continuidade; reversão preserva histórico. Hook da Fábrica: grava alias (fonte canônica).

Correções do ciclo: (1) comparação de revisão por CHAVE DE NEGÓCIO (ambiente/origem/domínio/entidade) — antes só por evento_id, evento novo da mesma chave/rev com hash diferente era aplicado em vez de conflito; (2) recibo 'duplicado' também conta na chave; (3) nota projetada idempotente por chave (repetição não viola índice único); (4) busca por chave sem limite; (5) fix de escopo no hook da Fábrica (alias → 500). Caso 7 do TDD: evento antigo após revogação → atrasado/duplicado, NUNCA reativa alias.

Publicadas: Funil v0.0.118 (58773c6), Fábrica v0.0.29 (c603535) — 22/10 11:46Z. Regressão: contrato 8/8 nos 2. Aguardando teste humano.

## 21/10/2026 21:45 — Task 0cda0d05 (PROJECAO-LOCAL) FECHADA com aceite do José ("Funcionou", 18:41)

CA-1-007-02 satisfeito nos dois lados (12/12 Funil + 9/9 Fábrica). Versões finais: Funil v0.0.111, Fábrica v0.0.26. 6 correções no ciclo, incluindo 2 defeitos de UX pegados pelo teste humano (projeções apagadas pela limpeza; formulário de edição com jargão "Only superusers" → selo "Projetado da Fábrica · somente leitura").

## 21/10/2026 20:30 — Task 199009b0 (CONTRATO-V1) FECHADA com aceite do José ("Pode prosseguir")

Fonte pura idêntica (md5 fb564fe7), fixture idêntica (e7207512), testes 8/8 nos 2 repos, ciclo pendente→aplicar→projeção→duplicado provado. Fase.md reconciliado (Fase 1 22/22).

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada. Fase 1: 22/22 tasks.
