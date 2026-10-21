# Histórico da construção

## 21/10/2026 19:44 — Task 199009b0 (CONTRATO-V1) COMPLETA nos dois apps — ciclo de projeção provado

Correção da reavaliação de recibo (SPEC fluxo 3) aplicada nos dois hooks sync_aplicar: recibo pendente/rejeitado/atrasado é REAVALIADO com o mesmo evento_id; só aplicado/duplicado/conflito trava a redecisão. Ciclo completo provado no Funil: evento sem mapeamento → pendente (sem owner fallback); mapeamento aprovado → MESMO evento reavaliado → aplicar; projeção criada (empresa "DEMO Indústria Ltda" com sync_global_id=FAB:teste:empresa:EMP-DEMO-0001); repetição → duplicado (não duplica). Publicadas: Fábrica v0.0.23 (c133bdf), Funil v0.0.106 (a0a7c0a).

Resíduo para limpeza: 1 empresa projetada + 1 mapeamento + recibos de teste 0001-0004 (delete via API deu 403 — limpar via UI admin no próximo ciclo).

## 21/10/2026 19:35 — Task 199009b0 implementada nos DOIS apps (Fase 2 aberta)

Contrato comum v1: fonte pura idêntica byte a byte nos dois repos; testes 8/8 nos dois; Fábrica recebeu fundação SPEC-1-001 (gates; enviar-funil sempre bloqueado); coleções sync_inbox/sync_mapeamentos/aliases_identidade; rota sync/aplicar-fixture. Lições: JSVM (fonte inline no callback; role via getString; hooks desatualizados → recarga com apply).

## 21/10/2026 14:41 — FASE 1 ENCERRADA — DEMO-F1 aprovada pelo José ("Funcionou")

Demonstração guiada em 8 etapas aprovada. Fase 1: 22/22 tasks. Pendências pós-fase confirmadas: cadastro→lead e limpeza (152 conflitos + fixtures TESTE).
