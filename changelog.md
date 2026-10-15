# Histórico da construção

## 14/10/2026 — Task f5668cd8 CONCLUÍDA (teste humano aprovado)

José testou e aprovou a COORTE-PESSOA: status de fria na aba Pessoas, pedido de saída bloqueando, protegidas nunca elegíveis. Seis tasks concluídas na Fase 1 (20c86bd2, 490ec843, d3e7d6da, ea6db5dc, 3b736d7d, f5668cd8). Próxima: FUSAO-REVERSIVEL (1b7f6fc7), que depende desta.

## 14/10/2026 — Task f5668cd8 implementada (skip 0b32a7c)

COORTE-PESSOA verde (SPEC-1-002, CA-1-002-03): função pura elegibilidadeFria (pedido de saída vence tudo; relacionamento conhecido exclui da fria; mesma empresa NÃO protege); migração 0014 (índices únicos de origens e bloqueios — reimportação cria nova origem, nunca duplica bloqueio); hook pessoas_origens_coorte (entrada fria não apaga relacionamento anterior, guia 3.3 com e.next()); aba Pessoas mostra status (Protegida/Elegível/Bloqueada) + botão pedido de saída. RED documentado (5 falhas de função inexistente). GREEN: 15/15 backend, 16/16 regras. Publicada (ref 0b32a7c).

## 14/10/2026 — Task 3b736d7d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a IDENTIDADE: duplicata recusada, fila de conflitos com decisão do admin, nada fundido automaticamente. Cinco tasks concluídas na Fase 1. Próxima: COORTE-PESSOA (f5668cd8).

## 14/10/2026 — Task 3b736d7d implementada (skip 02e2b6c)

IDENTIDADE verde (SPEC-1-002, CA-1-002-02): migração 0013 (conflitos_cadastro); função pura resolverIdentidade (reusar/criar/conflito; e-mail genérico e "mesma empresa" nunca identificam pessoa); hook pessoas_identidade com correção CRÍTICA do guia 3.3 (record hook sem e.next() engolia a escrita em silêncio); aba Conflitos em /cadastro. RED documentado. GREEN: 14/14 backend, 11/11 regras. Publicada (ref 02e2b6c).

## 14/10/2026 — Task ea6db5dc CONCLUÍDA (teste humano aprovado)

José testou e aprovou o cadastro único: abas Empresas e Pessoas com dados do backfill, cadastro funcionando, leads legados intactos.

## 14/10/2026 — Task ea6db5dc implementada (skip 0083166)

Cadastro único (SPEC-1-002, CA-1-002-01): migração 0012 (empresas, pessoas, pessoas_origens, bloqueios_contato, fusoes_cadastro com RLS só-admin + relations aditivas em leads); backfill rastreável (93 leads, 95 empresas, 11 pessoas); serviço cadastro.ts com normalizações; tela /cadastro. RED→GREEN; regressão 11/11 backend + 7/7 regras. Publicada (ref 0083166).

## 14/10/2026 — Task d3e7d6da CONCLUÍDA (teste humano aprovado)

José testou e aprovou: vendedor vê só a própria base, botões de IA desabilitados em TESTE, admin com acesso integral.

## 14/10/2026 — Task d3e7d6da implementada (skip f581d6f)

ACL-NEGATIVA verde: migração 0010 (role imutável com :changed, filhas pelo dono do lead pai, config/despachos só-admin) + 0011 (owner imutável com :changed) + dono do lead pai nos 3 hooks de IA. Lições: lista negada pode voltar 200-vazio; contaminação de massa corrigida. Publicada (ref f581d6f).

## 14/10/2026 — Task 490ec843 FECHADA (validação final v0.0.30)

José revalidou: aviso visível, abaixo do botão, com respiro. Task concluída (a0ac0e2).

## 14/10/2026 — Respiro vertical do aviso (skip a0ac0e2)

gap-y-1 no contêiner flex; folga de 4px verificada em produção.

## 14/10/2026 — Posição do aviso corrigida (skip 05c0c50)

Aviso em linha própria (w-full), abaixo do botão e na mesma coluna (v0.0.29).

## 14/10/2026 — Ajuste pós-teste humano (skip 924fdac)

Tooltip title trocado por aviso visível sob os botões de IA (v0.0.28). Lição: tooltip nativo não é feedback confiável.

## 14/10/2026 — Task 490ec843 implementada (skip 2fce7e3)

Gates SEM-EXTERNO completos: recursoHabilitado (falha fechada), botões de IA desabilitados na UI, testes SEM-EXTERNO no test:backend. RED→GREEN. Publicada (ref 2fce7e3).

## 15/10/2026 — Task 20c86bd2 concluída

Teste humano aprovado pelo José. Task fechada.

## 14/10/2026 — Correção após teste humano (skip 47d5170)

Migração 0008 dividida em 0008/0009; bool/number sem required; recarga forçada dos hooks (v0.0.22).

## 14/10/2026 — Task 20c86bd2 implementada e publicada (skip 88790b4)

Ambiente de demonstração preparado e provado: migração 0008, IA neutralizada com observador, runners reais node:test, manifesto de versão, backup/restauração, selo AMBIENTE DE TESTE. Publicada (ref 88790b4).

## 14/10/2026 — Repositório operacional no GitHub

Publicação autorizada pelo consultor na conta drkgod, como repositório privado simbiosia. Fase 1 com seis SPECs e 19 tasks.

## 14/10/2026 — Pacote da fase 1

Preparadas seis SPECs com TDD e 19 tasks, com IDs preservados. Aplicação base Funil MKT; Fábrica será módulo.
