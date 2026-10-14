# Histórico da construção

## 14/10/2026 — Task 3b736d7d CONCLUÍDA (teste humano aprovado)

José testou e aprovou a IDENTIDADE: duplicata recusada, fila de conflitos com decisão do admin, nada fundido automaticamente. Cinco tasks concluídas na Fase 1 (20c86bd2, 490ec843, d3e7d6da, ea6db5dc, 3b736d7d). Próxima: COORTE-PESSOA (f5668cd8), que depende desta.

## 14/10/2026 — Task 3b736d7d implementada (skip 02e2b6c)

IDENTIDADE verde (SPEC-1-002, CA-1-002-02): migração 0013 (conflitos_cadastro, fila de revisão do admin); função pura resolverIdentidade no cadastro.ts (reusar/criar/conflito; e-mail genérico e "mesma empresa" nunca identificam pessoa); hook pessoas_identidade com correção CRÍTICA do guia 3.3 (record hook sem e.next() engolia a escrita em silêncio — toda via agora termina em e.next() ou throw); aba Conflitos em /cadastro (admin marca "pessoas distintas"; fusão fica para a FUSAO-REVERSIVEL). RED documentado: duplicata de telefone era aceita sem registro de conflito. GREEN: 14/14 backend, 11/11 regras. Publicada (ref 02e2b6c).

## 14/10/2026 — Task ea6db5dc CONCLUÍDA (teste humano aprovado)

José testou e aprovou o cadastro único: abas Empresas (95) e Pessoas (11) com dados do backfill, cadastro funcionando, leads legados intactos. Quatro tasks concluídas na Fase 1 (20c86bd2, 490ec843, d3e7d6da, ea6db5dc). Próxima: IDENTIDADE (3b736d7d), que depende desta.

## 14/10/2026 — Task ea6db5dc implementada (skip 0083166)

Cadastro único (SPEC-1-002, CA-1-002-01): migração 0012 (empresas, pessoas, pessoas_origens, bloqueios_contato, fusoes_cadastro com RLS só-admin + relations aditivas empresa/pessoa/migracao_origem_id em leads); backfill rastreável (93 leads vinculados, 95 empresas, 11 pessoas — sem fusão por nome, contato genérico não vira pessoa); serviço cadastro.ts com normalizações (e-mail/telefone/LinkedIn) e tela /cadastro (abas Empresas/Pessoas). RED confirmado (404 coleções inexistentes) → GREEN; regressão 11/11 backend + 7/7 regras. Publicada (ref 0083166).

## 14/10/2026 — Task d3e7d6da CONCLUÍDA (teste humano aprovado)

José testou e aprovou: vendedor vê só a própria base, botões de IA desabilitados em TESTE, admin com acesso integral. Três primeiras tasks da SPEC-1-001 concluídas (20c86bd2, 490ec843, d3e7d6da).

## 14/10/2026 — Task d3e7d6da implementada (skip f581d6f)

ACL-NEGATIVA verde: migração 0010 (role imutável na autoedição com :changed, filhas pelo dono do lead pai, sistema_config/teste_despachos só-admin) + 0011 (owner imutável com :changed — a sintaxe @request.body.owner = owner não bloqueia) + verificação do dono do lead pai nos 3 hooks de IA (recusa 404 antes de expor dados). Lições: lista com regra negada no PocketBase pode voltar 200-vazio (asserção ajustada); contaminação de massa de teste corrigida. Publicada (ref f581d6f).

## 14/10/2026 — Task 490ec843 FECHADA (validação final v0.0.30)

José revalidou ("Testei e funcionou"): aviso visível, abaixo do botão, com respiro. Task concluída na versão a0ac0e2.

## 14/10/2026 — Respiro vertical do aviso (skip a0ac0e2)

Aviso encostava no botão (flex-wrap sem gap vertical). gap-y-1 adicionado; verificado em produção (v0.0.30, ref a0ac0e2): folga de 4px entre botão e aviso.

## 14/10/2026 — Posição do aviso corrigida (skip 05c0c50)

José reportou: aviso aparecia ao lado direito e levemente acima do botão (contêiner flex flex-wrap). Corrigido com w-full (linha própria), verificado geometricamente em produção (v0.0.29, ref 05c0c50): abaixo do botão e na mesma coluna.

## 14/10/2026 — Ajuste pós-teste humano (skip 924fdac)

José testou: botão desabilitado ok, mas o aviso (tooltip title) não aparecia ao passar o mouse. Trocado por aviso visível sob cada botão de IA ("Recurso desabilitado no ambiente de teste"), verificado em produção (v0.0.28, ref 924fdac). Lição: tooltip nativo não é feedback confiável — usar texto visível.

## 14/10/2026 — Task 490ec843 implementada (skip 2fce7e3)

Gates SEM-EXTERNO completos: função pura recursoHabilitado (falha fechada), botões de IA desabilitados na UI em ambiente de teste (com aviso), testes SEM-EXTERNO no test:backend (contador 0 nas 3 rotas como admin, controle positivo = 1 sem rede, PATCH de flags negado a não-admin, restore ok). RED→GREEN documentado. Publicada em produção (ref 2fce7e3).

## 15/10/2026 — Task 20c86bd2 concluída

Teste humano aprovado pelo José (selo TESTE visível, botão de IA respondendo interceptado, celular ok). Task fechada.

## 14/10/2026 — Correção após teste humano (skip 47d5170)

Erros reportados pelo José: selo não aparecia; botão de IA não encontrado. Causas: (1) migração 0008 com dois blocos migrate() no mesmo arquivo foi rejeitada pelo runtime — dividida em 0008/0009; (2) campos bool/number com required rejeitam false/0 como blank — required removido; (3) hooks em execução estavam na versão antiga — recarga forçada (v0.0.22). Verificado em produção.

## 14/10/2026 — Task 20c86bd2 implementada e publicada (skip 88790b4)

Ambiente de demonstração preparado e provado: migração 0008 (sistema_config + teste_despachos), despachos de IA instrumentados e neutralizados em TESTE, runners reais node:test, manifesto de versão e backup/restauração de fixtures sintéticas, selo AMBIENTE DE TESTE na UI. Publicada em https://funil-mkt-vendas-simbiosia-aa6fd.goskip.app (ref 88790b4).

## 14/10/2026 — Repositório operacional no GitHub

Publicação autorizada pelo consultor na conta drkgod, como repositório privado simbiosia. Fase 1 com seis SPECs e 19 tasks; os dois projetos de código preservados como submódulos dos repositórios de origem.

## 14/10/2026 — Pacote da fase 1

Preparadas seis SPECs com TDD e 19 tasks, com IDs preservados. Aplicação base Funil MKT; Fábrica será módulo.
