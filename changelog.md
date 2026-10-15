# Histórico da construção

## 15/10/2026 — Task 0574671d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a importação com relatório por linha. Nona task concluída na Fase 1; CA-1-003-02 entregue.

## 15/10/2026 — Task 0574671d implementada (skip 825971e)

IMPORTACAO-RELATORIO verde (CA-1-003-02): migração 0016 (importacoes com chave_lote única, importacao_linhas com checkpoint por linha, lista_membros com índice único lista+lead+ambiente); hook POST /backend/v1/importacoes/confirmar (só admin) — processa linha a linha com checkpoint persistido, resolve identidade contra TODA a base do ambiente (reusar/criar/conflito), não inventa decisor (sem contato individual ou e-mail genérico → incompleta, empresa entra pendente de contato), repetir lote concluído devolve o relatório existente sem duplicar, lote em andamento → 409; UI /importar com confirmação (coorte/dono/lista/ambiente) e relatório por linha com totais reconciliados. TDD: rules 36/36 (8 novos), backend 16/16. Prova funcional da rota no ambiente de teste: 6 linhas → 1 nova/2 repetidas/2 incompletas/1 erro, reconciliação ok, repetição devolveu o mesmo relatório, vendedor 403. Fixture de prova marcada e lotes marcados revertida (limpeza física fica para o rollback da 899395c4). Publicada (ref 825971e).

## 14/10/2026 — Task c7240db4 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a prévia da base. Oitava task concluída na Fase 1; primeira da SPEC-1-003.

## 14/10/2026 — Task c7240db4 implementada (skip 6b91544)

CSV-PREVIA + CSV-SEGURANCA verdes (SPEC-1-003, CA-1-003-01/04): módulo puro src/services/csv.ts — parser caracter a caracter (vírgula/ponto-e-vírgula, aspas escapadas, BOM, CRLF/LF, campo multilinha), detecção de separador fora de aspas, malformado e excedente de limite (1000 linhas/5 MB) recusados sem linhas, neutralização de fórmula (=,+,-,@ → apóstrofo). TDD achou e corrigiu 2 bugs reais do parser (aspas de fechamento reabriam campo; quebra após campo citado era rejeitada). Tela /importar: arquivo → prévia (separador, mapeamento automático das colunas, 5 primeiras linhas com fórmula neutralizada) → confirmação; nenhuma escrita antes. GREEN 28/28 regras. Publicada (ref 6b91544).

## 14/10/2026 — Task 1b7f6fc7 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a FUSAO-REVERSIVEL com os dois ajustes de UX (seleção por nome, selo Fundida). SPEC-1-002 COMPLETA (CA-01/02/03/04).

## 14/10/2026 — Selo Fundida na aba Pessoas (skip f534296)

Pessoas fundidas continuam listadas de propósito (reversibilidade) — agora com selo "Fundida — ver aba Fusões" (v0.0.47).

## 14/10/2026 — Fusão por seleção (skip 97da55c)

Dropdowns de seleção (nome + e-mail) no lugar de campos de ID cru (v0.0.46). Lição: nunca pedir ID interno ao usuário.

## 14/10/2026 — Task 1b7f6fc7 implementada (skip 9d985f7)

FUSAO-REVERSIVEL verde (CA-1-002-04): migração 0015 (fundida_em); rotas fundir/reverter-fusao (só admin, snapshot, 409 revisão); avaliarReversao; aba Fusões. GREEN: 16/16 backend, 20/20 regras. Publicada (ref 9d985f7).

## 14/10/2026 — Task f5668cd8 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a COORTE-PESSOA.

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
