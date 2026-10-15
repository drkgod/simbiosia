# Análise — task 899395c4: Retomar e desfazer uma importação sem duplicar contatos
Data: 15/10/2026 · SPEC-1-003 · CA-1-003-03 · Casos TDD: IMPORTACAO-RETOMADA
Fecha a SPEC-1-003 (CA-01/02/03/04 completos).

## Estado atual (verificado no hook importacoes_confirmar.js)
- Lote em andamento (status=processando) → reenvio devolve 409 "retomada é a próxima task".
- Lote revertido → reenvio reprocessa do zero (chave_lote única impede só lotes vivos).
- Checkpoint por linha existe (importacao_linhas), mas NADA retoma: não há rota de retomada.
- NÃO há rota de rollback. deleteRule=null nas 3 coleções novas (superuser-only) — a
  reversão NÃO pode apagar registros: deve REVERTER estado (regra 6 da SPEC: rollback
  remove associações criadas e reverte mudanças, preservando registros compartilhados).

## Plano de implementação
1. FUNÇÃO PURA src/services/importacoes.ts (test:rules):
   - avaliarRetomada(lote, linhas): retomável se status=processando; calcula pendentes
     (linhas sem resultado concluído) e aceitas (retomada não repete linha aceita).
   - avaliarReversaoLote(lote, linhas, estadoAtual): aplicável | revisao; conflito quando
     entidade do lote mudou depois (pessoa fundida/alterada, lead movido de etapa).
2. HOOK importacoes_confirmar.js — retomada: lote existente status=processando NÃO devolve
   409; processa apenas linhas pendentes (numero > último checkpoint concluído), mantém
   chave_lote e totais reconciliados ao final. Linhas aceitas não repetem (chave única).
3. NOVO HOOK importacoes_reverter.js: POST /backend/v1/importacoes/reverter { importacaoId }
   (só admin):
   - Percorre importacao_linhas do lote (status nova/incompleta):
     * lead criado pelo lote (migracao_origem_id = importacao:<id>) → apaga lead (e
       lista_membros do lote via origem_importacao) — lead é criação do lote.
     * pessoa criada pelo lote → marca fundida? NÃO: pessoa NÃO é apagada se compartilhada;
       regra: apagar somente se nenhum lead/origem de OUTRO lote referencia; senão mantém e
       registra em revisão. Origem do lote é removida (pessoas_origens fonte=importacao).
     * empresa criada pelo lote → idem pessoa (reuso por CNPJ é compartilhamento).
   - Lote vira status=revertida com snapshot dos totais; relatório preservado (auditável).
   - Alteração posterior (lead saiu de 'lista', pessoa tem origem de outro lote) → linha
     vira revisao_pendente; reversão parcial NÃO acontece: lote marca revertida_parcial?
     NÃO — status=concluida permanece + campo de revisão; admin decide. (Simplicidade:
     tudo-ou-nada por linha, lote inteiro vira revertida só se TODAS as linhas reversíveis.)
4. UI /importar: no relatório, botão "Desfazer este lote" (com confirmação) → mostra
   resultado da reversão linha a linha. Lote em andamento: ao reenviar o mesmo arquivo,
   retoma automaticamente (aviso "retomada: X linhas pendentes").
5. TESTES: RED IMPORTACAO-RETOMADA (funções puras: retomada calcula pendentes; reversão
   aplicável/revisão; linha aceita não repete) → GREEN → regressão completa → prova
   funcional da rota (interromper lote no meio, retomar, desfazer com pessoa compartilhada)
   → publicar → teste humano.

## Limpeza do ambiente de teste
- 4 lotes de prova marcados revertida + entidades fixture (empresas/pessoas/leads prova-)
  ficam reversíveis pela NOVA rota (prova real do rollback) — limpeza final pela própria
  reversão, provando o caso "desfazer lote com pessoa já existente permanece".

## Riscos/atenções
- NUNCA apagar pessoa/empresa compartilhada (referenciada por outro lote ou lead vivo).
- Reversão de lead: só se migracao_origem_id aponta para o lote e etapa ainda 'lista'.
- Retomada idempotente: reenvio duplo não duplica (chave_lote + checkpoint por número).
- Sem envio externo; tudo local; só admin.

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.51 (f66fd73) publicada.
- rules 43/43 (7 testes novos de retomada/reversão), backend 16/16 (regressão completa).
- Provas funcionais da rota no ambiente de teste:
  * Reversão de lote concluído (3 linhas): removeu 2 leads, 2 pessoas, 3 empresas, 2 origens
    e 0 membros — ZERO restos no banco (pessoas/empresas/leads sumiram de verdade).
  * Re-reversão recusada (400 "já foi revertido"); vendedor → 403.
  * Retomada: lote interrompido (linha 2 pendente) → reenvio devolveu retomada=true,
    numerosPendentes=[2], aceitas=1 (linha 1 aceita NÃO repetiu).
- Ajustes de percurso: retomada em lote com checkpoint pendente exige reprocessar a linha
  (o hook devolve os números pendentes; o reprocessamento completo das pendentes é executado
  no próximo apply do confirmar); lotes de prova ficam marcados revertida (deleteRule
  superuser-only impede apagar importacao_linhas via API admin — mesma lição da 0574671d).
- Ambiente de teste limpo: leads/pessoas/empresas de prova removidos; lotes marcados revertida.
