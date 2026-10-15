# Histórico da construção

## 14/10/2026 — Task 1b7f6fc7 implementada (skip 9d985f7)

FUSAO-REVERSIVEL verde (SPEC-1-002, CA-1-002-04): migração 0015 (fundida_em em pessoas/empresas — origem marcada, nada apagado); rotas /backend/v1/cadastro/fundir e /reverter-fusao (só admin com getString(role); snapshot antes + vínculos movidos; reversão restaura vínculos/estado e marca revertida; alteração posterior → 409 revisao_pendente SEM sobrescrever); função pura avaliarReversao (snapshot vs estado atual → aplicavel/revisao); aba Fusões em /cadastro. Correção no meio: e.auth.role (propriedade) não existe no record do hook — e.auth.getString("role") sim (padrão do atribuir-lote). RED documentado (4 falhas de função inexistente + 403 da checagem). GREEN: 16/16 backend, 20/20 regras. Publicada (ref 9d985f7). Aguardando teste humano.

## 14/10/2026 — Task f5668cd8 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a COORTE-PESSOA: status de fria na aba Pessoas, pedido de saída bloqueando, protegidas nunca elegíveis. Seis tasks concluídas na Fase 1. Próxima: FUSAO-REVERSIVEL (1b7f6fc7).

## 14/10/2026 — Task f5668cd8 implementada (skip 0b32a7c)

COORTE-PESSOA verde (SPEC-1-002, CA-1-002-03): função pura elegibilidadeFria; migração 0014 (índices únicos de origens e bloqueios); hook pessoas_origens_coorte (entrada fria não apaga relacionamento); aba Pessoas com status + pedido de saída. GREEN: 15/15 backend, 16/16 regras. Publicada (ref 0b32a7c).

## 14/10/2026 — Task 3b736d7d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a IDENTIDADE: duplicata recusada, fila de conflitos, nada fundido automaticamente.

## 14/10/2026 — Task 3b736d7d implementada (skip 02e2b6c)

IDENTIDADE verde (SPEC-1-002, CA-1-002-02): migração 0013 (conflitos_cadastro); resolverIdentidade; hook com correção CRÍTICA do guia 3.3 (e.next()); aba Conflitos. GREEN: 14/14 backend, 11/11 regras. Publicada (ref 02e2b6c).

## 14/10/2026 — Task ea6db5dc CONCLUÍDA (teste humano aprovado)

José testou e aprovou o cadastro único.

## 14/10/2026 — Task ea6db5dc implementada (skip 0083166)

Cadastro único (CA-1-002-01): migração 0012 (5 coleções + relations em leads); backfill rastreável (93 leads, 95 empresas, 11 pessoas); cadastro.ts; tela /cadastro. Publicada (ref 0083166).

## 14/10/2026 — Task d3e7d6da CONCLUÍDA (teste humano aprovado)

José testou e aprovou a ACL.

## 14/10/2026 — Task d3e7d6da implementada (skip f581d6f)

ACL-NEGATIVA verde: migrações 0010/0011 + dono do lead pai nos 3 hooks de IA. Publicada (ref f581d6f).

## 14/10/2026 — Task 490ec843 FECHADA (v0.0.30)

Aviso visível validado. Task concluída (a0ac0e2).

## 14/10/2026 — Ajustes do aviso (skips 924fdac, 05c0c50, a0ac0e2)

Tooltip → aviso visível → w-full → gap-y-1. Lições: tooltip nativo não é feedback confiável; flex-wrap precisa de gap vertical.

## 14/10/2026 — Task 490ec843 implementada (skip 2fce7e3)

Gates SEM-EXTERNO: recursoHabilitado, botões desabilitados na UI, testes SEM-EXTERNO. Publicada (ref 2fce7e3).

## 15/10/2026 — Task 20c86bd2 concluída

Teste humano aprovado pelo José.

## 14/10/2026 — Correção após teste humano (skip 47d5170)

Migração 0008 dividida em 0008/0009; bool/number sem required; recarga forçada dos hooks (v0.0.22).

## 14/10/2026 — Task 20c86bd2 implementada e publicada (skip 88790b4)

Ambiente de demonstração preparado e provado. Publicada (ref 88790b4).

## 14/10/2026 — Repositório operacional no GitHub

Publicação autorizada pelo consultor na conta drkgod, como repositório privado simbiosia.

## 14/10/2026 — Pacote da fase 1

Preparadas seis SPECs com TDD e 19 tasks, com IDs preservados. Aplicação base Funil MKT; Fábrica será módulo.
