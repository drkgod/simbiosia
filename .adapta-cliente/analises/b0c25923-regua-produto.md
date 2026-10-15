# Análise — task b0c25923: Editar e aprovar a régua de cada produto
Data: 15/10/2026 · SPEC-1-004 · CA-1-004-02/04 · Casos TDD: REGUA-VALIDACAO; REGUA-VERSAO
Última da SPEC-1-004 (fecha a SPEC).

## Objetivo
Editor de régua por produto: critérios (termos simples e grupos AND/OR com peso no termo),
regras eliminatórias, faixas A/B/C/D, método ponderado/pontos — com validação rigorosa,
VERSIONAMENTO com hash e APROVAÇÃO registrada. Editar cria rascunho novo; versão aprovada
é imutável. Sem eval.

## Plano de implementação
1. FUNÇÃO PURA src/services/regua.ts: validarCriterios, validarFaixas, validarRegua,
   hashRegua (canonizado), somaPesos.
2. MIGRAÇÃO 0021: regras_produto (produto, versao, status, metodo, criterios,
   regras_eliminatorias, faixas, hash, aprovado_por/em, ambiente; índice único
   produto+versao+ambiente; RLS admin).
3. HOOK regua_validar.js (inline): valida conteúdo, versão = próxima livre, hash no
   servidor, aprovada imutável, aprovação registra autor/data, delete só rascunho.
4. UI: aba Régua na /produtos (editor + lista de versões + aprovar/arquivar).
5. TESTES: RED REGUA-VALIDACAO/REGUA-VERSAO → GREEN → regressão → publicar → teste humano.

## Riscos/atenções
- Sem eval/código do usuário — critérios são dados estruturados.
- Versão aprovada imutável (prova no backend); edição cria rascunho novo.
- Aprovação NÃO ativa o produto (amarração régua→produto na SPEC-1-005).
- Hash calculado no servidor sobre conteúdo canonizado.

## Resultado da implementação (15/10/2026)
- Implementado conforme o plano. v0.0.65 (718ad2f) publicada.
- backend 21/21 (REGUA-VERSAO de ponta a ponta), rules 57/57 (REGUA-VALIDACAO completa).
- Ajustes de percurso:
  * Hook com funções top-level → QA do Skip recusa (scoping da VM) — lógica inline
    (mesma lição da 46c0fd51, agora no runbook).
  * Contrato de faixas: borda compartilhada com inclusão explícita (A 0-50 inclusiva,
    B 50-75 exclusiva) — sem sobreposição nem lacuna no domínio contínuo.
  * Fixture de régua aprovada permanece no banco (imutável e não-apagável por design —
    histórico preservado); o teste foi ajustado para não apagar o produto com régua
    aprovada.
  * Placeholder com aspas → expressão JSX.
