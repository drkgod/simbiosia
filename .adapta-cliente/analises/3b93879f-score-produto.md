# Análise — task 3b93879f: Mostrar a pontuação de cada produto com os motivos
Data: 15/10/2026 · SPEC-1-005 · CA-1-005-01/02 · Casos TDD: SCORE-PRODUTO; SCORE-BORDAS
Primeira da SPEC-1-005 (pontuação por produto).

## Objetivo
Motor determinístico que aplica a régua APROVADA de um produto aos dados de um lead
(empresa/pessoa) e mostra valor + faixa + MOTIVOS. Persistência por produto (pontuacoes)
com snapshot idempotente. UI lê o retorno do backend — nunca recalcula no navegador.

## Plano de implementação
1. MOTOR PURO scripts/domain/scoring-core.js (fonte única, sem rede/estado).
2. GERADOR scripts/gerar-scoring-core.mjs: embute a fonte no hook (VM própria) +
   teste SCORE-FONTE confere correspondência.
3. MIGRAÇÃO 0022: pontuacoes (idempotente lead×produto×versao×dados_hash) +
   leads.primeira_abordagem_em.
4. HOOK scoring_calcular.js: auth/papel/dono/ambiente/produto; régua APROVADA mais
   recente (sem régua → pendente); motor inline; persiste idempotente.
5. UI: seção Pontuação por produto no LeadDetail (só exibe retorno do backend).
6. TESTES: RED SCORE-PRODUTO/SCORE-BORDAS (fixture da SPEC) → GREEN → regressão →
   prova funcional → publicar → teste humano.

## Riscos/atenções
- Motor puro SEM rede/estado/Node API.
- Sem régua aprovada → pendente (nunca régua genérica).
- UI não recalcula — só exibe retorno do backend.
- Fixture TESTE explícita; nunca réguas reais do José.

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.69 (2ccd3a4) publicada.
- rules 65/65 (8 novos: fixture da SPEC 4 resultados, bordas, eliminatória, pendente,
  opcional ausente, grupos AND/OR, correspondência da fonte), backend 21/21.
- Prova funcional da rota: lead1×A=100/A, lead1×B=0/D, lead2×A=40/C, lead2×B=100/A
  (fixture da SPEC completa); idempotência ok; sem régua → pendente; vendedor 403 em
  lead alheio.
- Ajustes de percurso:
  * Bug real do motor achado pelo TDD: avaliador de termo retorna boolean e o de grupo
    retorna string — a comparação com 'satisfeito' não via boolean; corrigido com
    (r === 'satisfeito' || r === true).
  * SCORE-FONTE: teste e gerador usam a mesma limpeza (remove a linha export inteira).
  * Régua nasce rascunho (aprovação explícita) — a prova cria v1 e aprova.
  * pontuacoes deleteRule=null (superuser) — snapshots de fixture permanecem no banco
    (limpeza completa é do job da 2faac03b).
