# Análise — task 0574671d: Importar a base e mostrar o relatório por linha
Data: 15/10/2026 · SPEC-1-003 · CA-1-003-02 · Caso TDD: IMPORTACAO-RELATORIO

## Objetivo
Confirmar a prévia (c7240db4) → processar o lote → relatório por linha
(nova/repetida/incompleta/erro) com totais reconciliados; sem duplicar, sem inventar
pessoa ou fonte. Rollback NÃO entra aqui (é a task 899395c4).

## Fatos verificados
- Migrações aplicadas até 0015 (fusao_reversivel) → próximo ordinal livre: 0016.
- leads: owner (relation, required), nome_empresa (required), origem select [inbound,outbound],
  etapa select [lista..perdido], contato_nome/email/telefone, score, observacoes.
- Identidade pronta: resolverIdentidade (reusar/criar/conflito), eEmailGenerico,
  normalizarEmail/Telefone/Linkedin (cadastro.ts). Hook pessoas_identidade nega duplicata.
- Coorte: pessoas_origens (coorte select), hook pessoas_origens_coorte protege conhecido na fria.
- ACL: regraAdmin / regraDono (admin tudo; vendedor só owner). Hooks com e.next() obrigatório.
- csv.ts (c7240db4): parser puro + neutralizarFormula — prévia sem escrita já validada.

## Plano de implementação
1. FUNÇÃO PURA src/services/importacoes.ts (test:rules, IMPORTACAO-RELATORIO):
   - classificarLinha(linha, contexto): nova | repetida | incompleta | erro + motivo legível.
     * sem razao_social → erro; e-mail genérico ou sem contato individual → incompleta
       (não inventar decisor); resolverIdentidade → reusar (repetida) / criar (nova) /
       conflito (erro → revisão).
   - reconciliarTotais: nova+repetida+incompleta+erro = total; categorias mutuamente exclusivas.
   - chaveLote(arquivo_hash, mapeamento, lista, coorte, dono, ambiente): repetir devolve
     relatório existente (não duplica).
2. MIGRAÇÃO 0016 (aditiva, RLS admin):
   - importacoes: arquivo_hash, lista(rel), coorte(select), dono(rel users), ambiente(select),
     status(previa/processando/concluida/falha/revertida), total, novas, repetidas,
     incompletas, erros, created/updated.
   - importacao_linhas: importacao(rel cascade), numero, chave_linha, status, erros,
     pessoa/empresa/lead(rel), resultados_antes/depois(json); índice único (importacao,numero).
   - lista_membros: lista(rel), lead(rel), produto(rel opc), origem_importacao(rel),
     ambiente; índice único (lista,lead,ambiente) — associação repetida não duplica membro.
3. HOOK pocketbase/hooks/importacoes_confirmar.js: POST /backend/v1/importacoes/confirmar
   (só admin):
   - Recebe { arquivo_hash, mapeamento, linhas, lista, coorte, dono, ambiente }.
   - Lote existente concluído → devolve relatório existente (sem reescrever).
   - Linha a linha com checkpoint persistido: classifica → aplica (empresa/pessoa/lead
     via identidade) → grava importacao_linha com resultados_antes/depois; falha de linha
     vira status=erro e o lote continua (checkpoint preservado). Transacional POR LINHA.
   - Final: importacoes.status=concluida + totais reconciliados. Sem rede externa.
4. UI src/pages/Importar.tsx: passo de confirmação (lista/coorte/dono/ambiente + revisão do
   mapeamento) → botão "Confirmar importação" → relatório por linha + totais + aviso de que
   repetir devolve o mesmo relatório.
5. TESTES: RED IMPORTACAO-RELATORIO (fixture 6 linhas: nova, repetida individual, empresa
   sem pessoa, contato genérico, colisão, linha inválida) → GREEN (28 atuais + novos) →
   regressão completa (rules + backend) → publicar → teste humano.

## Riscos/atenções
- Não duplicar: identidade contra TODA a base do ambiente (não só o arquivo).
- Não inventar: empresa sem pessoa → incompleta; e-mail genérico não vira pessoa.
- Fria não sobrescreve origem conhecida (hook existente protege).
- Rollback fica para 899395c4 — aqui só relatório + checkpoint.
- RLS: admin importa; vendedor só entidades atribuídas.

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.50 (825971e) publicada.
- rules 36/36 (8 testes novos), backend 16/16 (regressão completa).
- Prova funcional da rota no ambiente de teste: fixture de 6 linhas →
  1 nova / 2 repetidas / 2 incompletas / 1 erro; reconciliação ok;
  repetição devolveu o MESMO relatório (sem duplicar); vendedor → 403.
- Ajustes de percurso: campo produto removido da lista_membros (coleção produtos não existe
  ainda); lint exigiu comentário em catch vazio; transpilador de regras agora reescreve
  imports relativos para .mjs; deleteRule=null nas coleções novas exige superuser — lotes de
  prova ficam marcados revertida (limpeza física no rollback da 899395c4).
