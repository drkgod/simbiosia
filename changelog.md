# Histórico da construção

## 15/10/2026 — Task 46c0fd51 implementada (skip 6a4b542)

ENTREVISTA verde (CA-1-004-03, SPEC-1-004): migração 0020 (entrevista_produtos: produto rel required, roteiro/respostas/pendencias json, status rascunho/confirmada, confirmada_por/em, ambiente; índice único produto+ambiente; RLS admin); hook entrevista_validar — confirmação com pendência é NEGADA no backend (400 com as pendências listadas), autor e data de confirmação registrados no servidor (não confia no payload), confirmação NÃO ativa produto; seção de entrevista na página /produtos: roteiro fixo de 10 perguntas (dor/área, decisor, segmento/porte, localização, campos/fontes, critérios, pesos, eliminatórias, faixas, abordagem), respostas parciais salvas como rascunho com pendências, botão Confirmar desabilitado enquanto houver pendência. TDD: backend 20/20 (ENTREVISTA), rules 47/47 (roteiro + completude). Ajustes: hook com lógica inline (scoping da VM de callbacks); teste do roteiro completo corrigido (10 respostas). Publicada (ref 6a4b542). Aguardando teste humano.

## 15/10/2026 — Task 7183cf59 CONCLUÍDA (teste humano aprovado)

José testou e aprovou o catálogo de produtos FAROL. Décima primeira task concluída na Fase 1; CA-1-004-01 entregue.

## 15/10/2026 — Task 7183cf59 implementada (skip 5e0fa0e)

PRODUTO-ACL verde (CA-1-004-01, SPEC-1-004): migração 0018 (coleção produtos) + 0019 (regras aplicadas na coleção existente — leitura para autenticados, escrita só admin); hook produtos_validar; página /produtos. Produto novo nasce rascunho/inativo. TDD: backend 19/19, rules 43/43. Publicada (ref 5e0fa0e).

## 15/10/2026 — Task 899395c4 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a retomada e o desfazer de importação após as duas correções. DÉCIMA task concluída na Fase 1. SPEC-1-003 COMPLETA (CA-1-003-01/02/03/04).

## 15/10/2026 — Correção 2 no teste humano da 899395c4 (skip e135610)

Reimportar um arquivo cujo lote foi DESFEITO falhava com erro genérico: o lote revertido continuava dono da chave_lote. Correção: migração 0017 (índice parcial — só lotes vivos ocupam a chave) + busca por chave_lote FILTRA lotes revertidos. Backend 17/17, rules 43/43. v0.0.55 (e135610) publicada.

## 15/10/2026 — Correção no teste humano da 899395c4 (skip 2f1452a)

Botão de confirmação do "Desfazer este lote" ficava esmaecido no 2º clique (disabled incluía o próprio estado de confirmação). Corrigido + aviso visível. v0.0.52 (2f1452a) publicada.

## 15/10/2026 — Task 899395c4 implementada (skip f66fd73)

IMPORTACAO-RETOMADA verde (CA-1-003-03): retomada no confirmar; rota reverter (só admin); UI desfazer. Rules 43/43, backend 16/16. Provas: reversão com ZERO restos; re-reversão recusada; vendedor 403; retomada ok. Publicada (ref f66fd73).

## 15/10/2026 — Task 0574671d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a importação com relatório por linha. Nona task concluída na Fase 1.

## 15/10/2026 — Task 0574671d implementada (skip 825971e)

IMPORTACAO-RELATORIO verde (CA-1-003-02): migração 0016; hook confirmar; UI com relatório por linha. Rules 36/36, backend 16/16. Prova: 6 linhas classificadas, reconciliação ok, repetição sem duplicar, vendedor 403. Publicada (ref 825971e).

## 14/10/2026 — Task c7240db4 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a prévia da base. Oitava task concluída na Fase 1.

## 14/10/2026 — Task c7240db4 implementada (skip 6b91544)

CSV-PREVIA + CSV-SEGURANCA verdes: módulo puro csv.ts (parser completo, 2 bugs corrigidos pelo TDD) + tela /importar com prévia. GREEN 28/28 regras. Publicada (ref 6b91544).

## 14/10/2026 — Task 1b7f6fc7 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a FUSAO-REVERSIVEL. SPEC-1-002 COMPLETA.

## 14/10/2026 — Selo Fundida na aba Pessoas (skip f534296)

Pessoas fundidas continuam listadas de propósito — agora com selo (v0.0.47).

## 14/10/2026 — Fusão por seleção (skip 97da55c)

Dropdowns nome + e-mail no lugar de IDs crus (v0.0.46).

## 14/10/2026 — Task 1b7f6fc7 implementada (skip 9d985f7)

FUSAO-REVERSIVEL verde: migração 0015; rotas fundir/reverter-fusao; avaliarReversao; aba Fusões. GREEN: 16/16 backend, 20/20 regras. Publicada (ref 9d985f7).

## 14/10/2026 — Task f5668cd8 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a COORTE-PESSOA.

## 14/10/2026 — Task f5668cd8 implementada (skip 0b32a7c)

COORTE-PESSOA verde: elegibilidadeFria; migração 0014; hook de coorte; status na aba Pessoas. GREEN: 15/15 backend, 16/16 regras. Publicada (ref 0b32a7c).

## 14/10/2026 — Task 3b736d7d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a IDENTIDADE.

## 14/10/2026 — Task 3b736d7d implementada (skip 02e2b6c)

IDENTIDADE verde: migração 0013; resolverIdentidade; hook com e.next(); aba Conflitos. GREEN: 14/14 backend, 11/11 regras. Publicada (ref 02e2b6c).

## 14/10/2026 — Task ea6db5dc CONCLUÍDA (teste humano aprovado)

José testou e aprovou o cadastro único.

## 14/10/2026 — Task ea6db5dc implementada (skip 0083166)

Cadastro único: migração 0012; backfill rastreável; cadastro.ts; tela /cadastro. Publicada (ref 0083166).

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
