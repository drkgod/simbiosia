# Histórico da construção

## 14/10/2026 — Selo Fundida na aba Pessoas (skip f534296)

José observou: após fundir, as duas pessoas continuam listadas sem explicação. Comportamento correto (fusão nunca apaga — é o que permite reverter), mas faltava clareza. Selo "Fundida — ver aba Fusões" adicionado ao card da pessoa fundida; verificado em produção (v0.0.47, ref f534296).

## 14/10/2026 — Fusão por seleção (skip 97da55c)

José tentou fundir colando o NOME nos campos de ID — erro 404 correto da rota, mas UX errada da tela. Trocado por dois dropdowns de seleção (nome + e-mail). Verificado em produção (v0.0.46, ref 97da55c). Lição: nunca pedir ID interno ao usuário.

## 14/10/2026 — Task 1b7f6fc7 implementada (skip 9d985f7)

FUSAO-REVERSIVEL verde (SPEC-1-002, CA-1-002-04): migração 0015 (fundida_em); rotas fundir/reverter-fusao (só admin, snapshot, 409 revisão com alteração posterior); avaliarReversao; aba Fusões em /cadastro. Correção no meio: e.auth.getString("role") em vez de e.auth.role. GREEN: 16/16 backend, 20/20 regras. Publicada (ref 9d985f7).

## 14/10/2026 — Task f5668cd8 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a COORTE-PESSOA. Seis tasks concluídas na Fase 1.

## 14/10/2026 — Task f5668cd8 implementada (skip 0b32a7c)

COORTE-PESSOA verde (CA-1-002-03): elegibilidadeFria; migração 0014; hook de coorte; status na aba Pessoas. GREEN: 15/15 backend, 16/16 regras. Publicada (ref 0b32a7c).

## 14/10/2026 — Task 3b736d7d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a IDENTIDADE.

## 14/10/2026 — Task 3b736d7d implementada (skip 02e2b6c)

IDENTIDADE verde (CA-1-002-02): migração 0013; resolverIdentidade; hook com e.next(); aba Conflitos. GREEN: 14/14 backend, 11/11 regras. Publicada (ref 02e2b6c).

## 14/10/2026 — Task ea6db5dc CONCLUÍDA (teste humano aprovado)

José testou e aprovou o cadastro único.

## 14/10/2026 — Task ea6db5dc implementada (skip 0083166)

Cadastro único (CA-1-002-01): migração 0012; backfill rastreável; cadastro.ts; tela /cadastro. Publicada (ref 0083166).

## 14/10/2026 — Task d3e7d6da CONCLUÍDA (teste humano aprovado)

José testou e aprovou a ACL.

## 14/10/2026 — Task d3e7d6da implementada (skip f581d6f)

ACL-NEGATIVA verde: migrações 0010/0011 + dono do lead pai nos 3 hooks de IA. Publicada (ref f581d6f).

## 14/10/2026 — Task 490ec843 FECHADA (v0.0.30)

Aviso visível validado (a0ac0e2).

## 14/10/2026 — Ajustes do aviso (skips 924fdac, 05c0c50, a0ac0e2)

Tooltip → aviso visível → w-full → gap-y-1.

## 14/10/2026 — Task 490ec843 implementada (skip 2fce7e3)

Gates SEM-EXTERNO. Publicada (ref 2fce7e3).

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
