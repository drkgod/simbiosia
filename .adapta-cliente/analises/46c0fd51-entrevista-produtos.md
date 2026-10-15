# Análise — task 46c0fd51: Registrar a entrevista de produtos e critérios
Data: 15/10/2026 · SPEC-1-004 · CA-1-004-03 · Caso TDD: ENTREVISTA

## Objetivo
Registrar no sistema a entrevista que o Maestro conduz sobre cada produto: roteiro fixo,
respostas persistidas (inclusive incompletas, com pendências), SEM permitir confirmar
entrevista incompleta. Produto/régua permanecem inativos — aprovação da régua é b0c25923.

## Plano de implementação
1. MIGRAÇÃO 0020: entrevista_produtos (produto rel required, roteiro/respostas/pendencias
   json, status rascunho/confirmada, confirmada_por/em, ambiente; índice único produto+ambiente).
2. FUNÇÃO PURA src/services/entrevista.ts: ROTEIRO fixo (10 perguntas do fluxo da SPEC) +
   avaliarEntrevista(respostas) → { completa, pendencias }.
3. HOOK entrevista_validar.js: confirmação com pendência NEGADA no backend; autor/data
   registrados no servidor; confirmação NÃO ativa produto.
4. UI: seção de entrevista na /produtos (roteiro, pendências, salvar rascunho/confirmar).
5. TESTES: RED ENTREVISTA → GREEN → regressão → publicar → teste humano.

## Riscos/atenções
- NÃO inventar dados reais: respostas vêm do José; fixture usa produto TESTE.
- Confirmação da entrevista NÃO ativa produto nem régua (aprovação é b0c25923).
- Uma entrevista por produto+ambiente (índice único) — reentrevista edita a existente.

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.61 (6a4b542) publicada.
- backend 20/20 (ENTREVISTA: confirmação com pendência negada 400; completa registra
  autor/data; produto continua inativo), rules 47/47 (roteiro fixo + pendências legíveis).
- Ajustes de percurso:
  * src/services/entrevista.ts gravado local antes do Skip — regravado no projeto.
  * Hook com função top-level → QA do Skip recusa (scoping da VM de callbacks) — lógica
    inline no callback (mesma lição do runbook: funções necessárias ficam no próprio
    callback ou geradas inline).
  * Teste backend: owner é required na entrevista_produtos (adicionado ao teste);
    roteiro completo exige as 10 respostas (teste corrigido de 3 para 10).
  * SEM-EXTERNO com janela de 60s falha quando execuções repetidas rodam em sequência
    (despachos de controle positivo de execuções anteriores contaminam a janela) —
    isolado e na sequência normal, passa (20/20).
