# Histórico da construção

## 21/10/2026 20:44 — Task 0cda0d05 (PROJECAO-LOCAL) COMPLETA — 12/12 Funil + 9/9 Fábrica

Causa raiz do 400 na Fábrica encontrada e corrigida: a Fábrica (template original) NUNCA teve o campo role em users — a migração 0009 (regras) era inócua sem ele; a 0010 cria o select role (admin/vendedor) e semeia admin para usuários sem role (jose@: undefined → admin, confirmado). Prova da Fábrica 9/9: estado_comercial projeta primeira_abordagem_em literal + revisão 2; protecao_global projeta bloqueio/motivo; repetição = duplicado; PATCH direto na projeção NEGADO. Ajuste de asserção: PocketBase normaliza data para "2026-10-14 15:00:00.000Z". Publicada Fábrica v0.0.26 (3064db4, 20:44Z). CA-1-007-02 satisfeito nos dois lados. Falta: teste humano → fechar task → acc7d79f.

## 21/10/2026 20:21 — Task 0cda0d05 (PROJECAO-LOCAL) implementada nos 2 apps — Funil provado 12/12, Fábrica com impedimento mapeado

Funil (v0.0.108/0495a0d): hook sync_aplicar estendido — cadastro_upsert projeta pessoa; catalogo_aprovado projeta produto INATIVO; pontuacao_snapshot persiste nota LITERAL (valor/faixa/hashes/motivos/fontes, sem recalcular; lookup do lead por nome_empresa — leads não tem ambiente). Migração 0030 (sync_global_id em pessoas/produtos/pontuacoes) e 0031 (trava de autoridade: updateRule null em produtos/pontuacoes — projeção em leitura). Prova 12/12: projeções literais, duplicado, PATCH negado (admin e vendedor).

Fábrica (v0.0.24/ded36bb): migração 0008 (coleção comercial_projetado — escrita só por hook, leitura admin) + hook projeta estado_comercial (primeira_abordagem_em, revisão) e protecao_global (bloqueio/motivo); ausência de evento NUNCA desbloqueia.

Fixtures novas idênticas nos 2 repos: golden-pontuacao/golden-comercial/golden-protecao. Regressão do contrato 8/8 nos dois.

## 21/10/2026 20:30 — Task 199009b0 (CONTRATO-V1) FECHADA com aceite do José ("Pode prosseguir")

Provas revalidadas do zero: fonte pura idêntica (md5 fb564fe743ecc83b3e8f96a75289f918 nos 2 repos), golden fixture idêntica (e7207512), contrato.test.mjs 8/8 nos DOIS repos, ciclo pendente→aplicar→projeção→duplicado provado no Funil (empresa DEMO Indústria Ltda projetada com sync_global_id). Fase.md reconciliado com a verdade: Fase 1 22/22 marcada, 199009b0 [x], 0cda0d05 e acc7d79f abertas (o GitHub tinha as marcações zeradas — corrigido). Provas em 06_notas/evidencias/fase-1/spec-1-007/199009b0-provas.md.

Clone local tinha divergido (10 commits órfãos pré-Fase 1 + 62 à frente no GitHub): conteúdo órfão já estava no GitHub via pushes do conector; clone realinhado ao origin/main com preservação das análises/evidências não enviadas.

## 21/10/2026 19:44 — Task 199009b0 (CONTRATO-V1) COMPLETA nos dois apps — ciclo de projeção provado

Correção da reavaliação de recibo (SPEC fluxo 3) aplicada nos dois hooks sync_aplicar: recibo pendente/rejeitado/atrasado é REAVALIADO com o mesmo evento_id; só aplicado/duplicado/conflito trava a redecisão. Ciclo completo provado no Funil: evento sem mapeamento → pendente (sem owner fallback); mapeamento aprovado → MESMO evento reavaliado → aplicar; projeção criada (empresa "DEMO Indústria Ltda" com sync_global_id=FAB:teste:empresa:EMP-DEMO-0001); repetição → duplicado (não duplica). Publicadas: Fábrica v0.0.23 (c133bdf), Funil v0.0.106 (a0a7c0a).

Resíduo para limpeza: fixtures de prova no banco (delete via API deu 403 — limpar via UI admin no próximo ciclo).

## 21/10/2026 19:35 — Task 199009b0 implementada nos DOIS apps (Fase 2 aberta)

Contrato comum v1: fonte pura idêntica byte a byte nos dois repos; testes 8/8 nos dois; Fábrica recebeu fundação SPEC-1-001 (gates; enviar-funil sempre bloqueado); coleções sync_inbox/sync_mapeamentos/aliases_identidade; rota sync/aplicar-fixture. Lições: JSVM (fonte inline no callback; role via getString; hooks desatualizados → recarga com apply).

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada. Fase 1: 22/22 tasks. Pendências pós-fase confirmadas: cadastro→lead e limpeza (152 conflitos + fixtures TESTE).
