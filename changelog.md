# Histórico da construção

## 21/10/2026 20:30 — Task 199009b0 (CONTRATO-V1) FECHADA com aceite do José ("Pode prosseguir")

Provas revalidadas do zero: fonte pura idêntica (md5 fb564fe743ecc83b3e8f96a75289f918 nos 2 repos), golden fixture idêntica (e7207512), contrato.test.mjs 8/8 nos DOIS repos, ciclo pendente→aplicar→projeção→duplicado provado no Funil (empresa DEMO Indústria Ltda projetada com sync_global_id). Fase.md reconciliado com a verdade: Fase 1 22/22 marcada, 199009b0 [x], 0cda0d05 e acc7d79f abertas (o GitHub tinha as marcações zeradas — corrigido). Provas em 06_notas/evidencias/fase-1/spec-1-007/199009b0-provas.md. STATUS.md atualizado (Fase 1 encerrada, Fase 2 em execução). Plataformas dos 2 submódulos atualizadas e enviadas (funil d8bda5a, fábrica 904edab).

Clone local tinha divergido (10 commits órfãos pré-Fase 1 + 62 à frente no GitHub): conteúdo órfão já estava no GitHub via pushes do conector; clone realinhado ao origin/main com preservação das análises/evidências não enviadas.

## 21/10/2026 19:44 — Task 199009b0 (CONTRATO-V1) COMPLETA nos dois apps — ciclo de projeção provado

Correção da reavaliação de recibo (SPEC fluxo 3) aplicada nos dois hooks sync_aplicar: recibo pendente/rejeitado/atrasado é REAVALIADO com o mesmo evento_id; só aplicado/duplicado/conflito trava a redecisão. Ciclo completo provado no Funil: evento sem mapeamento → pendente (sem owner fallback); mapeamento aprovado → MESMO evento reavaliado → aplicar; projeção criada (empresa "DEMO Indústria Ltda" com sync_global_id=FAB:teste:empresa:EMP-DEMO-0001); repetição → duplicado (não duplica). Publicadas: Fábrica v0.0.23 (c133bdf), Funil v0.0.106 (a0a7c0a).

Resíduo para limpeza: 1 empresa projetada + 1 mapeamento + recibos de teste 0001-0004 (delete via API deu 403 — limpar via UI admin no próximo ciclo).

## 21/10/2026 19:35 — Task 199009b0 implementada nos DOIS apps (Fase 2 aberta)

Contrato comum v1: fonte pura idêntica byte a byte nos dois repos; testes 8/8 nos dois; Fábrica recebeu fundação SPEC-1-001 (gates; enviar-funil sempre bloqueado); coleções sync_inbox/sync_mapeamentos/aliases_identidade; rota sync/aplicar-fixture. Lições: JSVM (fonte inline no callback; role via getString; hooks desatualizados → recarga com apply).

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada. Fase 1: 22/22 tasks. Pendências pós-fase confirmadas: cadastro→lead e limpeza (152 conflitos + fixtures TESTE).
